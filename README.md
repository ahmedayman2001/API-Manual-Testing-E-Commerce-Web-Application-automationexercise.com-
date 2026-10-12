#  API Manual Testing – E-Commerce Web Application

![Postman](https://img.shields.io/badge/Tool-Postman-FF6C37?logo=postman&logoColor=white)
![API Testing](https://img.shields.io/badge/Testing-API%20Testing-blue)
![JavaScript](https://img.shields.io/badge/Assertions-JavaScript-F7DF1E?logo=javascript&logoColor=black)
![Requests](https://img.shields.io/badge/APIs-14-success)
![Assertions](https://img.shields.io/badge/Assertions-91-brightgreen)

**API Manual Testing – E-Commerce Web Application** is a Postman collection that tests all **14 APIs** of the [Automation Exercise](https://www.automationexercise.com/) e-commerce website. Every request is verified with **JavaScript assertions** (`pm.test`) covering status codes, response time, response structure, data types, and business rules, for both positive and negative scenarios.

---

##  Demo & Documentation

| Resource | Link |
|---|---|
|  API Documentation | [automationexercise.com/api_list](https://www.automationexercise.com/api_list) |
| 🎥 Video Demo | [Watch the project demo](https://drive.google.com/file/d/13qja5WSIZ-tYK2Tcvo0OQlXcO0_HpDoy/view?usp=sharing) |

---

##  Project Overview

The goal of this project is to validate the behavior of the Automation Exercise REST APIs by:

- Verifying that each endpoint returns **HTTP 200 OK** and the expected **`responseCode` inside the response body**.
- Validating **response schema, field data types, and business rules**.
- Covering **negative scenarios** such as unsupported HTTP methods, missing parameters, and invalid credentials.
- Automating the checks with **Postman JavaScript test scripts**, so the whole collection can be run in one click.

---

## 🌐 Base URL & Important Notes

| Item | Value |
|---|---|
| **Domain** | `https://automationexercise.com` |
| **Collection variable** | `{{domain}}` |

Key behaviors of the Automation Exercise APIs that shape how the tests are written:

1. **All APIs return `200 OK` as the HTTP status**, even for negative scenarios. The *actual* result of each request is the **`responseCode` inside the response body** (e.g. `201`, `400`, `404`, `405`). Therefore, the main assertions are written on the **response body**, not only on the HTTP status line.
2. **Request data is sent as `x-www-form-urlencoded`.** Any data in the request body (search keyword, email, password, account details) is sent in Postman using **Body → x-www-form-urlencoded**, not JSON.
3. **Responses are returned as `text/html`.** The body is still JSON text, so the scripts parse it manually with `JSON.parse(pm.response.text())` instead of `pm.response.json()`.

---

## 🗂️ Collection Structure

The collection is organized into two folders:

### 🗂️ Products

> Every request below returns **HTTP 200 OK**. The last column is the `responseCode` returned inside the response body, which is what the assertions verify.

| ID | Request | Method | Endpoint | HTTP Status | Body `responseCode` |
|---|---|---|---|---|---|
| API 1 | Get All Products List | `GET` | `/api/productsList` | 200 OK | **200** |
| API 2 | POST To All Products List | `POST` | `/api/productsList` | 200 OK | **405** |
| API 3 | Get All Brands List | `GET` | `/api/brandsList` | 200 OK | **200** |
| API 4 | PUT To All Brands List | `PUT` | `/api/brandsList` | 200 OK | **405** |
| API 5 | POST To Search Product | `POST` | `/api/searchProduct` | 200 OK | **200** |
| API 6 | POST To Search Product without `search_product` parameter | `POST` | `/api/searchProduct` | 200 OK | **400** |

### 🗂️ User Account

| ID | Request | Method | Endpoint | HTTP Status | Body `responseCode` |
|---|---|---|---|---|---|
| API 7 | POST To Create/Register User Account | `POST` | `/api/createAccount` | 200 OK | **201** |
| API 8 | POST To Verify Login with valid details | `POST` | `/api/verifyLogin` | 200 OK | **200** |
| API 9 | POST To Verify Login without `email` parameter | `POST` | `/api/verifyLogin` | 200 OK | **400** |
| API 10 | DELETE To Verify Login | `DELETE` | `/api/verifyLogin` | 200 OK | **405** |
| API 11 | DELETE To Delete User Account | `DELETE` | `/api/deleteAccount` | 200 OK | **200** |
| API 12 | PUT To Update User Account | `PUT` | `/api/updateAccount` | 200 OK | **200** |
| API 13 | GET User Account Detail By Email | `GET` | `/api/getUserDetailByEmail` | 200 OK | **200** |
| API 14 | POST To Verify Login with invalid details | `POST` | `/api/verifyLogin` | 200 OK | **404** |

---

##  What Is Being Tested

Each request includes JavaScript assertions grouped into the following categories:

| Category | Examples |
|---|---|
| **Status & Performance** | HTTP status is `200 OK`, response time is below 3000 ms |
| **Response Format** | Body is a valid JSON object |
| **Body Validation** | Actual `responseCode` inside the body (200 / 201 / 400 / 404 / 405), exact `message` text, response contains only the expected keys |
| **Schema & Data Types** | Each product has `id` (number), `name`, `price`, `brand`, `category` (strings/objects); user object has all expected fields |
| **Business Rules** | Unique IDs, `usertype` is Men / Women / Kids, price format `Rs. <number>` and greater than zero, brand names belong to the known brands |
| **Search Relevance** | All returned products match the search keyword |
| **Negative Testing** | Body `responseCode` is 405 for unsupported methods, 400 for missing parameters, 404 for wrong credentials |
| **Security Check** | Password is **not** exposed in the user details response |
| **Data Consistency** | Email in the response matches the email sent in the request |

**Total: 14 requests · 91 assertions**

### Sample assertion

```javascript
let jsonData;

pm.test("Body is a valid JSON", function () {
    jsonData = JSON.parse(pm.response.text());
    pm.expect(jsonData).to.be.an("object");
});

pm.test("responseCode inside body is 200", function () {
    pm.expect(jsonData.responseCode).to.eql(200);
});

pm.test("Response time is less than 3000ms", function () {
    pm.expect(pm.response.responseTime).to.be.below(3000);
});
```

---

##  Dynamic Test Data (Pre-request Script)

The **Create/Register User Account** request generates a unique email and password before each run, so the test never fails because of an already registered user:

```javascript
const randomStr = Math.random().toString(36).substring(2, 8);
const timestamp = Date.now();

pm.collectionVariables.set("randomEmail", `user_${randomStr}_${timestamp}@gmail.com`);
pm.collectionVariables.set("randomPassword", `Pass@${Math.floor(Math.random() * 90000) + 10000}`);
```

### Collection Variables

| Variable | Description |
|---|---|
| `domain` | Base URL of the website: `https://automationexercise.com` |
| `randomEmail` | Auto-generated by the pre-request script |
| `randomPassword` | Auto-generated by the pre-request script |

---

##  How to Run

### Option 1: Postman App

1. Clone or download this repository.
2. Open **Postman** → **Import** → select the `.json` collection file.
3. Open the collection → **Variables** tab → set `domain` to `https://automationexercise.com`.
4. Click **Run collection** to execute all requests and view the test results.

### Option 2: Newman (CLI)

```bash
npm install -g newman

newman run API_Manual_Testing_Automation_Exercise_Website_APIs_Colletion_Postman_postman_collection.json \
  --env-var "domain=https://automationexercise.com"
```

>  **Tip:** Run the requests in folder order. The Create Account request (API 7) generates the credentials that the other User Account requests rely on.

---

##  Tools & Technologies

- **Postman**: API testing and collection runner
- **JavaScript (Chai / Postman `pm` API)**: test assertions
- **Newman**: command-line collection runner (optional)

---

## 📁 Repository Contents

```
├── API_Manual_Testing_Automation_Exercise_Website_APIs_Colletion_Postman_postman_collection.json
└── README.md
```

---

##  Author

**Ahmed Ayman**
Software QA Engineer | Manual, API & Automation Testing | Selenium & Rest Assured | ECPC Finalist | Competitive Programmer


---

#  API Manual Testing – E-Commerce Web Application

![Postman](https://img.shields.io/badge/Tool-Postman-FF6C37?logo=postman&logoColor=white)
![API Testing](https://img.shields.io/badge/Testing-API%20Testing-blue)
![JavaScript](https://img.shields.io/badge/Assertions-JavaScript-F7DF1E?logo=javascript&logoColor=black)
![Requests](https://img.shields.io/badge/APIs-14-success)
![Assertions](https://img.shields.io/badge/Assertions-91-brightgreen)

**API Manual Testing – E-Commerce Web Application** is a Postman collection that tests all **14 APIs** of the [Automation Exercise](https://www.automationexercise.com/) e-commerce website. Every request is verified with **JavaScript assertions** (`pm.test`) covering status codes, response time, response structure, data types, and business rules, for both positive and negative scenarios.

---

##  Demo & Documentation

| Resource | Link |
|---|---|
|  API Documentation | [automationexercise.com/api_list](https://www.automationexercise.com/api_list) |
| 🎥 Video Demo | [Watch the project demo](https://drive.google.com/file/d/13qja5WSIZ-tYK2Tcvo0OQlXcO0_HpDoy/view?usp=sharing) |

---

##  Project Overview

The goal of this project is to validate the behavior of the Automation Exercise REST APIs by:

- Verifying that each endpoint returns **HTTP 200 OK** and the expected **`responseCode` inside the response body**.
- Validating **response schema, field data types, and business rules**.
- Covering **negative scenarios** such as unsupported HTTP methods, missing parameters, and invalid credentials.
- Automating the checks with **Postman JavaScript test scripts**, so the whole collection can be run in one click.

---

## 🌐 Base URL & Important Notes

| Item | Value |
|---|---|
| **Domain** | `https://automationexercise.com` |
| **Collection variable** | `{{domain}}` |

Key behaviors of the Automation Exercise APIs that shape how the tests are written:

1. **All APIs return `200 OK` as the HTTP status**, even for negative scenarios. The *actual* result of each request is the **`responseCode` inside the response body** (e.g. `201`, `400`, `404`, `405`). Therefore, the main assertions are written on the **response body**, not only on the HTTP status line.
2. **Request data is sent as `x-www-form-urlencoded`.** Any data in the request body (search keyword, email, password, account details) is sent in Postman using **Body → x-www-form-urlencoded**, not JSON.
3. **Responses are returned as `text/html`.** The body is still JSON text, so the scripts parse it manually with `JSON.parse(pm.response.text())` instead of `pm.response.json()`.

---

## 🗂️ Collection Structure

The collection is organized into two folders:

### 🗂️ Products

> Every request below returns **HTTP 200 OK**. The last column is the `responseCode` returned inside the response body, which is what the assertions verify.

| ID | Request | Method | Endpoint | HTTP Status | Body `responseCode` |
|---|---|---|---|---|---|
| API 1 | Get All Products List | `GET` | `/api/productsList` | 200 OK | **200** |
| API 2 | POST To All Products List | `POST` | `/api/productsList` | 200 OK | **405** |
| API 3 | Get All Brands List | `GET` | `/api/brandsList` | 200 OK | **200** |
| API 4 | PUT To All Brands List | `PUT` | `/api/brandsList` | 200 OK | **405** |
| API 5 | POST To Search Product | `POST` | `/api/searchProduct` | 200 OK | **200** |
| API 6 | POST To Search Product without `search_product` parameter | `POST` | `/api/searchProduct` | 200 OK | **400** |

### 🗂️ User Account

| ID | Request | Method | Endpoint | HTTP Status | Body `responseCode` |
|---|---|---|---|---|---|
| API 7 | POST To Create/Register User Account | `POST` | `/api/createAccount` | 200 OK | **201** |
| API 8 | POST To Verify Login with valid details | `POST` | `/api/verifyLogin` | 200 OK | **200** |
| API 9 | POST To Verify Login without `email` parameter | `POST` | `/api/verifyLogin` | 200 OK | **400** |
| API 10 | DELETE To Verify Login | `DELETE` | `/api/verifyLogin` | 200 OK | **405** |
| API 11 | DELETE To Delete User Account | `DELETE` | `/api/deleteAccount` | 200 OK | **200** |
| API 12 | PUT To Update User Account | `PUT` | `/api/updateAccount` | 200 OK | **200** |
| API 13 | GET User Account Detail By Email | `GET` | `/api/getUserDetailByEmail` | 200 OK | **200** |
| API 14 | POST To Verify Login with invalid details | `POST` | `/api/verifyLogin` | 200 OK | **404** |

---

##  What Is Being Tested

Each request includes JavaScript assertions grouped into the following categories:

| Category | Examples |
|---|---|
| **Status & Performance** | HTTP status is `200 OK`, response time is below 3000 ms |
| **Response Format** | Body is a valid JSON object |
| **Body Validation** | Actual `responseCode` inside the body (200 / 201 / 400 / 404 / 405), exact `message` text, response contains only the expected keys |
| **Schema & Data Types** | Each product has `id` (number), `name`, `price`, `brand`, `category` (strings/objects); user object has all expected fields |
| **Business Rules** | Unique IDs, `usertype` is Men / Women / Kids, price format `Rs. <number>` and greater than zero, brand names belong to the known brands |
| **Search Relevance** | All returned products match the search keyword |
| **Negative Testing** | Body `responseCode` is 405 for unsupported methods, 400 for missing parameters, 404 for wrong credentials |
| **Security Check** | Password is **not** exposed in the user details response |
| **Data Consistency** | Email in the response matches the email sent in the request |

**Total: 14 requests · 91 assertions**

### Sample assertion

```javascript
let jsonData;

pm.test("Body is a valid JSON", function () {
    jsonData = JSON.parse(pm.response.text());
    pm.expect(jsonData).to.be.an("object");
});

pm.test("responseCode inside body is 200", function () {
    pm.expect(jsonData.responseCode).to.eql(200);
});

pm.test("Response time is less than 3000ms", function () {
    pm.expect(pm.response.responseTime).to.be.below(3000);
});
```

---

##  Dynamic Test Data (Pre-request Script)

The **Create/Register User Account** request generates a unique email and password before each run, so the test never fails because of an already registered user:

```javascript
const randomStr = Math.random().toString(36).substring(2, 8);
const timestamp = Date.now();

pm.collectionVariables.set("randomEmail", `user_${randomStr}_${timestamp}@gmail.com`);
pm.collectionVariables.set("randomPassword", `Pass@${Math.floor(Math.random() * 90000) + 10000}`);
```

### Collection Variables

| Variable | Description |
|---|---|
| `domain` | Base URL of the website: `https://automationexercise.com` |
| `randomEmail` | Auto-generated by the pre-request script |
| `randomPassword` | Auto-generated by the pre-request script |

---

##  How to Run

### Option 1: Postman App

1. Clone or download this repository.
2. Open **Postman** → **Import** → select the `.json` collection file.
3. Open the collection → **Variables** tab → set `domain` to `https://automationexercise.com`.
4. Click **Run collection** to execute all requests and view the test results.

### Option 2: Newman (CLI)

```bash
npm install -g newman

newman run API_Manual_Testing_Automation_Exercise_Website_APIs_Colletion_Postman_postman_collection.json \
  --env-var "domain=https://automationexercise.com"
```

>  **Tip:** Run the requests in folder order. The Create Account request (API 7) generates the credentials that the other User Account requests rely on.

---

##  Tools & Technologies

- **Postman**: API testing and collection runner
- **JavaScript (Chai / Postman `pm` API)**: test assertions
- **Newman**: command-line collection runner (optional)

---

## 📁 Repository Contents

```
├── API_Manual_Testing_Automation_Exercise_Website_APIs_Colletion_Postman_postman_collection.json
└── README.md
```

---

##  Author

**Ahmed Ayman**
Software QA Engineer | Manual, API & Automation Testing | Selenium & Rest Assured | ECPC Finalist | Competitive Programmer


---

