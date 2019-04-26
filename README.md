# react-native-spring-public
The React Native Spring Issues Repo

The purpose of this repo is to track and fix bugs for the Spring React Native SDK. Find documentation on the usage of the sdk below.

## React Native SDK
We adopted [React Native](https://facebook.github.io/react-native/) as our prefered mobile app development platform. This was an easy choice since it's Javascript, cross platform and renders native elements. The react native sdk is a react component that wraps the JS sdk and makes it easy to integrate the Spring payment gateway into a React Native project. If you are unfamiliar with the React Native platform then have a look at their [documentation](https://facebook.github.io/react-native/docs/getting-started) before going beyond this point.

### Including Spring in your project
To include the RN sdk in your RN project you can install the sdk from npm.

`npm install react-native-spring` 

or using yarn:

`yarn add react-native-spring`

### A simple login component
Below is a sample RN component that shows how you can render the login client interface in a RN project. The explanation for the example is found below.
```
import React from "react";
import { Pay } from 'react-native-spring';
import { View, Text } from 'react-native';
import { constants } from "../constants";

export class Login extends React.Component {

    constructor(props) {
        super(props);
        this.state = {
            gotaccess: false,
            access: '',
        }
        this.handleResponse = this.handleResponse.bind(this); // 1
    }

    componentDidMount(){
        let access = 'your_access_key'; // 2
        if(access){
            this.setState({
                gotaccess: true,
                access: access, 
            });
        }
    }

    handleResponse(response){
        if(response.success && response.payload.action === 'otp'){ // 3
            // do something with the response
        }
    }

    show(){
        if(this.state.gotaccess){
            return (<Pay // 4
                access={this.state.access} // 5
                action='login'
                response={this.handleResponse} // 6
                />);
        }else{
            return <Text style={{padding: 25}}>Loading...</Text>
        }
    }

    render() {
        return this.show();
    }
}
```
1. We bind the `handleResponse` function to the component instance to allow us to properly use it as a callback function.
2. This is the access key, ideally it would have come from an asynchronous request to a server side function.
3. After a successful exchange, the `handleResponse` method returns an object. It is important to check the `response.payload.action` variable to see if it matches the action that was specified when the action was invoked because users are at times prompted to go through other actions like login or registration before the actual intended action can be reached.
4. The `Pay` component is the only component needed to integrate Spring into your RN project, it knows what to render depending on the props that you pass it.
5. The component accepts the following props:
   - access
   - action
   - client_reference
   - total_amount
   - sdk
   - merchant_phone
   - response
  
  The data types for the props and which actions they are required for is identical to the specification indicated in the [JS API Actions section](#api-actions) except that the sdk field is automatically set to mobile when using RN and the access key is passed as a string prop. Also, the response prop accepts a method that has been bound to the parent component instance in order to accept call backs from the child `Pay` component.
