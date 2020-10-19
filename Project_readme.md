# project_name ![CI status](https://img.shields.io/badge/build-passing-brightgreen.svg)

Hi there, welcome! :tada:

project_name is a quick-start, zero configuration(almost) to get you started with building scalable production grade applications.

## Installation

### Requirements
* Linux/Mac/Windows/
* node 8+ (preferably yarn)

```
git clone https://repo_url.git

cd project_name

yarn install

npm install -g json-server

json-server --watch db.json --port 3004
```

## Running the development server
```
yarn start
```

## Tooling and packages used - The important ones.
### Tools
* webpack : For the run and build process.
* babel : ES6 to ES5 transpiler
* eslint : Used to prevent bad JS code from being written.
* pre-commit and pre-push : Used to prevent bad code from crawling into the repo.
* stylelint : Used to prevent bad CSS rules from being written.

### Packages
* css-modules : Enforces better CSS writing practices
* bootstrap4 : The latest and the greatest, yet.
* redux : Data layer
* react-redux : Connects the React Views with Redux Data
* react-router : Navigation
* redux-thunk : dispatch functions instead of plain actions. Useful for async actions.
* reduxForm : Makes form management easier, a lot.
* superagent : Library to make the API requests.

## Modules Explained
The React bit of the seeder project is broken down in the following way.
```
    |-- config (contains the scripts that are responsible for configuring webpack. Do not modify unless you want to add a webpack plugin or a config utility)
        |-- webpack.config.dev.js
        |-- webpack.config.prod.js
    |-- public
        |-- index.html (Do not modify unless you need to add a link to google fonts or 3rd party scripts)
    |-- scripts (contains scripts responsible for building/running the application. Do not modify)
    |-- README.md (The file you are reading right now!)
    |-- package.json (All dependencies go here, so do the build scripts execution)
    |-- src
        |-- assets
            |-- fonts (Fonts and Custom fonts like icomoon generated fonts go here)
            |-- images (You know the drill)
        |-- components
            |--*Form
               |--index.jsx (This file contains the reduxForm, the connect and the likes)
               |--anyCustomCSSfile.scss
               |--anyCustomCSSfile.css (This is auto generated)
            |--common
               |--Header
               |--Footer
               |--formElements
                  |--TextField
                     |--index.jsx (Contains the JSX for just the TextField and the custom styles applied to it)
                     |--TextField.scss (Contains styles relevant only to the TextField component)
                  |--Checkbox
                  |--..
            |--..
        |-- constants
            |-- api.js (Put all the API Endpoints here)
            |-- appConfig.js (Put the page titles here)
            |-- errorLabel.js (Put all custom error messages here)
            |-- messages.js (Put all the UI driven success / error toaster messages here)
        |-- containers
            |-- activeUser
                |-- index.jsx
                |--anyCustomCSSfile.scss
                |--anyCustomCSSfile.css (This is auto generated)
            |--..
        |-- helpers
            |-- ApiClient.js (This contains the configuration for superagent, the library which makes API requests possible)
        |-- include
            |-- bootstrap.js (imports the global css files, bootstrap JS and it's dependencies)
            |-- ..
        |-- reducers
            |-- middleware
                |-- clientMiddleware.js (This file is the middleman between the API requests and dispatch. Do not modify)
            |-- index.js (combines all the reducers)
            |-- *.js
        |-- scss
            |-- bootstrap
            |-- bootstrap_ext
            |-- _colors.scss
            |-- _fonts.scss
            |-- _utils.scss
            |-- _forms.scss
            |-- global.scss

```

### containers
This is the outermost parent that needs to access the data layer (redux store). It uses ```connect``` from ```react-redux``` to let ```react``` access the ```store```.

__Signature__

```javascript
import React, { Component } from 'react';
import PropTypes from 'prop-types';
import { connect } from 'react-redux';
import { bindActionCreators } from 'redux';
class ComponentName extends Component {
  constructor(props) {
    super(props);
    this.state = {};
  }
  render() {
    return (
      <div className="class-name">
        content
      </div>
    );
  }
}
function mapStateToProps({}){
  return {};
}
function mapDispatchToProps(dispatch){
  return bindActionCreators({},dispatch);
}
export default connect (mapStateToProps,mapDispatchToProps)(ComponentName);
```

### components
This is either a dumb component that receives the data from a container, or a ```ReduxForm``` that connects to the ```store```.

__ReduxForm Sample__

```javascript
import React from 'react';
import { reduxForm } from 'redux-form';
import { validationFields } from '@common/FormValidation';
import Textfield from '@common/Textfield';
import { ERROR_LABEL, APP_CONFIG } from '@constants';
const ComponentName = (props) => {
  const {
    handleSubmit,
    loading
  } = props;
  return (
    <form onSubmit={handleSubmit}>
      <Textfield
        name="fieldName"
        type="inputType"
        label="Placeholder Label"
        errorLabel="ERROR_LABEL.LABEL"
        validate={[validations]}
      />
      <Textfield
        name="fieldName"
        type="inputType"
        label="Placeholder Label"
        errorLabel="ERROR_LABEL.LABEL"
        validate={[validations]}
      />
      <button
        disabled={loading}
        type="submit"
      >
        buttonText
      </button>
    </form>
  );
};
export default (reduxForm({
  form: 'formIdentifier' // a unique identifier for this form
})(ComponentName));
```

