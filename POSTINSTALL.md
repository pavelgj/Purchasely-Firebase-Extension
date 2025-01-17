#### Set your Cloud Firestore security rules

It is crucial to limit data access to authenticated users only and for users to only be able to see their own information. For product and pricing information it is important to disable write access for client applications. If you use Firebase Authentication and set the Purchasely User ID with the user's Firebase Authention UID, use the rules below to restrict access as recommended in your project's [Cloud Firestore rules](https://console.firebase.google.com/project/${param:PROJECT_ID}/firestore/rules):

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /${param:PURCHASELY_SUBSCRIPTIONS_COLLECTION}/{documentId} {
      allow read: if request.auth.uid == resource.data.user.vendor_id;
    }

    match /${param:PURCHASELY_EVENTS_COLLECTION}/{documentId} {
      allow read: if request.auth.uid == resource.data.user.vendor_id;
    }
  }
}
```

You may need to use different Firestore Rules if you use another system or service to manage Authentication or setup your Purchasely User ID differently.

#### Configure Purchasely Webhook

You need to set up the webhook that sends your users' In-App Purchases & Subscriptions events to the Firebase project.

Here's how to set up the Client webhook URL:

   1. Go to the [Purchasely Console](https://console.purchasely.io/).

   1. Use the URL of your extension's function as the endpoint URL. 
      Here's your function's URL: `${function:purchaselyWebhookHandler.url}`

   1. Set it in your Purchasely Console (Purchasely Console > Applications > [YOUR APP] > App Settings > Backend & SDK configuration > Client webhook URL


### Using the extension

If you haven't already, you will need to setup your Purchasely App and your In-App Purchases in each app store your app is available on.
To do so, you can follow the following guide: [Quick Start - Console Configuration](https://docs.purchasely.com/quick-start/console-configuration)

#### Sign-up users with Firebase Authentication (optional)

The quickest way to sign-up new users is by using the [Firebase Authentication SDK for your platform](https://firebase.google.com/docs/auth). Follow the steps outlined in the official docs. When configuring the extension you can choose to keep Firebase Authentication user's custom claims (https://firebase.google.com/docs/auth/admin/custom-claims) updated with their in-app subscriptions. If set to 'ENABLED' (default), upon receiving events the extension considers the Purchasely Event's `user.vendor_id` property to match the user's Firebase Authentication UID and updates the user's custom claims. If set to 'DISABLED', the extension will NOT update your users' Firebase Authentication custom claims

#### List your user's active subscriptions

Users' subscriptions are normal collections and docs in your Cloud Firestore and can be queried as such:

Javascript:
```js
db.collection('${param:PURCHASELY_SUBSCRIPTIONS_COLLECTION}')
  .where('is_subscribed', '==', true)
  .whereField('user.vendor_id', '==', '$CURRENT_USER_FIRESTORE_AUTHENTICATION_ID')
  .get()
  .then(function (querySnapshot) {
    querySnapshot.forEach(async function (doc) {
      console.log(doc.id, ' => ', doc.data());
    });
  });
```

Swift:
```swift
firestore.collection('${param:PURCHASELY_SUBSCRIPTIONS_COLLECTION}')
  .whereField('is_subscribed', '==', true)
  .whereField('user.vendor_id', '==', '$CURRENT_USER_FIRESTORE_AUTHENTICATION_ID')
  .get()
  .then(function (querySnapshot) {
    querySnapshot.forEach(async function (doc) {
      console.log(doc.id, ' => ', doc.data());
    });
  });
```

#### List your user's consumables

Users' consumables are normal collections and docs in your Cloud Firestore and can be queried as such:

Javascript:
```js
db.collection('${param:PURCHASELY_CONSUMABLES_COLLECTION}')
  .whereField('user.vendor_id', '==', '$CURRENT_USER_FIRESTORE_AUTHENTICATION_ID')
  .get()
  .then(function (querySnapshot) {
    querySnapshot.forEach(async function (doc) {
      console.log(doc.id, ' => ', doc.data());
    });
  });
```

Swift:
```swift
firestore.collection('${param:PURCHASELY_CONSUMABLES_COLLECTION}')
  .whereField('user.vendor_id', '==', '$CURRENT_USER_FIRESTORE_AUTHENTICATION_ID')
  .get()
  .then(function (querySnapshot) {
    querySnapshot.forEach(async function (doc) {
      console.log(doc.id, ' => ', doc.data());
    });
  });
```

#### List your user's non-consumables

Users' non-consumables are normal collections and docs in your Cloud Firestore and can be queried as such:

Javascript:
```js
db.collection('${param:PURCHASELY_NON_CONSUMABLES_COLLECTION}')
  .whereField('user.vendor_id', '==', '$CURRENT_USER_FIRESTORE_AUTHENTICATION_ID')
  .get()
  .then(function (querySnapshot) {
    querySnapshot.forEach(async function (doc) {
      console.log(doc.id, ' => ', doc.data());
    });
  });
```

Swift:
```swift
firestore.collection('${param:PURCHASELY_NON_CONSUMABLES_COLLECTION}')
  .whereField('user.vendor_id', '==', '$CURRENT_USER_FIRESTORE_AUTHENTICATION_ID')
  .get()
  .then(function (querySnapshot) {
    querySnapshot.forEach(async function (doc) {
      console.log(doc.id, ' => ', doc.data());
    });
  });
```

### Monitoring

As a best practice, you can [monitor the activity](https://firebase.google.com/docs/extensions/manage-installed-extensions#monitor) of your installed extension, including checks on its health, usage, and logs.

Access the [Purchasely Console](https://console.purchasely.io/) to manage all aspects of your Purchasely Project

Enjoy and please submit any feedback and feature requests on [GitHub](https://github.com/Purchasely/Purchasely-Firebase-Extension/issues/new)
