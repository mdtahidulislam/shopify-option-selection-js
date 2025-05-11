# How to use:
## main product section:
``` liquid
{% if product.has_only_default_variant %}
  <input
    type="hidden"
    name="id"
    value="{{ product.selected_or_first_available_variant.id }}"
  >
{% else %}
  <select id="product-select" name="id">
    {% for variant in product.variants %}
      <option value="{{ variant.id }}">{{ variant.title }} - {{ variant.price | money }}</option>
    {% endfor %}
  </select>
{% endif %}
```
## theme.liquid:
``` js
{% if template == 'product' and product.has_only_default_variant == false %}
  <script src="{{ 'option_selection.js' | shopify_asset_url}}" defer="defer"></script>
  <script>
    document.addEventListener("DOMContentLoaded", (event) => {
      var selectCallback = function(variant, selector) {
        if (variant) {
          if (variant.available) {
            // Selected a valid variant that is available.
            $('#add-to-cart-button').removeClass('disabled').removeAttr('disabled').html('Add to Cart');
          } else {
            // Variant is sold out.
            $('#add-to-cart-button').html('Sold Out').addClass('disabled').attr('disabled', 'disabled');
          }
          // Whether the variant is in stock or not, we can update the price and compare at price.
          if ( variant.compare_at_price > variant.price ) {
            $('#price-field').html('<span class="product-price on-sale">'+ Shopify.formatMoney(variant.price, "{{ shop.money_with_currency_format }}") +'</span>'+'&nbsp;<s class="product-compare-price">'+Shopify.formatMoney(variant.compare_at_price, "{{ shop.money_with_currency_format }}")+ '</s>');
          } else {
            $('#price-field').html('<span class="product-price">'+  Shopify.formatMoney(variant.price, "{{ shop.money_with_currency_format }}") + '</span>' );
          }
        } else {
          // variant doesn't exist.
          $('#add-to-cart-button').val('Unavailable').addClass('disabled').attr('disabled', 'disabled');
        }
      }
      // initialize multi selector for product
      jQuery(function($) {
        new Shopify.OptionSelectors("YOURDOMID", {
          product: {{ product | json }} , // required
          onVariantSelected: selectCallback, // fire each time after selecting an option
          enableHistoryState: true // default: false
        });
      });
    });
  </script>
{% endif %}
```