__Stateful Component Signature__

```javascript
import React, { Component } from 'react';
export default class ComponentName extends Component {
  render() {
    return (
      <div className="className">
        content
      </div>
    );
  }
}
```

__Stateless Component Signature__

```javascript
import React from 'react';
const ComponentName = (props) => {
  return (
    <div className="className">
      content
    </div>
  );
}
export default ComponentName;
```

### constants
This contains the configurable constants for the application.

```api.js``` - Put all the API Endpoints here. Example,
```javascript
endpoints: {
  users: 'users',
  deleteUser: '/users',
  getUser: '/users',
  sendotp: '/api/account/generate/otp',
  verifyotp: '/api/account/verify/otp',
  register: '/api/account/register',
  login: '/account/login',
  reset_password: '/api/account/reset/password'
}
```

```appConfig.js``` - Put the page titles here. Example,

```javascript
const TITLE = '';

export default{
  LOGIN: `${TITLE} Login`,
  SIGNUP: `${TITLE} Sign up`,
  RESET_PASSWORD: `${TITLE} Reset password`,
  BASE_URL: process.env.PUBLIC_URL
};
```

```errorLabel.js``` - Put all custom error messages here. Example,
```javascript
export default {
  MOBILE_NUMBER: 'Please enter valid mobile number',
  PASSWORD: 'Please enter valid password',
  FULL_NAME: 'Please enter your full name',
  EMAIL_ID: 'Please enter valid email',
};
```

```messages.js``` - Put all the UI driven success / error toaster messages here. Example,
```javascript
export default {
  LOGIN_SUCCESS: 'Succesfully Logged In',
  OTP_SUCCESS: 'Otp Sent successfully to your registered Mobile Number'
};
```

### reducers
This boilerplate combines the actions and reducers into file, instead of two separate ones. This gives us the ability to write the reducer level constants in the same file without having to export the same.

__Reducer Signature__

```javascript
const currentReducer = 'REDUCER_NAME';
export const ACTION_REQUESTED = `${currentReducer}/ACTION_REQUESTED`;
export const ACTION_SUCCESS = `${currentReducer}/ACTION_SUCCESS`;
export const ACTION_FAILURE = `${currentReducer}/ACTION_FAILURE`;

const initialState = {
  loading: false,
  loaded: false,
  error: false,
  data: null
};

export default function(state = initialState, action) {
  switch (action.type) {
    case ACTION_REQUESTED:
      return {
        ...state,
        loading: true,
        loaded: false,
        error: false
      };
    }
    case ACTION_SUCCESS:
      return {
        ...state,
        loading: false,
        loaded: true,
        error: false,
        data: action.payload
      };
    }
    case ACTION_FAILURE:
      return {
        ...state,
        loading: false,
        loaded: true,
        error: true
      };
    }
    default:
      return state;
  }
}

export const REDUCER_NAME = (data) => {
  const headers = { has_auth_token: 0 };
  return {
    types: [ACTION_REQUESTED, ACTION_SUCCESS, ACTION_FAILURE],
    promise: client => client.REQUEST_TYPE('API_METHOD_INVOCATION', {
      headers,
      data
    })
  };
};
```

### SCSS
This is the directory where are global styles are declared, bootstrap 4 is imported etc.

The subdirectory __bootstrap__ contains
* _config.scss - This is the file where any bootstrap4 theme level customization options should be put. For example,
```
$theme-colors: (
  "primary": #0052CC,
  "danger": #ff4136
);
```
* bootstrap.scss - We have moved away from the usual way of including the entire bootstrap file and adopted scss based approach. This gives us the flexibility of including only the required modules. For example,
```
@import "bootstrap/scss/dropdown";
@import "bootstrap/scss/card";
@import "bootstrap/scss/images";
```

The subdirectory __bootstrap_ext__ contains
* _ext.scss - This is the file where you would include additional bootstrap configuration, such as styling the default ```.btn``` class. For example,
```
.btn {
    $button-border-width: 2px;
    border-width: $button-border-width;

    &.btn-dark {
      background-color: $black;
      border-color: $black;
      color: $white;

      &:hover {
        background-color: $white;
        color: $black;
      }
    }
}
```
The other scss files that are present are
* _colors.scss : Contains the variables for colors. For example,
```
  $color-primary: #0052CC;
```
It also contains the classes that use these variables. For example,
```
.primary {
    color: $color-primary;
}
```
* _fonts.scss : Contains the classes for fontsizes and fontweights. For example,
```
.fs-10 {
    font-size: 0.625rem;
}
```
__Note about font sizes__ - Always use rem for defining font-sizes. This is mandatory. Also, this is the only file where __you should__ define font sizes and weights.

* _utils.scss : Contains the classes for margins, paddings, line-heights etc. For example,
```
.p-10 {
    padding: 10px;
}
```
__Note about utility classes__ - This is the only file where __you should__ define utilities. You should ```compose``` your component classes from here.
* _forms.scss : As the name says, any global customization related to the forms should be done here.
* global.scss : This imports all the above mentioned SCSS files. <br/>
__Note__-Never write any classes here.
