
# @tcbs/react-native-exception-handler

![npm](https://img.shields.io/npm/v/@tcbs/react-native-exception-handler)
![downloads](https://img.shields.io/npm/dw/@tcbs/react-native-exception-handler)
![license](https://img.shields.io/npm/l/@tcbs/react-native-exception-handler)

A React Native module to register **global JavaScript and Native exception handlers**  
to gracefully handle fatal and non-fatal crashes in production apps.

---

## Fork Notice

This package is a **maintained fork** of  
[`react-native-exception-handler`](https://github.com/a7ul/react-native-exception-handler) originally created by **Atul R (@a7ul)**.

TCBS maintains this fork to:
- Support modern React Native versions
- Apply stability and bug fixes
- Ensure long-term maintenance
- Keep the API backward compatible

The original MIT license and contributor credits are fully preserved.

---

## Why this library?

In a typical React Native app:

- **DEV mode** → Red screen with stack trace
- **PRODUCTION (bundled) mode** → App crashes silently 😐

This library allows you to:
1. Capture uncaught **JS exceptions**
2. Capture **Native crashes** (Android & iOS)
3. Show a graceful message to users
4. Report crashes to analytics / backend
5. Avoid abrupt app termination without context

---

## What’s Supported

### Exception Types

| Type | Description |
|----|-----------|
| **JS Exceptions** | Errors from JavaScript / React code |
| **Native Exceptions** | Crashes from native Android / iOS code |

> ⚠️ Native exceptions are critical.  
> UI rendering via JS **is not possible** once a native crash occurs.

---

## Installation

```bash
npm install @tcbs/react-native-exception-handler
# or
yarn add @tcbs/react-native-exception-handler
```

### React Native ≥ 0.60

Autolinking is supported.
No manual linking required.

---

## Migration from original package

No code changes needed.

```diff
- react-native-exception-handler
+ @tcbs/react-native-exception-handler
```

All APIs remain the same.

---

## Usage

### Handling JavaScript Exceptions

```js
import {
  setJSExceptionHandler,
  getJSExceptionHandler,
} from '@tcbs/react-native-exception-handler';

setJSExceptionHandler((error, isFatal) => {
  console.log(error, isFatal);
});

// Optional: override Red Screen in DEV mode
setJSExceptionHandler(
  (error, isFatal) => {
    console.log(error, isFatal);
  },
  true
);

const currentHandler = getJSExceptionHandler();
```

---

### Handling Native Exceptions

```js
import { setNativeExceptionHandler } from '@tcbs/react-native-exception-handler';

setNativeExceptionHandler((exceptionString) => {
  console.log(exceptionString);
});
```

#### Advanced Usage

```js
setNativeExceptionHandler(
  exceptionString => {
    // Custom native error handling
  },
  true,   // forceAppQuit (Android only, default true)
  false   // executeDefaultHandler
);
```

| Parameter               | Platform      | Description                            |
| ----------------------- | ------------- | -------------------------------------- |
| `forceAppQuit`          | Android       | Force app exit after crash             |
| `executeDefaultHandler` | Android + iOS | Execute previously registered handlers |

---

## Important Notes

* `setNativeExceptionHandler` **only works in bundled / release mode**
* In DEV mode, the Red Screen will still appear
* Native crashes cannot show JS-based UI

---

## Customization

### JavaScript Exception Handler

You can show alerts, dialogs, log errors, or restart the app.

```js
setJSExceptionHandler((error, isFatal) => {
  if (isFatal) {
    // Show alert / send logs / restart app
  }
});
```

---

### Android Native Exception Customization (Recommended)

Override native handling inside `MainApplication.java`:

```java
ReactNativeExceptionHandlerModule.setNativeExceptionHandler(
  (thread, throwable, originalHandler) -> {
    // Custom native crash handling
  }
);
```

---

### iOS Native Exception Customization

In `AppDelegate.m`:

```objc
[ReactNativeExceptionHandler replaceNativeExceptionHandlerBlock:
 ^(NSException *exception, NSString *readableException) {

  // Show alert to user

  [NSTimer scheduledTimerWithTimeInterval:4.0
                                   target:[ReactNativeExceptionHandler class]
                                 selector:@selector(releaseExceptionHold)
                                 userInfo:nil
                                  repeats:NO];
}];
```

📌 iOS apps **cannot restart programmatically** after a native crash.

---

## Example: Restart App on Fatal Error

```js
import { Alert } from 'react-native';
import RNRestart from 'react-native-restart';
import { setJSExceptionHandler } from '@tcbs/react-native-exception-handler';

setJSExceptionHandler((error, isFatal) => {
  if (isFatal) {
    Alert.alert(
      'Unexpected error',
      'The app will restart.',
      [{ text: 'Restart', onPress: () => RNRestart.Restart() }]
    );
  }
});
```

---

## Known Issues

### react-native-navigation (Wix)

Set `forceAppQuit` to `false`:

```js
setNativeExceptionHandler(nativeErrorCallback, false);
```

---

### Preserve Previously Registered Handlers

If other analytics SDKs register global handlers:

```js
setNativeExceptionHandler(callback, true, true);
```

---

## Testing Native Crashes

Use the test module from the original author:

[https://github.com/master-atul/rn-test-exception-handler](https://github.com/master-atul/rn-test-exception-handler)

It intentionally triggers native crashes for validation.

---

## Contributors (Original Project)

* Atul R
* Zeh Fernando
* Fred Chasen
* Damien Solimando
* Gustavo Fão Valvassori
* and many more ❤️

(Full list preserved from upstream project)

---

## TCBS Maintainers

* Subrata Das (@subrata1977)

---

## License

MIT License
Same as the original project.

---

## Credits

This project stands on the solid foundation built by
**@a7ul and the open-source community**.

Peace ✌️

```

---
