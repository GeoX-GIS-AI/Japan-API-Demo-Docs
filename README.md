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

=> Steps to execute API in Postman
1. Create a new request.
2. In the Request URL field, paste your API's invoke URL which is https://api.geox-ai.com/api/v1/japan/parcels
3. Select the GET HTTP method
4. Setup authorization as mentioned above.
5. Now put your address and prefecture in the Params section with `address` (府大阪市西区九条南１丁目１２－２１) and `prefecture` (大阪) keys. Once updated your Request URL field should look like `https://api.geox-ai.com/api/v1/japan/parcels?prefecture=大阪&address=府大阪市西区九条南１丁目１２-２１`
6. Finally, execute the API.

=> Sample Postman request
[![Postman request](japan_api_example.png)](japan_api_example.png)

## Access API with Python
1. We will need following libraries to be installed
   - requests
   - aws_requests_auth

### Python code
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




if __name__ == '__main__':
    # Calling the API with address/prefecture
    m2m_response = request_single_location("YOUR_API_KEY", "YOUR_API_SECRET",
                               address="府大阪市西区九条南１丁目１２－２１", prefecture="大阪")
    print(json.dumps(m2m_response, indent=2))

```

## Access API with cURL
The API request needs to be signed with AWS Signature Version 4. Please follow this [link](https://docs.aws.amazon.com/general/addressest/gr/sigv4-signed-request-examples.html) for more details.

=> Example call of API with cURL
```
curl --location --request GET 'https://api.geox-ai.com/api/v1/japan/parcels?address=府大阪市西区九条南１丁目１２－２１&prefecture=大阪 ' \
--header 'X-Amz-Date: 20230317T135952Z' \
--header 'Authorization: AWS4-HMAC-SHA256 Credential=AKIAXXXXXXXXXXXXXXXX/20230317/us-east-1/execute-api/aws4_request, SignedHeaders=host;x-amz-date, Signature=XXXXXXXXXXXXXXXXXXXX6dab7c36709bf12c967dff8fb9d1a62XXXXXXXXXXXXXXXXXXXXXXXXX'
```

## Access API with wget
=> Example call of API with wget
```shell
wget --no-check-certificate --quiet \
  --method GET \
  --timeout=0 \
  --header 'X-Amz-Date: 20230317T135952Z' \
  --header 'Authorization: AWS4-HMAC-SHA256 Credential=AKIAXXXXXXXXXXXXXXXX/20230317/us-east-1/execute-api/aws4_request, SignedHeaders=host;x-amz-date, Signature=XXXXXXXXXXXXXXXXXXXX6dab7c36709bf12c967dff8fb9d1a62XXXXXXXXXXXXXXXXXXXXXXXXX' \
   'https://api.geox-ai.com/api/v1/japan/parcels?address=府大阪市西区九条南１丁目１２－２１&prefecture=大阪 '
```

# Request and Response Samples
Here are the sample request and response

## Request URL
```shell
https://api.geox-ai.com/api/v1/japan/parcels
```

## Request Query params Sample
```shell
address=府大阪市西区九条南１丁目１２－２１&prefecture=大阪 
```

## Response Sample
```json
{
    "data": [
        {
            "id": "13",
            "lon": "135.474451",
            "lat": "34.668775",
            "solar_panel_area_b": "0",
            "roof_type_b": "Flat",
            "building_height_b": "60",
            "number_of_floors_b": "13",
            "footprint_area_b": "1912",
            "air_conditioner_area_b": null,
            "air_conditioner_count_b": "6",
            "roof_condition_b": "Fair",
            "roof_material_b": "Concrete ",
            "dis_tree_b": "13.03",
            "dis_vegetation_b": "4.22",
            "sports_fields_p": "No",
            "dis_closestbuilding_b ": "1.96",
            "flat_area_b": "1912",
            "tree_overhang_b": "0",
            "tree_height_p": "4",
            "dis_coast_b": "57.56",
            "dis_firestation_b": "0.17",
            "dis_water_reservoir_b": "6.73",
            "dis_to_road _b": "18.53",
            "ground_height_from_sea_lvl_b": "-1"
        },
        {
            "id": "13",
            "lon": "135.47402321643",
            "lat": "34.6691330170861",
            "solar_panel_area_b": "0",
            "roof_type_b": "Flat",
            "building_height_b": "15",
            "number_of_floors_b": "4",
            "footprint_area_b": "1802",
            "air_conditioner_area_b": "0",
            "air_conditioner_count_b": "0",
            "roof_condition_b": "Fair",
            "roof_material_b": "Concrete ",
            "dis_tree_b": "5.37",
            "dis_vegetation_b": "5.11",
            "sports_fields_p": "No",
            "dis_closestbuilding_b ": "1.94",
            "flat_area_b": "1802",
            "tree_overhang_b": "0",
            "tree_height_p": "4",
            "dis_coast_b": "57.57",
            "dis_firestation_b": "0.15",
            "dis_water_reservoir_b": "6.73",
            "dis_to_road _b": "10.55",
            "ground_height_from_sea_lvl_b": "-1"
        }
    ],
    "msg": "OK",
    "status": 200
}
```