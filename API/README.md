___________
# Lab: Exploiting an API endpoint using documentation

- In this exercise we will execute an attack throught the **API** which accepts differents **HTTP request methods** 

    Let's use **Burp Suite** to capture the request and analyze it

<img width="624" height="302" alt="Captura de pantalla 2026-04-28 205826 1" src="https://github.com/user-attachments/assets/33a65c8b-f923-452f-98bd-5c7543ac5e70" />

- API is using the **PATCH** **HTTP request method** , as our goal is delete the user **Carlos** is the moment to replace the **HTTP request method** for **DELETE** and see if accept it  

<img width="230" height="20" alt="Captura de pantalla 2026-04-28 205633" src="https://github.com/user-attachments/assets/3c56b2ae-986e-41a9-9298-94d1bb97e8af" />

<img width="1307" height="308" alt="Captura de pantalla 2026-04-28 205539" src="https://github.com/user-attachments/assets/e3470158-3e2a-4727-af37-8c39b66fec0b" />

- We see **API** accepts the **DELETE** **HTTP request method** , User **Carlos** is deleted

<img width="1217" height="264" alt="Captura de pantalla 2026-04-28 205331" src="https://github.com/user-attachments/assets/e4f14139-3fb8-4f16-98bf-12efc887fbc7" />

## Key points

### 1
It's a bad practice which involves Web sites security severely when API accepts multiple **HTTP request methods** without any kind of sanitization allowing non-authorized users execute unauthorized actions.

____________

# Lab: Exploiting server-side parameter pollution in a query string

- In this exercise we are executing a **parameter pollution attack** apply to the **API** which embeds user input to the internal API without adequate encoding. The objective of the exercise is to delete the user Carlos once logged in as the administrator user

     Let's use **Burpsuite** to analyze requests 

<img width="437" height="96" alt="Captura de pantalla 2026-05-03 215538" src="https://github.com/user-attachments/assets/5fd95c15-d905-4f1d-85a0-ccb374961c77" />
     Forgot password request

 - There is a **Js file** associated to the **forgot Password functionality**, let's review it

<img width="1038" height="727" alt="Captura de pantalla 2026-05-03 215039" src="https://github.com/user-attachments/assets/44e9d792-38b6-4001-a7ea-6dd4f9c99c57" />

- We have found a **hidden parameter** which is crucial in the **API web security**, let's make a note of this due to in the right hands this **'simple data'** can be critical

