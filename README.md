# Overview
This API allows you to search properties from our vast range of footprints data.

# Autorization
The API requires to be signed with Version 4 signing process of AWS. Here is how we can do it in different ways:

## Postman
In the Authorization tab of Postman app, do the following:
- For Type, choose AWS Signature.
- For AccessKey and SecretKey, enter your IAM access key ID and secret access key.
- For AWS Region, enter the region which is ap-northeast-1.

## Python
We can install and use the AWS Requests Auth library from [here](https://pypi.org/project/aws-requests-auth/)

## Manual
To manually sign your requests using another tool or environment,
use the [Signature Version 4 signing process](https://docs.aws.amazon.com/general/addressest/gr/signature-version-4.html).
For more information, see [Signing requests](https://docs.aws.amazon.com/apigateway/api-reference/signing-requests/).


# HTTP response codes
- 200 — Success.
- 400 — Bad Request There was a validation error with the input. Most probably you missed to provide address/prefecture or correaddressionId in the request body.
- 401 — You Signging process was failed. Probably due to incorrect AWS Access Key/Secret Key.
- 403 — Forbidden.
- 404 — Not Found One or more of the required resources was not found, please make sure you entered correct endpoint URL.
- 500 — Internal Server Error This is an issue with our servers processing your request. In most cases we are notified so that we can investigate the issue.

# Getting Started

## Postman
Postman is an amazing application. It's a tool that allows developers to make HTTP requests extremely easy, save examples, work under different environments, we LOVE it!

Postman is downloadable for free use from here and supported with lots of developer docs here.We are providing you the full collection of this API for your usage with Postman, this collection includes examples of different requests & responses, the endpoints, documentation to immensely ease your interaction with our API.

After downloading Postman follow the instructions below to get started.

=> Single Location API with GET method
1. Create a new request.
2. In the Request URL field, paste your API's invoke URL which is https://api.geox-ai-japan-commercial-insights.com/api/v1/japan/parcels
3. Select the GET HTTP method
4. Setup authorization as mentioned above.
5. Now put your address and prefecture in the Params section with `address` (大阪市中央区谷町２丁目５−４) and `prefecture` (愛知県) keys. Once updated your Request URL field should look like `https://api.geox-ai-japan-commercial-insights.com/api/v1/japan/parcels?address=大分市王子西町１４-１&prefecture=大分県&occupation_type=事務所&square_footage=6900&business_name=豊の国情報ライブラリー`
6. Finally, execute the API.

=> Batch Location API with POST method
1. Create a new request.
2. In the Request URL field, paste your API's invoke URL which is https://api.geox-ai-japan-commercial-insights.com/api/v1/japan/parcels
3. Select the POST HTTP method
4. Setup authorization as mentioned above.
5. Now go to the Body section, select the raw radio button and select JSON from the dropdown.
6. Now put your address and prefecture in the Body section with `address` (大阪市中央区谷町２丁目５−４) and `prefecture` (愛知県) keys along with `correlationId` key.
7. You can add multiple locations in the body section.
8. Finally, hit the API.

## Access API with Python
1. We will need following libraries to be installed
   - requests
   - aws_requests_auth

### Python code for both single and batch requests
```python
import json
import requests
from aws_requests_auth.aws_auth import AWSRequestsAuth


def request_single_location(access_key, secret_key, address, prefecture, occupation_type, 
                            square_footage, business_name) -> dict:
    """
    This function will execute the API with location(address and prefecture) and return the response.
    :param access_key: AWS Access Key
    :param secret_key: AWS Secret Key
    :param address: address of the location
    :param prefecture: prefecture of the location
    :param occupation_type: occupation type of the location
    :param square_footage: square footage of the location
    :param business_name: business name of the location
    :return: Response of the API
    """
    assert address and prefecture, "Both address and prefecture required."
    
    api_url = "https://api.geox-ai-japan-commercial-insights.com/api/v1/japan/parcels"
    aws_details = {
        'aws_access_key': access_key,
        'aws_secret_access_key': secret_key,
        'aws_host': "api.geox-ai-japan-commercial-insights.com",
        'aws_region': "ap-northeast-1",
        'aws_service': "execute-api"
    }
    auth = AWSRequestsAuth(**aws_details)
    params = {
        "address": address,
        "prefecture": prefecture,
        "occupation_type": occupation_type,
        "square_footage": square_footage,
        "business_name": business_name
    }
    res = requests.get(api_url, auth=auth, params=request_params)
    assert res.status_code == 200, f"Request failed with status: {res.status_code}"
    res_data = res.json()
    return res_data

def m2m_locations_batch(access_key, secret_key, locations):
    api_url = "https://api.geox-ai-japan-commercial-insights.com/api/v1/japan/parcels"
    aws_details = {
        'aws_access_key': access_key,
        'aws_secret_access_key': secret_key,
        'aws_host': "api.geox-ai-japan-commercial-insights.com",
        'aws_region': "ap-northeast-1",
        'aws_service': "execute-api"
    }
    auth = AWSRequestsAuth(**aws_details)
    request_body = {
        "locations": locations
    }
    res = requests.post(api_url, auth=auth, json=request_body)
    assert res.status_code == 200, f"Request failed with status: {res.status_code}"
    res_data = res.json()
    return res_data


if __name__ == '__main__':
    # Calling single location API with address/prefecture
    m2m_response = request_single_location("YOUR_API_KEY", "YOUR_API_SECRET",
                               address="大阪市中央区谷町２丁目５−４", prefecture="愛知県")

    # Calling batch location API
    locations = [ # Each location object should contain address along with correlationId and optionally it can have prefecture, occupation_type, square_footage and business_name which affects the resulting confidence
        {
            "address": "大阪市中央区高麗橋２丁目６−１０",
            "prefecture": "大阪府",
            "correlationId": "1"
        },
        {
            "address": "大阪市中央区高麗橋２丁目６−９",
            "prefecture": "大阪府",
            "correlationId": "2"
        }
    ]
    m2m_response = m2m_locations_batch(access_key, secret_key, locations)
```

## Access API with cURL
The API request needs to be signed with AWS Signature Version 4. Please follow this [link](https://docs.aws.amazon.com/general/addressest/gr/sigv4-signed-request-examples.html) for more details.

=> Single Location API with GET method
```shell
curl --location --request GET 'https://api.geox-ai-japan-commercial-insights.com/api/v1/japan/parcels?address=大分市王子西町１４-１&prefecture=大分県&occupation_type=事務所&square_footage=6900&business_name=豊の国情報ライブラリー' \
--header 'X-Amz-Date: 20230409T093209Z' \
--header 'Authorization: AWS4-HMAC-SHA256 Credential=AKIA2TITFXXXXXXXXXX/20230409/ap-northeast-1/execute-api/aws4_request, SignedHeaders=host;x-amz-date, Signature=021611bd2dba2e3f90bXXXXXXXXXXXXXXXXXXXXXXXXXX'
```

=> Batch Location API with POST method
```shell
curl --location --request POST 'https://api.geox-ai-japan-commercial-insights.com/api/v1/japan/parcels' \
--header 'X-Amz-Content-Sha256: beaead3198f7da1e70d03ab969765e0821b24fc913697e929e726aeaebf0eba3' \
--header 'X-Amz-Date: 20230409T094608Z' \
--header 'Authorization: AWS4-HMAC-SHA256 Credential=AKIA2TITFXXXXXXX/20230409/ap-northeast-1/execute-api/aws4_request, SignedHeaders=host;x-amz-content-sha256;x-amz-date, Signature=74891c783cc33192f8d7c9802b2e0df49XXXXXXXXXXXX' \
--header 'Content-Type: application/json' \
--data-raw '{
    "locations": [
        {
            "address": "大阪市中央区高麗橋２丁目６−１０",
            "prefecture": "大阪府",
            "correlationId": "1"
        },
        {
            "address": "大阪市中央区高麗橋２丁目６−９",
            "prefecture": "大阪府",
            "correlationId": "2"
        }
    ]
}'
```

## Access API with wget
=> Single Location API with GET method
```shell
wget --no-check-certificate --quiet \
  --method GET \
  --timeout=0 \
  --header 'X-Amz-Date: 20230409T093209Z' \
  --header 'Authorization: AWS4-HMAC-SHA256 Credential=AKIA2TIXXX/20230409/ap-northeast-1/execute-api/aws4_request, SignedHeaders=host;x-amz-date, Signature=021611bd2dba2e3f90XXX' \
   'https://api.geox-ai-japan-commercial-insights.com/api/v1/japan/parcels?address=大分市王子西町１４-１&prefecture=大分県&occupation_type=事務所&square_footage=6900&business_name=豊の国情報ライブラリー'
```

=> Batch Location API with POST method
```shell
wget --no-check-certificate --quiet \
  --method POST \
  --timeout=0 \
  --header 'X-Amz-Content-Sha256: beaead3198f7da1e70d03ab969765e0821b24fc913697e929e726aeaebf0eba3' \
  --header 'X-Amz-Date: 20230409T094608Z' \
  --header 'Authorization: AWS4-HMAC-SHA256 Credential=AKIA2XXX/20230409/ap-northeast-1/execute-api/aws4_request, SignedHeaders=host;x-amz-content-sha256;x-amz-date, Signature=74891c783cc331XXX' \
  --header 'Content-Type: application/json' \
  --body-data '{
    "locations": [
        {
            "address": "大阪市中央区高麗橋２丁目６−１０",
            "prefecture": "大阪府",
            "correlationId": "1"
        },
        {
            "address": "大阪市中央区高麗橋２丁目６−９",
            "prefecture": "大阪府",
            "correlationId": "2"
        }
    ]
}' \
   'https://api.geox-ai-japan-commercial-insights.com/api/v1/japan/parcels'
```

# Request and Response Samples
Here are the sample request and response

## Request URL
```shell
https://api.geox-ai-japan-commercial-insights.com/api/v1/japan/parcels
```

## Request Query params Sample
```shell
address=大分市王子西町１４-１&prefecture=大分県&occupation_type=事務所&square_footage=6900&business_name=豊の国情報ライブラリー
```
## Request Body Sample (Batch Location API)
```json
{
    "locations": [
        {
            "address": "大阪市中央区高麗橋２丁目６−１０",
            "prefecture": "大阪府",
            "correlationId": "1"
        },
        {
            "address": "大阪市中央区高麗橋２丁目６−９",
            "prefecture": "大阪府",
            "correlationId": "2"
        }
    ]
}
```

## Response Sample

=> Single Loation API
```json
{
    "data": {
        "parcel": {
            "parcel_id": 44030197,
            "region": "Kyushu",
            "sports_fields_area": 0.0,
            "solar_panel_ground_area": 0.0,
            "solar_panel_buildings_area": 0.0,
            "prefecture": "Oita"
        },
        "buildings": [
            {
                "footprint_id": 44030197004000,
                "addr": "大分県大分市王子西町14-14 この住所で正解です。以前の駄原の提案は間違いでした。",
                "dis_vegetation": 0.0,
                "region": "Kyushu",
                "air_conditioner_area": 37.95,
                "building_volume": 62464.32,
                "floors_number": 3,
                "flat_area": 6519.71,
                "roof_condition": "fair",
                "lon": 131.5899140179686,
                "dis_fire_station": 0.32,
                "building_ground_height": 5.0,
                "solar_panel_area": 0.0,
                "dis_road": 0.01,
                "tree_overhang": 0.0,
                "roof_material": "concrete",
                "building_height": 29.0,
                "gable_length": 0.0,
                "air_conditioner_count": 5,
                "footprint_area": 6940.48,
                "ridge_length": 5.59,
                "roof_type": "flat",
                "dis_closest_building": 11.42,
                "dis_closest_tree": 0.27,
                "dis_coast": 0.64,
                "lat": 33.239374672965816,
                "dis_water_reservoir": 25.72,
                "business_name": "豊の国情報ライブラリー",
                "business_use": "Office",
                "prior_roof_condition": null,
                "prior_roof_condition_date": null,
                "elevation_range": null,
                "mountains_slope": "<1°",
                "landform_analysis": "flat surface",
                "landslide_type": null,
                "landslide_warning_status": null,
                "landslide_alert_level": null,
                "main_building": true
            }
        ],
        "confidence": 100
    },
    "msg": "OK",
    "status": 200
}
```

=> Batch Location API
```json
{
    "data": [
        {
            "parcel": {
                "parcel_id": 27041552,
                "region": "Kansai",
                "sports_fields_area": 0.0,
                "solar_panel_ground_area": 0.0,
                "solar_panel_buildings_area": 0.0,
                "prefecture": "Osaka"
            },
            "buildings": [
                {
                    "footprint_id": 27041552006000,
                    "addr": "大阪府大阪市中央区高麗橋2丁目6-10",
                    "dis_vegetation": 5.32,
                    "region": "Kansai",
                    "air_conditioner_area": 0.0,
                    "building_volume": 20579.52,
                    "floors_number": 13,
                    "flat_area": 454.28,
                    "roof_condition": "bad",
                    "lon": 135.50409928489512,
                    "dis_fire_station": 0.92,
                    "building_ground_height": 2.0,
                    "solar_panel_area": 0.0,
                    "dis_road": 0.01,
                    "tree_overhang": 0.0,
                    "roof_material": "concrete",
                    "building_height": 43.0,
                    "gable_length": 0.0,
                    "air_conditioner_count": 0,
                    "footprint_area": 527.68,
                    "ridge_length": 0.0,
                    "roof_type": "flat",
                    "dis_closest_building": 1.41,
                    "dis_closest_tree": 30.52,
                    "dis_coast": 6.95,
                    "lat": 34.68956661051362,
                    "dis_water_reservoir": 2.66,
                    "business_name": "ブルーブックスカフェ大阪",
                    "business_use": "Office",
                    "prior_roof_condition": null,
                    "prior_roof_condition_date": null,
                    "elevation_range": "<300m",
                    "mountains_slope": "<1°",
                    "landform_analysis": "lowlands",
                    "landslide_type": null,
                    "landslide_warning_status": null,
                    "landslide_alert_level": null,
                    "main_building": true
                },
                {
                    "footprint_id": 27041552006001,
                    "addr": "大阪府大阪市中央区高麗橋2丁目6-10",
                    "dis_vegetation": 27.26,
                    "region": "Kansai",
                    "air_conditioner_area": 0.0,
                    "building_volume": 4360.95,
                    "floors_number": 5,
                    "flat_area": 236.9,
                    "roof_condition": "fair",
                    "lon": 135.50420503488303,
                    "dis_fire_station": 0.91,
                    "building_ground_height": 2.0,
                    "solar_panel_area": 0.0,
                    "dis_road": 0.0,
                    "tree_overhang": 0.0,
                    "roof_material": "concrete",
                    "building_height": 17.0,
                    "gable_length": 0.0,
                    "air_conditioner_count": 0,
                    "footprint_area": 290.73,
                    "ridge_length": 0.0,
                    "roof_type": "flat",
                    "dis_closest_building": 1.41,
                    "dis_closest_tree": 24.89,
                    "dis_coast": 6.97,
                    "lat": 34.68968321542212,
                    "dis_water_reservoir": 2.66,
                    "business_name": "山源ビル",
                    "business_use": "Office",
                    "prior_roof_condition": null,
                    "prior_roof_condition_date": null,
                    "elevation_range": "<300m",
                    "mountains_slope": "<1°",
                    "landform_analysis": "lowlands",
                    "landslide_type": null,
                    "landslide_warning_status": null,
                    "landslide_alert_level": null
                }
            ],
            "confidence": 85,
            "correlationId": "1"
        },
        {
            "parcel": {
                "parcel_id": 27041552,
                "region": "Kansai",
                "sports_fields_area": 0.0,
                "solar_panel_ground_area": 0.0,
                "solar_panel_buildings_area": 0.0,
                "prefecture": "Osaka"
            },
            "buildings": [
                {
                    "footprint_id": 27041552006000,
                    "addr": "大阪府大阪市中央区高麗橋2丁目6-9 山源産業ビル",
                    "dis_vegetation": 5.32,
                    "region": "Kansai",
                    "air_conditioner_area": 0.0,
                    "building_volume": 20579.52,
                    "floors_number": 13,
                    "flat_area": 454.28,
                    "roof_condition": "bad",
                    "lon": 135.50409928489512,
                    "dis_fire_station": 0.92,
                    "building_ground_height": 2.0,
                    "solar_panel_area": 0.0,
                    "dis_road": 0.01,
                    "tree_overhang": 0.0,
                    "roof_material": "concrete",
                    "building_height": 43.0,
                    "gable_length": 0.0,
                    "air_conditioner_count": 0,
                    "footprint_area": 527.68,
                    "ridge_length": 0.0,
                    "roof_type": "flat",
                    "dis_closest_building": 1.41,
                    "dis_closest_tree": 30.52,
                    "dis_coast": 6.95,
                    "lat": 34.68956661051362,
                    "dis_water_reservoir": 2.66,
                    "business_name": "ブルーブックスカフェ大阪",
                    "business_use": "Office",
                    "prior_roof_condition": null,
                    "prior_roof_condition_date": null,
                    "elevation_range": "<300m",
                    "mountains_slope": "<1°",
                    "landform_analysis": "lowlands",
                    "landslide_type": null,
                    "landslide_warning_status": null,
                    "landslide_alert_level": null
                },
                {
                    "footprint_id": 27041552006001,
                    "addr": "大阪府大阪市中央区高麗橋2丁目6-9 山源産業ビル",
                    "dis_vegetation": 27.26,
                    "region": "Kansai",
                    "air_conditioner_area": 0.0,
                    "building_volume": 4360.95,
                    "floors_number": 5,
                    "flat_area": 236.9,
                    "roof_condition": "fair",
                    "lon": 135.50420503488303,
                    "dis_fire_station": 0.91,
                    "building_ground_height": 2.0,
                    "solar_panel_area": 0.0,
                    "dis_road": 0.0,
                    "tree_overhang": 0.0,
                    "roof_material": "concrete",
                    "building_height": 17.0,
                    "gable_length": 0.0,
                    "air_conditioner_count": 0,
                    "footprint_area": 290.73,
                    "ridge_length": 0.0,
                    "roof_type": "flat",
                    "dis_closest_building": 1.41,
                    "dis_closest_tree": 24.89,
                    "dis_coast": 6.97,
                    "lat": 34.68968321542212,
                    "dis_water_reservoir": 2.66,
                    "business_name": "山源ビル",
                    "business_use": "Office",
                    "prior_roof_condition": null,
                    "prior_roof_condition_date": null,
                    "elevation_range": "<300m",
                    "mountains_slope": "<1°",
                    "landform_analysis": "lowlands",
                    "landslide_type": null,
                    "landslide_warning_status": null,
                    "landslide_alert_level": null,
                    "main_building": true
                }
            ],
            "confidence": 85,
            "correlationId": "2"
        }
    ],
    "msg": "OK",
    "status": 200
}
```