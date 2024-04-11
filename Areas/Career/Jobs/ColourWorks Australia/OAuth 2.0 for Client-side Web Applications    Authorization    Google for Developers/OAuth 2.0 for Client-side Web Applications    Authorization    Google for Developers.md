[https://developers.google.com/identity/protocols/oauth2/javascript-implicit-flow](https://developers.google.com/identity/protocols/oauth2/javascript-implicit-flow)

  

![[teal.png]]

This document explains how to implement OAuth 2.0 authorization to access Google APIs from a JavaScript web application. OAuth 2.0 allows users to share specific data with an application while keeping their usernames, passwords, and other information private. For example, an application can use OAuth 2.0 to obtain permission from users to store files in their Google Drives.

This OAuth 2.0 flow is called the _implicit grant flow_. It is designed for applications that access APIs only while the user is present at the application. These applications are not able to store confidential information.

In this flow, your app opens a Google URL that uses query parameters to identify your app and the type of API access that the app requires. You can open the URL in the current browser window or a popup. The user can authenticate with Google and grant the requested permissions. Google then redirects the user back to your app. The redirect includes an access token, which your app verifies and then uses to make API requests.

## Google APIs Client Library and Google Identity Services

If you use [Google APIs client library for JavaScript](https://developers.google.com/api-client-library/javascript) to make authorized calls to Google, you should use [Google Identity Services](https://developers.google.com/identity/oauth2/web/guides/overview) JavaScript library to handle the OAuth 2.0 flow. Please see Google identity Services' [token model](https://developers.google.com/identity/oauth2/web/guides/use-token-model), which is based upon the OAuth 2.0 _implicit grant_ flow.

**Note:** Given the security implications of getting the implementation correct, we strongly encourage you to use OAuth 2.0 libraries such as Google identity Services' [token model](https://developers.google.com/identity/oauth2/web/guides/use-token-model) when interacting with Google's OAuth 2.0 endpoints. It is a best practice to use well-debugged code provided by others, and it will help you protect yourself and your users.  
  
Rest of this page details how to interact with Google's OAuth 2.0 endpoints directly **without** using any OAuth 2.0 library.

## Prerequisites

### Enable APIs for your project

Any application that calls Google APIs needs to enable those APIs in the API Console.

To enable an API for your project:

1. [Open the API Library](https://console.developers.google.com/apis/library) in the Google API Console.
2. If prompted, select a project, or create a new one.
3. The API Library lists all available APIs, grouped by product family and popularity. If the API you want to enable isn't visible in the list, use search to find it, or click **View All** in the product family it belongs to.
4. Select the API you want to enable, then click the **Enable** button.
5. If prompted, enable billing.
6. If prompted, read and accept the API's Terms of Service.

### Create authorization credentials

Any application that uses OAuth 2.0 to access Google APIs must have authorization credentials that identify the application to Google's OAuth 2.0 server. The following steps explain how to create credentials for your project. Your applications can then use the credentials to access APIs that you have enabled for that project.

1. Go to the [Credentials page](https://console.developers.google.com/apis/credentials).
2. Click **Create credentials > OAuth client ID**.
3. Select the **Web application** application type.
4. Complete the form. Applications that use JavaScript to make authorized Google API requests must specify authorized **JavaScript origins**. The origins identify the domains from which your application can send requests to the OAuth 2.0 server. These origins must adhere to [Google’s validation rules](https://developers.google.com/identity/protocols/oauth2/javascript-implicit-flow#origin-validation).

### Identify access scopes

Scopes enable your application to only request access to the resources that it needs while also enabling users to control the amount of access that they grant to your application. Thus, there may be an inverse relationship between the number of scopes requested and the likelihood of obtaining user consent.

Before you start implementing OAuth 2.0 authorization, we recommend that you identify the scopes that your app will need permission to access.

The [OAuth 2.0 API Scopes](https://developers.google.com/identity/protocols/oauth2/scopes) document contains a full list of scopes that you might use to access Google APIs.

If your public application uses scopes that permit access to certain user data, it must complete a verification process. If you see **unverified app** on the screen when testing your application, you must submit a verification request to remove it. Find out more about [unverified apps](https://support.google.com/cloud/answer/7454865) and get answers to [frequently asked questions about app verification](https://support.google.com/cloud/answer/9110914) in the Help Center.

## Obtaining OAuth 2.0 access tokens

The following steps show how your application interacts with Google's OAuth 2.0 server to obtain a user's consent to perform an API request on the user's behalf. Your application must have that consent before it can execute a Google API request that requires user authorization.

### Step 1: Redirect to Google's OAuth 2.0 server

To request permission to access a user's data, redirect the user to Google's OAuth 2.0 server.

### Step 2: Google prompts user for consent

In this step, the user decides whether to grant your application the requested access. At this stage, Google displays a consent window that shows the name of your application and the Google API services that it is requesting permission to access with the user's authorization credentials and a summary of the scopes of access to be granted. The user can then consent to grant access to one or more scopes requested by your application or refuse the request.

Your application doesn't need to do anything at this stage as it waits for the response from Google's OAuth 2.0 server indicating whether any access was granted. That response is explained in the following step.

### Errors

Requests to Google's OAuth 2.0 authorization endpoint may display user-facing error messages instead of the expected authentication and authorization flows. Common error codes and suggested resolutions are listed below.

### `admin_policy_enforced`

The Google Account is unable to authorize one or more scopes requested due to the policies of their Google Workspace administrator. See the Google Workspace Admin help article [Control which third-party & internal apps access Google Workspace data](https://support.google.com/a/answer/7281227) for more information about how an administrator may restrict access to all scopes or sensitive and restricted scopes until access is explicitly granted to your OAuth client ID.

### `disallowed_useragent`

The authorization endpoint is displayed inside an embedded user-agent disallowed by Google's [OAuth 2.0 Policies](https://developers.google.com/identity/protocols/oauth2/policies#browsers).

### `org_internal`

The OAuth client ID in the request is part of a project limiting access to Google Accounts in a specific [Google Cloud Organization](https://cloud.google.com/resource-manager/docs/cloud-platform-resource-hierarchy#organizations). For more information about this configuration option see the [User type](https://support.google.com/cloud/answer/10311615#user-type) section in the Setting up your OAuth consent screen help article.

### `invalid_client`

The origin from which the request was made is not authorized for this client. See [`origin_mismatch`](https://developers.google.com/identity/protocols/oauth2/javascript-implicit-flow#authorization-errors-origin-mismatch).

### `invalid_grant`

When using [incremental authorization](https://developers.google.com/identity/protocols/oauth2/javascript-implicit-flow#incrementalAuth), the token may have expired or has been invalidated. Authenticate the user again and ask for user consent to obtain new tokens. If you are continuing to see this error, ensure that your application has been configured correctly and that you are using the correct tokens and parameters in your request. Otherwise, the user account may have been deleted or disabled.

### `origin_mismatch`

The scheme, domain, and/or port of the JavaScript originating the authorization request may not match an authorized JavaScript origin URI registered for the OAuth client ID. Review authorized JavaScript origins in the Google API Console [Credentials page](https://console.developers.google.com/apis/credentials).

### `redirect_uri_mismatch`

The `redirect_uri` passed in the authorization request does not match an authorized redirect URI for the OAuth client ID. Review authorized redirect URIs in the Google API Console [Credentials page](https://console.developers.google.com/apis/credentials).

The scheme, domain, and/or port of the JavaScript originating the authorization request may not match an authorized JavaScript origin URI registered for the OAuth client ID. Review authorized JavaScript origins in the Google API Console [Credentials page](https://console.developers.google.com/apis/credentials).

The `redirect_uri` parameter may refer to the OAuth out-of-band (OOB) flow that has been deprecated and is no longer supported. Refer to the [migration guide](https://developers.google.com/identity/protocols/oauth2/resources/oob-migration) to update your integration.

### `invalid_request`

There was something wrong with the request you made. This could be due to a number of reasons:

- The request was not properly formatted
- The request was missing required parameters
- The request uses an authorization method that Google doesn't support. Verify your OAuth integration uses a recommended integration method

### Step 3: Handle the OAuth 2.0 server response

## Calling Google APIs

## Complete example

## JavaScript origin validation rules

Google applies the following validation rules to JavaScript origins in order to help developers keep their applications secure. Your JavaScript origins must adhere to these rules. See [RFC 3986 section 3](https://tools.ietf.org/html/rfc3986#section-3) for the definition of domain, host and scheme, mentioned below.

|   |   |
|---|---|
|Validation rules||
|[Scheme](https://tools.ietf.org/html/rfc3986#section-3.1)|JavaScript origins must use the HTTPS scheme, not plain HTTP. Localhost URIs (including localhost IP address URIs) are exempt from this rule.|
|[Host](https://tools.ietf.org/html/rfc3986#section-3.2.2)|Hosts cannot be raw IP addresses. Localhost IP addresses are exempted from this rule.|
|[Domain](https://tools.ietf.org/html/rfc1034)|Host TLDs ([Top Level Domains](https://tools.ietf.org/id/draft-liman-tld-names-00.html)) must belong to the [public suffix list](https://publicsuffix.org/list/). Host domains cannot be `“googleusercontent.com”`. JavaScript origins cannot contain URL shortener domains (e.g. `goo.gl`) unless the app owns the domain.|
|[Userinfo](https://tools.ietf.org/html/rfc3986#section-3.2.1)|JavaScript origins cannot contain the userinfo subcomponent.|
|[Path](https://tools.ietf.org/html/rfc3986#section-3.3)|JavaScript origins cannot contain the path component.|
|[Query](https://tools.ietf.org/html/rfc3986#section-3.4)|JavaScript origins cannot contain the query component.|
|[Fragment](https://tools.ietf.org/html/rfc3986#section-3.5)|JavaScript origins cannot contain the fragment component.|
|Characters|JavaScript origins cannot contain certain characters including:  <br>•  <br>Wildcard characters (`'*'`)  <br>•  <br>Non-printable ASCII characters  <br>•  <br>Invalid percent encodings (any percent encoding that does not follow URL-encoding form of a percent sign followed by two hexadecimal digits)  <br>•  <br>Null characters (an encoded NULL character, e.g., `%00`, `%C0%80`)|

## Incremental authorization

In the OAuth 2.0 protocol, your app requests authorization to access resources, which are identified by scopes. It is considered a best user-experience practice to request authorization for resources at the time you need them. To enable that practice, Google's authorization server supports incremental authorization. This feature lets you request scopes as they are needed and, if the user grants permission for the new scope, returns an authorization code that may be exchanged for a token containing all scopes the user has granted the project.

For example, an app that lets people sample music tracks and create mixes might need very few resources at sign-in time, perhaps nothing more than the name of the person signing in. However, saving a completed mix would require access to their Google Drive. Most people would find it natural if they only were asked for access to their Google Drive at the time the app actually needed it.

In this case, at sign-in time the app might request the `openid` and `profile` scopes to perform basic sign-in, and then later request the `https://www.googleapis.com/auth/drive.file` scope at the time of the first request to save a mix.

The following rules apply to an access token obtained from an incremental authorization:

- The token can be used to access resources corresponding to any of the scopes rolled into the new, combined authorization.
- When you use the refresh token for the combined authorization to obtain an access token, the access token represents the combined authorization and can be used for any of the `scope` values included in the response.
- The combined authorization includes all scopes that the user granted to the API project even if the grants were requested from different clients. For example, if a user granted access to one scope using an application's desktop client and then granted another scope to the same application via a mobile client, the combined authorization would include both scopes.
- If you revoke a token that represents a combined authorization, access to all of that authorization's scopes on behalf of the associated user are revoked simultaneously.

**Caution:** choosing to include granted scopes will automatically add scopes previously granted by the user to your authorization request. A warning or error page may be displayed if your app is not currently approved to request all scopes that may be returned in the response. See [Unverified apps](https://support.google.com/cloud/answer/7454865) for more information.

The code samples below show how to add scopes to an existing access token. This approach allows your app to avoid having to manage multiple access tokens.

## Revoking a token

In some cases a user may wish to revoke access given to an application. A user can revoke access by visiting [Account Settings](https://myaccount.google.com/permissions). See the [Remove site or app access section of the Third-party sites & apps with access to your account](https://support.google.com/accounts/answer/3466521#remove-access) support document for more information.

It is also possible for an application to programmatically revoke the access given to it. Programmatic revocation is important in instances where a user unsubscribes, removes an application, or the API resources required by an app have significantly changed. In other words, part of the removal process can include an API request to ensure the permissions previously granted to the application are removed.

**Note:** Following a successful revocation response, it might take some time before the revocation has full effect.