- As pentesters understand how to execute a **parameter pollution attack** step by step is the key, initially we need to test the **API** making sure a second parameter is accepted and for this we can use the **& character** (it's crutial to URL encode it) 

<img width="1273" height="270" alt="Captura de pantalla 2026-05-03 220844" src="https://github.com/user-attachments/assets/ec2acedb-3a65-467e-b659-4cf67e0be2b3" />

-  First condition fulfilled, now we need to proceed Truncating query strings by using the **# character** (It's necessary to URL-encode it, Otherwise the front-end application will interpret it as a fragment identifier and it won't be passed to the internal API).

      The aim of this step is to get all the clues there are in the system responses 

<img width="1300" height="281" alt="Captura de pantalla 2026-05-03 225937" src="https://github.com/user-attachments/assets/5db7a43f-47a8-4902-ad65-9f7dc9839b1c" />

- We receive a **"Field not specified"** message, it's a clue. System is telling us the **Field parameter** exists. At this point we need to list values leveraging the **Field parameter**

<img width="1281" height="265" alt="Captura de pantalla 2026-05-03 230618" src="https://github.com/user-attachments/assets/d821e151-7dea-45fa-8351-64fcbfc6288e" />
      System accepted the **Field parameter** but it's reporting **"Invalid field"**

- Let's use the **Intruder feature** to find a valid field 

<img width="1176" height="80" alt="Captura de pantalla 2026-05-03 231154" src="https://github.com/user-attachments/assets/c6e8b2fa-9357-47c2-905d-3f2cbe78e910" />

- We got found the **valid fields** **email** and **username**, if we try these ones system will accept them however is not listing information matter to us however as we mentioned before we already found a **hidden parameter** 

```
reset_token
```
      Let's add it 

<img width="1291" height="262" alt="Captura de pantalla 2026-05-03 231822" src="https://github.com/user-attachments/assets/badec6fd-7a7d-4470-a777-448332f3d323" />

- System is responding with a **200 status code** reporting us a **reset password token** of user **administrator** , let's add it as a parameter in the browser.

<img width="1074" height="535" alt="Captura de pantalla 2026-05-03 232344" src="https://github.com/user-attachments/assets/e606ef61-27a9-4682-9fe6-5c5c5db14494" />
     we got the option to reset the password

<img width="1230" height="553" alt="Captura de pantalla 2026-05-03 232452" src="https://github.com/user-attachments/assets/e47c5f61-c16f-467d-a46b-89aeeb241f6c" />

- Now as the administrator let's delete the user **Carlos**

<img width="1196" height="210" alt="Captura de pantalla 2026-05-03 232640" src="https://github.com/user-attachments/assets/78d9f6fc-f2dd-498f-a5ab-cd0c239b68af" />

<img width="1242" height="297" alt="Captura de pantalla 2026-05-03 232718" src="https://github.com/user-attachments/assets/5b025bc2-8388-4762-bc02-3c5e56bddd9a" />

## Key points

### 1
In real-world environments, this attack highlights the critical risk of "blind trust" between a **Front-end** and its internal **Back-end** services. When an application takes user input and concatenates it directly into internal API queries without proper encoding, it allows an attacker to manipulate the server's logic. By injecting URL-encoded characters like `%26` (&) and `%23` (#), an attacker isn't just sending data—they are **rewriting the query’s structure** to bypass security controls and extract sensitive information, such as administrative reset tokens.

To prevent this, organizations must move beyond "security through obscurity" (like hiding parameters in JavaScript files), as professional **reconnaissance** will always uncover them. The effective defense is implementing **strict input validation** and using **allow-lists** on the internal API. Every piece of user-supplied data must be properly sanitized or parameterized before being passed between services to ensure that special characters are treated as literal text rather than executable commands or delimiters.

________________

# Lab: Finding and exploiting an unused API endpoint

- In this exercise we will execute an attack which points to an **API endpoint** in order to modify an item price and acquire it.

    As pentesters regarding the **API subject** it's crucial the importance of the **endpoints** and these ones work with the different types of **HTTP requests methods**

- Portswigger provide us credentials and a specific item which is vulnerable


<img width="1184" height="744" alt="Captura de pantalla 2026-05-04 192735 1" src="https://github.com/user-attachments/assets/db5bd070-8cc7-4b7a-8745-a84fe30861da" />
     main page to place our order 

- Let's capture the request with **Burpsuite** 

```
/api/products/1/price
```

<img width="1585" height="196" alt="Captura de pantalla 2026-05-04 191207" src="https://github.com/user-attachments/assets/72ceb0a7-293b-49a5-baa8-c825a3ebc3d2" />

- We have found an **API endpoint** with a **GET HTTP request method**  let's test it changing the **HTTP request nethod** 

<img width="1283" height="191" alt="Captura de pantalla 2026-05-04 191721" src="https://github.com/user-attachments/assets/d361e188-ff4f-4a42-b00b-8bbf9be6de95" />

-  Important clue, is letting us know it does not accept **POST** but it accepts **GET** and **PATCH**, let's change it to **PATCH**

<img width="1437" height="145" alt="Captura de pantalla 2026-05-04 191959" src="https://github.com/user-attachments/assets/5eeb3a74-0021-49f7-a039-46efb9a2dd9f" />

```
"error":"Only 'application/json' Content-Type is supported"
```

- We have another clue, now we know it supports **only json'application/json' Content-Type**, let's add the price parameter with that specific format. 

<img width="1278" height="285" alt="Captura de pantalla 2026-05-04 192630" src="https://github.com/user-attachments/assets/5dc0ffde-c2dd-426a-878f-076afc46fefe" />

- It updates the Item price, we can use this in our favor due to our **store credit is 0** 

<img width="1214" height="593" alt="Captura de pantalla 2026-05-04 192929" src="https://github.com/user-attachments/assets/5d3074f0-8298-4848-be24-a1ae5e98d36b" />





<img width="1283" height="284" alt="Captura de pantalla 2026-05-04 193127" src="https://github.com/user-attachments/assets/f5b62279-20b3-4913-86c8-3d3b43bb82ce" />

<img width="419" height="148" alt="Captura de pantalla 2026-05-04 193232" src="https://github.com/user-attachments/assets/8c1023e3-9614-40c3-91fe-953d02d36bd9" />

- Price changed successfully now let's add the product with the new price 

<img width="1058" height="691" alt="Captura de pantalla 2026-05-04 193352" src="https://github.com/user-attachments/assets/898b1123-1a0e-42fb-b78b-dd30fa1381d6" />

- let's place the order

<img width="1082" height="433" alt="Captura de pantalla 2026-05-04 193514" src="https://github.com/user-attachments/assets/34b529e3-5853-4e5c-8e3c-25ddd8948366" />

- It worked successfully 

> This exercise taught us how a vague sanitization by simply changing the **HTTP request method** can trigger unauthorized actions besides of the fact of left an **API endpoint** which is not used exposed but can unchain critical actions 

___________
# Lab: Exploiting a mass assignment vulnerability

- In this exercise we will execute a **Mass assignment attack** in order to acquire an item without credits to do it

     **Mass assignment attack** consists basically when request parameters are binding to fields on an internal object without adequate validation and sanitization

- Let's start recognizing the **API** in the **Burp Suite request**

```
/api/checkout
```

 - Initially this API work with two **HTTP requests methods**

     **GET**
     
<img width="1224" height="275" alt="Captura de pantalla 2026-05-05 000020" src="https://github.com/user-attachments/assets/10f99f41-de7a-476f-9489-819898d580c0" />

```
{: 
   "chosen_discount": {
        "percentage": 0
   },: 
   "chosen_products": [
        {: 
            "product_id": "1",
            "name": "Lightweight \"l33t\" Leather Jacket",
            "quantity": 2,
            "item_price": 0
         }: 
   ]: 
}: 
```

      POST
     
<img width="1068" height="364" alt="Captura de pantalla 2026-05-05 000236" src="https://github.com/user-attachments/assets/51dcb076-4a3a-4511-b3e4-99f44d223b10" />

```
{
       "chosen_products":[ 
            {
                 "product_id":"1",
                 "quantity":1
            }
        ]
}
```

- There is a **chosen discount**  parameter in **json format** which is only in the **GET method request** we can try create a new parameter copying this **chosen discount** parameter to the **POST method request** and confirm if this one is bound to on an **internal object**

<img width="1192" height="405" alt="Captura de pantalla 2026-05-05 000129" src="https://github.com/user-attachments/assets/6c1a7bed-0bed-4659-955f-d250b2b5e79c" />

```
"chosen_discount": {
        "percentage": 100
   },
```

- System report us a confirmation after applied a 100% of discount in this item by modifying the **chosen discount** parameter 

<img width="1254" height="200" alt="Captura de pantalla 2026-05-04 235924" src="https://github.com/user-attachments/assets/721b5bab-f632-4d43-9759-c7e5da1d4d43" />

## Key points

## 1
This exercise highlights how properties such as "chosen discount" or "item price" should have a read-only permission in the client side and it should be managed only by the server side, common mistake produced by the **backend** accepting external parameters and embeding them as part of a internal object without any kind of sanitization in order to prevent **Mass assignment attacks**

__________________
# Lab: Exploiting server-side parameter pollution in a REST URL

 - In this exercise we are executing a parameter pollution attack in a path in the server side in order to log in as the **administrator user** and delete the **username Carlos**

- Initially we capture a request with **Burpsuite** which response report a **URL route** 

```
/static/js/forgotPassword.js
```

      If we add this route in the browser to the original URL we have found a interesting parameter '/forgot-password?passwordResetToken=' which will help us in the future  

<img width="916" height="72" alt="Captura de pantalla 2026-05-07 121001" src="https://github.com/user-attachments/assets/b5da0e3e-6d56-46e6-9432-e13ac9ed92f2" />

<img width="646" height="227" alt="Captura de pantalla 2026-05-07 121041" src="https://github.com/user-attachments/assets/ac6a40e4-f84b-4eb1-b3d4-b9555569ce42" />

- Let's now analyze another request we have in **BurpSuite** which correspond to the **forgot password feature**

     Initially we have the **username parameter** 

- Let's execute a **pollution attack** in order to acquire information about it

<img width="1413" height="179" alt="Captura de pantalla 2026-05-07 120453" src="https://github.com/user-attachments/assets/4e1ea82a-e8af-435c-a017-7e2d648ca0ae" />

- If we try with a **question mark** system indicate us an **invalid route**, thanks to this we can try with a **path traversal attack** to obtain more information.

<img width="496" height="31" alt="Captura de pantalla 2026-05-07 120549" src="https://github.com/user-attachments/assets/fbccc7da-8b75-4caf-9632-f7b07a47a28c" />

<img width="1869" height="204" alt="Captura de pantalla 2026-05-07 120658" src="https://github.com/user-attachments/assets/b7b0bf34-380c-4397-8c1c-56ef81f8aa45" />

<img width="923" height="159" alt="Captura de pantalla 2026-05-07 120733" src="https://github.com/user-attachments/assets/a166743f-861e-4935-a312-d25e764d7b43" />

- After multiple **../** we obtain an important message 

```
Not Found
```

- Due to this message we deduce we are aiming internal files but in this specific case we are aiming a non existing file thats why we will list some **end points** regarding the most **generic API's documentation** like the next ones:

```
- /api
- /swagger/index.html
- /openapi.json
```

<img width="655" height="285" alt="Captura de pantalla 2026-05-07 121219" src="https://github.com/user-attachments/assets/525ad2d7-90b2-47e6-9019-e6fcc0f6383c" />

<img width="928" height="204" alt="Captura de pantalla 2026-05-07 121300" src="https://github.com/user-attachments/assets/8224a34e-3ca1-4b88-8a8d-1ef44df33de8" />

- Listing the **/openapi.json**, system report us an important endpoint which refer an ***username*** and a **field parameter**

```
../../../../../api/internal/v1/users/{username}/field/{field}?
```

<img width="1606" height="262" alt="Captura de pantalla 2026-05-07 121416" src="https://github.com/user-attachments/assets/de52db5c-423f-44d8-b574-6fc404b0934c" />

- Let's start listing the **administrator username**

<img width="1154" height="179" alt="Captura de pantalla 2026-05-07 121510" src="https://github.com/user-attachments/assets/9b6ecf4d-2d37-4c6d-ae47-fba3cb8e3405" />

- adding the **field** **username** system is reporting us the **username content**, with this fact now we can list the **hidden parameter** we have found initially 

```
../../../../../api/internal/v1/users/administrator/field/passwordResetToken?
```

<img width="1294" height="285" alt="Captura de pantalla 2026-05-07 132716" src="https://github.com/user-attachments/assets/3a04775a-1845-4efa-a750-2507c9cc5635" />

- System is reporting a **passwordResetToken**, to be specific from the **administrator user** . We can use this information to access as **administrator** changing his password

```
/forgot-password?passwordResetToken=8hs6xpi8ne2huervm20ityody2d7xul3
```

<img width="1109" height="531" alt="Captura de pantalla 2026-05-07 133135" src="https://github.com/user-attachments/assets/599671de-2c21-4cd6-9958-8b51b7f570e2" />

<img width="1227" height="569" alt="Captura de pantalla 2026-05-07 134032" src="https://github.com/user-attachments/assets/b78d9f7c-014e-43fb-b1d8-6759c5c41429" />

- Perfect, logged in as **administrator** we just need to delete the **user Carlos**

<img width="1186" height="182" alt="Captura de pantalla 2026-05-07 134058" src="https://github.com/user-attachments/assets/b09334d7-6b3a-44b0-b37e-e03fedc927b7" />

<img width="1237" height="294" alt="Captura de pantalla 2026-05-07 134117" src="https://github.com/user-attachments/assets/dab2ee36-2a3b-4038-88c0-82f5182c8d7d" />

- Exercise solved

## Key points

## 1
This exercise highlight how a **pollution attack** can reveal crucial information, acting as a leak which it's become in a guidance for attackers to aim attacks showing the effects of a vague sanitization

## 2
This exercise teach us how a **generic API endpoint** as **/openapi.json** can reveal **API documentation** which help attacker to execute attacks, in this case reveals the end point

 ``../../../../../api/internal/v1/users/{username}/field/{field}?``

Which allow us to list internal files and  crucial information as we have shown in the development of this exercise 

__________________











```
/forgot-password?passwordResetToken=8hs6xpi8ne2huervm20ityody2d7xul3
```

[burp-payloads/Server-side variable names.pay at master · antichown/burp-payloads · GitHub](https://github.com/antichown/burp-payloads/blob/master/Server-side%20variable%20names.pay)
