> **NOTE: This is not the official documentation of the EUVD API! - Use it as is!**

# EU Vulnerability Database API Documentation
The Vulnerability Database of the European Union is online and can be reached under the URL: https://euvd.enisa.europa.eu/.  
Due to lack of documentation of the API, I've tried to document what I've found so far by using the Dev-tools of the Browser.  
So please take these notes with a grain of salt, they may be outdated quickly.  
As of writing this on 17.04.2025, the EUVD is online as a BETA.

## URL
This is the API URL where GET requests can be used to retrieve Vulnerabilities from the EU Vulnerability Database EUVD.

**Note:** The URL might change in the future.

```
https://euvdservices.enisa.europa.eu/api/vulnerabilities?assigner=&product=&vendor=&text=&fromDate=&toDate=&fromScore=0&toScore=10&fromEpss=0&toEpss=100&exploited=false&page=0&size=1
```

### Fields
|Field|Type|Description & Allowed values|Examples|
|---|---|---|---|
|**assigner**|string|The identifier assigning CVE Numbering Authority (CNA)|oracle|
|**product**|string|Product names. <br> Spaces within the name need to be encoded as a plus sign (+).|Microsoft Windows will be Microsoft+Windows|
|**vendor**| string | Name of Vendors. <br> Spaces within the name need to be encoded as a plus sign (+).| Douglas Wilson will be Douglas+Wilson |
|**text**| string | Search within the description of the CVE.<br> Spaces need to be encoded as plus sign (+).| port+Axeda+agent |
|**fromDate**| Date | Dates formatted as YYYY-MM-DD | 2023-12-14 |
|**toDate**| Date | Dates formatted as YYYY-MM-DD | 2023-12-25 |
|**fromScore**| Integer | 0 - 10 | 5 |
|**toScore**| Integer | 0 - 10 | 6 |
|**fromEpss**| Integer | 0 - 100 | 1 |
|**toEpss**| Integer | 0 - 100 | 3 |
|**exploited**|Boolean|true or false|true|
|**page**| Integer | 0 to [value of total that is returned within the JSON] -1 <br> If size is > 1 the max value is [value of total/size].|  |
|**size**| Integer | 1 to 100. <br> **Note:** To retrieve more than 100 you have to send multiple requests.| 5 |


For testing you can use this simple curl command to retrieve **one** Vulnerability.
```
curl --request GET \
     --url "https://euvdservices.enisa.europa.eu/api/vulnerabilities?assigner=&product=&vendor=&text=&fromDate=&toDate=&fromScore=0&toScore=10&fromEpss=0&toEpss=100&exploited=false&page=0&size=1" \
     --header 'accept: application/json'
```

## JSON
The returned JSON looks like this and can be easily read by any program for further analysis.

|Field|Type|Format|Description & Example|
|---|---|---|---|
|items|array|||
|id|string|EUVD-YYYY-#####|EUVD-2025-10933|
|description|string||Description of the Vulnerability|
|datePublished| string | | Date when the Vulnerability was published. <br> **Example:** Apr 15, 2025, 8:30:58 PM |
|dateUpdated| string | | Date when the Entry was updated. <br> **Example:** Apr 17, 2025, 3:55:40 AM |
|baseScore| decimal | 0 - 10.0 | CVSS value e.g., 7.4 |
|baseScoreVersion| string |  | Version of the used CVSS e.g., 3.1 |
|baseScoreVector| string | CVSS:\<version>/\<vector> | CVSS Vector e.g., CVSS:3.1/AV:N/AC:H/PR:N/UI:N/S:U/C:H/I:H/A:N |
|references| string | URLs separated be \n | References for more information. https://\<URL1>\nhttps://\<URL2>\n |
|aliases| string | alias#1\n\alias#2\n| Contains the aliases under which the vulnerability is also tracked, CVE number, GSD number, etc. Separated by \n <br> **Example:** CVE-2022-46319\nGSD-2022-46319\n|
|assigner| string | | The identifier assigning CVE Numbering Authority (CNA) e.g., oracle |
|epss| decimal | 0.0 - 1.0 | Exploit Prediction Scoring System (EPSS) from FIRST. The EPSS score is representing the probability of exploitation in the wild in the next 30 days.|
|enisaIdProduct| array | Multiple entries | **id:** A Universally Unique Identifier (UUID) for the product, e.g., 644dcaa2-ecd6-31a6-b547-95d0e30f4bf7 <br> **product_version:** the affected Version, e.g., 3.1 or All Versions|
|enisaIdVendor| array | | **id:** A Universally Unique Identifier (UUID) for the vendor|
|total| Integer | | Count of vulnerabilities currently in the EU Vulnerability Database.|

### Example JSON
```
{
    "items": [{
        "id": "EUVD-2025-#####",
        "description": "Vulnerability description",
        "datePublished": "Apr 15, 2025, 8:30:58 PM",
        "dateUpdated": "Apr 17, 2025, 3:55:40 AM",
        "baseScore": 7.4,
        "baseScoreVersion": "3.1",
        "baseScoreVector": "CVSS:3.1/AV:N/AC:H/PR:N/UI:N/S:U/C:H/I:H/A:N",
        "references": "<References separated by \n>",
        "aliases": "<Aliases separated by \n>",
        "assigner": "<assignee>",
        "epss": 0.0,
        "enisaIdProduct": [{
            "id": "<Unique Product ID>",
            "product": {
                "name": "<Affected Product Name>"
            },
            "product_version": "<Version of the affected product>"
        }, {
            "id": "<Unique Product ID>",
            "product": {
                "name": "<Affected Product Name>"
            },
            "product_version": "<Version of the affected product>"
        }],
        "enisaIdVendor": [{
            "id": "<Unique Vendor ID>",
            "vendor": {
                "name": "<Vendor>"
            }
        }]
    }],
    "total": 260423
}
```
