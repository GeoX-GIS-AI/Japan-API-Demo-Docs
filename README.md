# Overview
This API allows you to search properties from our vast range of footprints data.

# Autorization
The API requires to be signed with Version 4 signing process of AWS. Here is how we can do it in different ways:

## Postman
In the Authorization tab of Postman app, do the following:
- For Type, choose AWS Signature.
- For AccessKey and SecretKey, enter your IAM access key ID and secret access key.

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
2. In the Request URL field, paste your API's invoke URL which is https://api.geox-ai.com/api/v1/japan/parcels
3. Select the GET HTTP method
4. Setup authorization as mentioned above.
5. Now put your address and prefecture in the Params section with `address` (松原市天美東５丁目４−３３) and `prefecture` (大阪府) keys. Once updated your Request URL field should look like `https://api.geox-ai.com/api/v1/japan/parcels?prefecture=大阪府&address=松原市天美東５丁目４−３３`
6. Finally, execute the API.

=> Batch Location API with POST method
1. Create a new request.
2. In the Request URL field, paste your API's invoke URL which is https://api.geox-ai.com/api/v1/japan/parcels
3. Select the POST HTTP method
4. Setup authorization as mentioned above.
5. Now go to the Body section, select the raw radio button and select JSON from the dropdown.
6. Now put your address and prefecture in the Body section with `address` (松原市天美東５丁目４−３３) and `prefecture` (大阪府) keys along with `correlationId` key.
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


def request_single_location(access_key, secret_key, address, prefecture) -> dict:
    """
    This function will execute the API with location(address and prefecture) and return the response.
    :param access_key: AWS Access Key
    :param secret_key: AWS Secret Key
    :param address: address of the location
    :param prefecture: prefecture of the location
    :return: Response of the API
    """
    assert address and prefecture, "Both address and prefecture required."
    
    api_url = "https://api.geox-ai.com/api/v1/japan/parcels"
    aws_details = {
        'aws_access_key': access_key,
        'aws_secret_access_key': secret_key,
        'aws_host': "api.geox-ai.com",
        'aws_region': "us-east-1",
        'aws_service': "execute-api"
    }
    auth = AWSRequestsAuth(**aws_details)
    params = {
        "address": address,
        "prefecture": prefecture
    }
    res = requests.get(api_url, auth=auth, params=request_params)
    assert res.status_code == 200, f"Request failed with status: {res.status_code}"
    res_data = res.json()
    return res_data

def m2m_locations_batch(access_key, secret_key, locations):
    api_url = "https://api.geox-ai.com/api/v1/japan/parcels"
    aws_details = {
        'aws_access_key': access_key,
        'aws_secret_access_key': secret_key,
        'aws_host': "api.geox-ai.com",
        'aws_region': "us-east-1",
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
                               address="松原市天美東５丁目４−３３", prefecture="大阪府")

    # Calling batch location API
    locations = [ # Each location object should contain address and prefecture along with correlationId
        {
            "address": "松原市天美東５丁目４−３３",
            "prefecture": "大阪府",
            "correlationId": "123"
        },
        {
            "address": "大阪市中央区谷町２丁目５−４",
            "prefecture": "大阪府",
            "correlationId": "789"
        }
    ]
    m2m_response = m2m_locations_batch(access_key, secret_key, locations)
