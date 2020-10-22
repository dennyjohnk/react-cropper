# TRD Header Bidding
Header Bidding using Prebid.js. This project need to be JS project. The output file should be js files.

## Goal:
* Use prebid latest version
* Use Consent Management & Enforcement module to comply with GDPR & CCPA
* Make is an module to add and remove bidder's information from prebid.

## Requiments:
* [Prebid.js 4.0 and up](https://github.com/prebid/Prebid.js)
* Modules
  * [GDPR Consent Management](https://docs.prebid.org/dev-docs/modules/consentManagement.html)
  * [GDPR Enforcement](https://docs.prebid.org/dev-docs/modules/gdprEnforcement.html)
  * [US Privacy Consent Management](https://docs.prebid.org/dev-docs/modules/consentManagementUsp.html)
* Custom Bidder manager module
  * Please see the below on what we are looking for in the module

## Steps:
1. Build this project using [prebid.js](https://github.com/prebid/Prebid.js) GitHub Repo. You will need to clone it into this repo. How to setup and install guide [https://github.com/prebid/Prebid.js#install](https://github.com/prebid/Prebid.js#install).
2. Add the module listed in requiments section to this project. How to setup and use modules guide [https://github.com/prebid/Prebid.js#build-optimization](https://github.com/prebid/Prebid.js#build-optimization)
3. Build module which will read the CSV file of the bidder in `src/bidders.csv` and created `adBidders` js object durning build process.

### CSV File
Below are the columns for CSV file. Anything after devices will need to be `param` object. Any column value which is empty will not be added to object.
* bidder - `appnexus`
* parent ad unit - `trd-chicago`
* postions - `top`
* devices - when set to `all` this key shouldn't be set in the object
* placementId - base on bidder
* accountId - base on bidder
* siteId - base on bidder
* zoneId - base on bidder

Here is example how the adBidders object should look like.

#### Ad Bidders Object
```js
var adBidders = {
  'trd-chicago': {
    top: [
      {
        bidder: 'appnexus',
        params: {
          placementId: 13144370
        }
      },
      {
        bidder: 'rubicon',
        params: {
          accountId: "20242",
          siteId: "242082",
          zoneId: "1194328"
        },
        devices: [ "desktop", "tablet" ]
      },
      ...
    ]
  }
};
```

#### Ad Units Object
Use can loop over the `adPositions` and `adBidders` to build `adUnits` object array.

Example:
```js
var adUnits = [
  {
    code: pageSlotPrefix + '/' + 'top',
    mediaTypes: {
      banner: {
        sizes: adSizes.top.sizes
      }
    },
    bids: adBidders[ 'trd-chicago' ][ 'top' ]
  },
  ...
];
```

Global vars
```js
var networkCode     = '1015965'; // will not change
var topLevelAdUnit  = 'trd-chicago'; // will change base on region
var pageLevelAdUnit = 'news-articles'; // will change base on page
var pageSlotPrefix  = networkCode + '/' + topLevelAdUnit + '/' + pageLevelAdUnit; // add all to make prefix slot name
var device          = 'mobile'; // can be "mobile, tablet, or desktop"
var adPositions     = {
  "skin": {
    id: "div-id-for-skin",
    desktop: "OutOfPage",
    tablet: false,
    mobile: false,
    lazyload: 0,
    refresh: 0
    },
  "pushdown": {
    id: "div-id-for-pushdown", 
    desktop: [[1,1]], 
    tablet: false, 
    mobile: false, 
    lazyload: 0, 
    refresh: 0
  },
  ...
};
```
