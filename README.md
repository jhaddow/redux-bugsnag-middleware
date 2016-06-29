# redux-bugsnag-middleware

Redux middleware that integrates [Bugsnag](https://github.com/bugsnag/bugsnag-js). If exception happens during the action, it will send information to Bugsnag:

* about the error,
* about the action that caused it
* the whole redux store state

__It allows to easily override sent state for sanitization (e.g. if you store password or credit card number in your applicationt state).__

It demands basically no additional configuration except default Bugsnag snippet.

## Installation
Run `npm install --save redux-bugsnag-middleware`.

[Add Bugsnag snipped according to docs](https://github.com/bugsnag/bugsnag-js#how-to-install).

## Usage
Import `bugsnagMiddleware` function from package:
```
import bugsnagMiddleware from 'redux-bugsnag-middleware';
```

Create middleware in your store creator:
```
export default function configureStore(initialState) {
  const bugsnag = bugsnagMiddleware();

  return createStore(
    rootReducer,
    initialState,
    applyMiddleware(bugsnag)
  );
}
```

## State sanitization
If you want to sanitize state before sending to bugsnag, pass the sanitization function to `bugsnagMiddleware` function. It accepts current application state and should return a state that will be send to Bugsnag.

For example, assume that you store credit card number in your `billing` object inside your application state:
```
{
  billing: {
    number: '1234 1234 1234 1234',
    (...)
  },
  (...)
}
```
To sanitize it, you should provide following function:

```
const stateSanitizer = function(state) {
  // make sure you don't change state tree
  const stateCopy = Object.assign({}, state);
  // make sure you don't change billing object (by reference)
  const billingCopy = Object.assign({}, stateCopy.billing);
  // override number in billing copy
  billingCopy.number = '######';
  // pass billing copy to state copy
  stateCopy.billing = billingCopy;
  // return sanitized state
  return stateCopy;
}

export default function configureStore(initialState) {
  const bugsnag = bugsnagMiddleware(stateSanitizer);

  return createStore(
    rootReducer,
    initialState,
    applyMiddleware(bugsnag)
  );
}
```

## Maintainers
* [Kuba Niechciał](https://github.com/jniechcial)

## License

The gem is available as open source under the terms of the [MIT License](http://opensource.org/licenses/MIT).
