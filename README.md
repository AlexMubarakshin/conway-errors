# conway-errors

(Library API in progress)

## Idea

1. Implement root error types (FrontendLogickError, BackendInteractionError)
2. Split and structurize errors with Conway's law. (<https://en.wikipedia.org/wiki/Conway%27s_law>) - per team or area
3. Implement context (team/area) with this library
4. Create features
5. Throw throw throw! :)

## Usage

### Basic usage

```ts
import createError from "conway-errors"; // (!!!) at this moment not published

// (1) init error types 
const createErrorContext = createError(["FrontendLogickError", "BackendLogickError"]);

// (2) create contexts (per team or area)
const errorAuthTeamContext = createErrorContext("AuthTeamContext");
const errorPaymentTeamContext = createErrorContext("PaymentTeamContext");

// (3) create features
const oauthError = errorAuthTeamContext.feature("OauthError");
const paymentError = errorPaymentTeamContext.feature("PaymentError");

// (4) throw errors
oauthError.throw("FrontendLogickError", "User not found");
paymentError.throw("BackendLogickError", "Payment already processed"); // node.js:
```

### Nested context (subcontext)

```ts
import createError from "conway-errors"; // (!!!) at this moment not published

// (1) init error types 
const createErrorContext = createError(["FrontendLogickError", "BackendLogickError"]);

// (2) create context 
const authTeamErrorContext = createErrorContext("AuthTeamContext");

// (3) create subcontext for authTeamErrorContext
const socialAuthErrorContext = createErrorContext.context("SocialAuth");
const phoneAuthErrorContext = createErrorContext.context("PhoneAuth");

// (4) create features

const facebookError = socialAuthErrorContext.feature("FacebookAuth");
const smsSendError = phoneAuthErrorContext.feature("SmsSender");

facebookError.throw("FrontendLogickError", "Account inactive");
smsSendError.throw("BackendLogickError", "Limit exceed");
```

### Overload throwing (Sentry example)

```ts
import createError from "conway-errors"; // (!!!) at this moment not published
import * as Sentry from "@sentry/nextjs";

const createErrorContext = createError(["FrontendLogickError", "BackendLogickError"], {
  throwFn: (err) => { // overload throwing behavior
    Sentry.captureException(err);
  },
});
```

this code will throw error to sentry, but not throw global error
