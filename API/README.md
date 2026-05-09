___________
# Lab: Exploiting an API endpoint using documentation

- In this exercise we will execute an attack throught the **API** which accepts differents **HTTP request methods** 

    Let's use **Burp Suite** to capture the request and analyze it

![[Captura de pantalla 2026-04-28 205826 1.png]]

- API is using the **PATCH** **HTTP request method** , as our goal is delete the user **Carlos** is the moment to replace the **HTTP request method** for **DELETE** and see if accept it  

![[Captura de pantalla 2026-04-28 205633.png]]

![[Captura de pantalla 2026-04-28 205539.png]]

- We see **API** accepts the **DELETE** **HTTP request method** , User **Carlos** is deleted

![[Captura de pantalla 2026-04-28 205331.png]]

## Key points

### 1
It's a bad practice which involves Web sites security severely when API accepts multiple **HTTP request methods** without any kind of sanitization allowing non-authorized users execute unauthorized actions.

____________

# Lab: Exploiting server-side parameter pollution in a query string

- In this exercise we are executing a **parameter pollution attack** apply to the **API** which embeds user input to the internal API without adequate encoding. The objective of the exercise is to delete the user Carlos once logged in as the administrator user

     Let's use **Burpsuite** to analyze requests 

![[Captura de pantalla 2026-05-03 215538.png]]
      Forgot password request

 - There is a **Js file** associated to the **forgot Password functionality**, let's review it

![[Captura de pantalla 2026-05-03 215039.png]]

- We have found a **hidden parameter** which is crucial in the **API web security**, let's make a note of this due to in the right hands this **'simple data'** can be critical

