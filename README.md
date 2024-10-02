# Lavui Documentation

## Introduction

The project is an e-commerce platform that allows users to browse products, add them to a cart, and make purchases. The platform supports various payment gateways and provides real-time stock updates.

**Technologies Used**:
- Frontend: Vue.js, HTML, CSS, Tailwind.
- Backend: Laravel, MySQL, **[Media Library](https://spatie.be/docs/laravel-medialibrary/v11/introduction)**, **[Spaite Localization](https://spatie.be/docs/laravel-translatable/v6/introduction)**.
- Others: (**[Tamara](https://docs.tamara.co/reference/tamara-api-reference-documentation)**, **[Hyperpay](https://github.com/devinweb/laravel-hyperpay)**) API for payment, **[Mshastra](https://www.mshastra.com/download/api_configuration.pdf)** for SMS Gateway.

## Installation & Setup

#### Clone the repository:
   ```bash
   git clone https://github.com/isyncloud/lavui.git
   ```

#### Install dependencies for the frontend and backend:
   ```bash
   npm install
   composer install
   ```

#### Environment Variables
- `APP_URL`: Laravel application url.
- `DEMO`: Accept True OR False, that declares the status of the application is in demo stage OR live.
- `MIX_HOST`: Vue variable that include value of APP_URL
- `MIX_DEMO`: Vue variable that include value of DEMO

Note: When the application is live, you must make DEMO=false to make SMS gateways and payment gateways work successfully.

#### Set up the database:

Note: Before making anything, you must change the collection from S3 too public to not save images on the LAVUI S3 bucket.

To change S3 to public, you can search about "toMediaCollection" or "'s3');" and replace it by "'s3');".
- Create a new database named `lavui_db`.
- Import database if you want to run project with the real database.
- If you want to use dummy data run this command, after set DEMO=true.
   ```bash
   php artisan init:project
   ```
  This command will migrate database and insert dummy data, then create account for you as administrator.

#### Pre-requisites

1. Ensure the following are installed:
   - Node.js >= 20.17.x
   - PHP >= 8.2
   - MySQL >= 8.0

2. PHP extension:
   - OpenSSL
   - PDO
   - Mbstring
   - Tokenizer
   - XML
   - Exif
   - Fileinfo
   - Ctype
   - JSON
   - BCMath
   - GD (or Imagick)
   - PHP Zip Archive
   - PHP JSON
   - PHP cURL
   - Rewrite Module (Apache or Nginx)
   - open_basedir must be disabled

You can read also **[Shopking](https://docs.shopking.dev/)** documentation.

#### Development and Production

1. Development the frontend:
   ```bash
   npm run dev
   ```
2. Production the frontend:
   ```bash
   npm run prod
   ```
Note: Should use production, not development, on local or server because the app.js file on the prod status was smaller than the dev status.

## How to publish images by seeder code?

#### We've got three ways:
1. Upload images from local with make toMediaCollection S3, and link database with live database.
2. Upload folder images to the public folder at the Laravel project, then customize code to fetch images from the public path, then run Seeder.
3. Take backup from database and upload images from local to S3, then images to table media and bucket, so in this case you must export the database file and import it to the server.

in all ways you must run UploadProductImageSeeder to push images.

## Project Architecture

The project follows a clean and modular architecture where different components have well-defined responsibilities. This architecture helps to maintain the separation of concerns, making the system easier to manage, test, and scale.

#### Controller Layer

The Controller acts as an adapter between the client (e.g., frontend or external systems) and the business logic of the application. Its main role is to handle incoming HTTP requests, delegate the processing to the appropriate Service, and return the result. The controller itself contains minimal logic, as it mainly focuses on routing and request validation.

Controllers map incoming requests to the appropriate business logic in the Service layer.
Each controller is responsible for delegating tasks and ensuring that the correct response is sent back to the client.

#### Service Layer

The Service layer encapsulates the core business logic of the application. All the processing, validation, and handling of the application’s main functionalities happen within this layer. This layer ensures that the controller remains thin and focuses only on delegation, while the Service contains all the logic that powers the application.

Service classes contain all the business logic and rules of the application.
This layer interacts with repositories, external APIs, and other necessary components to fulfill requests from the controller.

The Service ensures that all validations, data transformations, and processes follow the business requirements.

#### Resource Layer

The Resource layer is responsible for shaping and formatting the response that the API sends back to the client. Once the business logic is executed by the Service layer, the data is returned in the form of a Resource. This ensures that the API's response structure is consistent, predictable, and easy to consume by clients.

Resources are used to transform raw data from services into well-defined responses.
They ensure that sensitive or unnecessary fields are omitted from the response, maintaining a clean and secure API output.

##### Summary of Flow
- Controller: Receives the HTTP request and delegates it to the relevant service.
- Service: Executes the necessary business logic and processes the request.
- Resource: Formats the response data before sending it back to the client.

### API Documentation

#### Admin End Points

- **Authentication**
  1. register
     - **Endpoint**: `POST /api/auth/signup/register`
     - **Description**: Registers a new user by first_name, last_name, email, password, password_confirmation, country_code, phone, gender and birthday.
     - **Request Body**:
       ```json
       {
         "first_name": "ibrahim",
         "last_name": "essam",
         "email": "ibrahim_esam@gmail.com",
         "password": "123456789",
         "password_confirmation": "123456789",
         "country_code": "+20",
         "phone": "1145621669",
         "gender": 5,
         "birthday": "2001-08-01"
       }
       ```
         gender value between (5, 10), the meaning of 5 "male" and 10 "female".
     - **Response**:
       ```json
       {
         "status": true,
         "message": "Please check your phone for the code."
       }
       ```
  2. login by email
     - **Endpoint**: `POST /api/auth/login`
     - **Description**: Authenticates the user with email and password, returning token.
     - **Request Body**:
       ```json
       {
         "email": "Mohamed-Gamal@ektml.com",
         "password": "Mm11221122"
       }
       ```
     - **Response**:
       ```json
       {
         "message": "Login Successfully.",
         "token": "5|M63nN21WbPKep4fZklYmdXxE1sevXzFl1zFqlwpCffc446b6"
       }
       ```
       with user data and role & permission.
  3. login by phone
      - **Endpoint**: `POST /api/auth/login`
      - **Description**: Authenticates the user with country code, phone and password, returning token.
      - **Request Body**:
        ```json
        {
          "country_code": "+20",
          "phone": "1097609636",
          "password": "Mm11221122"
        }
        ```
      - **Response**:
        ```json
        {
          "message": "Login Successfully.",
          "token": "6|mb4yxcx5OC9KAwN8Y225crlcBn22qZMWz0nth0hy4dc72597"
        }
        ```
        with user data and role & permission.
  4. logout
     - **Endpoint**: `POST /api/auth/logout`
     - **Description**: Logout the user with token.
     - **Request Body**:
       ```php
       {
         "Authorization": Bearer 6|mb4yxcx5OC9KAwN8Y225crlcBn22qZMWz0nth0hy4dc72597
       }
       ```
     - **Response**:
       ```json
       {
         "message": "Logged Out Successfully."
       }
       ```
  5. refresh token
      - **Endpoint**: `POST /api/refresh-token`
      - **Description**: Refresh token.
      - **Request Body**:
        ```json
        {
          "token": "7|CrdA2W8ScKsuHxBM9ddTE6yPbMpTLkrjhQRY0MwL9153c219"
        }
        ```
      - **Response**:
        ```json
        {
          "token": "8|g910rjkpjpp4V3ofqvBFojZNXoWRnGimjmffVs6zdf66897f"
        }
        ```
- **Slider**
  1. index
      - **Endpoint**: `GET /api/admin/setting/slider`
      - **Description**: Fetches a list of all available sliders.
      - **Request Body**:
        ```php
        { 
          "Authorization": Bearer 9|qqtFAmtPqL5Ux1Pm0f2uy2aasArEsHgYXcR26N9M17556e54
        }
        ```
      - **Response**:
        ```json
        {
          "data": [ 
            {
                "id": 3,
                "title": {
                    "en": "Slider One",
                    "ar": "الصوره الثالثه"
                },
                "description": {
                    "en": "Slider Three",
                    "ar": "الصوره الثالثه"
                },
                "status": 5,
                "link": null,
                "mean_slider": 10,
                "image": "https://lavui-bucket.s3.eu-central-1.amazonaws.com/slider/13/conversions/slider_three-cover.webp",
                "creator": {
                    "id": 1,
                    "first_name": "Mohammad",
                    "last_name": "Gamal",
                    "email": "Mohamed-Gamal@ektml.com",
                    "phone": "1097609636",
                    "username": "mohammad_gamal1",
                    "badge": 5,
                    "points": 0,
                    "email_verified_at": "2024-08-12T21:10:21.000000Z",
                    "device_token": null,
                    "web_token": null,
                    "status": 5,
                    "birthday": null,
                    "gender": null,
                    "country_code": "+20",
                    "is_guest": 10,
                    "balance": "0.000000",
                    "creator_id": 1,
                    "editor_id": 1,
                    "deleter_id": null,
                    "created_at": "2023-07-23T17:38:44.000000Z",
                    "updated_at": "2024-08-12T21:10:21.000000Z",
                    "deleted_at": null
                },
                "editor": null,
                "deleter": null,
                "deleted_at": null
            }
          ]
        }
        ```
        return list of slider, with data of creator, editor, deleter and deleted at. 
  2. create
      - **Endpoint**: `POST /api/admin/setting/slider`
      - **Description**: Add a new slider.
      - **Request Body**:
        ```php
        {
          "Authorization": Bearer 9|qqtFAmtPqL5Ux1Pm0f2uy2aasArEsHgYXcR26N9M17556e54,
          "status": 5,
          "mean_slider": 5,
          "image": <image file>
        }
        ```
        mean_slider value between (5, 10), 5 make this slider as banner "main slider at frontend", 10 make image as slider.

        only one slider can be mean_slider = 5, and other be 10.
        
        status value between (5, 10), 5 make this slider active, 10 make this slider not active.
      - **Response**:
        ```json
        {
          "data": [ 
            {
                "id": 4,
                "title": {
                    "en": "",
                    "ar": ""
                },
                "description": {
                    "en": "",
                    "ar": ""
                },
                "status": 5,
                "link": null,
                "mean_slider": 5,
                "image": "http://127.0.0.1:8000/storage/slider/202/conversions/1678279093431-cover.webp",
                "creator": {
                    "id": 1,
                    "first_name": "Mohammad",
                    "last_name": "Gamal",
                    "email": "Mohamed-Gamal@ektml.com",
                    "phone": "1097609636",
                    "username": "mohammad_gamal1",
                    "badge": 5,
                    "points": 0,
                    "email_verified_at": "2024-08-12T21:10:21.000000Z",
                    "device_token": null,
                    "web_token": null,
                    "status": 5,
                    "birthday": null,
                    "gender": null,
                    "country_code": "+20",
                    "is_guest": 10,
                    "balance": "0.000000",
                    "creator_id": 1,
                    "editor_id": 1,
                    "deleter_id": null,
                    "created_at": "2023-07-23T17:38:44.000000Z",
                    "updated_at": "2024-08-12T21:10:21.000000Z",
                    "deleted_at": null
                },
                "editor": null,
                "deleter": null,
                "deleted_at": null
            }
          ]
        }
        ```
        return slider data with data of creator.
  3. delete
      - **Endpoint**: `DELETE /api/admin/setting/slider/{slider_id}`
      - **Description**: Delete an existing slider.
      - **Request Body**:
        ```php
        {
          "Authorization": Bearer 9|qqtFAmtPqL5Ux1Pm0f2uy2aasArEsHgYXcR26N9M17556e54
        }
        ```
      - **Response**:
        ```json
        {
            
        }
        ```
        return empty json data, that mean slider was deleted successfully.
  4. update
      - **Endpoint**: `POST /api/admin/setting/slider/{slider_id}`
      - **Description**: Updates the details of an existing slider.
      - **Request Body**:
        ```php
        {
          "Authorization": Bearer 9|qqtFAmtPqL5Ux1Pm0f2uy2aasArEsHgYXcR26N9M17556e54,
          "status": 10,
          "mean_slider": 10
        }
        ```
     - **Response**:
       ```json
       {
         "data": { 
               "id": 4,
               "title": {
                   "en": "",
                   "ar": ""
               },
               "description": {
                   "en": "",
                   "ar": ""
               },
               "status": 10,
               "link": null,
               "mean_slider": 10,
               "image": "http://127.0.0.1:8000/storage/slider/202/conversions/1678279093431-cover.webp",
               "creator": {
                   "id": 1,
                   "first_name": "Mohammad",
                   "last_name": "Gamal",
                   "email": "Mohamed-Gamal@ektml.com",
                   "phone": "1097609636",
                   "username": "mohammad_gamal1",
                   "badge": 5,
                   "points": 0,
                   "email_verified_at": "2024-08-12T21:10:21.000000Z",
                   "device_token": null,
                   "web_token": null,
                   "status": 5,
                   "birthday": null,
                   "gender": null,
                   "country_code": "+20",
                   "is_guest": 10,
                   "balance": "0.000000",
                   "creator_id": 1,
                   "editor_id": 1,
                   "deleter_id": null,
                   "created_at": "2023-07-23T17:38:44.000000Z",
                   "updated_at": "2024-08-12T21:10:21.000000Z",
                   "deleted_at": null
               },
               "editor": {
                   "id": 1,
                   "first_name": "Mohammad",
                   "last_name": "Gamal",
                   "email": "Mohamed-Gamal@ektml.com",
                   "phone": "1097609636",
                   "username": "mohammad_gamal1",
                   "badge": 5,
                   "points": 0,
                   "email_verified_at": "2024-08-12T21:10:21.000000Z",
                   "device_token": null,
                   "web_token": null,
                   "status": 5,
                   "birthday": null,
                   "gender": null,
                   "country_code": "+20",
                   "is_guest": 10,
                   "balance": "0.000000",
                   "creator_id": 1,
                   "editor_id": 1,
                   "deleter_id": null,
                   "created_at": "2023-07-23T17:38:44.000000Z",
                   "updated_at": "2024-08-12T21:10:21.000000Z",
                   "deleted_at": null
               },
               "deleter": null,
               "deleted_at": null
         }
       }
       ```
       return slider data with data of creator and editor.
  5. show
      - **Endpoint**: `GET /api/admin/setting/slider/show/{slider_id}`
      - **Description**: Retrieve details of a single slider based on it ID.
      - **Request Body**:
        ```php
        { 
          "Authorization": Bearer 9|qqtFAmtPqL5Ux1Pm0f2uy2aasArEsHgYXcR26N9M17556e54
        }
        ```
      - **Response**:
        ```json
        {
            "data": {
                "id": 1,
                "title": {
                    "en": "Slider One",
                    "ar": "الصوره الاولي"
                },
                "description": {
                    "en": "Slider One",
                    "ar": "الصوره الاولي"
                },
                "status": 5,
                "link": null,
                "mean_slider": 10,
                "image": "https://lavui-bucket.s3.eu-central-1.amazonaws.com/slider/11/conversions/slider_one-cover.webp",
                "creator": {
                    "id": 1,
                    "first_name": "Mohammad",
                    "last_name": "Gamal",
                    "email": "Mohamed-Gamal@ektml.com",
                    "phone": "1097609636",
                    "username": "mohammad_gamal1",
                    "badge": 5,
                    "points": 0,
                    "email_verified_at": "2024-08-12T21:10:21.000000Z",
                    "device_token": null,
                    "web_token": null,
                    "status": 5,
                    "birthday": null,
                    "gender": null,
                    "country_code": "+20",
                    "is_guest": 10,
                    "balance": "0.000000",
                    "creator_id": 1,
                    "editor_id": 1,
                    "deleter_id": null,
                    "created_at": "2023-07-23T17:38:44.000000Z",
                    "updated_at": "2024-08-12T21:10:21.000000Z",
                    "deleted_at": null
                    },
                "editor": null,
                "deleter": null,
                "deleted_at": null
            }
        }
        ```
- **Banner**
    1. index
        - **Endpoint**: `GET /api/admin/setting/banner`
        - **Description**: Fetches a list of all available banners.
          - **Request Body**:
            ```php
            { 
              "Authorization": Bearer 9|qqtFAmtPqL5Ux1Pm0f2uy2aasArEsHgYXcR26N9M17556e54
            }
            ```
            - **Response**:
             ```json
             {
                "data": [
                    {
                        "id": 2,
                        "text": {
                            "en": "",
                            "ar": ""
                        },
                        "position": 2,
                        "link": null,
                        "type": 5,
                        "image": "http://127.0.0.1:8000/storage/banner/203/conversions/1678279093431-cover.webp",
                        "status": 5,
                        "creator": {
                            "id": 1,
                            "first_name": "Mohammad",
                            "last_name": "Gamal",
                            "email": "Mohamed-Gamal@ektml.com",
                            "phone": "1097609636",
                            "username": "mohammad_gamal1",
                            "badge": 5,
                            "points": 0,
                            "email_verified_at": "2024-08-12T21:10:21.000000Z",
                            "device_token": null,
                            "web_token": null,
                            "status": 5,
                            "birthday": null,
                            "gender": null,
                            "country_code": "+20",
                            "is_guest": 10,
                            "balance": "0.000000",
                            "creator_id": 1,
                            "editor_id": 1,
                            "deleter_id": null,
                            "created_at": "2023-07-23T17:38:44.000000Z",
                            "updated_at": "2024-08-12T21:10:21.000000Z",
                            "deleted_at": null
                        },
                        "editor": null,
                        "deleter": null,
                        "deleted_at": null
                        },
                        {
                        "id": 1,
                        "text": {
                            "en": "discount 50%",
                            "ar": "خصم 50%"
                        },
                        "position": 1,
                        "link": null,
                        "type": null,
                        "image": "",
                        "status": 5,
                        "creator": {
                            "id": 1,
                            "first_name": "Mohammad",
                            "last_name": "Gamal",
                            "email": "Mohamed-Gamal@ektml.com",
                            "phone": "1097609636",
                            "username": "mohammad_gamal1",
                            "badge": 5,
                            "points": 0,
                            "email_verified_at": "2024-08-12T21:10:21.000000Z",
                            "device_token": null,
                            "web_token": null,
                            "status": 5,
                            "birthday": null,
                            "gender": null,
                            "country_code": "+20",
                            "is_guest": 10,
                            "balance": "0.000000",
                            "creator_id": 1,
                            "editor_id": 1,
                            "deleter_id": null,
                            "created_at": "2023-07-23T17:38:44.000000Z",
                            "updated_at": "2024-08-12T21:10:21.000000Z",
                            "deleted_at": null
                        },
                        "editor": null,
                        "deleter": {
                            "id": 1,
                            "first_name": "Mohammad",
                            "last_name": "Gamal",
                            "email": "Mohamed-Gamal@ektml.com",
                            "phone": "1097609636",
                            "username": "mohammad_gamal1",
                            "badge": 5,
                            "points": 0,
                            "email_verified_at": "2024-08-12T21:10:21.000000Z",
                            "device_token": null,
                            "web_token": null,
                            "status": 5,
                            "birthday": null,
                            "gender": null,
                            "country_code": "+20",
                            "is_guest": 10,
                            "balance": "0.000000",
                            "creator_id": 1,
                            "editor_id": 1,
                            "deleter_id": null,
                            "created_at": "2023-07-23T17:38:44.000000Z",
                            "updated_at": "2024-08-12T21:10:21.000000Z",
                            "deleted_at": null
                        },
                        "deleted_at": "03:06 PM, 29-09-2024"
                    }
                ]
            }
            ```
          return list of banner, with data of creator, editor, deleter and deleted at.
  2. create
      - **Endpoint**: `POST /api/admin/setting/banner`
      - **Description**: Add a new banner.
      - **Request Body**:
        first choose position
        - position = 1, that mean "Text" then text_en, text_ar, status (required).
        - position = 2, that mean "General" then link, type(5 "Fixed", 10 "Carousel"), image, status (required).
        - position = 3, that mean "Category" then link, type(5 "Fixed", 10 "Carousel"), image, status (required).
        - position = 4, that mean "Virtual Try" then image, status (required).
        - position = 5, that mean "Subscription" then image, status (required).
        ```php
        {
          "Authorization": Bearer 9|qqtFAmtPqL5Ux1Pm0f2uy2aasArEsHgYXcR26N9M17556e54,
          "position": 1,
          "text_en": "discount 50%",
          "text_ar": "خصم %50",
          "status": 5
        }
        ```
          - position = 1, that mean position at text banner at homepage.
          - position = 2, that mean position at general position at homepage.
          - position = 3, that mean position at category position at homepage.
          - position = 4, that mean position at virtual try position at homepage.
          - position = 5, that mean position at subscription position at homepage.
      - **Response**:
        ```json
          {
              "data": {
                  "id": 1,
                  "text": {
                      "en": "discount 50%",
                      "ar": "خصم %50"
                  },
                  "position": 1,
                  "link": null,
                  "type": null,
                  "image": "",
                  "status": 5,
                  "creator": {
                      "id": 1,
                      "first_name": "Mohammad",
                      "last_name": "Gamal",
                      "email": "Mohamed-Gamal@ektml.com",
                      "phone": "1097609636",
                      "username": "mohammad_gamal1",
                      "badge": 5,
                      "points": 0,
                      "email_verified_at": "2024-08-12T21:10:21.000000Z",
                      "device_token": null,
                      "web_token": null,
                      "status": 5,
                      "birthday": null,
                      "gender": null,
                      "country_code": "+20",
                      "is_guest": 10,
                      "balance": "0.000000",
                      "creator_id": 1,
                      "editor_id": 1,
                      "deleter_id": null,
                      "created_at": "2023-07-23T17:38:44.000000Z",
                      "updated_at": "2024-08-12T21:10:21.000000Z",
                      "deleted_at": null
                  },
                  "editor": null,
                  "deleter": null,
                  "deleted_at": null
              }
          }
          ```
        return banner data with data of creator.
  3. delete
      - **Endpoint**: `DELETE /api/admin/setting/banner/{banner_id}`
      - **Description**: Delete an existing banner.
      - **Request Body**:
        ```php
        {
          "Authorization": Bearer 9|qqtFAmtPqL5Ux1Pm0f2uy2aasArEsHgYXcR26N9M17556e54
        }
        ```
      - **Response**:
        ```json
        {
              
        }
        ```
        return empty json data, that mean banner was deleted successfully.
  4. update
      - **Endpoint**: `POST /api/admin/setting/banner/{banner_id}`
      - **Description**: Updates the details of an existing banner.
      - **Request Body**:
        ```php
        {
          "Authorization": Bearer 9|qqtFAmtPqL5Ux1Pm0f2uy2aasArEsHgYXcR26N9M17556e54,
          "position": 2,
          "image": <image file>,
          "type": 10,
          "status": 10
        }
        ```
        - **Response**:
          ```json
          {
              "data": {
                  "id": 2,
                  "text": {
                      "en": "",
                      "ar": ""
                  },
                  "position": 2,
                  "link": null,
                  "type": 10,
                  "image": "http://127.0.0.1:8000/storage/banner/204/conversions/3-cover.webp",
                  "status": 5,
                  "creator": {
                      "id": 1,
                      "first_name": "Mohammad",
                      "last_name": "Gamal",
                      "email": "Mohamed-Gamal@ektml.com",
                      "phone": "1097609636",
                      "username": "mohammad_gamal1",
                      "badge": 5,
                      "points": 0,
                      "email_verified_at": "2024-08-12T21:10:21.000000Z",
                      "device_token": null,
                      "web_token": null,
                      "status": 5,
                      "birthday": null,
                      "gender": null,
                      "country_code": "+20",
                      "is_guest": 10,
                      "balance": "0.000000",
                      "creator_id": 1,
                      "editor_id": 1,
                      "deleter_id": null,
                      "created_at": "2023-07-23T17:38:44.000000Z",
                      "updated_at": "2024-08-12T21:10:21.000000Z",
                      "deleted_at": null
                  },
                  "editor": {
                      "id": 1,
                      "first_name": "Mohammad",
                      "last_name": "Gamal",
                      "email": "Mohamed-Gamal@ektml.com",
                      "phone": "1097609636",
                      "username": "mohammad_gamal1",
                      "badge": 5,
                      "points": 0,
                      "email_verified_at": "2024-08-12T21:10:21.000000Z",
                      "device_token": null,
                      "web_token": null,
                      "status": 5,
                      "birthday": null,
                      "gender": null,
                      "country_code": "+20",
                      "is_guest": 10,
                      "balance": "0.000000",
                      "creator_id": 1,
                      "editor_id": 1,
                      "deleter_id": null,
                      "created_at": "2023-07-23T17:38:44.000000Z",
                      "updated_at": "2024-08-12T21:10:21.000000Z",
                      "deleted_at": null
                  },
                  "deleter": null,
                  "deleted_at": null
                  }
              }
          ```
          return banner data with data of creator and editor.
  5. show
      - **Endpoint**: `GET /api/admin/setting/banner/show/{banner_id}`
      - **Description**: Retrieve details of a single banner based on it ID.
      - **Request Body**:
        ```php
        { 
          "Authorization": Bearer 9|qqtFAmtPqL5Ux1Pm0f2uy2aasArEsHgYXcR26N9M17556e54
        }
        ```
      - **Response**:
         ```json
         {
             "data": {
             "id": 2,
             "text": {
                 "en": "",
                 "ar": ""
             },
             "position": 2,
             "link": null,
             "type": 5,
             "image": "http://127.0.0.1:8000/storage/banner/203/conversions/1678279093431-cover.webp",
             "status": 5,
             "creator": {
                 "id": 1,
                 "first_name": "Mohammad",
                 "last_name": "Gamal",
                 "email": "Mohamed-Gamal@ektml.com",
                 "phone": "1097609636",
                 "username": "mohammad_gamal1",
                 "badge": 5,
                 "points": 0,
                 "email_verified_at": "2024-08-12T21:10:21.000000Z",
                 "device_token": null,
                 "web_token": null,
                 "status": 5,
                 "birthday": null,
                 "gender": null,
                 "country_code": "+20",
                 "is_guest": 10,
                 "balance": "0.000000",
                 "creator_id": 1,
                 "editor_id": 1,
                 "deleter_id": null,
                 "created_at": "2023-07-23T17:38:44.000000Z",
                 "updated_at": "2024-08-12T21:10:21.000000Z",
                 "deleted_at": null
             },
             "editor": null,
             "deleter": null,
             "deleted_at": null
             }
         }
         ```
- **Product Category**
- **Product Brand**
    1. index
        - **Endpoint**: `GET /api/admin/setting/product-brand`
        - **Description**: Fetches a list of all available product brands.
        - **Request Body**:
          ```php
          { 
            "Authorization": Bearer 9|qqtFAmtPqL5Ux1Pm0f2uy2aasArEsHgYXcR26N9M17556e54
          }
          ```
        - **Response**:
          ```json
          {
            "data": [ 
                {
                    "id": 90,
                    "name": {
                        "en": "Kenzo",
                        "ar": "كينزو"
                    },
                    "slug": "kenzo",
                    "description": {
                        "en": "Kenzo Description",
                        "ar": "نص كينزو"
                    },
                    "status": 5,
                    "thumb": "https://lavui-bucket.s3.eu-central-1.amazonaws.com/product-brand/186/conversions/kenzo-thumb.webp",
                    "cover": "https://lavui-bucket.s3.eu-central-1.amazonaws.com/product-brand/186/conversions/kenzo-cover.webp",
                    "creator": null,
                    "editor": null,
                    "deleter": null,
                    "deleted_at": null
                }
            ]
          }
          ```
          return list of product brands, with data of creator, editor, deleter and deleted at.
    2. create
        - **Endpoint**: `POST /api/admin/setting/product-brand`
        - **Description**: Add a new product brand.
        - **Request Body**:
             ```php
             {
               "Authorization": Bearer 9|qqtFAmtPqL5Ux1Pm0f2uy2aasArEsHgYXcR26N9M17556e54,
               "name_en": "product brand name en",
               "name_ar": "product brand name ar",
               "description_en": "product brand description en",
               "description_ar": "product brand description ar",
               "status": 5,
               "image": <image file>
             }
             ```
        - **Response**:
          ```json
           {
             "data": [ 
               {
                   "id": 1,
                   "name": {
                       "en": "product brand name en",
                       "ar": "product brand name ar"
                   },
                   "description": {
                       "en": "product brand description en",
                       "ar": "product brand description ar"
                   },
                   "slug": "product-brand-name-en",
                   "status": 5,
                   "thumb": "http://127.0.0.1:8000/storage/product-brand/208/conversions/1-thumb.webp",
                   "cover": "http://127.0.0.1:8000/storage/product-brand/208/conversions/1-cover.webp",
                   "creator": {
                       "id": 1,
                       "first_name": "Mohammad",
                       "last_name": "Gamal",
                       "email": "Mohamed-Gamal@ektml.com",
                       "phone": "1097609636",
                       "username": "mohammad_gamal1",
                       "badge": 5,
                       "points": 0,
                       "email_verified_at": "2024-08-12T21:10:21.000000Z",
                       "device_token": null,
                       "web_token": null,
                       "status": 5,
                       "birthday": null,
                       "gender": null,
                       "country_code": "+20",
                       "is_guest": 10,
                       "balance": "0.000000",
                       "creator_id": 1,
                       "editor_id": 1,
                       "deleter_id": null,
                       "created_at": "2023-07-23T17:38:44.000000Z",
                       "updated_at": "2024-08-12T21:10:21.000000Z",
                       "deleted_at": null
                   },
                   "editor": null,
                   "deleter": null,
                   "deleted_at": null
               }
             ]
           }
           ```
          return product brand data with data of creator.
    3. delete
        - **Endpoint**: `DELETE /api/admin/setting/product-brand/{product-brand_id}`
        - **Description**: Delete an existing product brand.
        - **Request Body**:
          ```php
          {
            "Authorization": Bearer 9|qqtFAmtPqL5Ux1Pm0f2uy2aasArEsHgYXcR26N9M17556e54
          }
          ```
        - **Response**:
          ```json
          {
       
          }
          ```
          return empty json data, that mean product brand was deleted successfully.
    4. update
        - **Endpoint**: `POST /api/admin/setting/product-brand/{product-brand_id}`
        - **Description**: Updates the details of an existing product brand.
        - **Request Body**:
          ```php
          {
            "Authorization": Bearer 9|qqtFAmtPqL5Ux1Pm0f2uy2aasArEsHgYXcR26N9M17556e54,
            "name_en":  "product brand name en new",
            "name_ar":  "product brand name ar new",
            "description_en":  "product brand description en new",
            "description_ar":  "product brand description ar new",
            "status": 10
          }
          ```
        - **Response**:
          ```json
          {
            "data": { 
                  "id": 1,
                  "name": {
                      "en": "product brand name en new",
                      "ar": "product brand name ar new"
                  },
                  "description": {
                      "en": "product brand description en new",
                      "ar": "product brand description en new"
                  },
                  "slug": "product-brand-name-en-new",
                  "status": 10,
                  "thumb": "http://127.0.0.1:8000/storage/product-brand/208/conversions/1-thumb.webp",
                  "cover": "http://127.0.0.1:8000/storage/product-brand/208/conversions/1-cover.webp",
                  "creator": {
                      "id": 1,
                      "first_name": "Mohammad",
                      "last_name": "Gamal",
                      "email": "Mohamed-Gamal@ektml.com",
                      "phone": "1097609636",
                      "username": "mohammad_gamal1",
                      "badge": 5,
                      "points": 0,
                      "email_verified_at": "2024-08-12T21:10:21.000000Z",
                      "device_token": null,
                      "web_token": null,
                      "status": 5,
                      "birthday": null,
                      "gender": null,
                      "country_code": "+20",
                      "is_guest": 10,
                      "balance": "0.000000",
                      "creator_id": 1,
                      "editor_id": 1,
                      "deleter_id": null,
                      "created_at": "2023-07-23T17:38:44.000000Z",
                      "updated_at": "2024-08-12T21:10:21.000000Z",
                      "deleted_at": null
                  },
                  "editor": {
                      "id": 1,
                      "first_name": "Mohammad",
                      "last_name": "Gamal",
                      "email": "Mohamed-Gamal@ektml.com",
                      "phone": "1097609636",
                      "username": "mohammad_gamal1",
                      "badge": 5,
                      "points": 0,
                      "email_verified_at": "2024-08-12T21:10:21.000000Z",
                      "device_token": null,
                      "web_token": null,
                      "status": 5,
                      "birthday": null,
                      "gender": null,
                      "country_code": "+20",
                      "is_guest": 10,
                      "balance": "0.000000",
                      "creator_id": 1,
                      "editor_id": 1,
                      "deleter_id": null,
                      "created_at": "2023-07-23T17:38:44.000000Z",
                      "updated_at": "2024-08-12T21:10:21.000000Z",
                      "deleted_at": null
                  },
                  "deleter": null,
                  "deleted_at": null
            }
          }
          ```
          return benefit data with data of creator and editor.
    5. show
        - **Endpoint**: `GET /api/admin/setting/product-brand/show/{product-brand_id}`
        - **Description**: Retrieve details of a single product brand based on it ID.
        - **Request Body**:
          ```php
          { 
            "Authorization": Bearer 9|qqtFAmtPqL5Ux1Pm0f2uy2aasArEsHgYXcR26N9M17556e54
          }
          ```
        - **Response**:
          ```json
          {
              "data": {
                  "id": 1,
                  "name": {
                      "en": "product brand name en new",
                      "ar": "product brand name ar new"
                  },
                  "description": {
                      "en": "product brand description en new",
                      "ar": "product brand description en new"
                  },
                  "status": 10,
                  "thumb": "http://127.0.0.1:8000/storage/product-brand/205/conversions/1-thumb.webp",
                  "cover": "http://127.0.0.1:8000/storage/product-brand/205/conversions/1-cover.webp",
                  "creator": {
                      "id": 1,
                      "first_name": "Mohammad",
                      "last_name": "Gamal",
                      "email": "Mohamed-Gamal@ektml.com",
                      "phone": "1097609636",
                      "username": "mohammad_gamal1",
                      "badge": 5,
                      "points": 0,
                      "email_verified_at": "2024-08-12T21:10:21.000000Z",
                      "device_token": null,
                      "web_token": null,
                      "status": 5,
                      "birthday": null,
                      "gender": null,
                      "country_code": "+20",
                      "is_guest": 10,
                      "balance": "0.000000",
                      "creator_id": 1,
                      "editor_id": 1,
                      "deleter_id": null,
                      "created_at": "2023-07-23T17:38:44.000000Z",
                      "updated_at": "2024-08-12T21:10:21.000000Z",
                      "deleted_at": null
                      },
                  "editor": null,
                  "deleter": null,
                  "deleted_at": null
              }
          }
          ```
    6. un deleted list
       - **Endpoint**: `GET /api/admin/setting/product-brand/un-deleted-list`
       - **Description**: Fetches a list of un deleted list available product brands with data of creator, editor, deleter and deleted at..
       - **Request Body**:
         ```php
         { 
           "Authorization": Bearer 9|qqtFAmtPqL5Ux1Pm0f2uy2aasArEsHgYXcR26N9M17556e54
         }
         ```
       - **Response**:
         ```json
         {
           "data": [ 
               {
                   "id": 90,
                   "name": {
                       "en": "Kenzo",
                       "ar": "كينزو"
                   },
                   "slug": "kenzo",
                   "description": {
                       "en": "Kenzo Description",
                       "ar": "نص كينزو"
                   },
                   "status": 5,
                   "thumb": "https://lavui-bucket.s3.eu-central-1.amazonaws.com/product-brand/186/conversions/kenzo-thumb.webp",
                   "cover": "https://lavui-bucket.s3.eu-central-1.amazonaws.com/product-brand/186/conversions/kenzo-cover.webp",
                   "creator": null,
                   "editor": null,
                   "deleter": null,
                   "deleted_at": null
               }
           ]
         }
         ```
- **Product Attribute**
    1. index
        - **Endpoint**: `GET /api/admin/setting/product-attribute`
        - **Description**: 
          - Fetches a list of all available product attributes.
          - Type value between (5, 10): 5 mean this attribute 'Color', 10 mean this attribute 'Drop Down List'
        - **Request Body**:
          ```php
          { 
            "Authorization": Bearer 9|qqtFAmtPqL5Ux1Pm0f2uy2aasArEsHgYXcR26N9M17556e54
          }
          ```
        - **Response**:
          ```json
          {
              "data": [
                  {
                  "id": 3,
                  "name": "Right",
                  "type": 10,
                  "options": []
                  },
                  {
                  "id": 2,
                  "name": "Left",
                  "type": 10,
                  "options": []
                  },
                  {
                  "id": 1,
                  "name": "Color",
                  "type": 5,
                  "options": []
                  }
              ]
          }
          ```
          return list of product attribute with all options.
    2. create
        - **Endpoint**: `POST /api/admin/setting/product-attribute`
        - **Description**: Add a new product attrbiute.
        - **Request Body**:
             ```php
             {
               "Authorization": Bearer 9|qqtFAmtPqL5Ux1Pm0f2uy2aasArEsHgYXcR26N9M17556e54,
               "name": "Glass Type",
               "type": 10
             }
             ```
        - **Response**:
          ```json
           {
             "data": [ 
                  {
                  "id": 4,
                  "name": "Glass Type",
                  "type": 10,
                  "options": []
                  }
             ]
           }
           ```
    3. delete
        - **Endpoint**: `DELETE /api/admin/setting/product-attribute/{product-attribute_id}`
        - **Description**: Delete an existing product attribute.
        - **Request Body**:
          ```php
          {
            "Authorization": Bearer 9|qqtFAmtPqL5Ux1Pm0f2uy2aasArEsHgYXcR26N9M17556e54
          }
          ```
        - **Response**:
          ```json
          {
       
          }
          ```
          return empty json data, that mean product attribute was deleted successfully.
    4. update
        - **Endpoint**: `POST /api/admin/setting/product-attribute/{product-attribute_id}?_method=PATCH`
        - **Description**: Updates the details of an existing product attribute.
        - **Request Body**:
          ```php
          {
            "Authorization": Bearer 9|qqtFAmtPqL5Ux1Pm0f2uy2aasArEsHgYXcR26N9M17556e54,
            "name":  "Glasses Lens Type",
            "type": 10
          }
          ```
        - **Response**:
          ```json
          {
            "data": { 
              "id": 4,
              "name": "Glasses Lens Type",
              "type": 10
            }
          }
          ```
    5. show
        - **Endpoint**: `GET /api/admin/setting/product-attribute/show/{product-attribute_id}`
        - **Description**: Retrieve details of a single product attribute based on it ID.
        - **Request Body**:
          ```php
          { 
            "Authorization": Bearer 9|qqtFAmtPqL5Ux1Pm0f2uy2aasArEsHgYXcR26N9M17556e54
          }
          ```
        - **Response**:
          ```json
          {
              "data": [
                  {
                  "id": 3,
                  "name": "Right",
                  "type": 10,
                  "options": []
                  },
                  {
                  "id": 2,
                  "name": "Left",
                  "type": 10,
                  "options": []
                  },
                  {
                  "id": 1,
                  "name": "Color",
                  "type": 5,
                  "options": []
                  }
              ]
          }
          ```
- **Product Attribute Option**
- **Product**
- **Product Section**
    1. index
        - **Endpoint**: `GET /api/admin/product-section`
        - **Description**:
            - Fetches a list of all available product sections.
        - **Request Body**:
          ```php
          {
            "Authorization": Bearer 9|qqtFAmtPqL5Ux1Pm0f2uy2aasArEsHgYXcR26N9M17556e54
          }
          ```
        - **Response**:
          ```json
          {
              "data": [
                  {
                  "id": 2,
                  "title": {
                      "en": "New From Gucci",
                      "ar": "جديد من جوتشي"
                  },
                  "description": {
                      "en": "New From Gucci Description",
                      "ar": "جديد من جوتشي نص"
                  },
                  "class": 2,
                  "slug": "new-from-gucci",
                  "image": "",
                  "status": 5
                  },
                  {
                  "id": 1,
                  "title": {
                      "en": "New From Fendi",
                      "ar": "جديد من فيندي"
                  },
                  "description": {
                      "en": "New From Fendi Description",
                      "ar": "جديد من فيندي نص"
                  },
                  "class": 1,
                  "slug": "new-from-fendi",
                  "image": "",
                  "status": 5
                  }
              ]
          }
          ```
          return list of product section.
    2. create
        - **Endpoint**: `POST /api/admin/product-section`
        - **Description**: Add a new product section.
        - **Request Body**:
             ```php
             {
               "Authorization": Bearer 9|qqtFAmtPqL5Ux1Pm0f2uy2aasArEsHgYXcR26N9M17556e54,
               "title_en": "New From Fendi",
               "title_ar": "جديد من فيندي",
               "description_en": "New From Fendi Description",
               "description_ar": "جديد من فيندي نص",
               "status": 5,
               "image": <image file>, (required if class = description)
               "class": 1
             }
             ```
        - **Response**:
          ```json
           {
             "data": { 
                  "id": 1,
                  "title": {
                      "en": "New From Fendi",
                      "ar": "جديد من فيندي"
                  },
                  "description": {
                      "en": "New From Fendi Description",
                      "ar": "جديد من فيندي نص"
                  },
                  "class": 1,
                  "slug": "new-from-fendi",
                  "image": "",
                  "status": 5
             }
           }
           ```
    3. delete
        - **Endpoint**: `DELETE /api/admin/product-section/{product-section_id}`
        - **Description**: Delete an existing product section.
        - **Request Body**:
          ```php
          {
            "Authorization": Bearer 9|qqtFAmtPqL5Ux1Pm0f2uy2aasArEsHgYXcR26N9M17556e54
          }
          ```
        - **Response**:
          ```json
          {
       
          }
          ```
          return empty json data, that mean product section was deleted successfully.
    4. update
        - **Endpoint**: `POST /api/admin/product-section/{product-section_id}`
        - **Description**: Updates the details of an existing product section.
        - **Request Body**:
          ```php
          {
            "Authorization": Bearer 9|qqtFAmtPqL5Ux1Pm0f2uy2aasArEsHgYXcR26N9M17556e54,
            "title_en": "New From Fendi",
            "title_ar": "جديد من فيندي",
            "description_en": "New From Fendi Description",
            "description_ar": "جديد من فيندي نص",
            "status": 5,
            "image": <image file>, (required if class = description)
            "class": 1
          }
          ```
        - **Response**:
          ```json
          {
            "data": { 
                  "id": 1,
                  "title": {
                      "en": "New From Fendi",
                      "ar": "جديد من فيندي"
                  },
                  "description": {
                      "en": "New From Fendi Description",
                      "ar": "جديد من فيندي نص"
                  },
                  "class": 1,
                  "slug": "new-from-fendi",
                  "image": "",
                  "status": 5
            }
          }
          ```
    5. show
        - **Endpoint**: `GET /api/admin/product-section/show/{product-section_id}`
        - **Description**: Retrieve details of a single product section based on it ID.
        - **Request Body**:
          ```php
          { 
            "Authorization": Bearer 9|qqtFAmtPqL5Ux1Pm0f2uy2aasArEsHgYXcR26N9M17556e54
          }
          ```
        - **Response**:
          ```json
          {
              "data": [
                  {
                  "id": 2,
                  "title": {
                      "en": "New From Gucci",
                      "ar": "جديد من جوتشي"
                  },
                  "description": {
                      "en": "New From Gucci Description",
                      "ar": "جديد من جوتشي نص"
                  },
                  "class": 9,
                  "slug": "new-from-gucci",
                  "image": "",
                  "status": 5
                  }
              ]
          }
          ```
- **Product Section Product**
    1. index
        - **Endpoint**: `GET /api/admin/product-section`
        - **Description**:
            - Fetches a list of all available product sections.
        - **Request Body**:
          ```php
          {
            "Authorization": Bearer 9|qqtFAmtPqL5Ux1Pm0f2uy2aasArEsHgYXcR26N9M17556e54
          }
          ```
        - **Response**:
          ```json
          {
              "data": [
                  {
                  "id": 2,
                  "title": {
                      "en": "New From Gucci",
                      "ar": "جديد من جوتشي"
                  },
                  "description": {
                      "en": "New From Gucci Description",
                      "ar": "جديد من جوتشي نص"
                  },
                  "class": 2,
                  "slug": "new-from-gucci",
                  "image": "",
                  "status": 5
                  },
                  {
                  "id": 1,
                  "title": {
                      "en": "New From Fendi",
                      "ar": "جديد من فيندي"
                  },
                  "description": {
                      "en": "New From Fendi Description",
                      "ar": "جديد من فيندي نص"
                  },
                  "class": 1,
                  "slug": "new-from-fendi",
                  "image": "",
                  "status": 5
                  }
              ]
          }
          ```
          return list of product section.
    2. create
        - **Endpoint**: `POST /api/admin/product-section`
        - **Description**: Add a new product section.
        - **Request Body**:
             ```php
             {
               "Authorization": Bearer 9|qqtFAmtPqL5Ux1Pm0f2uy2aasArEsHgYXcR26N9M17556e54,
               "title_en": "New From Fendi",
               "title_ar": "جديد من فيندي",
               "description_en": "New From Fendi Description",
               "description_ar": "جديد من فيندي نص",
               "status": 5,
               "image": <image file>, (required if class = description)
               "class": 1
             }
             ```
        - **Response**:
          ```json
           {
             "data": { 
                  "id": 1,
                  "title": {
                      "en": "New From Fendi",
                      "ar": "جديد من فيندي"
                  },
                  "description": {
                      "en": "New From Fendi Description",
                      "ar": "جديد من فيندي نص"
                  },
                  "class": 1,
                  "slug": "new-from-fendi",
                  "image": "",
                  "status": 5
             }
           }
           ```
    3. delete
        - **Endpoint**: `DELETE /api/admin/product-section/{product-section_id}`
        - **Description**: Delete an existing product section.
        - **Request Body**:
          ```php
          {
            "Authorization": Bearer 9|qqtFAmtPqL5Ux1Pm0f2uy2aasArEsHgYXcR26N9M17556e54
          }
          ```
        - **Response**:
          ```json
          {
       
          }
          ```
          return empty json data, that mean product section was deleted successfully.
    4. update
        - **Endpoint**: `POST /api/admin/product-section/{product-section_id}`
        - **Description**: Updates the details of an existing product section.
        - **Request Body**:
          ```php
          {
            "Authorization": Bearer 9|qqtFAmtPqL5Ux1Pm0f2uy2aasArEsHgYXcR26N9M17556e54,
            "title_en": "New From Fendi",
            "title_ar": "جديد من فيندي",
            "description_en": "New From Fendi Description",
            "description_ar": "جديد من فيندي نص",
            "status": 5,
            "image": <image file>, (required if class = description)
            "class": 1
          }
          ```
        - **Response**:
          ```json
          {
            "data": { 
                  "id": 1,
                  "title": {
                      "en": "New From Fendi",
                      "ar": "جديد من فيندي"
                  },
                  "description": {
                      "en": "New From Fendi Description",
                      "ar": "جديد من فيندي نص"
                  },
                  "class": 1,
                  "slug": "new-from-fendi",
                  "image": "",
                  "status": 5
            }
          }
          ```
- **Currency**
    1. index
        - **Endpoint**: `GET /api/admin/setting/currency`
        - **Description**: Fetches a list of all available currencies.
        - **Request Body**:
          ```php
          { 
            "Authorization": Bearer 9|qqtFAmtPqL5Ux1Pm0f2uy2aasArEsHgYXcR26N9M17556e54
          }
          ```
        - **Response**:
          ```json
          {
              "data": [
                  {
                  "id": 2,
                  "name": "egypt",
                  "name_symbol": "egypt (egy)",
                  "symbol": "egy",
                  "code": "egy",
                  "is_cryptocurrency": 5,
                  "exchange_rate": 5,
                  "creator": {
                      "id": 1,
                      "first_name": "Mohammad",
                      "last_name": "Gamal",
                      "email": "Mohamed-Gamal@ektml.com",
                      "phone": "1097609636",
                      "username": "mohammad_gamal1",
                      "badge": 5,
                      "points": 0,
                      "email_verified_at": "2024-08-12T21:10:21.000000Z",
                      "device_token": null,
                      "web_token": null,
                      "status": 5,
                      "birthday": null,
                      "gender": null,
                      "country_code": "+20",
                      "is_guest": 10,
                      "balance": "0.000000",
                      "creator_id": 1,
                      "editor_id": 1,
                      "deleter_id": null,
                      "created_at": "2023-07-23T17:38:44.000000Z",
                      "updated_at": "2024-08-12T21:10:21.000000Z",
                      "deleted_at": null
                  },
                  "editor": null,
                  "deleter": {
                      "id": 1,
                      "first_name": "Mohammad",
                      "last_name": "Gamal",
                      "email": "Mohamed-Gamal@ektml.com",
                      "phone": "1097609636",
                      "username": "mohammad_gamal1",
                      "badge": 5,
                      "points": 0,
                      "email_verified_at": "2024-08-12T21:10:21.000000Z",
                      "device_token": null,
                      "web_token": null,
                      "status": 5,
                      "birthday": null,
                      "gender": null,
                      "country_code": "+20",
                      "is_guest": 10,
                      "balance": "0.000000",
                      "creator_id": 1,
                      "editor_id": 1,
                      "deleter_id": null,
                      "created_at": "2023-07-23T17:38:44.000000Z",
                      "updated_at": "2024-08-12T21:10:21.000000Z",
                      "deleted_at": null
                  },
                  "deleted_at": "01:18 PM, 30-09-2024"
                  },
                  {
                  "id": 1,
                  "name": "SAR",
                  "name_symbol": "SAR (SAR)",
                  "symbol": "SAR",
                  "code": "SAR",
                  "is_cryptocurrency": 10,
                  "exchange_rate": 1,
                  "creator": {
                      "id": 1,
                      "first_name": "Mohammad",
                      "last_name": "Gamal",
                      "email": "Mohamed-Gamal@ektml.com",
                      "phone": "1097609636",
                      "username": "mohammad_gamal1",
                      "badge": 5,
                      "points": 0,
                      "email_verified_at": "2024-08-12T21:10:21.000000Z",
                      "device_token": null,
                      "web_token": null,
                      "status": 5,
                      "birthday": null,
                      "gender": null,
                      "country_code": "+20",
                      "is_guest": 10,
                      "balance": "0.000000",
                      "creator_id": 1,
                      "editor_id": 1,
                      "deleter_id": null,
                      "created_at": "2023-07-23T17:38:44.000000Z",
                      "updated_at": "2024-08-12T21:10:21.000000Z",
                      "deleted_at": null
                  },
                  "editor": null,
                  "deleter": null,
                  "deleted_at": null
                  }
              ]
          }
          ```
          return list of currencies, with data of creator, editor, deleter and deleted at.
    2. create
        - **Endpoint**: `POST /api/admin/setting/currency`
        - **Description**: Add a new currency.
        - **Request Body**:
             ```php
             {
               "Authorization": Bearer 9|qqtFAmtPqL5Ux1Pm0f2uy2aasArEsHgYXcR26N9M17556e54,
               "name": "egypt",
               "symbol": "egy",
               "code": "egy",
               "is_cryptocurrency": 5,
               "exchange_rate": 5
             }
             ```
        - **Response**:
          ```json
           {
             "data": [ 
               {
                   "id": 1,
                   "name": "egypt",
                   "name_symbol": "egypt (egy)",
                   "symbol": "egy",
                   "code": "egy",
                   "is_cryptocurrency": 5,
                   "exchange_rate": 5,
                   "creator": {
                       "id": 1,
                       "first_name": "Mohammad",
                       "last_name": "Gamal",
                       "email": "Mohamed-Gamal@ektml.com",
                       "phone": "1097609636",
                       "username": "mohammad_gamal1",
                       "badge": 5,
                       "points": 0,
                       "email_verified_at": "2024-08-12T21:10:21.000000Z",
                       "device_token": null,
                       "web_token": null,
                       "status": 5,
                       "birthday": null,
                       "gender": null,
                       "country_code": "+20",
                       "is_guest": 10,
                       "balance": "0.000000",
                       "creator_id": 1,
                       "editor_id": 1,
                       "deleter_id": null,
                       "created_at": "2023-07-23T17:38:44.000000Z",
                       "updated_at": "2024-08-12T21:10:21.000000Z",
                       "deleted_at": null
                   },
                   "editor": null,
                   "deleter": null,
                   "deleted_at": null
               }
             ]
           }
           ```
          return currency data with data of creator.
    3. delete
        - **Endpoint**: `DELETE /api/admin/setting/currency/{currency_id}`
        - **Description**: Delete an existing currency.
        - **Request Body**:
          ```php
          {
            "Authorization": Bearer 9|qqtFAmtPqL5Ux1Pm0f2uy2aasArEsHgYXcR26N9M17556e54
          }
          ```
        - **Response**:
          ```json
          {
       
          }
          ```
          return empty json data, that mean currency was deleted successfully.
    4. update
        - **Endpoint**: `POST /api/admin/setting/currency/{currency_id}?_method=PATCH`
        - **Description**: Updates the details of an existing currency.
        - **Request Body**:
          ```php
          {
            "Authorization": Bearer 9|qqtFAmtPqL5Ux1Pm0f2uy2aasArEsHgYXcR26N9M17556e54,
            "name":  "saudi arabia",
            "symbol":  "SAR",
            "code":  "SAR",
            "is_cryptocurrency": 5,
            "exchange_rate": 5
          }
          ```
        - **Response**:
          ```json
          {
            "data": { 
                  "id": 1,
                  "name": "saudi arabia",
                  "name_symbol": "saudi arabia (SAR)",
                  "symbol": "SAR",
                  "code": "SAR",
                  "is_cryptocurrency": 5,
                  "exchange_rate": 5,
                  "creator": {
                      "id": 1,
                      "first_name": "Mohammad",
                      "last_name": "Gamal",
                      "email": "Mohamed-Gamal@ektml.com",
                      "phone": "1097609636",
                      "username": "mohammad_gamal1",
                      "badge": 5,
                      "points": 0,
                      "email_verified_at": "2024-08-12T21:10:21.000000Z",
                      "device_token": null,
                      "web_token": null,
                      "status": 5,
                      "birthday": null,
                      "gender": null,
                      "country_code": "+20",
                      "is_guest": 10,
                      "balance": "0.000000",
                      "creator_id": 1,
                      "editor_id": 1,
                      "deleter_id": null,
                      "created_at": "2023-07-23T17:38:44.000000Z",
                      "updated_at": "2024-08-12T21:10:21.000000Z",
                      "deleted_at": null
                  },
                  "editor": {
                      "id": 1,
                      "first_name": "Mohammad",
                      "last_name": "Gamal",
                      "email": "Mohamed-Gamal@ektml.com",
                      "phone": "1097609636",
                      "username": "mohammad_gamal1",
                      "badge": 5,
                      "points": 0,
                      "email_verified_at": "2024-08-12T21:10:21.000000Z",
                      "device_token": null,
                      "web_token": null,
                      "status": 5,
                      "birthday": null,
                      "gender": null,
                      "country_code": "+20",
                      "is_guest": 10,
                      "balance": "0.000000",
                      "creator_id": 1,
                      "editor_id": 1,
                      "deleter_id": null,
                      "created_at": "2023-07-23T17:38:44.000000Z",
                      "updated_at": "2024-08-12T21:10:21.000000Z",
                      "deleted_at": null
                  },
                  "deleter": null,
                  "deleted_at": null
            }
          }
          ```
          return currency data with data of creator and editor.
    5. show
        - **Endpoint**: `GET /api/admin/setting/currency/show/{currency_id}`
        - **Description**: Retrieve details of a single currency based on it ID.
        - **Request Body**:
          ```php
          { 
            "Authorization": Bearer 9|qqtFAmtPqL5Ux1Pm0f2uy2aasArEsHgYXcR26N9M17556e54
          }
          ```
        - **Response**:
          ```json
          {
              "data": {
                  "id": 1,
                  "name": "saudi arabia",
                  "name_symbol": "saudi arabia (SAR)",
                  "symbol": "SAR",
                  "code": "SAR",
                  "is_cryptocurrency": 5,
                  "exchange_rate": 5,
                  "creator": {
                      "id": 1,
                      "first_name": "Mohammad",
                      "last_name": "Gamal",
                      "email": "Mohamed-Gamal@ektml.com",
                      "phone": "1097609636",
                      "username": "mohammad_gamal1",
                      "badge": 5,
                      "points": 0,
                      "email_verified_at": "2024-08-12T21:10:21.000000Z",
                      "device_token": null,
                      "web_token": null,
                      "status": 5,
                      "birthday": null,
                      "gender": null,
                      "country_code": "+20",
                      "is_guest": 10,
                      "balance": "0.000000",
                      "creator_id": 1,
                      "editor_id": 1,
                      "deleter_id": null,
                      "created_at": "2023-07-23T17:38:44.000000Z",
                      "updated_at": "2024-08-12T21:10:21.000000Z",
                      "deleted_at": null
                      },
                  "editor": null,
                  "deleter": null,
                  "deleted_at": null
              }
          }
          ```
- **Payment Gateway**
    1. Add it by seeder. 
- **SMS Gateway**
    1. Add it by seeder. 
- **Benefit**
    1. index
        - **Endpoint**: `GET /api/admin/setting/benefit`
        - **Description**: Fetches a list of all available benefits.
        - **Request Body**:
          ```php
          { 
            "Authorization": Bearer 9|qqtFAmtPqL5Ux1Pm0f2uy2aasArEsHgYXcR26N9M17556e54
          }
          ```
        - **Response**:
          ```json
          {
            "data": [ 
              {
                  "id": 1,
                  "title": {
                      "en": "Quality & Savings",
                      "ar": "Quality & Savings"
                  },
                  "description": {
                      "en": "Quality & Savings",
                      "ar": "Quality & Savings"
                  },
                  "status": 5,
                  "thumb": "http://127.0.0.1:8000/storage/benefit/205/conversions/1-thumb.webp",
                  "cover": "http://127.0.0.1:8000/storage/benefit/205/conversions/1-cover.webp",
                  "creator": {
                      "id": 1,
                      "first_name": "Mohammad",
                      "last_name": "Gamal",
                      "email": "Mohamed-Gamal@ektml.com",
                      "phone": "1097609636",
                      "username": "mohammad_gamal1",
                      "badge": 5,
                      "points": 0,
                      "email_verified_at": "2024-08-12T21:10:21.000000Z",
                      "device_token": null,
                      "web_token": null,
                      "status": 5,
                      "birthday": null,
                      "gender": null,
                      "country_code": "+20",
                      "is_guest": 10,
                      "balance": "0.000000",
                      "creator_id": 1,
                      "editor_id": 1,
                      "deleter_id": null,
                      "created_at": "2023-07-23T17:38:44.000000Z",
                      "updated_at": "2024-08-12T21:10:21.000000Z",
                      "deleted_at": null
                  },
                  "editor": null,
                  "deleter": null,
                  "deleted_at": null
              }
            ]
          }
          ```
          return list of benefit, with data of creator, editor, deleter and deleted at.
    2. create
       - **Endpoint**: `POST /api/admin/setting/benefit`
       - **Description**: Add a new benefit.
       - **Request Body**:
            ```php
            {
              "Authorization": Bearer 9|qqtFAmtPqL5Ux1Pm0f2uy2aasArEsHgYXcR26N9M17556e54,
              "title_en": "Quality & Savings",
              "title_ar": "Quality & Savings",
              "description_en": "Quality & Savings",
              "description_ar": "Quality & Savings",
              "status": 5,
              "image": <image file>
            }
            ```
       - **Response**:
         ```json
          {
            "data": [ 
              {
                  "id": 1,
                  "title": {
                      "en": "Quality & Savings",
                      "ar": "Quality & Savings"
                  },
                  "description": {
                      "en": "Quality & Savings",
                      "ar": "Quality & Savings"
                  },
                  "status": 5,
                  "thumb": "http://127.0.0.1:8000/storage/benefit/205/conversions/1-thumb.webp",
                  "cover": "http://127.0.0.1:8000/storage/benefit/205/conversions/1-cover.webp",
                  "creator": {
                      "id": 1,
                      "first_name": "Mohammad",
                      "last_name": "Gamal",
                      "email": "Mohamed-Gamal@ektml.com",
                      "phone": "1097609636",
                      "username": "mohammad_gamal1",
                      "badge": 5,
                      "points": 0,
                      "email_verified_at": "2024-08-12T21:10:21.000000Z",
                      "device_token": null,
                      "web_token": null,
                      "status": 5,
                      "birthday": null,
                      "gender": null,
                      "country_code": "+20",
                      "is_guest": 10,
                      "balance": "0.000000",
                      "creator_id": 1,
                      "editor_id": 1,
                      "deleter_id": null,
                      "created_at": "2023-07-23T17:38:44.000000Z",
                      "updated_at": "2024-08-12T21:10:21.000000Z",
                      "deleted_at": null
                  },
                  "editor": null,
                  "deleter": null,
                  "deleted_at": null
              }
            ]
          }
          ```
          return benefit data with data of creator.
    3. delete
       - **Endpoint**: `DELETE /api/admin/setting/benefit/{benefit_id}`
       - **Description**: Delete an existing benefit.
       - **Request Body**:
         ```php
         {
           "Authorization": Bearer 9|qqtFAmtPqL5Ux1Pm0f2uy2aasArEsHgYXcR26N9M17556e54
         }
         ```
       - **Response**:
         ```json
         {
      
         }
         ```
         return empty json data, that mean benefit was deleted successfully.
    4. update
       - **Endpoint**: `POST /api/admin/setting/benefit/{benefit_id}`
       - **Description**: Updates the details of an existing benefit.
       - **Request Body**:
         ```php
         {
           "Authorization": Bearer 9|qqtFAmtPqL5Ux1Pm0f2uy2aasArEsHgYXcR26N9M17556e54,
           "title_en":  "Quality & Savings",
           "title_ar":  "Quality & Savings",
           "description_en":  "Quality & Savings",
           "description_ar":  "Quality & Savings",
           "status": 10
         }
         ```
       - **Response**:
         ```json
         {
           "data": { 
                 "id": 1,
                 "title": {
                     "en": "Quality & Savings",
                     "ar": "Quality & Savings"
                 },
                 "description": {
                     "en": "Quality & Savings",
                     "ar": "Quality & Savings"
                 },
                 "status": 10,
                 "thumb": "http://127.0.0.1:8000/storage/benefit/205/conversions/1-thumb.webp",
                 "cover": "http://127.0.0.1:8000/storage/benefit/205/conversions/1-cover.webp",
                 "creator": {
                     "id": 1,
                     "first_name": "Mohammad",
                     "last_name": "Gamal",
                     "email": "Mohamed-Gamal@ektml.com",
                     "phone": "1097609636",
                     "username": "mohammad_gamal1",
                     "badge": 5,
                     "points": 0,
                     "email_verified_at": "2024-08-12T21:10:21.000000Z",
                     "device_token": null,
                     "web_token": null,
                     "status": 5,
                     "birthday": null,
                     "gender": null,
                     "country_code": "+20",
                     "is_guest": 10,
                     "balance": "0.000000",
                     "creator_id": 1,
                     "editor_id": 1,
                     "deleter_id": null,
                     "created_at": "2023-07-23T17:38:44.000000Z",
                     "updated_at": "2024-08-12T21:10:21.000000Z",
                     "deleted_at": null
                 },
                 "editor": {
                     "id": 1,
                     "first_name": "Mohammad",
                     "last_name": "Gamal",
                     "email": "Mohamed-Gamal@ektml.com",
                     "phone": "1097609636",
                     "username": "mohammad_gamal1",
                     "badge": 5,
                     "points": 0,
                     "email_verified_at": "2024-08-12T21:10:21.000000Z",
                     "device_token": null,
                     "web_token": null,
                     "status": 5,
                     "birthday": null,
                     "gender": null,
                     "country_code": "+20",
                     "is_guest": 10,
                     "balance": "0.000000",
                     "creator_id": 1,
                     "editor_id": 1,
                     "deleter_id": null,
                     "created_at": "2023-07-23T17:38:44.000000Z",
                     "updated_at": "2024-08-12T21:10:21.000000Z",
                     "deleted_at": null
                 },
                 "deleter": null,
                 "deleted_at": null
           }
         }
         ```
         return benefit data with data of creator and editor.
    5. show
       - **Endpoint**: `GET /api/admin/setting/benefit/show/{benefit_id}`
       - **Description**: Retrieve details of a single benefit based on it ID.
       - **Request Body**:
         ```php
         { 
           "Authorization": Bearer 9|qqtFAmtPqL5Ux1Pm0f2uy2aasArEsHgYXcR26N9M17556e54
         }
         ```
       - **Response**:
         ```json
         {
             "data": {
                 "id": 1,
                 "title": {
                     "en": "Quality & Savings",
                     "ar": "Quality & Savings"
                 },
                 "description": {
                     "en": "Quality & Savings",
                     "ar": "Quality & Savings"
                 },
                 "status": 10,
                 "thumb": "http://127.0.0.1:8000/storage/benefit/205/conversions/1-thumb.webp",
                 "cover": "http://127.0.0.1:8000/storage/benefit/205/conversions/1-cover.webp",
                 "creator": {
                     "id": 1,
                     "first_name": "Mohammad",
                     "last_name": "Gamal",
                     "email": "Mohamed-Gamal@ektml.com",
                     "phone": "1097609636",
                     "username": "mohammad_gamal1",
                     "badge": 5,
                     "points": 0,
                     "email_verified_at": "2024-08-12T21:10:21.000000Z",
                     "device_token": null,
                     "web_token": null,
                     "status": 5,
                     "birthday": null,
                     "gender": null,
                     "country_code": "+20",
                     "is_guest": 10,
                     "balance": "0.000000",
                     "creator_id": 1,
                     "editor_id": 1,
                     "deleter_id": null,
                     "created_at": "2023-07-23T17:38:44.000000Z",
                     "updated_at": "2024-08-12T21:10:21.000000Z",
                     "deleted_at": null
                     },
                 "editor": null,
                 "deleter": null,
                 "deleted_at": null
             }
         }
         ```
- **Badge**
    1. index
        - **Endpoint**: `GET /api/admin/setting/badge`
        - **Description**: Fetches a list of all available badges, we add the total of points product's user buy, check the points level, and update the level of user badge.
        - **Request Body**:
          ```php
          { 
            "Authorization": Bearer 9|qqtFAmtPqL5Ux1Pm0f2uy2aasArEsHgYXcR26N9M17556e54
          }
          ```
        - **Response**:
          ```json
          {
            "data": { 
                "bronze": 1000,
                "silver": 2000,
                "platinum": 3000,
                "gold": 4000
            }
          }
          ```
- **Theme**
    1. index
        - **Endpoint**: `GET /api/admin/setting/theme`
        - **Description**: Fetches a list of all available themes.
        - **Request Body**:
          ```php
          { 
            "Authorization": Bearer 9|qqtFAmtPqL5Ux1Pm0f2uy2aasArEsHgYXcR26N9M17556e54
          }
          ```
        - **Response**:
          ```json
          {
            "data": { 
                "theme_logo": "http://127.0.0.1:8000/images/required/theme-logo.png",
                "theme_favicon_logo": "http://127.0.0.1:8000/images/required/theme-favicon-logo.png",
                "theme_footer_logo": "http://127.0.0.1:8000/images/required/theme-footer-logo.png",
                "theme_mobile_logo": "http://127.0.0.1:8000/images/required/theme-mobile-logo.png"
            }
          }
          ```
          return list of themes.
  2. update
      - **Endpoint**: `POST /api/admin/setting/theme`
      - **Description**: Add new themes.
      - **Request Body**:
        ```php
        { 
          "Authorization": Bearer 9|qqtFAmtPqL5Ux1Pm0f2uy2aasArEsHgYXcR26N9M17556e54,
          "theme_logo": <image file>, (optional)         
          "theme_favicon_logo": <image file>, (optional)         
          "theme_footer_logo": <image file>, (optional)         
          "theme_mobile_logo": <image file> (optional)         
        }
        ```
      - **Response**:
        ```json
        {
          "data": { 
              "theme_logo": "http://127.0.0.1:8000/images/required/theme-logo.png",
              "theme_favicon_logo": "http://127.0.0.1:8000/images/required/theme-favicon-logo.png",
              "theme_footer_logo": "http://127.0.0.1:8000/images/required/theme-footer-logo.png",
              "theme_mobile_logo": "http://127.0.0.1:8000/images/required/theme-mobile-logo.png"
          }
        }
        ```
        return list of themes.

#### End User Points

- **Slider**
    1. index
        - **Endpoint**: `GET /api/frontend/slider`
        - **Description**: Fetches a list of un deleted list sliders.
        - **Request Body**:
          ```php
          { 
            "Authorization": Bearer 9|qqtFAmtPqL5Ux1Pm0f2uy2aasArEsHgYXcR26N9M17556e54
          }
          ```
        - **Response**:
          ```json
          {
              "data": [
                {
                  "id": 3,
                  "title": {
                      "en": "Slider Three",
                      "ar": "الصوره الثالثه"
                  },
                  "description": {
                      "en": "Slider Three",
                      "ar": "الصوره الثالثه"
                  },
                  "status": 5,
                  "link": null,
                  "mean_slider": 10,
                  "image": "https://lavui-bucket.s3.eu-central-1.amazonaws.com/slider/13/conversions/slider_three-cover.webp",
                  "creator": {
                      "id": 1,
                      "first_name": "Mohammad",
                      "last_name": "Gamal",
                      "email": "Mohamed-Gamal@ektml.com",
                      "phone": "1097609636",
                      "username": "mohammad_gamal1",
                      "badge": 5,
                      "points": 0,
                      "email_verified_at": "2024-08-12T21:10:21.000000Z",
                      "device_token": null,
                      "web_token": null,
                      "status": 5,
                      "birthday": null,
                      "gender": null,
                      "country_code": "+20",
                      "is_guest": 10,
                      "balance": "0.000000",
                      "creator_id": 1,
                      "editor_id": 1,
                      "deleter_id": null,
                      "created_at": "2023-07-23T17:38:44.000000Z",
                      "updated_at": "2024-08-12T21:10:21.000000Z",
                      "deleted_at": null
                  },
                  "editor": null,
                  "deleter": null,
                  "deleted_at": null
                  },
                  {
                  "id": 2,
                  "title": {
                      "en": "Slider Two",
                      "ar": "الصوره الثانيه"
                  },
                  "description": {
                      "en": "Slider Two",
                      "ar": "الصوره الثانيه"
                  },
                  "status": 5,
                  "link": null,
                  "mean_slider": 10,
                  "image": "https://lavui-bucket.s3.eu-central-1.amazonaws.com/slider/12/conversions/slider_two-cover.webp",
                  "creator": {
                      "id": 1,
                      "first_name": "Mohammad",
                      "last_name": "Gamal",
                      "email": "Mohamed-Gamal@ektml.com",
                      "phone": "1097609636",
                      "username": "mohammad_gamal1",
                      "badge": 5,
                      "points": 0,
                      "email_verified_at": "2024-08-12T21:10:21.000000Z",
                      "device_token": null,
                      "web_token": null,
                      "status": 5,
                      "birthday": null,
                      "gender": null,
                      "country_code": "+20",
                      "is_guest": 10,
                      "balance": "0.000000",
                      "creator_id": 1,
                      "editor_id": 1,
                      "deleter_id": null,
                      "created_at": "2023-07-23T17:38:44.000000Z",
                      "updated_at": "2024-08-12T21:10:21.000000Z",
                      "deleted_at": null
                  },
                  "editor": null,
                  "deleter": null,
                  "deleted_at": null
                  },
                  {
                  "id": 1,
                  "title": {
                      "en": "Slider One",
                      "ar": "الصوره الاولي"
                  },
                  "description": {
                      "en": "Slider One",
                      "ar": "الصوره الاولي"
                  },
                  "status": 5,
                  "link": null,
                  "mean_slider": 10,
                  "image": "https://lavui-bucket.s3.eu-central-1.amazonaws.com/slider/11/conversions/slider_one-cover.webp",
                  "creator": {
                      "id": 1,
                      "first_name": "Mohammad",
                      "last_name": "Gamal",
                      "email": "Mohamed-Gamal@ektml.com",
                      "phone": "1097609636",
                      "username": "mohammad_gamal1",
                      "badge": 5,
                      "points": 0,
                      "email_verified_at": "2024-08-12T21:10:21.000000Z",
                      "device_token": null,
                      "web_token": null,
                      "status": 5,
                      "birthday": null,
                      "gender": null,
                      "country_code": "+20",
                      "is_guest": 10,
                      "balance": "0.000000",
                      "creator_id": 1,
                      "editor_id": 1,
                      "deleter_id": null,
                      "created_at": "2023-07-23T17:38:44.000000Z",
                      "updated_at": "2024-08-12T21:10:21.000000Z",
                      "deleted_at": null
                  },
                  "editor": null,
                  "deleter": null,
                  "deleted_at": null
                  }
              ]
          }
          ```
- **Product Brand**
    1. index
        - **Endpoint**: `GET /api/frontend/product-brand`
        - **Description**: Fetches a list of un deleted list product-brand, with random 12 record.
        - **Request Body**:
          ```php
          { 
            "Authorization": Bearer 9|qqtFAmtPqL5Ux1Pm0f2uy2aasArEsHgYXcR26N9M17556e54
          }
          ```
        - **Response**:
          ```json
          {
              "data": {
                    "id": 36,
                    "name": {
                        "en": "MCM",
                        "ar": "إم سي إم"
                    },
                    "slug": "mcm",
                    "description": {
                        "en": "MCM, also known as Modern Creation München, is an esteemed luxury brand founded in 1976 in Germany. At its core, MCM embodies a visionary concept that artfully merges luxurious travel essentials and accessories with a dynamic, avant-garde perspective.",
                        "ar": "إم سي إم، المعروفة أيضًا باسم مودرن كرييشن ميونيخ، هي علامة تجارية فاخرة مرموقة تأسست في عام 1976 في ألمانيا. تجسد إم سي إم مفهومًا يمزج ببراعة بين ضروريات السفر الفاخرة والإكسسوارات مع منظور أنيق ومتطور."
                    },
                    "status": 5,
                    "thumb": "https://lavui-bucket.s3.eu-central-1.amazonaws.com/product-brand/82/conversions/mcm-thumb.webp",
                    "cover": "https://lavui-bucket.s3.eu-central-1.amazonaws.com/product-brand/82/conversions/mcm-cover.webp",
                    "creator": null,
                    "editor": null,
                    "deleter": null,
                    "deleted_at": null
              }
          }
          ```
        This is only item but i code return 12 record.
    2. show brand products
        - **Endpoint**: `GET /api/frontend/product?brand={brand_id}`
        - **Description**: Fetches a list of product-brand products.
        - **Request Body**:
          ```php
          { 
            "Authorization": Bearer 9|qqtFAmtPqL5Ux1Pm0f2uy2aasArEsHgYXcR26N9M17556e54
          }
          ```
        - **Response**:
          ```json
          {
              "data": {
                    "id": 1651,
                    "name": {
                        "en": "DKNY DK1034 440",
                        "ar": "DKNY DK1034 440"
                    },
                    "brand": {
                        "id": 18,
                        "name": {
                            "en": "Dkny",
                            "ar": "دكني"
                        },
                        "slug": "dkny",
                        "description": {
                            "en": "DKNY is a contemporary fashion brand founded in 1984 by Donna Karan in New York City, USA. With a distinctive concept that seamlessly fused luxury and streetwear, all while maintaining a touch of understated elegance. DKNY's eyewear collection encapsulates the essence of the brand by harmonizing timeless and modern elements.",
                            "ar": "دكني علامة أزياء معاصرة تأسست في عام 1984 على يد دونا كاران في مدينة نيويورك بالولايات المتحدة. بفكرة مميزة تجمع بين الفخامة وأسلوب الشارع، مع الحفاظ على لمسة من الأناقة البسيطة. تجسد مجموعة النظارات الشمسية والطبية لـ دكني جوهر العلامة من خلال مزج العناصر الخالدة والعصرية."
                        },
                        "status": 5,
                        "creator_id": null,
                        "editor_id": null,
                        "deleter_id": null,
                        "created_at": "2024-09-26T13:45:04.000000Z",
                        "updated_at": "2024-09-26T13:45:04.000000Z",
                        "deleted_at": null
                    },
                    "slug": "dkny-dk1034-4401649",
                    "currency_price": "SAR609.00",
                    "cover": "http://127.0.0.1:8000/images/default/product/cover.png",
                    "flash_sale": false,
                    "is_offer": false,
                    "discounted_price": "SAR609.00",
                    "rating_star": null,
                    "rating_star_count": 0,
                    "wishlist": false,
                    "model_image": null
              }
          }
          ```
       This is only item but i code return 12 record.
- **Benefit**
    1. index
        - **Endpoint**: `GET /api/frontend/benefit`
        - **Description**: Fetches a list of un deleted list benefits.
        - **Request Body**:
          ```php
          { 
            "Authorization": Bearer 9|qqtFAmtPqL5Ux1Pm0f2uy2aasArEsHgYXcR26N9M17556e54
          }
          ```
        - **Response**:
          ```json
          {
            "data": [
              {
                "id": 2,
                "title": {
                    "en": "Quality & Savings",
                    "ar": "Quality & Savings"
                },
                "description": {
                    "en": "Quality & Savings",
                    "ar": "Quality & Savings"
                },
                "status": 5,
                "thumb": "http://127.0.0.1:8000/storage/benefit/210/conversions/3-thumb.webp",
                "cover": "http://127.0.0.1:8000/storage/benefit/210/conversions/3-cover.webp",
                "creator": {
                    "id": 1,
                    "first_name": "Mohammad",
                    "last_name": "Gamal",
                    "email": "Mohamed-Gamal@ektml.com",
                    "phone": "1097609636",
                    "username": "mohammad_gamal1",
                    "badge": 5,
                    "points": 0,
                    "email_verified_at": "2024-08-12T21:10:21.000000Z",
                    "device_token": null,
                    "web_token": null,
                    "status": 5,
                    "birthday": null,
                    "gender": null,
                    "country_code": "+20",
                    "is_guest": 10,
                    "balance": "0.000000",
                    "creator_id": 1,
                    "editor_id": 1,
                    "deleter_id": null,
                    "created_at": "2023-07-23T17:38:44.000000Z",
                    "updated_at": "2024-08-12T21:10:21.000000Z",
                    "deleted_at": null
                },
                "editor": null,
                "deleter": null,
                "deleted_at": null
              }
            ]
          }
          ```
        Return all un deleted benefits.
- **Contact**
    1. index
        - **Endpoint**: `POST /api/frontend/contact`
        - **Description**: Send information to technical support first_name, last_name, email, message.
        - **Request Body**:
          ```php
          { 
            "first_name": "ahmed",
            "last_name": "awad",
            "email": "ahmed.awad@isynclouds.com",
            "message": "message"
          }
          ```
        - **Response**:
          ```json
          {
            
          }
          ```
          return empty json data, that mean data send to technical support team successfully.
- **Banner**
    1. index
        - **Endpoint**: `POST /api/frontend/banner`
        - **Description**: Return all banners.
        - **Request Body**:
          ```php
          { 
            "Authorization": Bearer 9|qqtFAmtPqL5Ux1Pm0f2uy2aasArEsHgYXcR26N9M17556e54
          }
          ```
        - **Response**:
          ```json
          {
              "data": [
                  {
                      "id": 5,
                      "text": {
                          "en": "",
                          "ar": ""
                      },
                      "position": 5,
                      "link": null,
                      "type": 5,
                      "image": "http://127.0.0.1:8000/storage/banner/213/conversions/3-cover.webp",
                      "status": 5,
                      "creator": {
                          "id": 1,
                          "first_name": "Mohammad",
                          "last_name": "Gamal",
                          "email": "Mohamed-Gamal@ektml.com",
                          "phone": "1097609636",
                          "username": "mohammad_gamal1",
                          "badge": 5,
                          "points": 0,
                          "email_verified_at": "2024-08-12T21:10:21.000000Z",
                          "device_token": null,
                          "web_token": null,
                          "status": 5,
                          "birthday": null,
                          "gender": null,
                          "country_code": "+20",
                          "is_guest": 10,
                          "balance": "0.000000",
                          "creator_id": 1,
                          "editor_id": 1,
                          "deleter_id": null,
                          "created_at": "2023-07-23T17:38:44.000000Z",
                          "updated_at": "2024-08-12T21:10:21.000000Z",
                          "deleted_at": null
                      },
                      "editor": null,
                      "deleter": null,
                      "deleted_at": null
                  },
                  {
                      "id": 4,
                      "text": {
                          "en": "",
                          "ar": ""
                      },
                      "position": 4,
                      "link": null,
                      "type": 5,
                      "image": "http://127.0.0.1:8000/storage/banner/212/conversions/3-cover.webp",
                      "status": 5,
                      "creator": {
                          "id": 1,
                          "first_name": "Mohammad",
                          "last_name": "Gamal",
                          "email": "Mohamed-Gamal@ektml.com",
                          "phone": "1097609636",
                          "username": "mohammad_gamal1",
                          "badge": 5,
                          "points": 0,
                          "email_verified_at": "2024-08-12T21:10:21.000000Z",
                          "device_token": null,
                          "web_token": null,
                          "status": 5,
                          "birthday": null,
                          "gender": null,
                          "country_code": "+20",
                          "is_guest": 10,
                          "balance": "0.000000",
                          "creator_id": 1,
                          "editor_id": 1,
                          "deleter_id": null,
                          "created_at": "2023-07-23T17:38:44.000000Z",
                          "updated_at": "2024-08-12T21:10:21.000000Z",
                          "deleted_at": null
                      },
                      "editor": null,
                      "deleter": null,
                      "deleted_at": null
                  },
                  {
                      "id": 3,
                      "text": {
                          "en": "",
                          "ar": ""
                      },
                      "position": 2,
                      "link": null,
                      "type": 5,
                      "image": "http://127.0.0.1:8000/storage/banner/211/conversions/3-cover.webp",
                      "status": 5,
                      "creator": {
                          "id": 1,
                          "first_name": "Mohammad",
                          "last_name": "Gamal",
                          "email": "Mohamed-Gamal@ektml.com",
                          "phone": "1097609636",
                          "username": "mohammad_gamal1",
                          "badge": 5,
                          "points": 0,
                          "email_verified_at": "2024-08-12T21:10:21.000000Z",
                          "device_token": null,
                          "web_token": null,
                          "status": 5,
                          "birthday": null,
                          "gender": null,
                          "country_code": "+20",
                          "is_guest": 10,
                          "balance": "0.000000",
                          "creator_id": 1,
                          "editor_id": 1,
                          "deleter_id": null,
                          "created_at": "2023-07-23T17:38:44.000000Z",
                          "updated_at": "2024-08-12T21:10:21.000000Z",
                          "deleted_at": null
                      },
                      "editor": null,
                      "deleter": null,
                      "deleted_at": null
                  },
                  {
                      "id": 2,
                      "text": {
                          "en": "",
                          "ar": ""
                      },
                      "position": 2,
                      "link": null,
                      "type": 10,
                      "image": "http://127.0.0.1:8000/storage/banner/204/conversions/3-cover.webp",
                      "status": 5,
                      "creator": {
                          "id": 1,
                          "first_name": "Mohammad",
                          "last_name": "Gamal",
                          "email": "Mohamed-Gamal@ektml.com",
                          "phone": "1097609636",
                          "username": "mohammad_gamal1",
                          "badge": 5,
                          "points": 0,
                          "email_verified_at": "2024-08-12T21:10:21.000000Z",
                          "device_token": null,
                          "web_token": null,
                          "status": 5,
                          "birthday": null,
                          "gender": null,
                          "country_code": "+20",
                          "is_guest": 10,
                          "balance": "0.000000",
                          "creator_id": 1,
                          "editor_id": 1,
                          "deleter_id": null,
                          "created_at": "2023-07-23T17:38:44.000000Z",
                          "updated_at": "2024-08-12T21:10:21.000000Z",
                          "deleted_at": null
                      },
                      "editor": {
                          "id": 1,
                          "first_name": "Mohammad",
                          "last_name": "Gamal",
                          "email": "Mohamed-Gamal@ektml.com",
                          "phone": "1097609636",
                          "username": "mohammad_gamal1",
                          "badge": 5,
                          "points": 0,
                          "email_verified_at": "2024-08-12T21:10:21.000000Z",
                          "device_token": null,
                          "web_token": null,
                          "status": 5,
                          "birthday": null,
                          "gender": null,
                          "country_code": "+20",
                          "is_guest": 10,
                          "balance": "0.000000",
                          "creator_id": 1,
                          "editor_id": 1,
                          "deleter_id": null,
                          "created_at": "2023-07-23T17:38:44.000000Z",
                          "updated_at": "2024-08-12T21:10:21.000000Z",
                          "deleted_at": null
                      },
                      "deleter": null,
                      "deleted_at": null
                  }
              ]
          }
          ```
          - position = 1, that mean position at text banner at homepage.
          - position = 2, that mean position at general position at homepage.
          - position = 3, that mean position at category position at homepage.
          - position = 4, that mean position at virtual try position at homepage.
          - position = 5, that mean position at subscription position at homepage.
- **Product Category**
    1. index
        - **Endpoint**: `GET /api/frontend/product-category`
        - **Description**: Fetches a list of un deleted list product-category.
        - **Request Body**:
          ```php
          { 
            "Authorization": Bearer 9|qqtFAmtPqL5Ux1Pm0f2uy2aasArEsHgYXcR26N9M17556e54
          }
          ```
        - **Response**:
          ```json
          {
          "data": [
            {
              "id": 2,
              "name": {
                  "en": "Sunglasses",
                  "ar": "النظارات الشمسية"
              },
              "slug": "sunglasses9",
              "description": {
                  "en": "Pick your sunglasses from Lavui's extensive genuine designer brands.",
                  "ar": "مجموعة لافوي الواسعة من النظارات الشمسية الأصلية. اختاري نظارتك المكملة لأناقتك من بين عشرات علامات المصممين الأصلية."
              },
              "parent_category": null,
              "status": 5,
              "parent_id": null,
              "thumb": "https://lavui-bucket.s3.eu-central-1.amazonaws.com/product-category/189/conversions/sunglasses-thumb.webp",
              "cover": "https://lavui-bucket.s3.eu-central-1.amazonaws.com/product-category/189/conversions/sunglasses-cover.webp",
              "preview": "https://lavui-bucket.s3.eu-central-1.amazonaws.com/product-category/189/conversions/sunglasses-preview.webp",
              "creator": {
                  "id": 1,
                  "first_name": "Mohammad",
                  "last_name": "Gamal",
                  "email": "Mohamed-Gamal@ektml.com",
                  "phone": "1097609636",
                  "username": "mohammad_gamal1",
                  "badge": 5,
                  "points": 0,
                  "email_verified_at": "2024-08-12T21:10:21.000000Z",
                  "device_token": null,
                  "web_token": null,
                  "status": 5,
                  "birthday": null,
                  "gender": null,
                  "country_code": "+20",
                  "is_guest": 10,
                  "balance": "0.000000",
                  "creator_id": 1,
                  "editor_id": 1,
                  "deleter_id": null,
                  "created_at": "2023-07-23T17:38:44.000000Z",
                  "updated_at": "2024-08-12T21:10:21.000000Z",
                  "deleted_at": null
              },
              "editor": null,
              "deleter": null,
              "deleted_at": null
            },
            {
              "id": 3,
              "name": {
                  "en": "Contact Lenses",
                  "ar": "العدسات اللاصقة"
              },
              "slug": "contact-lenses20",
              "description": {
                  "en": "Explore Lavui's selected brands in disposable contact lenses, with a wide range of powers and durations that suit your lifestyle.",
                  "ar": "اخترنا لك أفضل علامات العدسات اللاصقة الطبية بمختلف أنواع القوة التصحيحية والمدة التي تناسب أسلوب حياتك."
              },
              "parent_category": null,
              "status": 5,
              "parent_id": null,
              "thumb": "http://127.0.0.1:8000/images/default/category/thumb.png",
              "cover": "http://127.0.0.1:8000/images/default/category/cover.png",
              "preview": "http://127.0.0.1:8000/images/default/category/preview.png",
              "creator": {
                  "id": 1,
                  "first_name": "Mohammad",
                  "last_name": "Gamal",
                  "email": "Mohamed-Gamal@ektml.com",
                  "phone": "1097609636",
                  "username": "mohammad_gamal1",
                  "badge": 5,
                  "points": 0,
                  "email_verified_at": "2024-08-12T21:10:21.000000Z",
                  "device_token": null,
                  "web_token": null,
                  "status": 5,
                  "birthday": null,
                  "gender": null,
                  "country_code": "+20",
                  "is_guest": 10,
                  "balance": "0.000000",
                  "creator_id": 1,
                  "editor_id": 1,
                  "deleter_id": null,
                  "created_at": "2023-07-23T17:38:44.000000Z",
                  "updated_at": "2024-08-12T21:10:21.000000Z",
                  "deleted_at": null
              },
              "editor": null,
              "deleter": null,
              "deleted_at": null
            },
            {
              "id": 4,
              "name": {
                  "en": "Accessories",
                  "ar": "الإكسسوارات"
              },
              "slug": "accessories34",
              "description": {
                  "en": "Explore Lavui's extensive collection of accessories that suit your sunglasses or eyeglasses.",
                  "ar": "اخترنا لك مجموعة واسعة من الإكسسوارات المتميزة للنظارات الشمسية والطبية."
              },
              "parent_category": null,
              "status": 5,
              "parent_id": null,
              "thumb": "https://lavui-bucket.s3.eu-central-1.amazonaws.com/product-category/194/conversions/accessories-thumb.webp",
              "cover": "https://lavui-bucket.s3.eu-central-1.amazonaws.com/product-category/194/conversions/accessories-cover.webp",
              "preview": "https://lavui-bucket.s3.eu-central-1.amazonaws.com/product-category/194/conversions/accessories-preview.webp",
              "creator": {
                  "id": 1,
                  "first_name": "Mohammad",
                  "last_name": "Gamal",
                  "email": "Mohamed-Gamal@ektml.com",
                  "phone": "1097609636",
                  "username": "mohammad_gamal1",
                  "badge": 5,
                  "points": 0,
                  "email_verified_at": "2024-08-12T21:10:21.000000Z",
                  "device_token": null,
                  "web_token": null,
                  "status": 5,
                  "birthday": null,
                  "gender": null,
                  "country_code": "+20",
                  "is_guest": 10,
                  "balance": "0.000000",
                  "creator_id": 1,
                  "editor_id": 1,
                  "deleter_id": null,
                  "created_at": "2023-07-23T17:38:44.000000Z",
                  "updated_at": "2024-08-12T21:10:21.000000Z",
                  "deleted_at": null
              },
              "editor": null,
              "deleter": null,
              "deleted_at": null
            },
            {
              "id": 5,
              "name": {
                  "en": "Eyeglasses",
                  "ar": "النظارات الطبية"
              },
              "slug": "eyeglasses54",
              "description": {
                  "en": "Lavui's ladies collection of genuine eyeglasses that complement your elegance. Pick your style with the lenses that suit you.",
                  "ar": "مجموعتنا النسائية من النظارات الطبية الأصلية المختارة لتصحيح النظر، والمكمّلة لإطلالتك. حددي تصميمك المفضل مع العدسات المناسبة."
              },
              "parent_category": null,
              "status": 5,
              "parent_id": null,
              "thumb": "https://lavui-bucket.s3.eu-central-1.amazonaws.com/product-category/196/conversions/eyeglasses-thumb.webp",
              "cover": "https://lavui-bucket.s3.eu-central-1.amazonaws.com/product-category/196/conversions/eyeglasses-cover.webp",
              "preview": "https://lavui-bucket.s3.eu-central-1.amazonaws.com/product-category/196/conversions/eyeglasses-preview.webp",
              "creator": {
                  "id": 1,
                  "first_name": "Mohammad",
                  "last_name": "Gamal",
                  "email": "Mohamed-Gamal@ektml.com",
                  "phone": "1097609636",
                  "username": "mohammad_gamal1",
                  "badge": 5,
                  "points": 0,
                  "email_verified_at": "2024-08-12T21:10:21.000000Z",
                  "device_token": null,
                  "web_token": null,
                  "status": 5,
                  "birthday": null,
                  "gender": null,
                  "country_code": "+20",
                  "is_guest": 10,
                  "balance": "0.000000",
                  "creator_id": 1,
                  "editor_id": 1,
                  "deleter_id": null,
                  "created_at": "2023-07-23T17:38:44.000000Z",
                  "updated_at": "2024-08-12T21:10:21.000000Z",
                  "deleted_at": null
              },
              "editor": null,
              "deleter": null,
              "deleted_at": null
              }
            ]
          }
          ```
    2. show category products
        - **Endpoint**: `GET /api/frontend/product?category={product-category-slug}`
        - **Description**: Fetches a list of product-category products.
        - **Request Body**:
          ```php
          { 
            "Authorization": Bearer 9|qqtFAmtPqL5Ux1Pm0f2uy2aasArEsHgYXcR26N9M17556e54
          }
          ```
        - **Response**:
          ```json
          {
              "data": {
                    "id": 1651,
                    "name": {
                        "en": "DKNY DK1034 440",
                        "ar": "DKNY DK1034 440"
                    },
                    "brand": {
                        "id": 18,
                        "name": {
                            "en": "Dkny",
                            "ar": "دكني"
                        },
                        "slug": "dkny",
                        "description": {
                            "en": "DKNY is a contemporary fashion brand founded in 1984 by Donna Karan in New York City, USA. With a distinctive concept that seamlessly fused luxury and streetwear, all while maintaining a touch of understated elegance. DKNY's eyewear collection encapsulates the essence of the brand by harmonizing timeless and modern elements.",
                            "ar": "دكني علامة أزياء معاصرة تأسست في عام 1984 على يد دونا كاران في مدينة نيويورك بالولايات المتحدة. بفكرة مميزة تجمع بين الفخامة وأسلوب الشارع، مع الحفاظ على لمسة من الأناقة البسيطة. تجسد مجموعة النظارات الشمسية والطبية لـ دكني جوهر العلامة من خلال مزج العناصر الخالدة والعصرية."
                        },
                        "status": 5,
                        "creator_id": null,
                        "editor_id": null,
                        "deleter_id": null,
                        "created_at": "2024-09-26T13:45:04.000000Z",
                        "updated_at": "2024-09-26T13:45:04.000000Z",
                        "deleted_at": null
                    },
                    "slug": "dkny-dk1034-4401649",
                    "currency_price": "SAR609.00",
                    "cover": "http://127.0.0.1:8000/images/default/product/cover.png",
                    "flash_sale": false,
                    "is_offer": false,
                    "discounted_price": "SAR609.00",
                    "rating_star": null,
                    "rating_star_count": 0,
                    "wishlist": false,
                    "model_image": null
              }
          }
          ```
       This is only item but in code return all products included to the category.
- **Product Section**
    1. index
        - **Endpoint**: `GET /api/frontend/product-section/trend`
        - **Description**: Fetches a list of product trend products.
        - **Request Body**:
          ```php
          { 
            "Authorization": Bearer 9|qqtFAmtPqL5Ux1Pm0f2uy2aasArEsHgYXcR26N9M17556e54
          }
          ```
        - **Response**:
          ```json
          {
            "data": [
              {
                "id": 4,
                "title": {
                     "en": "Product Trend",
                     "ar": "تريند"
                },
                "description": {
                     "en": "Product Trend description",
                     "ar": "نص تريند"
                },
                "class": 1,
                "slug": "product-trend",
                "status": 5,
                "image": "http://127.0.0.1:8000/storage/product-section/214/conversions/1-cover.webp",
                "products": []
              }
            ]
          }
          ```
       Return all product section with products.
- **Product Trend**
    1. index
        - **Endpoint**: `GET /api/frontend/product-section/trend`
        - **Description**: Fetches a list of product trend products.
        - **Request Body**:
          ```php
          { 
            "Authorization": Bearer 9|qqtFAmtPqL5Ux1Pm0f2uy2aasArEsHgYXcR26N9M17556e54
          }
          ```
        - **Response**:
          ```json
          {
            "data": [
              {
                "id": 4,
                "title": {
                     "en": "Product Trend",
                     "ar": "تريند"
                },
                "description": {
                     "en": "Product Trend description",
                     "ar": "نص تريند"
                },
                "class": 1,
                "slug": "product-trend",
                "status": 5,
                "image": "http://127.0.0.1:8000/storage/product-section/214/conversions/1-cover.webp",
                "products": []
              }
            ]
          }
          ```
       Return product trend section with products.
- **Product Story**
    1. index
        - **Endpoint**: `GET /api/frontend/product-section/story`
        - **Description**: Fetches a list of product story products.
        - **Request Body**:
          ```php
          { 
            "Authorization": Bearer 9|qqtFAmtPqL5Ux1Pm0f2uy2aasArEsHgYXcR26N9M17556e54
          }
          ```
        - **Response**:
          ```json
          {
            "data": [
              {
                "id": 3,
                "title": {
                     "en": "Product Story",
                     "ar": "استوري"
                },
                "description": {
                     "en": "Product Story description",
                     "ar": "نص استوري"
                },
                "class": 2,
                "slug": "product-story",
                "status": 5,
                "image": "http://127.0.0.1:8000/storage/product-section/214/conversions/1-cover.webp",
                "products": []
              }
            ]
          }
          ```
       Return product story section with products.
- **New Arrival**
    1. index
        - **Endpoint**: `GET /api/frontend/product-section/new-arrival`
        - **Description**: Fetches a list of new arrival products.
        - **Request Body**:
          ```php
          { 
            "Authorization": Bearer 9|qqtFAmtPqL5Ux1Pm0f2uy2aasArEsHgYXcR26N9M17556e54
          }
          ```
        - **Response**:
          ```json
          {
            "data": [
              {
                "id": 2,
                "title": {
                     "en": "New Arrival",
                     "ar": "جديد لدينا"
                },
                "description": {
                     "en": "New Arrival description",
                     "ar": "نص جديد لدينا"
                },
                "class": 3,
                "slug": "new-arrival",
                "status": 5,
                "image": "http://127.0.0.1:8000/storage/product-section/214/conversions/1-cover.webp",
                "products": []
              }
            ]
          }
          ```
       Return new arrival section with products.
- **Discount**
    1. index
        - **Endpoint**: `GET /api/frontend/product-section/discount`
        - **Description**: Fetches a list of discount products.
        - **Request Body**:
          ```php
          { 
            "Authorization": Bearer 9|qqtFAmtPqL5Ux1Pm0f2uy2aasArEsHgYXcR26N9M17556e54
          }
          ```
        - **Response**:
          ```json
          {
            "data": [
              {
                "id": 4,
                "title": {
                     "en": "Discount",
                     "ar": "هبوط اسعار"
                },
                "description": {
                     "en": "Discount description",
                     "ar": "نص هبوط الاسعار"
                },
                "class": 4,
                "slug": "discount",
                "status": 5,
                "image": "http://127.0.0.1:8000/storage/product-section/214/conversions/1-cover.webp",
                "products": []
              }
            ]
          }
          ```
       Return discount section with products.
- **Custom Collection**
    1. index
        - **Endpoint**: `GET /api/frontend/product-section/custom-collection`
        - **Description**: Fetches a list of custom collection products.
        - **Request Body**:
          ```php
          { 
            "Authorization": Bearer 9|qqtFAmtPqL5Ux1Pm0f2uy2aasArEsHgYXcR26N9M17556e54
          }
          ```
        - **Response**:
          ```json
          {
            "data": [
              {
                "id": 5,
                "title": {
                     "en": "Custom Collection",
                     "ar": "كوليكشن"
                },
                "description": {
                     "en": "Custom Collection description",
                     "ar": "نص كوليكشن"
                },
                "class": 5,
                "slug": "custom-collection",
                "status": 5,
                "image": "http://127.0.0.1:8000/storage/product-section/214/conversions/1-cover.webp",
                "products": []
              }
            ]
          }
          ```
       Return custom collection section with products.
- **Gift**
    1. index
        - **Endpoint**: `GET /api/frontend/product-section/gift`
        - **Description**: Fetches a list of gift products.
        - **Request Body**:
          ```php
          { 
            "Authorization": Bearer 9|qqtFAmtPqL5Ux1Pm0f2uy2aasArEsHgYXcR26N9M17556e54
          }
          ```
        - **Response**:
          ```json
          {
            "data": [
              {
                "id": 6,
                "title": {
                     "en": "Gift",
                     "ar": "هدايه"
                },
                "description": {
                     "en": "Gift description",
                     "ar": "نص هدايه"
                },
                "class": 6,
                "slug": "gift",
                "status": 5,
                "image": "http://127.0.0.1:8000/storage/product-section/214/conversions/1-cover.webp",
                "products": []
              }
            ]
          }
          ```
       Return gift section with products.
- **Group**
    1. index
        - **Endpoint**: `GET /api/frontend/product-section/groups`
        - **Description**: Fetches a list of groups with his products.
        - **Request Body**:
          ```php
          { 
            "Authorization": Bearer 9|qqtFAmtPqL5Ux1Pm0f2uy2aasArEsHgYXcR26N9M17556e54
          }
          ```
        - **Response**:
          ```json
          {
            "data": [
              {
                "id": 7,
                "title": {
                     "en": "Group One",
                     "ar": "جروب"
                },
                "description": {
                     "en": "Group One description",
                     "ar": "نص جروب"
                },
                "class": 7,
                "slug": "group-one",
                "status": 5,
                "image": "http://127.0.0.1:8000/storage/product-section/214/conversions/1-cover.webp",
                "products": []
              },
              {
                "id": 8,
                "title": {
                     "en": "Group Two",
                     "ar": "جروب"
                },
                "description": {
                     "en": "Group Two description",
                     "ar": "نص جروب"
                },
                "class": 7,
                "slug": "group-two",
                "status": 5,
                "image": "http://127.0.0.1:8000/storage/product-section/214/conversions/1-cover.webp",
                "products": []
              }
            ]
          }
          ```
       Return groups section with products.
- **Picked For You**
    1. index
        - **Endpoint**: `GET /api/frontend/product-section/picked-for-you`
        - **Description**: Fetches a list of picked for you products.
        - **Request Body**:
          ```php
          { 
            "Authorization": Bearer 9|qqtFAmtPqL5Ux1Pm0f2uy2aasArEsHgYXcR26N9M17556e54
          }
          ```
        - **Response**:
          ```json
          {
            "data": [
              {
                "id": 8,
                "title": {
                     "en": "Picked For YOu",
                     "ar": "اخترنا لك"
                },
                "description": {
                     "en": "Picked For YOu description",
                     "ar": "نص اخترنا لك"
                },
                "class": 8,
                "slug": "picked-for-you",
                "status": 5,
                "image": "http://127.0.0.1:8000/storage/product-section/214/conversions/1-cover.webp",
                "products": []
              }
            ]
          }
          ```
       Return picked for you section with products.
- **General**
    1. index
        - **Endpoint**: `GET /api/frontend/product-section/general`
        - **Description**: Fetches a list of general groups products.
        - **Request Body**:
          ```php
          { 
            "Authorization": Bearer 9|qqtFAmtPqL5Ux1Pm0f2uy2aasArEsHgYXcR26N9M17556e54
          }
          ```
        - **Response**:
          ```json
          {
            "data": [
              {
                "id": 9,
                "title": {
                     "en": "New From Prada",
                     "ar": "جديد من برادا"
                },
                "description": {
                     "en": "New From Prada description",
                     "ar": "نص جديد من برادا"
                },
                "class": 9,
                "slug": "new-from-prada",
                "status": 5,
                "image": "http://127.0.0.1:8000/storage/product-section/214/conversions/1-cover.webp",
                "products": []
              },
              {
                "id": 10,
                "title": {
                     "en": "New From Gucci",
                     "ar": "جديد من جوتشي"
                },
                "description": {
                     "en": "New From Gucci description",
                     "ar": "نص جديد من جوتشي"
                },
                "class": 9,
                "slug": "new-from-gucci",
                "status": 5,
                "image": "http://127.0.0.1:8000/storage/product-section/214/conversions/1-cover.webp",
                "products": []
              }
            ]
          }
          ```
       Return general groups section with products.
- **Random Group**
    1. index
        - **Endpoint**: `GET /api/frontend/product-section/random-groups`
        - **Description**: Fetches a list of random groups products.
        - **Request Body**:
          ```php
          { 
            "Authorization": Bearer 9|qqtFAmtPqL5Ux1Pm0f2uy2aasArEsHgYXcR26N9M17556e54
          }
          ```
        - **Response**:
          ```json
          {
            "data": [
              {
                "id": 11,
                "title": {
                     "en": "New From Fendi",
                     "ar": "جديد من فندي"
                },
                "description": {
                     "en": "New From Fendi description",
                     "ar": "نص جديد من فندي"
                },
                "class": 10,
                "slug": "new-from-fendi",
                "status": 5,
                "image": "http://127.0.0.1:8000/storage/product-section/214/conversions/1-cover.webp",
                "products": []
              }
            ]
          }
          ```
       Return random groups section with products, that return every time only one random group.
- **Benefit**
    1. index
        - **Endpoint**: `GET /api/frontend/benefit`
        - **Description**: Fetches a list of benefits.
        - **Request Body**:
          ```php
          { 
            "Authorization": Bearer 9|qqtFAmtPqL5Ux1Pm0f2uy2aasArEsHgYXcR26N9M17556e54
          }
          ```
        - **Response**:
          ```json
          {
          "data": [
              {
              "id": 2,
              "title": {
                  "en": "Quality & Savings",
                  "ar": "Quality & Savings"
              },
              "description": {
                  "en": "Quality & Savings",
                  "ar": "Quality & Savings"
              },
              "status": 5,
              "thumb": "http://127.0.0.1:8000/storage/benefit/210/conversions/3-thumb.webp",
              "cover": "http://127.0.0.1:8000/storage/benefit/210/conversions/3-cover.webp",
              "creator": {
                  "id": 1,
                  "first_name": "Mohammad",
                  "last_name": "Gamal",
                  "email": "Mohamed-Gamal@ektml.com",
                  "phone": "1097609636",
                  "username": "mohammad_gamal1",
                  "badge": 5,
                  "points": 0,
                  "email_verified_at": "2024-08-12T21:10:21.000000Z",
                  "device_token": null,
                  "web_token": null,
                  "status": 5,
                  "birthday": null,
                  "gender": null,
                  "country_code": "+20",
                  "is_guest": 10,
                  "balance": "0.000000",
                  "creator_id": 1,
                  "editor_id": 1,
                  "deleter_id": null,
                  "created_at": "2023-07-23T17:38:44.000000Z",
                  "updated_at": "2024-08-12T21:10:21.000000Z",
                  "deleted_at": null
              },
              "editor": null,
              "deleter": null,
              "deleted_at": null
            }
          ]
          }
          ```
       Return benefits.

## Known Issues & Future Enhancements

#### Known Issues

- SKU Constraints: Products cannot have a SKU (Stock Keeping Unit) value of NULL. All products must be assigned a unique SKU to ensure proper inventory management and tracking.

- Incomplete Product Information: It is mandatory to enter all relevant product information during creation. Each product entry must be complete to maintain data integrity and consistency within the system.

- User Data Requirements: User profiles must contain values for the following attributes: attribute, country, city, state, and address. Incomplete user information may lead to issues with order processing and communication.

- Shopking System Registration Flow: After a user registers, the system saves the data to the database and subsequently sends an OTP (One-Time Password) code. Currently, users can log in regardless of whether they entered the OTP or not. To address this, the system utilizes a is_guest attribute to manage user registration status:

    - we can use **is_guest**

    - If is_guest = 10, it indicates that the user registered without successfully entering the OTP.
    - If is_guest = 5, it signifies that the user successfully registered and entered the OTP.

- State Availability: Not all countries and provinces have corresponding state entries in the system. Therefore, it is necessary for the administrator to manually add missing states through the dashboard to ensure users can select valid locations during registration and checkout.

Note: you can know all attributes must not be **NULL** at Create Checkout Session
in **[Tamara](https://docs.tamara.co/reference/createcheckoutsession)** Documentation.

#### Future Enhancements

- Virtual Try-On Support: Implement a virtual try-on feature that allows users to see how products will look on them before purchasing.

- Special Offer Discounts: Add functionality to support discounts through a "Special Offer" system, allowing dynamic discounts for products.

- Reward Points System: Enable users to earn points with each product purchase. Points will be accumulated and added to the user's total, which will then update the status of their loyalty badge.

- System Testing Code: Write comprehensive testing code to ensure the stability and reliability of the entire system.

- Gift Section Enhancement: Display all products that belong to the "Gifts" section on a dedicated Gifts page.

- Banner Link Integration: Connect the "Text Banner" in the "Banners" section to the main banner on the homepage for a unified experience.

- Dynamic Countdown for Discounts: Link the countdown timer for discount sections to the backend for dynamic and accurate updates, rather than using a front-end timer.

- Role-Based Permissions: Extend system-wide support for role-based functionalities such as creator, editor, deleter, and include deleted_at functionality for soft deletes.

- Eyeglasses Product Attributes: For prescription glasses, ensure that product attributes like material and type are available. When creating a new eyeglasses product, automatically assign the attribute 'eyeglasses' with specific options.

- New Product Type - Eyeglasses: Add a new product type 'eyeglasses' to the system to handle eyewear-specific attributes.

- User Size Integration: Link the front-end form for adding new eyeglass sizes with the backend. These sizes, including sph, cyl, add, and axis, should be stored as JSON for both right and left lenses in the user_size_glasses table.

- Product Feedback Points: When customers provide feedback on products (e.g., star rating, comments), reward them with points.

- Complete User Profile: Reward users with points when they complete secondary profile information, such as birthday and gender.

- Invite Link Points: Implement an invitation system where users earn points for inviting others.

- Social Media Sharing: Reward users with points for sharing posts about products on platforms like Facebook and Instagram.

- Order Tracking System: When the order status is marked "OnTheWay," automatically send a tracking link from the shipping company to the customer.

- Customer Follow-Up System:
    1. After an order is delivered, send follow-up communications via email, SMS, WhatsApp, or phone notifications.
    2. Send a feedback request 30 days after the order is delivered.
    3. Notify customers about new offers via email.
    4. Send personalized birthday discounts to customers as a special offer.

## Important Notes:

#### After website be production & Upload all images:
1. We must delete folder sql at public path.
2. Folder images at public path has 4 folders seeder, default, installer, required.
   - default: if any image at system can't read, system go to default folder and get image from it.
   - installer: form installer cycle.
   - required: this is required images.
   - seeder: when make seeder upload images from it.
3. Uncomment for code at RootController & master.blade.php 

#### Must to know Enums at app folder

#### Administrator must make sure all data moved from old database is clean.

