Telemetry CampaignService
=========================

CampaignService uses HTTP POST against *endpointurl*`/User/(campaignservice)`*functionname* for example if your test service is:

    url=https://test5823901.telemetry.com/
    username=x
    password=x

and you wish to perform the [getAdvertisers](#getAdvertisers) function, you perform an HTTP post against:

     https://test5823901.telemetry.com/User/(campaignservice)getAdvertisers

## getAdvertisers

Using the parameter `(campaignservice)getAdvertisers` this will return a mapping of ID:Name for all Advertisers available to your Campaign Self Service account. An Advertiser ID will be required to create and update Campaign data.

No input data is required for this function, besides the authentication cookie.

Example Output:

    {
      "success":true,
      "advertisers":
      {
        "800001":"Advertiser One",
        "800002":"Advertiser Two"
      }
    }


## getAdvertisersBrands

Using the parameter `(campaignservice)getAdvertisersBrands` this will return a mapping of ID:Name for all Advertisers, Brands, and a mapping of BrandID:AdvertiserID for the Brands' advertiser, that are available to your Campaign Self Service account. An Advertiser ID will be required to create and update Campaign data.

No input data is required for this function, besides the authentication cookie.

Example Output:

    {
      "success":true,
      "advertisers":
      {
        "800001":"Advertiser One",
        "800002":"Advertiser Two"
      },
      "brands":
      {
        "970001":"Brand One",
        "970002":"Brand Two",
        "970003":"Brand Three"
      },
      "brandadvertisers":
      {
        "970001":"800001",
        "970002":"800001",
        "970003":"800002"
      }
    }


## getRegions

Using the parameter `(campaignservice)getRegions` this will return a mapping of ID:Name for all Regions available to the provided Advertiser.

The HTTP POST argument (application/x-www-form-urlencoded) "advertiserID" should be set to the advertiser ID.

Example Input:

    advertiserID=800001

Example Output:

    {
      "success":true,
      "regions":
      {
        "920001":"USA",
        "920002":"Canada"
      }
    }

## getBrands


Using the parameter `(campaignservice)getBrands` this will return a mapping of ID:Name for all Brands available to the provided Advertiser. A Brand name will be required to create and update Campaign data.

The HTTP POST argument (application/x-www-form-urlencoded) "advertiserID" should be set to the advertiser ID.

Example Input:

    advertiserID=800001

Example Output:

    {
      "success":true,
      "brands":
      {
        "970001":"Brand One",
        "970002":"Brand Two"
      }
    }


## manageCampaign

Using the parameter `(campaignservice)manageCampaign` will create a new Campaign, or update an existing Campaign, using the supplied JSON data. Each Campaign is linked to an Advertiser and a Brand, and is a container for Placements used to deliver ads for that Brand. If an ID is supplied for an existing Campaign, that Campaign will be updated (see getCampaigns below to retrieve existing IDs from the system). In a campaign update, if either the year or the group suffix specified is different that that previously used for the campaign, then the campaign will be moved into a new group using the supplied options.

The input for a new Campaign requires an Advertiser ID (see getAdvertisers above), and a set of data representing the Campaign: this should contain a year, a brand name, and SQL-formatted start and end dates. Example:

The HTTP POST argument (application/x-www-form-urlencoded):

* `advertiserID` should be set to the advertiser ID
* `data[title]` should be the (display) name of the campaign
* `data[year]` should be the campaign year (usually the same as the start date)
* `data[brand]` should be the string name of the Brand (but matching something returned from getBrands above)
* `data[start]` should be the ISO-formatted start date
* `data[end]` should be the ISO-formatted end date
* `data[brand]` should be set to the brand name
* `data[groupSuffix]` optionally specifies the group name suffix to use (default is Telemetry)
* `data[country]` optionally specifies the name of the country (default is USA)

Example Input:

    advertiserID=800001&data[title]=New%20Campaign&data[year]=2014&data[brand]=Brand%20One&data[start]=2014-05-01&data[end]=2014-07-01

For an existing campaign, you must provide the Campaign ID as `data[campaignID]`:

    advertiserID=800001&data[title]=New%20Campaign&data[year]=2014&data[brand]=Brand%20One&data[start]=2014-05-01&data[end]=2014-07-01&data[campaignID]=642001

Successful Output:

     { "result": 1, "message": "Success", "data": [] }

## getCampaigns

Using the parameter `(campaignservice)getCampaigns` will return a mapping of ID:Name for Campaigns belonging to the provided Advertiser and year (under `campaigns`). A Campaign ID will be required for subsequent operations to manage Placements, Creatives, and Tags within that Campaign. The call also shows start & end dates for each campaign (under `campaignDates`), and whether or not a campaign is using "Advertiser Policies" (under `hasAdvertiserPolicies`). This latter flag should be used to determine whether not to use "Products" in the "Type" column during the `uploadPlans` call.

The HTTP POST argument (application/x-www-form-urlencoded) "advertiserID" should be set to the advertiser ID,
and the target campaign year as "year" should be supplied.

Example Input:

    advertiserID=800001&year=2014

Example Output:

    {
      "success":true,
      "campaigns":
      {
        "642001":"Renamed Campaign [2014]",
        "642002":"Campaign Two [2014]"
      },
      "campaignDates":
      {
        "642001":{ "start":"2014-01-01", "end":"2014-12-31" },
        "642002":{ "start":"2014-01-07", "end":"2014-12-01" }
      },
      "hasAdvertiserPolicies":
      {
        "642001":false,
        "642002":true
      }
    }
    

## getCampaignPlanEnd

Using the parameter `(campaignservice)getCampaignPlanEnd` will search all Placements' plans within the given campaign *after* the given threshold and return the date on which the final plan ends. If all plans end *before* the given threshold, the return valid will be 0. The return value is provided in the "end" variable of the output.

The HTTP POST argument (application/x-www-form-urlencoded) "campaignID" should be set to the campaign ID,
and the threshold is provided in SQL-format (date only)  as "threshold".

Example Input:

    campaignID=642001&threshold=2015-01-27

Example Output:

    {
      "success":true,
      "end": "2015-02-28"
    }
    
## getCampaignPlanEndPlacements

Using the parameter `(campaignservice)getCampaignPlanEndPlacements` will search all Placements' plans within the given campaign and return a list of placements with the date on which the final plan ends, the plan volume and the placement name. If nothing is found, it will return an empty array. The return values are provided in the "result" variable of the output.

The HTTP POST argument (application/x-www-form-urlencoded) "campaignID" should be set to the campaign ID.

Example Input:

    campaignID=642001

Example Output:

    {
      "success":true,
      "result": {
        "2165465": {
            "end_date": "2015-02-28",
            "volume": "0",
            "name": "Test Placement 1"
        },
        "2165565": {
            "end_date": "2015-07-18",
            "volume": "0",
            "name": "Test Placement 2"
        },
        "2165597": {
            "end_date": "2015-03-12",
            "volume": "0",
            "name": "Test Placement 3"
        }
      }
    }

## hideCampaign

Using the parameter `(campaignservice)hideCampaign` will ensure the given Campaign has been removed from all Groups, effectively hiding the Campaign from reporting. It will also clear any pending data on this Campaign (which may have occured during a manageCampaign or uploadPlans error).

The input for hideCampaign requires only a Campaign ID (see getCampaigns above). 

Example Input:

    campaignID=642001

Example Response:

Successful Output:

     { "result": 1, "message": "Success", "data": [] }


Once hidden, a Campaign can only be brought back into reporting using the [unhideCampaign](#unhideCampaign) call, with details of the year/group name it is to be assigned to.


## unhideCampaign

Using the parameter `(campaignservice)unhideCampaign` will ensure the given Campaign is available in reporting, by attaching it to a Group matching the given Advertiser, year, and (if specificed) a group name suffix and country name. If a Group cannot be found matching the given data, an error will be returned - the Group must exist from a previous manageCampaign call.

The input to un-hide a Campaign requires an Advertiser ID (see getAdvertisers above), and a set of data to assign the Campaign: this should contain at least a valid Campaign ID, and year. Example:

The HTTP POST argument (application/x-www-form-urlencoded):

* `advertiserID` should be set to the advertiser ID
* `data[campaignID]` should be set to the campaign ID
* `data[year]` should be the campaign year
* `data[groupSuffix]` optionally specifies the group name suffix to use (default is Telemetry)
* `data[country]` optionally specifies the country to use (default is USA)

Example Input:

    advertiserID=800001&data[year]=2015&data[campaignID]=642001

Successful Output:

     { "result": 1, "message": "Success", "data": [] }



## getVendors


Returns a mapping of ID:Name for all on-boarded Vendors available to the provided Advertiser. Vendor names will be required to upload Plan information into a Campaign; the Vendor must be associated with the Advertiser already through the on-boarding process that precedes each Vendor’s involvement in a Campaign.

Note that occasionally a vendor may be referred to with multiple names so note the "alternateVendorNames" section if it is present.

The HTTP POST argument (application/x-www-form-urlencoded) "advertiserID" should be set to the advertiser ID.

    advertiserID=800001

Example Output:

    {
      "success":true,
      "vendors":
      {
        "840001":"Test Vendor 1",
        "840002":"Test Vendor 2"
      },
      "alternateVendorNames":
      {
        "Test Vendor 1 Old Name":"840001"
      }
    }


## uploadPlans

Using the parameter `(campaignservice)uploadPlans` will import a spreadsheet of data into the given campaign, creating Placements and outlining their delivery and spend plans. There are also a number of optional attributes which can be provided to enhance Placement reporting and planning.

The HTTP POST argument contains a (multipart/form-data) file (CSV, XLS or XLSX) file called "file"
and an already-existing "campaignID" of the appropriate format.

The input spreadsheet should be separated into a line per Placement "flight". That is to say, a Placement’s information should be repeated for every set of delivery dates in the plan. See the [sample plan file](./samples/placement_and_plan.xlsx) file for an example.

The spreadsheet *must* contain these column headers:

* **Placement Name** - should be unique to the Vendor and Campaign
* **Site Name** - Vendor’s name
* **Start Date**
* **End Date** - signifies range for the given "flight" - dates should be SQL formatted, eg 2014-01-27
* **Units** - planned number of deliveries for the given date range
* **Type** - the type of Placement, or the type of Product which this item belongs to. Valid options are detailed below [1].
* **Cost Structure** - designates the method of payment for deliveries on the Placement; can be one of: 
 * CPM (cost per 1000 ad deliveries)
 * CPC (cost per ad click)
 * CPE (cost per ad engagement)
 * Fixed
 * Free
* **Rate** - the amount, in USD, of the rate of payment in relation to the provided Cost Structure

The spreadsheet *may* have these column headers:

* **Telemetry GUID** - supply an existing placement ID to modify the details and/or plan of this placement
* **External Ref** - the client’s own identifier for the placement, for easier reference in reporting
* **Io Id** - IO number if supplied by the agency
* **Tracking Level** - signifies the integration level of tags produced for this Placement, required when *not* using a "Product" in the "Type" column; can be one of: 
 * Integrated
 * VPAID
 * Mid Level
 * Pixel
 * Hulu
 * Hulu and Integrated

[1] the valid "Type" options depend on whether or not the campaign is set up to use "Advertiser Policies". If enabled, the Advertiser will be set in the system to use one or more "Products". Use of these Products is to generally indicate a set of different Placement types under a single planned line item. The value of `hasAdvertiserPolicies` within the `getCampaigns` response indicates, for each campaign, whether or not it is using Advertiser Policies, and thus using Products.

If using Advertiser Policies and Products, the Type value can be one of these groupings, or invidual products:

* **Desktop** or **Online Video**, contains Products:
 * Desktop Video
 * On Hulu
 * Site Served
 * On YouTube
* **Mobile**, contains Products:
 * Mobile Video
 * On Hulu Plus
* **Cross Device**, contains the Products for both *Mobile* and *Desktop*
* **Display**, a single-product grouping, for Banner items (not currently in use)
* **Package** - not a Product type or group, but allows tag items to be moved under it, using the packageItems function.

When a grouping is used, Placements are created for all Products under the grouping which are currently enabled for the Advertiser in the system, as per their policies.

Otherwise, if we are not using Products, then Type can be one of:

* **Online Video**
* **Display**
* **Mobile**
* **Package** - will not contain tag items unless items are moved under it, using the packageItems function.

Successful Output:

    { "result": 1, "message": "Success", "data": [] }



## packageItems

Using the parameter `(campaignservice)packageItems` will assign a set of placements underneath a "package" placement's plan. For each package assignment, you provide the ID for the parent; that is the item with the plan you wish to use, and a set of child IDs; that is those items you wish to move underneath this plan. 

All placements must belong to the given campaignID, and the same vendor in order to be packaged together.

The HTTP POST argument (application/x-www-form-urlencoded):

* `campaignID` should be the campaign containing all the given placements,
* `packages[0][parentID]` should be the ID of the item we wish to set as the first parent package,
* `packages[0][children][]` should be the ID of an item we wish to move into this first package,
* `packages[0][children][]` should be the ID of another item we wish to move into this first package,
* `packages[1][parentID]` should be the ID of the item we wish to set as the second parent package,
* `packages[1][children][]` should be the ID of an item we wish to move into this second package,

Example Input:

    campaignID=642001&packages[0][parentID]=3300080&packages[0][children][]=3300082&packages[0][children][]=3300081


Successful Output:

    { "result": 1, "message": "Success", "data": [] }


## getCreatives

Using the parameter `(campaignservice)getCreatives` will returns a mapping of ID:Info for Creatives (Ads) available to the given Campaign; ie those matching the Campaign’s Brand. Creative IDs will be needed when assigning Ads to Placements and managing tracking.

The HTTP POST argument (application/x-www-form-urlencoded) "campaignID" is a campaign ID that has
already been created.

Sample input:

    campaignID=642001


The output contains, for each Creative ID, the corresponding name and type. The type can be used for identifying between regular video ads, pixels, and companion ads. Example:

    {
      "success":true,
      "creatives":
      {
        "750001":
        {
          "name":"My Brand 640x480",
          "type":"Video"
        },
        "750002":
        {
          "name":"My Brand 300x60 Companion",
          "type":"Display/Companion"
        }
      }
    }

## getCreativesEditions

Using the parameter `(campaignservice)getCreativesEditions` will return a mapping of ID:Info for Creative Groups available to the given Brand.

The HTTP POST argument (application/x-www-form-urlencoded) "brandID" is a brand ID that has already been created.

Sample input:

    brandID=970001

The output contains, for each Creative Group ID, the corresponding name and the creatives in that group. For each Creative ID, the output contains the corresponding name, path and type. The path is the URI path on the CDN for the ad's primary flash video or banner file. The type can be used for identifying between regular video ads, pixels, and companion ads. If the creative group has been created with no creatives attached, the creatives data will be empty. Example:

    {
      "success":true,
      "creativegroups":
      {
        "710001":
        {
          "name":"My Brand 640x480",
          "creatives":{
          	"750001": {
          	  "name":"My Brand 640x480",
          	  "path":"/content/mybrand/mybrand_640x480.swf",
          	  "type":"Video"
          	}
          }
        },
        "710003":
        {
          "name":"My Brand 300x250 Companion",
          "creatives":[]
        }
      }
    }


## getAssignments

Used to determine which Ads are running on which Placements. Returns a set of JSON data representing the current assignment of Editions (aka Creatives) and Companion Creatives to Placements within the given Campaign ID. Should be used to determine existing Placement IDs, needed to update these Assignments and Tracking, and also create Tags.

The HTTP POST argument (application/x-www-form-urlencoded) "campaignID" should be set to the campaign ID.

Sample input:

    campaignID=642001

The JSON returned describes the Creatives assigned to each Placement, and each "series" of Companion Creatives on each assignment. In both cases, the "weighting" of the Creative or Companion is also shown. If the Placement has a schedule for Creatives & Companions, then the Creatives appear in a different object, with a start and end date for each scheduled block. The below example output shows 2 placements; one with a schedule comprised of 3 editions; and another with 1 edition, complete with 1 companion:

    {
      "success":true,
      "assignment":
      [
        {
          "placementID":3300081,
          "placementName":"Test Placement 1",
          "vendorProducts":["Desktop Video", "Mobile Video"]
          "edition_schedules":
          [
            {
              "editionID":750001,
              "editionName":"My Brand 640x480",
              "weight":100,
              "policy":"",
              "companionSeries":[],
              "start":"2014-08-01 00:00:00",
              "end":"2014-08-14 23:59:59"
            },
            {
              "editionID":750073,
              "editionName":"My Different Ad 640x480",
              "weight":50,
              "policy":"",
              "companionSeries":[],
              "start":"2014-08-15 00:00:00",
              "end":"2014-08-29 23:59:59"
            },
            {
              "editionID":750179,
              "editionName":"Another Ad 640x480",
              "weight":50,
              "policy":"",
              "companionSeries":[],
              "start":"2014-08-15 00:00:00",
              "end":"2014-08-29 23:59:59"
            }
          ]
        },
        {
          "placementID":3300082,
          "placementName":"Test Placement 2",
          "vendorProducts":["Desktop Video","On Hulu"],
          "editions":
          [
            {
              "editionID":750001,
              "editionName":"My Brand 640x480",
              "weight":100,
              "policy":"",
              "companionSeries":
              [
                {
                  "seriesID":1,
                  "weight":100,
                  "companions":
                  [
                    {
                      "companionID":750002,
                      "companionName":"My Brand 300x60 Companion",
                      "policy":""
                    }
                  ]
                }
              ]
            }
          ]
        }
      ]
    }
    
Note that the data also describes the Placement items in terms of ID, Name, and "Vendor Products" (if the campaign is using "Advertiser Policies"). We can use this information to validate the results of `uploadPlans`, before we assign any creatives - the returned objects will show each Placement, but with no Creatives assigned.

## uploadAssignments

Using the parameter `(campaignservice)uploadAssignments` will imports a spreadsheet of data describing the ads (Editions, aka Creatives) to run on a Placement, along with Companion Ads. The Placement IDs can be retrieved using getAssignments, and the Creative IDs should correspond to those pulled using the getCreatives operation.

The HTTP POST argument contains a (multipart/form-data) file (CSV, XLS or XLSX) file called "file"
and an already-existing "campaignID" of the appropriate format.

The input spreadsheet should be separated into a line per Placement-Edition, with the weight specified. For assigning companions. further lines are then created with the same Placement-Edition information, and a weighting and series ID for each Placement-Edition-Companion line. The "Series ID" is used to group multiple companions together; i.e. these will be served together alongside the same Placement-Edition. Please see [a sample assignment file](./samples/creative_assignment.xlsx).

You can also schedule which Creatives or Companions run at a particular time on a Placement. Repeat each row with UTC Start and End dates for every scheduled period, setting the weight of each active Creative or Companion at the appropriate time. When uploading, the full schedule must be included and will take precedence over any previous schedules on each Placement. Please see [an example of creative scheduling](./samples/creative_assignment_schedules.xlsx).

The spreadsheet *must* contain these column headers:

* **Placement ID** - Telemetry Guid for the Placement, use getAssignments operation to figure out the correct IDs. Note these are numeric so they must not have double-quotes around them.
* **Creative ID** - Telemetry Guid for the Creative, use getCreatives operation to figure out the correct IDs. Note these are numeric so they must not have double-quotes around them.
* **Weight** - The weighting for the Ad to be shown in relation to other ads on "rotation" for this Placement. Eg, 2 ads both at 50 weighting will each show half of the time. Note that if a Companion Creative ID is provided (see below) then this weighting describes that of the Companion on this combination. Note the weights numeric so they must not have double-quotes around them.

If Companions are required:

* **Companion Creative ID** - Telemetry Guid for the Creative, use getCreatives operation to figure out the correct IDs - should match a Creative of type "Display/Companion".  Note these are numeric so they must not have double-quotes around them.
* **Companion Series** - Those Companion Creatives on the same Placement-Edition combination with a matching Companion Series identifier will be shown at the same time. Thus the weighting must match for each Companion in a series. The identifier can be anything you like, so long as it is unique to the combination.
 
And if scheduling:

* **Start** - SQL formatted date (and optional time) *in UTC* for the start of a scheduled creative, eg "2014-08-01 09:00:00". If only a date is specified, it assumed to be the start of the given day (00:00:00).
* **End** - SQL formatted date (and optional time) *in UTC* for the end of a schedule creative. If only a date is specified, it is assumed to be the end of the given day (23:59:59). Within a placement's schedule, there must be no gaps and overlaps of time periods.

Additional optional values:

* **Policy** - Rules to apply during tag serving for the placement edition. The value in this column reads like a set of GET-line arguments, eg "MMCOUNTRY=225&DMA=\*gHAAACQygIBCIQADACA\*YAAAABA\*oCAAAw=". Some examples of Policy rules include MMCOUNTRY, MMDMA, and DMA - more details of the Policies can be found in [this document](./PolicyRules.md).
* **ISCI Code** - The ISCI code of the Video creative being assigned to the placement. This property should only be set on rows that represent the primary video weighting, not in companion assignment rows.

Note that you can include name columns (e.g. Placement Name, Creative Name) for reference, though these are not used during the upload process.

Note that when scheduling, the Creatives & Companions on the earliest time period will run from the moment of a successful `createTags` call until the given end date. Similarly, the Creatives on the latest time period will run indefinitely, until new schedules are uploaded, if a tag served beyond the given end date. If only one time block is specified, it will have the same effect as no schedule at all; these Creatives & Companions will run from now, indefinitely.

Succesful Output:

    { "result": 1, "message": "Success", "data": [] }
    

## getTracking

Using the parameter `(campaignservice)getTracking` will return a set of JSON data similar to the getAssignments operation, but with the inclusion of Tracking pixels assigned to Placement-Creative, and Placement-Creative-Companion combinations. Each combination may have multiple Tracking types, detailed in the uploadTracking operation, and each type may have multiple URLs. This is primarily used to check existing tracking contained within a given campaign.

The HTTP POST argument (application/x-www-form-urlencoded) "campaignID" is a campaign ID that has
already been created.

Sample input:

    campaignID=642001

The output shows assignments, minus weighting information, alongside tracking data (types and sets of pixel URLs). Note that a tracking pixel URL will appear if *any* of the tags associated with the placement includes the pixel; some of the tags may have had the URL excluded during `uploadTracking`. In the example output, a Placement-Edition has 2 "Ad Start" pixels, and a Placement-Edition-Companion contains both 1 "Ad Start" pixel and 1 "Clickthrough" tracking pixel:

    {
      "success":true,
      "assignment":
      [
        {
          "placementID":3300081,
          "placementName":"Test Placement 1",
          "editions":
          [
            {
              "editionID":750001,
              "editionName":"My Brand 640x480",
              "companions":[],
              "tracking":
              [
                {
                  "trackingType":"Ad Start",
                  "trackingPixels":
                  [
                    "www.telemetry.com\/sample_impression_tracking\/456",
                    "www.telemetry.com\/sample_impression_tracking_version_two\/456",
                  ]
                }
              ]
            },
            {
              "editionID":750073,
              "editionName":"My Different Ad 640x480",
              "companions":[],
              "tracking": []
            }
          ]
        },
        {
          "placementID":3300082,
          "placementName":"Test Placement 2",
          "editions":
          [
            {
              "editionID":750001,
              "editionName":"My Brand 640x480",
              "companions":
              [
                {
                  "companionID":750002,
                  "companionName":"My Brand 300x60 Companion",
                  "tracking":
                  [
                    {
                      "trackingType":"Ad Start",
                      "trackingPixels":
                      [
                        "www.telemetry.com\/sample_impression_tracking\/123"
                      ]
                    },
                    {
                      "trackingType":"Clickthrough",
                      "trackingPixels":
                      [
                        "www.telemetry.com\/click_tracking\/123"
                      ]
                    }
                  ]
                }
              ]
            }
          ],
          tracking: []
        }
      ]
    }



## uploadTracking

Using the parameter `(campaignservice)uploadTracking` will import a spreadsheet of data describing the assignments of Tracking pixels (used to send event notifications to 3rd parties) to Placements, Editions, and Companions. The Placement IDs can be retrieved using getAssignments, and the Creative IDs should correspond to those pulled using the getCreatives operation.

The HTTP POST argument contains a (multipart/form-data) file (CSV, XLS or XLSX) file called "file"
and an already-existing "campaignID" of the appropriate format.

Each line in the spreadsheet is used per Tracking Pixel URL we want to assign. Details of the combination to assign to; that is Placement-Edition, or Placement-Edition-Companion; are given alongside the Tracking Event (details below) and Tracking Pixel URL. For multiple URLs, repeat the Event and Assignment for each URL. Please see a [sample tracking sheet](./samples/tracking.xlsx).

The spreadsheet *must* contain these column headers:

* **Placement ID** - Telemetry Guid for the Placement, use getAssignments operation to figure out the correct IDs. Note these are numeric so they must not have double-quotes around them.
* **Creative ID** - Telemetry Guid for the Creative, use getCreatives operation to figure out the correct IDs. Note these are numeric so they must not have double-quotes around them.
* **Tracking Type** - the event we want to fire a pixel for; can be one of:
 * **Ad Request** (when the ad is requested by the player/hosting site)
 * **Ad Start** (when the ad begins playback)
 * **Click Event** (tracks when the user clicks on the ad)
 * **Clickthrough Landing** (where the user should end up, ie be redirected to, having clicked on the ad)
 * **Ad Completion**
 * **Mouse Over** (the user’s mouse pointer moves over the ad)
 * **Interaction** (the user engages with a specific interactive element)
 * **Interaction Success** (the user succesfully completes an interactive element)
 * **Interaction Mouseover** (the user’s mouse pointer moves over an interactive element)
 * **Interaction Explored** (useful in interactive elements)
 * **Sustained Mouseover** (mouse is held for a particular period of time)
* **Tracking Pixel** - The 3rd party URL to be fired/redirected to (in the case of Clickthrough Landing event).

Some plugins will expose *additional* tracking types. These are always presented in `ALL_UPPERCASE`.

If Companions are being assigned to, there will be an additional column:

* **Companion Creative ID** - Telemetry Guid for the Creative, use getCreatives operation to figure out the correct IDs - should match a Creative of type "Display/Companion". Note these are numeric so they must not have double-quotes around them.

And if you wish to exclude one or more tag types from being assigned the tracking pixel, include the optional column:

* **Exclude Tags** - separate each tag type with a pipe ("|") eg "VAST2_AS2|VAST2_LINEAR". Available tag type values are:
 * **VAST2_AS3**
 * **VAST2_LINEAR**
 * **VPAID_HTML5**
 * **MRAID**
 * **MRAID_JAVASCRIPT**
 * **IMPRESSION**
 * **HTML**
 * **JAVASCRIPT**
 * **CAROUSEL**
 
 
Note that you can include name columns (e.g. Placement Name, Creative Name) for reference, though these are not used during the upload process.

Successful Output:

    { "result": 1, "message": "Success", "data": [] }

## createCreative

Using the parameter `(campaignservice)createCreative` will create a new creative entity on a given brand, with a given name. Creatives are used to group Creative Editions.

The HTTP POST argument (application/x-www-form-urlencoded) "advertiserID" should be set to the advertiser ID, "brandID" should be set to the brand ID, "name" is the name to assign to the new creative.

The advertiser ID provided must match the advertiser that is for the brand ID provided. If a creative already exists on the given brand, with the name provided, details for the existing creative will be returned; no new creative will be generated in this case.

Sample Input:

    advertiserID=800001&brandID=970001&name=New%20Creative
    
Successful Output:

    {"success":1,"creative":{"ADVERTISER_ID":800001,"BRAND_ID":970001,"CREATIVE_NAME":"New Creative","CREATIVE_ID":710005},"result":1}

## createCreativeEdition

Using the parameter `(campaignservice)createCreativeEdition` will create a new creative edition entity on a given creative, with a given name and path.

The HTTP POST argument (application/x-www-form-urlencoded) "creativeID" should be set to an existing creative ID, "name" is the name to assign to the new creative edition, "path" is the path to assign to the creative edition. "interactive" is optional, and if set should be either 0 or 1, 0 indicating not interactive, 1 indicating interactive; if not provided, 0 is assumed.

Sample Input:

    creativeID=710005&name=New%20Creative%20Edition&path=/content/ads.swf
    
Successful Output:

    {"success":1,"edition":{"CREATIVE_ID":710005,"EDITION_NAME":"New Creative Edition","EDITION_VERSION":0,"EDITION_INTERACTIVE":0,"EDITION_PATH":"\/content\/ads.swf","EDITION_ID":750009},"result":1}

## setEditionConcept

Using the parameter `(campaignservice)setEditionConcept` will set an edition to be on a creative with a given name, on the same brand that it is already assigned to.

The HTTP POST argument (application/x-www-form-urlencoded) "editionID" should be set to an existing edition ID, "conceptname" is the name of the creative that the edition should be grouped using.

If a creative already exists on the brand with the given concept name, the edition is set to be grouped on that creative, otherwise, if the creative the edition is currently grouped in only has the one edition, this creative is renamed to the given concept name, otherwise a new create is created on the brand using the given name.

Sample Input:

    editionID=750009&conceptname=Other%20Concept
    
Successful Output:

    {"success":1,"edition":{"EDITION_ID":750009,"EDITION_NAME":"New Creative Edition","EDITION_PATH":"\/content\/ads.swf","CREATIVE_ID":710005},"result":1}

## setEditionPath

Using the parameter `(campaignservice)setEditionPath` updates the details for a given Creative Edition ID and Creative Type. This will set the item (or add it if it does not exist) with the provided Path, Duration, Width, Height, and Bitrate properties.

The input arguments must contain an Edition ID, a Type, and a set of Data.
type
data[height]
data[width]
data[bitrate]
data[path]
data[duration]

The HTTP POST argument (application/x-www-form-urlencoded):

* `editionID` should be set to the target edition ID,
* `type` should be the type identifier of the creative item, currently supported types are:
 * **quicktime** for MOV path
* `data[width]` should be the width, in pixels, of the creative
* `data[height]` should be the height, in pixels, of the creative
* `data[bitrate]` should be the bitrate of the creative
* `data[duration]` should be the length, in second, of the creative
* `data[path]` should be the absolute path (including http:// or https://) to the creative.

Example Input:

    editionID=750001&type=quicktime&data[width]=640&data[height]=480&data[bitrate]=512&data[duration]=30&data[path]=http://www.mycreativepaths/my_files/ad_750001.mov

Successful Output:

    { "result": 1, "message": "Success", "data": [] }


## setBannerPaths

Using the parameter `(campaignservice)setBannerPaths` updates the details for a given Creative Edition ID. This will set the item (or add it if it does not exist) with the provided banner Paths, Width and Height properties.

The input arguments must contain an Edition ID and a set of Data.
data[height]
data[width]
data[raw-swf-path]
data[raw-jpg-path]
data[raw-gif-path]

The HTTP POST argument (application/x-www-form-urlencoded):

* `editionID` should be set to the target edition ID,
* `data[width]` should be the width, in pixels, of the creative
* `data[height]` should be the height, in pixels, of the creative
* `data[raw-swf-path]` should be the absolute path (including http:// or https://) to the swf banner file.
* `data[raw-jpg-path]` should be the absolute path (including http:// or https://) to the jpg banner file.
* `data[raw-gif-path]` should be the absolute path (including http:// or https://) to the gif banner file.

Example Input:

    editionID=750101&data[width]=300&data[height]=60&data[raw-swf-path]=http://www.mycreativepaths/my_files/ad_750001.swf&data[raw-jpg-path]=http://www.mycreativepaths/my_files/ad_750001.jpg
    
Successful Output:

    { "result": 1, "message": "Success", "data": [] }


## createTags

Using the parameter `(campaignservice)createTags` will produce or update tags, to be served by the appropriate Vendor, for the given Campaign ID and Placement IDs. Tag production will take the required Placement, Creative, and Tracking information from previous steps, and publish tags on the corresponding Vendor’s own Tag Self Service page within TGX.

The HTTP POST argument (application/x-www-form-urlencoded) "campaignID" should be set to the existing
campaign ID, and the placements should be supplied as multiple "placements[]" arguments. Each item in the "placements[]" arguments refers to a single placement; supplying a package ID (for a parent used in packageItems) will not automatically create tags for everything underneath it.

Sample input:

    campaignID=642001&placements[]=3300081&placements[]=3300082

Successful Output:

    { "result": 1, "message": "Success", "data": [] }


Including the argument "testp=1" will bypass actual tag creation, instead returning the export XML, which would have been sent to Tag Exporter, to the user.

Sample testing input:

    campaignID=642001&placements[]=3300081&testp=1

Successful test output:

    { "result": 1, "message": "Success", "xml": "<?xml version=\"1.0\"?>\n<CARMA type=\"manual\"><PLACEMENT><TYPE>In Player<\/TYPE><DATA><HAS_CATEGORY>0<\/HAS_CATEGORY><TAGTYPE><NAME>VAST2_AS3<\/NAME><TAG_TEMPLATE><VENDOR>vast2.0_wrapper_template.st<\/VENDOR><AD>vast2.0_inline_template.st<\/AD><\/TAG_TEMPLATE><\/TAGTYPE><PLACEMENT_ID>1110081<\/PLACEMENT_ID><PLACEMENT_NAME>My Test Item - Integrated<\/PLACEMENT_NAME><PACKAGE_ID>3300081<\/PACKAGE_ID><PACKAGE_NAME>My Test Item<\/PACKAGE_NAME><BOOKING_ID>642001<\/BOOKING_ID><BOOKING_NAME>Test Campaign [2014]<\/BOOKING_NAME><ADVERTISER_ID>800001<\/ADVERTISER_ID><ADVERTISER_NAME>My Advertiser<\/ADVERTISER_NAME><REGION>USA<\/REGION><COUNTRY>USA<\/COUNTRY><PUBNET_NAME>Test Pubnet 1<\/PUBNET_NAME><PLAN_END_DATE>1-1-1<\/PLAN_END_DATE><REDIRECTPIXEL>0<\/REDIRECTPIXEL><STATICPIXEL>0<\/STATICPIXEL><FORMAT><WIDTH>640<\/WIDTH><HEIGHT>360<\/HEIGHT><DIRECT_LOAD>0<\/DIRECT_LOAD><AD_REQUESTS>FIRST_CALL<\/AD_REQUESTS><\/FORMAT><FLASHVARS><FLASHVAR><PARAMETER>hold<\/PARAMETER><VALUE>0<\/VALUE><\/FLASHVAR><FLASHVAR><PARAMETER>TAGEXPORTER<\/PARAMETER><VALUE>1344438248<\/VALUE><\/FLASHVAR><FLASHVAR><PARAMETER>ENABLEINPLAYERCONTROLS<\/PARAMETER><VALUE>1<\/VALUE><\/FLASHVAR><FLASHVAR><PARAMETER>enableinplayercontrols<\/PARAMETER><VALUE>1<\/VALUE><\/FLASHVAR><FLASHVAR><PARAMETER>TAGEXPORTER<\/PARAMETER><VALUE>1400082783<\/VALUE><\/FLASHVAR><\/FLASHVARS><EDITION><EDITION_ID>750001<\/EDITION_ID><EDITION_NAME>My Brand (640x360)<\/EDITION_NAME><URL_AS2>\/content\/my_advertiser\/my_brand\/r0001\/my_brand_640x360.swf<\/URL_AS2><URL_AS3>\/content\/my_advertiser\/my_brand\/r0001\/my_brand_640x360_as3.swf<\/URL_AS3><WEIGHT>100<\/WEIGHT><\/EDITION><\/DATA><\/PLACEMENT><\/CARMA>\n" }


## getAvailableTagTypes

Using the parameter `(campaignservice)getAvailableTagTypes` will return a mapping of Placements to possible Tag Types which can be ordered for the given Campaign ID. You can also provide an optional Placement ID to return the possible Tag Types for that item only. These available Tag Types can then be used to order tags with the [createOneTag](#createOneTag) call. 

For a placement to have any tag types available to order, it must have the functionality enabled on the server at the time of the [uploadPlans](#uploadPlans)  call.

The HTTP POST argument (application/x-www-form-urlencoded) "campaignID" is required, and must be a valid ID (which can be found with a [getCampaigns](#getCampaigns) call).

Sample input:

    campaignID=642001
    
There is also an optional "placementID" argument; the placement must be part of the provided campaign:

    campaignID=642001&placementID=3300081


The output's "available" field contains for each Placement ID, a set of available "tag type orders". Each of these contains a description of the tag type; "vendorTagType" is the main descriptor, and "telemetryTagType" is how the tag type is referred to by Telemetry on the server side. The order also contains a breakdown of flashvars included, should this type be chosen, during a [createOneTag](#createOneTag) call.

An additional "existing" field in the output contains, again for each Placement ID, a list of any available tag types which have already been ordered.

Example:

    {
        "success":true,
        "available": 
        {
            "3300081":
            [
                {
                    "vendorTagType":"Desktop VPAID",
                    "telemetryTagType":"VAST2_AS3",
                    "features":
                    {
                        "hold":"0",
                        "bitrate":"1500",
                        "enableinplayercontrols":"0"
                    }
                },
                {
                    "vendorTagType":"Site Serve Pixels",
                    "telemetryTagType":"IMPRESSION",
                    "features":
                    {
                        "hold":"0"
                    }
                }
            ],
            "3300082":
            [
                {
                    "vendorTagType":"Desktop VPAID",
                    "telemetryTagType":"VAST2_AS3",
                    "features":
                    {
                        "hold":"0"
                    }
                }
            ]
        }
        "existing":
        {
            "3300081":
            {
                "Desktop VPAID": 1
            }
        }
    }


## createOneTag

Using the parameter `(campaignservice)createOneTag` will take the given sets of "orders" and, for each one, create a tag of the given type, under the campaign and placement. Where a tag has already been ordered, it will only be exported, in the same manner as a "createTags" call.

The HTTP POST argument (application/x-www-form-urlencoded) "tagOrders" is the only required parameter. This is a list of order objects, each one containing 3 parameters:

* `campaignID` the campaign to create the tag under, can be taken from a (getCampaigns)[#getCampaigns] call.
* `placementID` the placement to create the tag under, which must already be part of the Campaign.
* `tagType` the type of tag to order; must be an available `vendorTagType` under this Placement, from the results of a  [getAvailableTagTypes](#getAvailableTagTypes) call.

Sample input:

    tagOrders[0][campaignID]=642001&tagOrders[0][placementID]=3300080&tagOrders[0][tagType]='Desktop VPAID'
    
    
The returned object will contain a value of 1 for the `success` field when the orders go through, along with an `orders` field containing the objects which were successful. An `errors` field is present for when `success` is 0, showing the issues encountered during the ordering process.

Example:

    {
        "success":true,
        "errors": [],
        "orders": 
        [
            {
                "tagType":"Desktop VPAID",
                "placementID": 3300080
                "campaignID": 642001
            }
        ]
    }
    
