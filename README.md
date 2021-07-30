# Nok Nok FIDO Auth Node
The Nok Nok S3 Authentication Suite enables FIDO certified multi-factor authentication (FIDO UAF, FIDO U2F, WebAuthn, and FIDO2) for mobile
and web applications. Rather than the unpleasant experience of entering usernames and passwords, users can enjoy using the latest biometric
authentication technology that already exists on their devices.

This authentication node allows you to easily integrate FIDO into your ForgeRock Access Manager implementation.

## About Nok Nok Labs
Nok Nok is the trusted leader in next-generation consumer authentication providing passwordless solutions to the world’s largest organizations. Delivering the most innovative authentication solutions in the market today, Nok Nok empowers global organizations to improve the user experience to access digital services, while meeting the most advanced privacy and regulatory requirements. The Nok Nok™ S3 Authentication Suite integrates into existing security environments to deliver a cost-effective, future-proof and standards-based authentication solution. As a founder of the FIDO Alliance and inventor of FIDO specifications, Nok Nok is the expert in deploying standards-based authentication.  For more information, visit www.noknok.com.

## Installation
The following are the installation steps:

**1.** Copy the `nnl-auth-nodes.jar` file and the specified external dependencies (excluding gwtutorial.war) provided into the following directory where AM is deployed.
```bash
../$CATALINA_HOME/webapps/openam/WEB-INF/lib
```
**2.** Copy the `jwt_config.json` file (JSON Web Key Set for token validation) provided to the following directory 
```bash
../$CATALINA_HOME/webapps/openam/WEB-INF/classes/configurations/default/token
```
**3.** Extract the gwturorial.war in the following directory.  
```bash
../$CATALINA_HOME/webapps/gwtutorial/
```
**4.** Copy the `jwt_config.json` file (JSON Web Key Set for token validation) provided to the following directory 
```bash
../$CATALINA_HOME/webapps/gwtutorial/WEB-INF/classes/configurations/default/SessionPlugin
```
**5.** Update the API endpoints for the login page by editing the following file:
```json
gwtutorial/config/config.js
```
Then replace the content of the file with the following:

```javascript
var tenant = "{{tenant}}";
var amserver = "{{am-server-domain-and-port}}";

var nnlConfig = {
    "version": "1.2",
    "nnlappsdk_url": "https://cloud.noknok.com/" + tenant + "/webapps/nnlgateway-6.0.3",
    "reg_endpoint": "https://cloud.noknok.com/" + tenant + "/webapps/nnlgateway/nnl/reg",
    "auth_endpoint": "https://cloud.noknok.com/" + tenant + "/webapps/nnlgateway/nnl/auth",
	"login_url": "https://" + amserver + "/gwtutorial/login",
	"fed_login_url": null,
    "fed_logout_url": null,
    "recovery_setup_endpoint": "https://cloud.noknok.com/" + tenant + "/webapps/nnlgateway/recovery/setup",
    "recovery_verify_endpoint": "https://cloud.noknok.com/" + tenant + "/webapps/nnlgateway/recovery/verify",
    "netverify_endpoint": "https://" + amserver + "/gwtutorial/nvinit",
    "federation_enabled": false,
    "sso_enabled": true
}
``` 
Replace {{am-server-domain-and-port}} with the domain and port where you installed the Access Manager and {{tenant}} with the supplied tenant name.

**6.** Restart the web container to pick up the new node. The custom node then appears in the **authentication trees menu**.

### External Dependencies

| Distribution Package | Description |
| ------ | ------ |
| log4j-slf4j-impl-2.12.0.jar | The Apache Log4j SLF4J API binding to Log4j 2 Core  |
| log4j-jcl-2.12.0.jar | The Apache Log4j Commons Logging Adapter |
| log4j-core-2.12.0.jar, log4j-api-2.12.0.jar | The Apache Log4j Implementation |
| log4j-1.2-api-2.12.0.jar | The Apache Log4j dependency for 1.x Compatibility API |
| nnlgateway.jar | Session Manager library that manages JWT Token Configurations and performs JWT Token validation |
| gwtutorial.war | Tutorial WebApp client for LoginPage supporting FIDO Authentication |

## Configuration
To enable sign in using NNL authentication Node:

- From **Authentication → Trees menu**, create a new authentication tree e.g **NNLFIDOAuthTree**

<img src="./images/createTree.png" width=60%>

- The following is an example for the authentication tree using the **NNL Validator Node**. You can add your own or standard **username/password** authenticator nodes provided by AMServer for authentication using passwords.

<img src="./images/sampletree.png">

- Save the Tree after adding all the required nodes.

- Update the **Authentication → Settings** for using the **NNLFIDOAuthTree** tree to be the default for authentication flow.

<img src="./images/treeSettings.png">

## NNL Token Validator Node
Once you install the jar file the NNL Token Validator Node is available in the tree designer pallet:

<img src="./images/node.png" width=30%>

NNL Token Validator Node is a Tree Node that serves 2 purposes. 
- It acts as a redirection node to the Nok Nok Authentication client hosted with AM server. This NNL client enables FIDO certified multi-factor authentication (FIDO UAF, FIDO U2F, WebAuthn, and FIDO2) for web applications. 
- It validates the JWT Token received from the Nok Nok Authentication Server after successful user authentication using FIDO and generates user session information.

These are the Node Configuration Properties:  

- **Node Name:** name you choose for the node, e.g., NNL Token Validator Node
- **Authorization Cookie Name:** name of the authentication cookie which contains the JWT received from NNL Auth Server, e.g., Authorization
- **AM Endpoint:** URI to the AM endpoint of the hosted AM server, e.g., https://{{hostname}}/openam
- **Login Page Endpoint:** URI to the tutorial webapp login page hosted on the server, e.g., https://{{hostname}}/gwtutorial

Update the hostname in the settings to match your installation:

<img src="./images/node_configuration.png" width=30%>

## Registration Flow
Before being able to use FIDO, you need to register a FIDO credential. Navigate to https://{{am-server-domain-and-port}}/gwtutorial in a WebAuthn capable browser. Also,
make sure you also create the same user in the Access Manager identity store.

**1.** Login using a username defined in your Access Manager identity store, e.g., "demo", and hardcoded password "noknok".

<img src="./images/signinpage.png" width=50%>

**2.** Click on Setup. This will trigger a WebAuthn registration.

<img src="./images/RegisterFIDO.png" width=40%>

**3.** You will see the new registration listed.

<img src="./images/successReg.png" width=40%>

**4.** Now click the top left menu and then pick logout.

## Authentication Flow

You can test the authentication flow using the Access Manager console.

**1.** Navigate to your Access Manager console, e.g., https://{{am-server-domain-and-port}}/openam.

**2.** The **NNL Token Validator Node** redirects to the NNL Authentication Client's *Sign-In* page hosted on your Access Manager server.

<img src="./images/signinpage.png" width=50%>

**3.** User can sign in using **FIDO2**.

<img src="./images/Fido2signin.png" width=50%>

<img src="./images/fidopopup.png" width=50%>

**4.** After successful authentication User is redirected to the **consent page** on the Access Manager console.

<img src="./images/consent.png" width=50%>

**5.** Once User consent, the browser is redirected to Access Manager console and the user is logged in.

<img src="./images/resource.png" width=50%>

# Support
For more information on this node or to request a demonstration, please contact: Frank Gasparovic - frank.gasparovic@forgerock.com or info@noknok.com