- As pentesters understand how to execute a **parameter pollution attack** step by step is the key, initially we need to test the **API** making sure a second parameter is accepted and for this we can use the **& character** (it's crutial to URL encode it) 

![[Captura de pantalla 2026-05-03 220844.png]]

-  First condition fulfilled, now we need to proceed Truncating query strings by using the **# character** (It's necessary to URL-encode it, Otherwise the front-end application will interpret it as a fragment identifier and it won't be passed to the internal API).

      The aim of this step is to get all the clues there are in the system responses 

![[Captura de pantalla 2026-05-03 225937.png]]

- We receive a **"Field not specified"** message, it's a clue. System is telling us the **Field parameter** exists. At this point we need to list values leveraging the **Field parameter**

![[Captura de pantalla 2026-05-03 230618.png]]
      System accepted the **Field parameter** but it's reporting **"Invalid field"**

- Let's use the **Intruder feature** to find a valid field 

![[Captura de pantalla 2026-05-03 231154.png]]

- We got found the **valid fields** **email** and **username**, if we try these ones system will accept them however is not listing information matter to us however as we mentioned before we already found a **hidden parameter** 

```
reset_token
```
      Let's add it 

![[Captura de pantalla 2026-05-03 231822.png]]

- System is responding with a **200 status code** reporting us a **reset password token** of user **administrator** , let's add it as a parameter in the browser.

![[Captura de pantalla 2026-05-03 232344.png]]
      we got the option to reset the password

![[Captura de pantalla 2026-05-03 232452.png]]

- Now as the administrator let's delete the user **Carlos**

![[Captura de pantalla 2026-05-03 232640.png]]

![[Captura de pantalla 2026-05-03 232718.png]]

## Key points

### 1
In real-world environments, this attack highlights the critical risk of "blind trust" between a **Front-end** and its internal **Back-end** services. When an application takes user input and concatenates it directly into internal API queries without proper encoding, it allows an attacker to manipulate the server's logic. By injecting URL-encoded characters like `%26` (&) and `%23` (#), an attacker isn't just sending data—they are **rewriting the query’s structure** to bypass security controls and extract sensitive information, such as administrative reset tokens.

To prevent this, organizations must move beyond "security through obscurity" (like hiding parameters in JavaScript files), as professional **reconnaissance** will always uncover them. The effective defense is implementing **strict input validation** and using **allow-lists** on the internal API. Every piece of user-supplied data must be properly sanitized or parameterized before being passed between services to ensure that special characters are treated as literal text rather than executable commands or delimiters.

________________

# Lab: Finding and exploiting an unused API endpoint

- In this exercise we will execute an attack which points to an **API endpoint** in order to modify an item price and acquire it.

    As pentesters regarding the **API subject** it's crucial the importance of the **endpoints** and these ones work with the different types of **HTTP requests methods**

- Portswigger provide us credentials and a specific item which is vulnerable


![[Captura de pantalla 2026-05-04 192735 1.png]]
     main page to place our order 

- Let's capture the request with **Burpsuite** 

```
/api/products/1/price
```

![[Captura de pantalla 2026-05-04 191207.png]]

- We have found an **API endpoint** with a **GET HTTP request method**  let's test it changing the **HTTP request nethod** 

![[Captura de pantalla 2026-05-04 191721.png]]

-  Important clue, is letting us know it does not accept **POST** but it accepts **GET** and **PATCH**, let's change it to **PATCH**

![[Captura de pantalla 2026-05-04 191959.png]]

```
"error":"Only 'application/json' Content-Type is supported"
```

- We have another clue, now we know it supports **only json'application/json' Content-Type**, let's add the price parameter with that specific format. 

![[Captura de pantalla 2026-05-04 192630.png]]

- It updates the Item price, we can use this in our favor due to our **store credit is 0** 

![[Captura de pantalla 2026-05-04 192929.png]]




![[Captura de pantalla 2026-05-04 193127.png]]


![[Captura de pantalla 2026-05-04 193232.png]]

- Price changed successfully now let's add the product with the new price 

![[Captura de pantalla 2026-05-04 193352.png]]

- let's place the order

![[Captura de pantalla 2026-05-04 193514.png]]

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
     
![[Captura de pantalla 2026-05-05 000020.png]]

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
     
![[Captura de pantalla 2026-05-05 000236.png]]

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

![[Captura de pantalla 2026-05-05 000129.png]]

```
"chosen_discount": {
        "percentage": 100
   },
```

- System report us a confirmation after applied a 100% of discount in this item by modifying the **chosen discount** parameter 

![[Captura de pantalla 2026-05-04 235924.png]]

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

![[Captura de pantalla 2026-05-07 121001.png]]

![[Captura de pantalla 2026-05-07 121041.png]]

- Let's now analyze another request we have in **BurpSuite** which correspond to the **forgot password feature**

     Initially we have the **username parameter** 

- Let's execute a **pollution attack** in order to acquire information about it

![[Captura de pantalla 2026-05-07 120453.png]]

- If we try with a **question mark** system indicate us an **invalid route**, thanks to this we can try with a **path traversal attack** to obtain more information.

![[Captura de pantalla 2026-05-07 120549.png]]

![[Captura de pantalla 2026-05-07 120658.png]]

![[Captura de pantalla 2026-05-07 120733.png]]

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

![[Captura de pantalla 2026-05-07 121219.png]]

![[Captura de pantalla 2026-05-07 121300.png]]

- Listing the **/openapi.json**, system report us an important endpoint which refer an ***username*** and a **field parameter**

```
../../../../../api/internal/v1/users/{username}/field/{field}?
```

![[Captura de pantalla 2026-05-07 121416.png]]

- Let's start listing the **administrator username**

![[Captura de pantalla 2026-05-07 121510.png]]

- adding the **field** **username** system is reporting us the **username content**, with this fact now we can list the **hidden parameter** we have found initially 

```
../../../../../api/internal/v1/users/administrator/field/passwordResetToken?
```

![[Captura de pantalla 2026-05-07 132716.png]]

- System is reporting a **passwordResetToken**, to be specific from the **administrator user** . We can use this information to access as **administrator** changing his password

```
/forgot-password?passwordResetToken=8hs6xpi8ne2huervm20ityody2d7xul3
```

![[Captura de pantalla 2026-05-07 133135.png]]

![[Captura de pantalla 2026-05-07 134032.png]]

- Perfect, logged in as **administrator** we just need to delete the **user Carlos**

![[Captura de pantalla 2026-05-07 134058.png]]

![[Captura de pantalla 2026-05-07 134117.png]]

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