```

## Access API with cURL
The API request needs to be signed with AWS Signature Version 4. Please follow this [link](https://docs.aws.amazon.com/general/addressest/gr/sigv4-signed-request-examples.html) for more details.

=> Single Location API with GET method
```shell
curl --location --request GET 'https://api.geox-ai.com/api/v1/japan/parcels?address=大阪市中央区谷町２丁目５−４&prefecture=大阪府' \
--header 'X-Amz-Date: 20230409T093209Z' \
--header 'Authorization: AWS4-HMAC-SHA256 Credential=AKIA2TITFXXXXXXXXXX/20230409/us-east-1/execute-api/aws4_request, SignedHeaders=host;x-amz-date, Signature=021611bd2dba2e3f90bXXXXXXXXXXXXXXXXXXXXXXXXXX'
```

=> Batch Location API with POST method
```shell
curl --location --request POST 'https://api.geox-ai.com/api/v1/japan/parcels' \
--header 'X-Amz-Content-Sha256: beaead3198f7da1e70d03ab969765e0821b24fc913697e929e726aeaebf0eba3' \
--header 'X-Amz-Date: 20230409T094608Z' \
--header 'Authorization: AWS4-HMAC-SHA256 Credential=AKIA2TITFXXXXXXX/20230409/us-east-1/execute-api/aws4_request, SignedHeaders=host;x-amz-content-sha256;x-amz-date, Signature=74891c783cc33192f8d7c9802b2e0df49XXXXXXXXXXXX' \
--header 'Content-Type: application/json' \
--data-raw '{
    "locations": [
        {
            "address": "松原市天美東５丁目４−３３",
            "prefecture": "大阪府",
            "correlationId": "123"
        },
        {
            "address": "大阪市中央区谷町２丁目５−４",
            "prefecture": "大阪府",
            "correlationId": "789"
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
  --header 'Authorization: AWS4-HMAC-SHA256 Credential=AKIA2TIXXX/20230409/us-east-1/execute-api/aws4_request, SignedHeaders=host;x-amz-date, Signature=021611bd2dba2e3f90XXX' \
   'https://api.geox-ai.com/api/v1/japan/parcels?address=大阪市中央区谷町２丁目５−４&prefecture=大阪府'
```

=> Batch Location API with POST method
```shell
wget --no-check-certificate --quiet \
  --method POST \
  --timeout=0 \
  --header 'X-Amz-Content-Sha256: beaead3198f7da1e70d03ab969765e0821b24fc913697e929e726aeaebf0eba3' \
  --header 'X-Amz-Date: 20230409T094608Z' \
  --header 'Authorization: AWS4-HMAC-SHA256 Credential=AKIA2XXX/20230409/us-east-1/execute-api/aws4_request, SignedHeaders=host;x-amz-content-sha256;x-amz-date, Signature=74891c783cc331XXX' \
  --header 'Content-Type: application/json' \
  --body-data '{
    "locations": [
        {
            "address": "松原市天美東５丁目４−３３",
            "prefecture": "大阪府",
            "correlationId": "123"
        },
        {
            "address": "大阪市中央区谷町２丁目５−４",
            "prefecture": "大阪府",
            "correlationId": "789"
        }
    ]
}' \
   'https://api.geox-ai.com/api/v1/japan/parcels'
```

# Request and Response Samples
Here are the sample request and response

## Request URL
```shell
https://api.geox-ai.com/api/v1/japan/parcels
```

## Request Query params Sample
```shell
address=大阪市中央区谷町２丁目５−４&prefecture=大阪府
```
## Request Body Sample (Batch Location API)
```json
{
    "locations": [
        {
            "address": "松原市天美東５丁目４−３３",
            "prefecture": "大阪府",
            "correlationId": "123"
        },
        {
            "address": "大阪市中央区谷町２丁目５−４",
            "prefecture": "大阪府",
            "correlationId": "789"
        }
    ]
}
```

## Response Sample

=> Single Loation API
```json
{
    "confidence": 0.95,
    "data": [
        {
            "addr": "大阪市中央区谷町２丁目５−４",
            "air_conditioner_area": 0.0,
            "air_conditioner_count": 27,
            "building_footprint_id": 23,
            "building_ground_height": 15,
            "building_height": "36",
            "centroid_lat": 34.6855071,
            "centroid_long": 135.5169481,
            "dis_closest_building": 0.85,
            "dis_closest_tree": 6.65,
            "dis_coast": 63.24,
            "dis_fire_station": 0.34,
            "dis_road": 7.86,
            "dis_vegetation": 4.21,
            "dis_water_reservoir": 12.5,
            "flat_area": 457.0,
            "floors_number": "10",
            "footprint_area": 457.0,
            "prefecture": "大阪府",
            "roof_condition": "fair",
            "roof_material": "concrete ",
            "roof_type": "flat",
            "solar_panel_area": "0",
            "sports_fields_area": "false",
            "tree_height": 6,
            "tree_overhang": "false"
        },
        {
            "addr": "大阪市中央区糸屋町１丁目１−１",
            "air_conditioner_area": 0.0,
            "air_conditioner_count": 2,
            "building_footprint_id": 23,
            "building_ground_height": 15,
            "building_height": "18",
            "centroid_lat": 34.6855604972551,
            "centroid_long": 135.516783499364,
            "dis_closest_building": 0.85,
            "dis_closest_tree": 15.23,
            "dis_coast": 63.23,
            "dis_fire_station": 0.34,
            "dis_road": 7.57,
            "dis_vegetation": 3.35,
            "dis_water_reservoir": 12.6,
            "flat_area": 57.9,
            "floors_number": "6",
            "footprint_area": 57.9,
            "prefecture": "大阪府",
            "roof_condition": "good ",
            "roof_material": "concrete ",
            "roof_type": "flat",
            "solar_panel_area": "0",
            "sports_fields_area": "false",
            "tree_height": 6,
            "tree_overhang": "false"
        }
    ]
}
```


=> Batch Location API
```json
{
    "data": [
        {
            "confidence": 0.95,
            "data": [
                {
                    "addr": "松原市天美東５丁目４−３３",
                    "air_conditioner_area": 6.54,
                    "air_conditioner_count": 5,
                    "building_footprint_id": 0,
                    "building_ground_height": 10,
                    "building_height": "12",
                    "centroid_lat": 34.5919896,
                    "centroid_long": 135.5354218,
                    "dis_closest_building": 4.6,
                    "dis_closest_tree": 2.11,
                    "dis_coast": 55.42,
                    "dis_fire_station": 1.26,
                    "dis_road": 6.18,
                    "dis_vegetation": 1.68,
                    "dis_water_reservoir": 10.43,
                    "flat_area": 1019.0,
                    "floors_number": "4",
                    "footprint_area": 1019.0,
                    "prefecture": "大阪府",
                    "roof_condition": "good ",
                    "roof_material": "concrete ",
                    "roof_type": "flat",
                    "solar_panel_area": "0",
                    "sports_fields_area": "false",
                    "tree_height": 16,
                    "tree_overhang": "false"
                },
                {
                    "addr": "松原市天美東５丁目４−３３",
                    "air_conditioner_area": 0.0,
                    "air_conditioner_count": 0,
                    "building_footprint_id": 0,
                    "building_ground_height": 10,
                    "building_height": "30",
                    "centroid_lat": 34.5920989989664,
                    "centroid_long": 135.535425913682,
                    "dis_closest_building": 4.06,
                    "dis_closest_tree": 11.26,
                    "dis_coast": 55.43,
                    "dis_fire_station": 1.25,
                    "dis_road": 23.01,
                    "dis_vegetation": 2.11,
                    "dis_water_reservoir": 10.44,
                    "flat_area": 674.0,
                    "floors_number": "9",
                    "footprint_area": 674.0,
                    "prefecture": "大阪府",
                    "roof_condition": "fair",
                    "roof_material": "concrete ",
                    "roof_type": "flat",
                    "solar_panel_area": "0",
                    "sports_fields_area": "false",
                    "tree_height": 16,
                    "tree_overhang": "false"
                }
            ],
            "correlationId": "123"
        },
        {
            "confidence": 0.95,
            "data": [
                {
                    "addr": "大阪市中央区谷町２丁目５−４",
                    "air_conditioner_area": 0.0,
                    "air_conditioner_count": 27,
                    "building_footprint_id": 23,
                    "building_ground_height": 15,
                    "building_height": "36",
                    "centroid_lat": 34.6855071,
                    "centroid_long": 135.5169481,
                    "dis_closest_building": 0.85,
                    "dis_closest_tree": 6.65,
                    "dis_coast": 63.24,
                    "dis_fire_station": 0.34,
                    "dis_road": 7.86,
                    "dis_vegetation": 4.21,
                    "dis_water_reservoir": 12.5,
                    "flat_area": 457.0,
                    "floors_number": "10",
                    "footprint_area": 457.0,
                    "prefecture": "大阪府",
                    "roof_condition": "fair",
                    "roof_material": "concrete ",
                    "roof_type": "flat",
                    "solar_panel_area": "0",
                    "sports_fields_area": "false",
                    "tree_height": 6,
                    "tree_overhang": "false"
                },
                {
                    "addr": "大阪市中央区糸屋町１丁目１−１",
                    "air_conditioner_area": 0.0,
                    "air_conditioner_count": 2,
                    "building_footprint_id": 23,
                    "building_ground_height": 15,
                    "building_height": "18",
                    "centroid_lat": 34.6855604972551,
                    "centroid_long": 135.516783499364,
                    "dis_closest_building": 0.85,
                    "dis_closest_tree": 15.23,
                    "dis_coast": 63.23,
                    "dis_fire_station": 0.34,
                    "dis_road": 7.57,
                    "dis_vegetation": 3.35,
                    "dis_water_reservoir": 12.6,
                    "flat_area": 57.9,
                    "floors_number": "6",
                    "footprint_area": 57.9,
                    "prefecture": "大阪府",
                    "roof_condition": "good ",
                    "roof_material": "concrete ",
                    "roof_type": "flat",
                    "solar_panel_area": "0",
                    "sports_fields_area": "false",
                    "tree_height": 6,
                    "tree_overhang": "false"
                }
            ],
            "correlationId": "789"
        }
    ],
    "msg": "OK",
    "status": 200
}
```