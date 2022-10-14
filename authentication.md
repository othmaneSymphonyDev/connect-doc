# Authentication

**RSA public key registration**\
Prior to any calls, a customer needs to provide (at least) one pem-encoded public key, associated with a name that will identify this key on the Connect platform (WECHAT or WHATSAPP or SMS). These two pieces of information will enable the customer to generate a Java Web Token (JWT) required with all calls to the Connect platform API endpoints.\
For more information on this authentication mechanism and the key pair format, please refer to this documentation: https://developers.symphony.com/restapi/docs/rsa-bot-authentication-workflow#create-a-rsa-key-pair:\
For convenience, here is the sequence to create a RSA key pair:

```
cert_prefix="my-rsa-pair"
openssl genrsa -out "${cert_prefix}_privatekey.pem" 4096
openssl req -newkey rsa:$bitlength -x509 -key "${cert_prefix}_privatekey.pem" -out "${cert_prefix}_publickey.cer"
openssl pkcs8 -topk8 -nocrypt -in "${cert_prefix}_privatekey.pem" -out "${cert_prefix}_privatekey.pkcs8"
openssl x509 -pubkey -noout -in "${cert_prefix}_publickey.cer"  > "${cert_prefix}_publickey.pem"
```

For security reasons, the private key MUST NOT be shared. Symphony employees will not be asking for the private key.\
Should you need to revoke the key or should you have lost it, you can request its removal or replacement by opening a ticket with Symphony support.\
\
\
**API calls authentication**\
The JWT must be provided by the caller as a **Bearer Token** in the **Authorization** header of each HTTP request (https://swagger.io/docs/specification/authentication/bearer-authentication)

```
Authorization: Bearer <jwt token>
```

The Connect platform requires the JWT token to include this specific information:

````
  |                   | JWT claim name | Description |
  | ------------------| -------------- | ------------|
  | Subject           | sub            | The subject must follow format ces:customer:public_key_name where public_key_name is the name of the public key registered in Connect platform system|
  | Issued At         | iat            | The creation date of the token, following the RFC7519 format|
  | Expiration date   | exp            | The expiration date of the token, following the RFC7519 format. This must be at most equal to iat + 30 minutes.|
  | JWT ID            | jti            | A unique ID for your JWT (e.g., a random UUID)|


Here is a Java example using *io.jsonwebtoken:jjwt* library (https://github.com/jwtk/jjwt - Connect to preview). This library is a dependency of the Symphony SDK, meaning that if you are setup to work with Symphony APIs, you do not require any additional library.
```
public static String generate(PrivateKey privateKey, String publicKeyName) {
  return Jwts.builder()
    .setSubject("ces:customer:" + publicKeyName)
    .setId(UUID.randomUUID().toString())
    .setIssuedAt(Date.from(now.toInstant()))
    .setExpiration(Date.from(now.plusMinutes(30).toInstant()))
    .signWith(SignatureAlgorithm.RS512, privateKey)
    .compact();
}
```
Example of an API call using Curl
```
curl --location --request GET 'https://connect.dev.symphony.com/admin/api/v1/customer/permissions' \
                --header 'Authorization: Bearer eyJhbGciOiJSU....42sMd9soxkrnn7et44OM'
```
# Permissions
As of release 21.03, the available permissions are

  |    WECHAT PERMISSION          |   WHATSAPP PERMISSION       |  SMS PERMISSION            |  Description                 |
  |-------------------------------|-----------------------------|----------------------------|------------------------------|
  |  create:room                  |   create:room               |  create:room               | Required to create a room. It also allows the user to use the Rooms tab to manage rooms: create a room, deactivate a room, add members, remove members, transfer room ownership. If the user doesn’t have the create:room permission, the ‘New room’ button is not displayed. (All users have access to the Rooms tab, even if they do not have the create:room permission.) |
  |  create:contact               |   create:contact            |  create:contact            | Required to create a contact via the Contacts tab. If the user doesn’t have the create:contact permission, the ‘New contact’ button is not displayed on the Contacts tab. (All users have access to the Contacts tab, even if they do not have the create:contact permission.) Users without the create:contact permission will have contacts created each time they are added to rooms with new contacts. Also required (along with Admin:list-customers and on-behalf:onboard) to add new advisor connections to a client from the Admin tab. |
  |  on-behalf:onboard            |   on-behalf:onboard         |  on-behalf:onboard         | Enables the user to onboard contacts on behalf of other users. Also required (along with Admin:list-customers) to add a connection to a contact via the Admin tab. |
  |  -                            |   edit:contact              |  edit:contact              | Required to edit a contact via the Contacts tab. If the user doesn’t have the edit:contact permission, the ‘Edit contact’ option is not displayed in the Contacts tab. A contact’s first and last name cannot be edited if the contact has already been added to WhatsApp Connect by another user. Company name cannot be edited if the contact is already a participant in a chat room. Email address and telephone number cannot be edited. Also required (along with Admin:list-customers and on-behalf:onboard) to edit client contact details from the Admin tab. |
  |  -                            |   delete:contact            |  delete:contact            | Required to delete a contact via the Contacts tab. If the user doesn’t have the delete:contact permission, the ‘Remove contact’ option is not displayed in the Contact tab. This permission is not required to delete a contact in the Admin tab. |
  |  on-behalf:simple-offboard    |   on-behalf:simple-offboard |  on-behalf:simple-offboard | Required to off-board a contact or to remove a connection from a user via the Admin tab (along with Admin:list-customers). This permission is *not* required to remove a contact via the Contact tab. |
  |  admin:list-customers         |   admin:list-customers      |  admin:list-customers      | Gives the user access to the Admin tab, which displays all contacts onboarded for the current tenant and allows the user to add a contact to an existing room, view rooms a contact is part of, and view a given contact’s advisor connections. Additional permissions required for additional functionalities on the Admin tab:<ul><li> To offboard a contact or remove a connection from an advisor via the Admin tab, the user requires the On-behalf:simple-offboard permission in addition to the Admin:list-customers.<li> To create new contacts on behalf of advisors from the Admin tab, the user requires both the Create:contact and the On-behalf:onboard permissions.<li> To edit contact details, the user requires both Edit:contact and On-behalf:onboard</ul>|
  |  add:multi-company-contact    |   add:multi-company-contact |  add:multi-company-contact | Allows the user to participate in a room with contacts from different companies. Requires create:room permission. |

**Notes:**\
  <ul>
    <li> By default, when an Advisor is entitled, they are assigned the following permissions:  create:room , create:contact, edit:contact, delete:contact, on-behalf:onboard.</li>
    <li> for Multi-company-contacts, the contacts must be from the same network, e.g. all from WeChat or all from WhatsApp. Cross network rooms are not authorized.</li>
  </ul>

# APIs path
For all the APIs, CONNECT-DOMAIN refers to the domain name for the WECHAT or WHATSAPP or SMS platforms.\
Production CONNECT-DOMAIN:
<ul>
    <li>WECHAT:     https://connect.symphony.com/</li>
    <li>WHATSAPP:   https://connect.symphony.com/</li>
    <li>SMS:        https://connect.symphony.com/</li>
</ul>

for reference here are the TEST CONNECT-Domain:
<ul>
    <li>WECHAT:     https://connect.uat.symphony.com/</li>
    <li>WHATSAPP:   https://connect.uat.symphony.com/</li>
    <li>SMS:        https://connect.uat.symphony.com/</li>
</ul>

In addition, the following should be added after the domain:
<ul>
    <li> For WECHAT:   /admin </li>
    <li> For WHATSAPP: /admin </li>
    <li> For SMS:      /admin </li>
    <li> For example, the request to get the list of available entitlements would be (headers have been omitted in this example): </li>
    <ul>
        <li>WECHAT:    curl -X GET "https://connect.symphony.com/admin/api/v2/customer/entitlements" </li>
        <li>WHATSAPP:  curl -X GET "https://connect.symphony.com/admin/api/v2/customer/entitlements" </li>
        <li>SMS:       curl -X GET "https://connect.symphony.com/admin/api/v2/customer/entitlements" </li>
    </ul>
</ul>

# Contact profile limitation
<ul>
    <li>For a given phone number, the email address of a contact across all tenants must be the same.<br />
        This means that if Tenant onboards a contact with phone number +86 1234567890 and email address contactABC@someDomain.tld (it's the 1st time this contact is onboarded on the platform), when another tenant wishes to onboard a contact with phone number +86 1234567890 the email to be provided must also be contactABC@someDomain.tld or an error will be returned (usually 409: \"Conflict\" or \"Contact already exists\" / \"Contact already onboarded)\"</li>
    <li>The firstName and lastName entered for that contact (+86 1234567890) must also match the existing contact on record for a </li>
    <li>A contact's company name has a maximum length of 25 characters</li>
</ul>
````
