# Error Stacks
ECMAScript Proposal, specs, and reference implementation for `Error.prototype.stack`/`System.getStack`/`System.getStackString`

Spec drafted by [@ljharb](https://github.com/ljharb) along with [@erights](https://github.com/erights).

This proposal is currently [stage 1](https://github.com/tc39/proposals/blob/master/README.md) of the [process](https://tc39.github.io/process-document/).

## Rationale

Errors have never had a stack trace attached in the language spec — however, implementations have very consistently provided one on a property named “stack” on instantiated `Error` objects. There has long been concern about standardizing stack traces improperly - such that implementations could not claim to be fully compliant while also providing security guarantees. This proposal is an attempt to standardize the intersection of existing browser behavior, in a way that addresses these security concerns.

## Compatibility

```js
Object.getOwnPropertyDescriptor(new Error(), 'stack');
Object.getOwnPropertyDescriptor(Error.prototype, 'stack');
```

V8 in Node 7+ and Chrome 54+: `stack` is an own property on `Error` instances which claims to be a value property (`{"value":"(stack here)","writable":true,"enumerable":false,"configurable":true`), but V8's `Error` has exotic behavior for [[GetOwnProperty]]  that acts like a getter the first time (if there's been no set first) by triggering `prepareStackTrace` and setting `stack`, as discussed in [this thread](https://esdiscuss.org/topic/getownpropertydescriptor-side-effects). After that, it behaves like a standard value property.

V8 in Node <= 6 and Chrome <= 53: `stack` is an own property on `Error` instances with a getter/setter: `{ configurable: true, enumerable: false }`
 - The getter returns `undefined` with a non-Error receiver, and returns the `stack` property (whatever its current value may be) otherwise.
 - The setter is a no-op with a non-Error object receiver, and sets the return of the `stack` getter otherwise.

Safari: `stack` is an own string data property on `Error` instances: `{ configurable: true, enumerable: false, writable: true }`

Firefox: `stack` is an own property on `Error.prototype`: `{ configurable: true, enumerable: false }`
 - The getter throws on a non-Error receiver, and returns the original stack trace otherwise.
 - The setter sets the `stack` property on a non-Error receiver, and is a no-op on an Error receiver.

## Naming

`Error.prototype.stack` is set in stone - this is a defacto reality, and will be implemented in Annex B.

## Spec
You can view the spec rendered as [HTML](https://tc39.github.io/proposal-error-stacks/).
