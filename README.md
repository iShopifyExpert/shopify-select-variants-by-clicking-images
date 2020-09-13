# shopify-select-variants-by-clicking-images

From your Shopify admin, go to Online Store > Themes.

Find the theme you want to edit, and then click Actions > Edit code.

In the Sections folder, open the product-template.liquid template. (If your theme is Venture, see the note above.)

Copy all the code in this file hosted on GitHub.

Paste the copied code at the bottom of the product-template.liquid template.

Click Save.

Open the theme.js or theme.js.liquid file in your Assets folder.

Copy all the code in this file hosted on GitHub.

Paste the copied code at the bottom of the theme.js or theme.js.liquid file.

Click Save.




Honestly. All of this can be simplified as most of the code is working backwards to figure out which select should be used and what values should be set. Only 3 options are available so code can be super simple.

I simplified this by doing the following:

Added this script to the top of product-template.liquid:
      <script>
         var product_variant_map = {};
         {% for variant in product.variants %}
          product_variant_map["{{variant.id}}"] = {{ variant | json }};
         {% endfor %}
      </script>

Add data-variant-id to the img. This will require shifting from looping through product.variants rather than product.images. This should be acceptable as we only want to show variant images.

Add the following js:

    $(document).ready(function() {
     thumbnails = $('img[src*="/products/"]');
     if (thumbnails.length) {
       thumbnails.bind('click', function(event) {
         var item = $(event.currentTarget);
         // Update the alternate selector
          var v = product_variant_map[item.data('variant-id')];
          if ( v.option1 !== null ) { $('.single-option-selector:eq(0)').val(v.option1).trigger('change'); }
          if ( v.option2 !== null ) { $('.single-option-selector:eq(1)').val(v.option2).trigger('change'); }
          if ( v.option3 !== null ) { $('.single-option-selector:eq(2)').val(v.option3).trigger('change'); }
        });
     }
    });
    
    {% for image in product.images %}
                  {% unless image contains featured_image %}
                    <div class="grid__item one-fifth">
                      <a class="product-single__thumbnail">
                        <img class="product-single__thumb" src="{{ image.src | img_url: '150x150', crop: 'center' }}" alt="{{ image.alt | escape }}" data-variant-id="{% for variant in image.variants %}{% if image == variant.image %}{{ variant.id }}{% endif %}{% endfor %}">
                      </a>
                    </div>
                  {% endunless %}
    {% endfor %}
