## Folders:

### auth0_pages

This folder contains pages that should be used in auth0.

They overwrite the default screens to make them look like they are from o2.

#### login.html

This is the main screen with login.html. User enters their username and password from Auth0 and they are logged in to
Tableau.

#### password_reset.html

This screen is showed to user after they request password reset from the login.html. They get to this screen by clicking
a link sent to their email address.

#### third_party_cookies.html

Because embedded tableau doesn't work without third party cookies enabled, this website is used as a 'fallback'.

It accepts user's email to know what dashboard it should redirect the user to. For example: user with email
matej.frnka@billigence.com will be redirected to
dashboard https://dub01.online.tableau.com/t/tableau_site_name/views/matej_frnkabilligence_com/matej_frnkabilligence_com?:origin=card_share_link&:embed=y&:tabs=no&:toolbar=no&:tooltip=no

Notice the /views/matej_frnkabilligence_com/matej_frnkabilligence_com - this is the name of the dashboard and view (they
have the same name). When creating this in tableau, users have to make sure they call the dashboard and view '
matej.frnka@billigence.com' to make it match this url.

The email will not be needed anymore if you use row level security because all users will be redirected to the same
dashboard

### server_pages

These pages need to be hosted on the server. I am using github pages here to host them online. See
here: https://matejfrnka.github.io/test-gh-pages/server_pages/index.html

#### index.html

This is the main page that should be displayed to any user coming to the application. It is very simple, the only thing
it contains is a Tableau embedding.

If you are using row level security, this tableau embedding should be the "main" dashboard. The row level security will
then determine what the user should see.

If you are not using row level security, this tableau embedding needs to take a look at the user's email and then
redirect to another tableau embedding (same way the third_party_cookies.html would, so user matej.frnka@billigence.com
would be redirected to .../views/matej_frnkabilligence_com/matej_frnkabilligence_com?...)

Because the embedding doesn't look good by itself, there is another page called **embedding.html**, which takes a query
parameter user: www....../embedding?**user=matej.frnka@billigence.com** and embeds the new tableau dashboard for the
given user nicely.

## Setting up tableau to work with auth0

I had problems with this and I had to do it mulitple times, so I recorded a video of me doing it for my future self.
Sorry for the background music: https://drive.google.com/file/d/1jh_UZmeX4SEfggVfYTZW3egQ9GehrBun/view?usp=sharing

Here is also a article I found that was useful: https://alexeskinasy.medium.com/configuring-auth0-as-sso-idp-for-tableau-115707ca1d0c

And here is a snippet of Auth0 settings I used:
```json
{
  "audience": "https://sso.online.tableau.com/public/sp/metadata/ae95f1a7-0aa1-4031-9fa4-43bea02e6022",
  "recipient": "https://sso.online.tableau.com/public/sp/SSO/ae95f1a7-0aa1-4031-9fa4-43bea02e6022",
  "mappings": {
    "email": "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress"
  },
  "createUpnClaim": false,
  "passthroughClaimsWithNoMapping": false,
  "mapUnknownClaimsAsIs": false,
  "mapIdentities": true,
  "signatureAlgorithm": "rsa-sha256",
  "digestAlgorithm": "sha256",
  "destination": "https://sso.online.tableau.com/public/sp/SSO/ae95f1a7-0aa1-4031-9fa4-43bea02e6022",
  "lifetimeInSeconds": 3600,
  "signResponse": false,
  "nameIdentifierFormat": "urn:oasis:names:tc:SAML:2.0:attrname-format:basic",
  "nameIdentifierProbes": [
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name"
  ],
  "logout": {
    "callback": "https://dev-5hcubzu1pohinlc8.eu.auth0.com/v2/logout"
  },
  "binding": "urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect"
}
```

Callback url should be the same as recipient 

