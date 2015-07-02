---
title: AccuRanker Partner API Reference

language_tabs:
  - shell: cURL
  - ruby: Ruby
  - python: Python
  - php: PHP

toc_footers:
  - <a href='mailto:support@accuranker.com?Subject=Partner+API'>Sign Up for a Developer Key</a>
  - <a href='https://www.accuranker.com/'>Go to AccuRanker.com</a>

includes:
  - errors

search: true
---

# AccuRanker Partner API

Welcome to the AccuRanker Partner API. You can use our API to get search results for
the keywords you post to it.

We have language bindings in Shell, Ruby, and Python! You can view code examples in the dark area to the right, and you can switch the programming language of the examples with the tabs in the top right.

If you want to experiment with the API, you can perform live requests from our
[API console](https://apigee.com/accuranker/embed/console/accuranker).

# Authentication

> If the auth_token is not included in the call, you will receive an error like this:

```json
{
    "title": "Missing parameter",
    "description": "The \"auth_token\" parameter is required."
}
```

> If the auth_token is invalid, you will receive an error like this:

```json
{
    "title": "Invalid auth_token",
    "description": "Please check that your authentication token is correct"
}
```

AccuRanker uses API keys to allow access to the API.

The API key must be included in all API requests. Add it as a parameter to the URL
being requested. For instance:

`/status?auth_token=your-auth-token`

The `auth_token` parameter is left out of the examples on this page. You must always include it. If missing you will receive an error like:

<aside class="notice">
You must replace <code>your-auth-token</code> with your personal API key.
</aside>

# Rate Limiting

> If you exceed the rate limit, you will receive an error like this:

```json
{
    "title": "Rate limit",
    "description": "You have hit the rate limit of your requests"
}
```

To ensure stability of the system, all accounts have rate limits in place.
The starting rate limits are 1000 `POST`s and 2000 `GET`s per hour.

Calls to certain endpoints does not impact your limits. You can use the status
endpoint to view your current limits without impacting your current limit.

Your current limits are returned as headers in the response:

`X-RateLimit-Limit` - Your maximum hourly limit.

`X-RateLimit-Remaining` - Remaining hourly limit.

Please contact us if you need the limits raised.

# Account Status

## Get Account Status

```shell
curl "http://api.accuranker.com/status"
```

> The above command returns JSON structured like this:

```json
{
    "limits": {
        "current": {
            "get": 2000,
            "post": 1000
        },
        "get": 2000,
        "post": 1000
    },
    "units": 9428470
}
```

This endpoint retrieves your account status. It includes your maximum limits,
your current limits and your remaining API units.

### HTTP Request

`GET http://api.accuranker.com/status`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
include_cats | false | If set to true, the result will also include cats.
available | true | If set to false, the result will include kittens that have already been adopted.

<aside class="success">
Remember — you must include the auth_token as a parameter in your request!
</aside>

# Search Engines

## List All Search Engines

```shell
curl "http://api.accuranker.com/engines"
```

> The above command returns JSON structured like this:

```json
[
  {
    "engine": "google",
    "supported": true,
    "locales": {
      "af-na": {
        "description": "Namibia - Afrikaans",
        "tld": "www.google.com.na"
      },
      "af-za": {
        "description": "South Africa - Afrikaans",
        "tld": "www.google.co.za"
      },
      "am-et": {
        "description": "Ethiopia - Amharic",
        "tld": "www.google.com.et"
      },
      "ar-ae": {
        "description": "United Arab Emirates - Arabic",
        "tld": "www.google.ae"
      }
    }
  },
  {
    "engine": "bing",
    "supported": false,
    "locales": {
      "da-dk": {
        "description": "Denmark - Danish",
        "tld": "www.bing.dk"
      },
      "de-at": {
        "description": "Austria - German",
        "tld": "www.bing.at"
      },
      "de-ch": {
        "description": "Switzerland - German",
        "tld": "www.bing.ch"
      },
      "de-de": {
        "description": "Germany - German",
        "tld": "www.bing.de"
      }
    }
  }
]
```

This endpoint will return a list of all search engines and their supported locales.

### HTTP Request

`GET http://api.accuranker.com/engines`

# Keywords

## Submit Keyword to Queue

```shell
curl "http://api.accuranker.com/keywords"
  -d "keyword=search+for&engine=google&locale=en-us&callback=http://requestb.in/xfel53xf&geo=New+York&mobile=false&tablet=false"
```

> When you submit a keyword to the queue, you will receive a response like this.
> It will only contain the fields that you specified in the request.

```json
{
  "callback": "http://requestb.in/xfel53xf",
  "engine": "google",
  "keyword": "search for",
  "locale": "en-us",
  "geo": "New York",
  "mobile": false,
  "tablet": false
}
```

This endpoint will submit the specified keyword to the search queue.

If you want to perform a default desktop search, simply leaving both `mobile`
and `tablet` blank or set to false.

If no callback is specified, the callback URL set in your account will be used.

When testing we recommend that you use [requestb.in](http://requestb.in) as
the callback URL. It will allow you to inspect the callback.

### HTTP Request

`POST http://api.accuranker.com/keywords`

### Query Payload

Parameter | Required | Default | Description
--------- | -------- | ------- | -----------
keyword | true | | Make sure it's utf-8 encoded.
engine | false | google | See available engines [here](#list-all-search-engines).
locale | false | en-us | See available locales [here](#list-all-search-engines).
callback | false | | This URL will receive the callback when the search is complete.
geo | false | | Set the geo-location of the search.
mobile | false | false | Search as a mobile device.
tablet | false | false | Search as a tablet.

## Callback

When the search is complete, you will receive a callback from AccuRanker. This is
how it looks.

### HTTP Request

`POST http://your-specific-callback.url/`

<aside class="warning">
This is not an endpoint at AccuRanker, but rather what will be sent to the
callback URL that you specify in the queue request.
</aside>

### Query Payload

Parameter | Example | Description
--------- | ------- | -----------
status | success | `success` if the search was successful, `failed` if not.
status_code | 200 | <TODO>
status_description | Success | <TODO>
rank_date | 2015-12-01 | Date (in UTC) of the search.
json_callback | | URL to query for the parsed JSON result.
html_callback | | URL to query for the raw HTML result.
keyword | search for | Keyword searched for.
engine | google | Engine searched.
locale | en-us | Locale searched.
geo | New York | The location of the search.
mobile | false | Search performed from mobile device.
tablet | false | Search performed from tablet.

## Get Search Result

```shell
curl "http://api.accuranker.com/results?id=<ID>&format=json"
```

> The above command returns JSON structured like this:

```json
{
  "engine": "google",
  "requested_at": "2015-06-24 13:33:24.525716+00:00",
  "total_number_of_results": 1200000,
  "tablet": false,
  "mobile": false,
  "set_location": "",
  "locale": "en-us",
  "rank_date": "2015-06-24",
  "keyword": "best electric razor for women",
  "serp": {
    "1": {
      "url": "http://itsgr9.com/top-10-best-electric-shavers-women-review/",
      "base_domain": "itsgr9.com",
      "base_url": "itsgr9.com",
      "description": "There are a lot of electric razors available for women, and choosing just one can prove to be difficult. If you want to make sure you are choosing the right razor for ...",
      "title": "Top 10 Best Electric Shavers For Women in 2015 ... - ItsGr9"
    },
    "2": {
      "url": "http://www.realsimple.com/beauty-fashion/hair/hair-removal/best-razors-women",
      "base_domain": "realsimple.com",
      "base_url": "www.realsimple.com",
      "description": "Best Electric. Remington Wet/Dry Shaver This rechargeable has two rows of blades for regular shaving (one is visible here), which sit atop a third row that can ...",
      "title": "The Best Razors for Women | Real Simple"
    },
    "3": {
      "url": "http://www.shavingmachine.org/best-electric-shavers-women/",
      "base_domain": "shavingmachine.org",
      "base_url": "www.shavingmachine.org",
      "description": "Check our reviews and comparisons of best electric razors for women 2015! Learn about top rated female electric shavers and find out about the best deals!",
      "title": "Best Electric Shaver For Women 2014/2015"
    },
    "4": {
      "url": "http://www.amazon.com/Panasonic-ES2216PC-Curves-Shaver-Attachment/product-reviews/B000G0GW6K",
      "base_domain": "amazon.com",
      "base_url": "www.amazon.com",
      "description": "I had never tried electric shaving, but this Panasonic shaver is inexpensive and has ... Given that women shave more of their bodies than men it surprises me that there .... No razor burn, no irritated skin, and best of all a really close shave.",
      "title": "Panasonic ES2216PC Close Curves Wet/Dry Shaver for ..."
    },
    "5": {
      "url": "http://www.bestbuy.com/site/shavers-trimmers/womens-electric-shavers/abcat0915008.c?id=abcat0915008",
      "base_domain": "bestbuy.com",
      "base_url": "www.bestbuy.com",
      "description": "Free Shipping on womens electric shavers and razors at BestBuy.com. Find a variety of products including bikini shavers, pink shavers, ladies cordless razors ...",
      "title": "Women's Electric Shavers - Free Shipping - Best Buy"
    }
  }
}
```

This endpoint will return the search result as parsed JSON or raw HTML.

The URL for the search result is returned in the callback. You must however
append the `auth_token` to the URL before requesting the result.

### HTTP Request

`GET http://api.accuranker.com/results`

### Query Parameters

Parameter | Required | Default | Description
--------- | -------- | ------- | -----------
id | true | | The ID is returned in the callback.
format | false | json | This can be either json or html.

<aside class="notice">
Remember — search results are only stored for seven days, so remember to get the result before that.
</aside>
