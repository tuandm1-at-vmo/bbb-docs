# Cannondale

## All bikes

Accessing the site [[1]] lets us see all available bikes on the Cannondale site.

Some thing called `categories` can be easily retrieved via the followinng part in the Cannondale's HTML source:

```html
<script>window.plpSearchData.categories.push("{DD87270D-12A4-49C8-8585-F65EB493F799}");</script>
```

We will use this `categories` for querying Cannondale bikes by invoking this **search API**:

```http
POST https://www.cannondale.com/api/coveo/search

{
    "ConstantQueryExpression": "@source==Cannondale-Push",
    "AdvancedQueryExpression": "@language==\"en\" @firstavailableon<=today+0d @categories=('DD87270D-12A4-49C8-8585-F65EB493F799')",
    "Pipeline": "cannondale",
    "EnableDuplicateFilter": false,
    "NumberOfResults": 1000
}
```

<details>
  <summary>The query result will lay in the <code>Data.QueryResult</code> field of the response's body. After some extraction steps, the bike's very-first information can be obtained.</summary>

  ```json
  {
    "title": "Topstone Neo 5",
    /* REDUCTED */
    "raw": {
      "salsifyid": "0375-2021-SMU",
      /* REDUCTED */
      "variants": [
        {
          "sku": "C62561M10SM",
          /** REDUCTED **/
        },
        /** REDUCTED **/
      ]
    }
  }
  ```

  <em>The JSON-completed file is available <a href="result.json">here</a>.</em>

</details>

## Bike details

The very-first information of a bike we have obtained from the **search API** is not sufficient due to its lacking of the pricing data (including the bike's `MSRP`).

To retrieve the pricing-relevant data, the **product API** has to take place:

```http
GET https://www.cannondale.com/en/api/products/{{salsifyid}}
```

The URI variable `salsifyid` has been already available from the previous response. The responded data will look like:

```json
{
  "IsSuccessful": true,
  "Data": [
    {
      /* REDUCTED */
    }
  ]
}
```

<details>
  <summary>The only object in the <code>Data</code> field consists of the information (also containing the ones obtainable from the <strong>search API</strong>) of the target bike.</summary>

  ```json
  {
    "SalsifyId": "0375-2021-SMU",
    "ModelYear": "2021",
    "Category": "Electric",
    "Subcategory": "E-Road",
    "Title": "Topstone Neo",
    "Subtitle": "5",
    "Name": "2021-Topstone Neo 5 SMU",
    "DisplayName": "2021-Topstone Neo 5 SMU",
    /* REDUCTED */
    "Variants": [
      {
        "Sku": "C62561M10SM",
        "ColorName": "Black Pearl",
        "Size": "SM",
        "Upc": "884603865231",
        "Prices": [
          {
            "Msrp": 7215.0,
            /* REDUCTED */
          },
          /* REDUCTED */
        ]
      },
      /* REDUCTED */
    ]
  }
  ```

  <em>The JSON-completed file is available <a href="product.json">here</a>.</em>

</details>

## However

Since a Cannondale bike can have as many variants as its colors and sizes, the `Data.Variants.Prices` field in the **product API**'s response does not contain a single `Msrp` value, and the Cannondale site itself does not show any pricing piece of the bike, we cannot detect the primary `MSRP` of the bike to store its information in our system.

> The development team has made some comparisons between multiple bike-saling sources just to detect which information should be taken but it seems like the information is not completely consistent between those. We are trying our best to figure out a way to handle this problem.

[1]: https://www.cannondale.com/en/bikes
