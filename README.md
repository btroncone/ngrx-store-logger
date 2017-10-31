# ngrx-store-logger
Advanced logging for @ngrx/store applications, ported from [redux-logger](https://github.com/fcomb/redux-logger).

![ngrx-store-logger](http://imgur.com/Fm2qfb5.png)

## Dependencies
`ngrx-store-logger` depends on [@ngrx/store](https://github.com/ngrx/store) and [Angular 2+](https://github.com/angular/angular).

## Usage
```bash
npm install ngrx-store-logger --save
```
**UPDATED FOR NGRX 4**
1. Import `ngrx-store-logger`
2. Wrap in an exported function
3. Include in your meta-reducers in `StoreModule.forRoot` call.

```ts
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { StoreModule, ActionReducer } from '@ngrx/store';
import { storeLogger } from 'ngrx-store-logger';
import { reducers } from './reducers';

export function logger(reducer: ActionReducer<State>): any {
  // default, no options
  return storeLogger()(reducer);
}

export const metaReducers = environment.production ? [] : [logger];

@NgModule({
  imports: [
    BrowserModule,
    StoreModule.forRoot(
        reducers,
        {metaReducers}
    )
  ]
})
export class MyAppModule {}
```

## API
### `storeLogger(options : LoggerOptions  = {}) : Reducer`
Initializes logger with appropriate options (logical defaults if no options supplied)
*Returns a meta-reducer*

#### Arguments
* `options` \(*Object*): Available logger options

##### Options

``` ts
interface LoggerOptions {
    level? : 'log' | 'console' | 'warn' | 'error' | 'info'; //default log
    collapsed? : boolean; //Should log group be collapsed? default: false
    duration? : boolean; //Print duration with action? default: true
    timestamp? : boolean; //Print timestamp with action? default: true
    filter?: {
      whitelist?: string[], // Only print actions included in this list - has priority over blacklist
      blacklist?: string[] // Only print actions that are NOT included in this list
    }
    stateTransformer? : (state : Object) => Object; //Transform state before print default: state => state
    actionTransformer? : (actn : Object) => Object; //Transform action before print default: actn => actn
    colors? : {
        title: (action : Object) => string;
        prevState: (prevState : Object) => string;
        action: (action: Object) => string;
        nextState: (nextState : Object) => string;
        error: (error: any, prevState: Object) => string;
    }
}
```

### Filtering
#### Whitelist
Only actions included in the list will be printed
Example:
``` ts
const options: LoggerOptions = {
  filter: {
    whitelist: ['set-value']
  }
}
storeLogger(options) : Reducer
```
With this setup, only action *set-value* will be logged

#### Blacklist
Action included in the blacklist will not be printed
Example:
``` ts
const options: LoggerOptions = {
  filter: {
    blacklist: ['set-value']
  }
}
storeLogger(options) : Reducer
```
With this setup, all actions except *set-value* will be printed

*Note*: Whitelist takes precedence over blacklist. If both are defined, only whitelist will be considered
