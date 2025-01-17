# Firebase JavaScript SDK

> The [Firebase JavaScript SDK](https://firebase.google.com/docs/reference/js) implements the client-side libraries used by applications using Firebase services.

## How to use this plugin with the Firebase JavaScript SDK

By default, this plugin signs the user in only on the native layer of the app.
In order to use the Firebase JavaScript SDK, a sign-in on the web layer is required.
To do this, follow these steps:

1. [Add Firebase to your JavaScript project](https://firebase.google.com/docs/web/setup)
1. Set the configuration option `skipNativeAuth` to `true` (see [here](https://github.com/robingenz/capacitor-firebase-authentication#configuration)).
1. Sign in on the native layer, create web credentials and sign in on the web using [`signInWithCredential`](https://firebase.google.com/docs/reference/js/auth.md#signinwithcredential) (see [Examples](#examples)).

## Examples

```js
import { FirebaseAuthentication } from '@robingenz/capacitor-firebase-authentication';
import 'firebase/auth';
import firebase from 'firebase/app';

const signInWithApple = async () => {
  // 1. Sign in on the native layer
  const signInResult = await FirebaseAuthentication.signInWithApple();
  // 2. Sign in on the web layer using the id token and nonce
  const provider = new firebase.auth.OAuthProvider('apple.com');
  const credential = provider.credential({
    idToken: result.credential?.idToken,
    nonce: result.credential?.nonce,
  });
  await firebase.auth().signInWithCredential(credential);
};

const signInWithGoogle = async () => {
  // 1. Sign in on the native layer
  const result = await FirebaseAuthentication.signInWithGoogle();
  // 2. Sign in on the web layer using the id token
  const credential = firebase.auth.GoogleAuthProvider.credential(
    result.credential?.idToken,
  );
  await firebase.auth().signInWithCredential(credential);
};

const signInWithPhoneNumber = async () => {
  // 1. Start phone number verification
  const { verificationId } = FirebaseAuthentication.signInWithPhoneNumber({
    phoneNumber: '123456789',
  });
  // 2. Let the user enter the SMS code
  const verificationCode = window.prompt(
    'Please enter the verification code that was sent to your mobile device.',
  );
  // 3. Sign in on the web layer using the verification ID and verification code.
  const credential = firebase.auth.PhoneAuthProvider.credential(
    verificationId,
    verificationCode,
  );
  await firebase.auth().signInWithCredential(credential);
};
```

The dependencies used in these examples:

- `firebase@8.10.0`
- `@robingenz/capacitor-firebase-authentication@0.3.7`

## Limitations

Unfortunately, it is currently not possible to sign in to Apple on Android using the Firebase JavaScript SDK (see [#53](https://github.com/robingenz/capacitor-firebase-authentication/issues/53)).
