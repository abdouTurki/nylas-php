<div align="center">
  <img width="635" height="217" src="media/nylas-php.png" />
</div>

# Nylas PHP SDK

PHP bindings for the Nylas REST API (V2.0). https://docs.nylas.com/reference</br>

**What's new?**</br>

1. API 2.0 support</br>
2. All Nylas APIs have been implemented within this SDK.</br>
3. Chained calls and good code hints, easy to use</br>
4. Support send & get message in raw type</br>
5. Support async batch upload & download</br>
   -- Contact picture download</br>
   -- File upload & download<br>
6. The parameters that required by methods almost the same as nylas official api required.</br>
7. Support async batch get & delete & send (since version 3.1).

`(new \Nylas\Client($options))->`![apis](media/apis.png)</br>


## Installation

This library is available on https://packagist.org/packages/lanlin/nylas-php</br>
You can install it by running

```shell
composer require lanlin/nylas-php
```


## Usage

### App ID and Secret

Before you can interact with the Nylas REST API,</br>
you need to create a Nylas developer account at [https://www.nylas.com/](https://www.nylas.com/).</br>
After you've created a developer account, you can create a new application to generate an App ID / Secret pair.</br>

Generally, you should store your App ID and Secret into environment variables to avoid adding them to source control.</br>
The test projects use configuration files instead, to make it easier to get started.</br>

### Init Nylas-PHP

```php
use Nylas\Client;

$options =
[
    'debug'            => true,
    'log_file'         => dirname(__FILE__) . '/test.log',
    'account_id'       => 'your account id',
    'access_token'     => 'your access token',
    'client_id'        => 'your client id',        // required
    'client_secret'    => 'your client secret'     // required
    'off_decode_error' => false,                   // disable json_decode error or not, default enable.
];

$nylas = new Client($options);
```

### Options Setting

You can modify options with these methods:

$nylas->Options()-></br>
![option](media/option.png)</br>


### Batch Request

Most of the methods that have the get & delete prefix support batch request.

```php
$id  = 'id_xxx';
$ids = ['id_xxx', 'id_yyy', ...];

// one per time
$dataA = $nylas->Contacts()->Contact()->getContact($id);
$dataB = $nylas->Contacts()->Contact()->deleteContact($id);

// batch request
$dataC = $nylas->Contacts()->Contact()->getContact($ids);
$dataD = $nylas->Contacts()->Contact()->deleteContact($ids);
```

For more detail about the batch request, you should have to read the source code.</br>
Sorry, I have no time to write documents.


### Authentication

There are two ways you can authenticate users to your application.</br>
Hosted & Native are both supported.</br>

Here's the server-side(three-legged) OAuth example:</br>

1. You redirect the user to nylas login page, along with your App Id and Secret</br>
2. Your user logs in</br>
3. She is redirected to a callback URL of your own, along with an access code</br>
4. You use this access code to get an authorization token to the API</br>

For more information about authenticating with Nylas,</br>
visit the [Developer Documentation](https://docs.nylas.com/reference#authentication).</br>

In practice, the Nylas REST API client simplifies this down to two steps.</br>

**Step 1: Redirect the user to Nylas:**

```php
$params =
[
    'state'        => 'testing',
    'login_hint'   => 'test@gmail.com',
    'redirect_uri' => 'https://www.test.com/redirect_callback',
];

// generate the url that your user need be redirect to.
$url = $nylas->Authentication()->Hosted()->getOAuthAuthorizeUrl($params);
```

**Step 2: your user logs in:**</br>
**Step 3: you got the access code from the nylas callback:**</br>
Please implement the above 2 & 3 steps yourself.</br>

**Step 4: Get authorization token with access code:**

```php
$data = $nylas->Authentication()->Hosted()->postOAuthToken($params);

// save your token some where
// or update the client option
$nylas->Options()->setAccessToken("pass the token you got");
```

## Supported Methods

The parameters that required by methods almost the same as nylas official api required.

For more detail, you can view the tests or the source code of validation rules for that method.

### [Accounts](https://docs.nylas.com/reference#accounts)

$nylas->Accounts()->Account()-></br>
![accounts](media/accounts.png)</br>

$nylas->Accounts()->Manage()-></br>
![manage](media/manage.png)</br>


### [Authentication](https://docs.nylas.com/reference#authentication)

$nylas->Authentication()->Hosted()-></br>
![hosted](media/hosted.png)</br>

$nylas->Authentication()->Native()-></br>
![native](media/native.png)</br>


### [Calendars](https://docs.nylas.com/reference#calendars)

$nylas->Calendars()->Calendar()-></br>
![calendar](media/calendar.png)</br>


### [Contacts](https://docs.nylas.com/reference#contacts-intro)

$nylas->Contacts()->Contact()-></br>
![contact](media/contact.png)</br>

```php
// multiple contact pictures download
$params =
[
    [
        'id'   => 'contact id',
        'path' => 'this can be a file path, resource or stream handle',
    ],
    [
        'id'   => 'xxxx',
        'path' => dirname(__FILE__) . '/correct.png',
    ],
    // ...
];

$nylas->Contacts()->Contact()->getContactPicture($params);
```


### [Deltas](https://docs.nylas.com/reference#deltas)

$nylas->Deltas()->Delta()-></br>
![delta](media/delta.png)</br>


### [Draft](https://docs.nylas.com/reference#drafts)

$nylas->Drafts()->Draft()-></br>
![draft](media/draft.png)</br>

$nylas->Drafts()->Sending()-></br>
![send-draft](media/send-draft.png)</br>


### [Events](https://docs.nylas.com/reference#events)

$nylas->Events()->Event()-></br>
![event](media/event.png)</br>


### [Files](https://docs.nylas.com/reference#files)

$nylas->Files()->File()-></br>
![file](media/file.png)</br>


```php
// multiple files download
$params =
[
    [
        'id'   => 'file id',
        'path' => 'this can be a file path, resource or stream handle',
    ],
    [
        'id'   => 'xxxx',
        'path' => dirname(__FILE__) . '/correct.png',
    ],
    // ...
];

$nylas->Files()->File()->downloadFile($params);


// multiple files upload
$params =
[
    [
        'contents' => 'this can be a file path, resource or stream handle',
        'filename' => 'your file name'
    ],
    [
        'contents' => dirname(__FILE__) . '/correct.png',
        'filename' => 'test_correct.png'
    ],
    // ...
];

$nylas->Files()->File()->uploadFile($params);
```


### [Folders](https://docs.nylas.com/reference#folders)

$nylas->Folders()->Folder()-></br>
![folder](media/folder.png)</br>


### [Labels](https://docs.nylas.com/reference#labels)

$nylas->Labels()->Label()-></br>
![label](media/label.png)</br>


### [Messages](https://docs.nylas.com/reference#messages)

$nylas->Messages()->Message()-></br>
![message](media/message.png)</br>

$nylas->Messages()->Search()-></br>
![search-message](media/search-message.png)</br>

$nylas->Messages()->Sending()-></br>
![send-message](media/send-message.png)</br>

$nylas->Messages()->Smart()-></br>
![message-smart](media/message-smart.png)</br>


### [Threads](https://docs.nylas.com/reference#threads)

$nylas->Threads()->Search()-></br>
![search-thread](media/search-thread.png)</br>

$nylas->Threads()->Thread()-></br>
![thread](media/thread.png)</br>

$nylas->Threads()->Smart()-></br>
![thread-smart](media/thread-smart.png)</br>


### [Webhooks](https://docs.nylas.com/reference#webhooks)

$nylas->Webhooks()->Webhook()-></br>
![webhook](media/webhook.png)</br>


## Contributing

For more usage demos, please view the tests.</br>
Please feel free to use it and send me a pull request if you fix anything or add a feature, though.</br>


## License

This project is licensed under the MIT license.
