# Add Infinite Scroll to Shopify's Debut theme

This one seems to come up alot, so I've done it for you, if you'd like to do it yourself you can follow the steps below.

# Lets get started!

We will be editing three files.

1. theme.liquid
2. collection-template.liquid
3. theme.scss



Step 1: theme.liquid
----

Locate and fine the following snippet in theme.liquid
At the time of writing it should be located on line 36

    {% include 'google-fonts' %}

Add the following script directly below it

    <script src="https://cdnjs.cloudflare.com/ajax/libs/jquery/3.2.1/jquery.min.js"></script>

Once you've done that locate the following snippet

    {% section 'footer' %}

Add the following directly below it

        <script>
      {% if template contains 'collection' %}
      function ScrollExecute() {
        if($(document).height() - 800 < ($(document).scrollTop() + $(window).height())) {
          var  scrollNode = $('#more').last();    
          var  scrollURL = $('#more p a').last().attr("href");
          if(scrollNode.length > 0 && scrollNode.css('display') != 'none') {
            $.ajax({
              type: 'GET',
              url: scrollURL,
              beforeSend: function() {
                scrollNode.clone().empty().insertAfter(scrollNode).append('<img src=\"{{ "ajax-loader.gif" | asset_img_url : '40x40'}}\" />');
                                                                          scrollNode.hide();
              },
              success: function(data) {
                // remove loading feedback
                scrollNode.next().remove();
                var filteredData = $(data).find("#append-data-here").html(),
                    nexturl =$(data).find('#more').html();
                $("#append-data-here").append(filteredData);
                console.log(nexturl);
                if(nexturl !== undefined)
                $("#more").html(nexturl).show();
                //filteredData.insertBefore( $("#product-list-foot") );                   
              },
              dataType: "html"
            });
          }
        }
      }

      $(document).ready(function () {
        $(window).scroll(function(){
          setTimeout(function(){
            ScrollExecute();
          },2000);
        });
      });
      {% endif %}
    </script>

Step 2: collection-template.liquid
----

On line 154 we need to add the ID to the div

    id='append-data-here'

You should end up with something like this

    <div id='append-data-here' class="grid grid--uniform{% if collection.products_count > 0 %} grid--view-items{% endif %}">

Locate and find the pagination, it should look like this:

    {% if paginate.pages > 1 %}
      {% include 'pagination' %}
    {% endif %}

Replace it with the following

    {% if paginate.next %}
    <div id="more">
      <p>
        <a href="{{ paginate.next.url }}">More</a>
      </p>
    </div>
    {% endif %}

Step 3: theme.scss
----

Lets add some styling real quick!

    .infinite-scroll {
    text-align: center;
    margin-top: 50px;
    display: block;
    }

    #more {
    text-align: center;
    margin: 4% 0;
    }
