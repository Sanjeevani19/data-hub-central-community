### Before You Start
You will need access to a Data Hub project somewhere on your disk. You'll also need a running MarkLogic with the data hub deployed to it.

### Prereqs
For the Middle tier you need:
 - Java sdk 8 or newer (can be openjdk)
 - [MarkLogic 10.0+](https://developer.marklogic.com/products/marklogic-server/10.0)
 - a [MarkLogic Data Hub Framework 5.1.0](https://github.com/marklogic/marklogic-data-hub) project on disk and running in MarkLogic

For the UI you need:
 - [Node 12.x or later](https://nodejs.org/en/)
 - Npm 6.9.0 or later

### Data Hub Central Community Edition Installation Instructions 

First follow the instructions in the [Contributing doc](./CONTRIBUTING.md) for how to properly fork and clone and branch. Once you've done that come back here.

### Running the project (Single Tenant)
Single tenant is the traditional way to run DHCCE. It relies on MarkLogic's users/roles to provide access.

#### Development Mode (single-tenant)

Open 2 terminal tabs/windows

##### Middle Tier (single-tenant)
Open a terminal in the project root: /path/to/dhcce

The command to run the middle tier is:  
`gradlew -DdhfDir=/full/path/to/your/datahub bootrun`

**OPTIONAL** Also you might want to override where the Concept Connector models live. Otherwise the models go in `./conceptConnectorModels`  
`gradlew -DdhfDir=/full/path/to/your/datahub -DmodelsDir=/full/path/to/your/models/dir bootrun`

Lastly, you may want to choose a different Hub environment (qa, prod, etc). The default is local.  
`gradlew -DdhfDir=/full/path/to/your/datahub -DmodelsDir=/full/path/to/your/models/dir -DdhfEnv=prod bootrun`

#### Running Middle Tier Tests
You will still need a running Data Hub Framework
**WARNING:** This command will wipe out all your data. Do not point it at a production instance.

`gradlew -DdhfDir=/full/path/to/your/datahub test`

##### User Interface (single-tenant)
Open a terminal in the project root: /path/to/dhcce

launch the ui in develop mode
`gradlew runui`

### Running the project (Multi Tenant)
Multi tenant mode allows you to run DHCCE in a hosted environment. It provides a signup flow for new users. It's main purpose is for running DHCCE as a self-service demo.

#### Development Mode (multi-tenant)

Open 2 terminal tabs/windows

##### Middle Tier (multi-tenant)
Open a terminal in the project root: /path/to/dhcce

Edit application.properties:
```
envision.multiTenant=true
```

The command to run the middle tier is:  
`gradlew -Penv=cloud -DdhfDir=/full/path/to/your/datahub bootrun`

**OPTIONAL** Also you might want to override where the Concept Connector models live. Otherwise the models go in `./conceptConnectorModels`  
`gradlew -Penv=cloud -DdhfDir=/full/path/to/your/datahub -DmodelsDir=/full/path/to/your/models/dir bootrun`

##### User Interface (multi-tenant)
Open a terminal in the project root: /path/to/dhcce

launch the ui in develop mode
`gradlew -Penv=cloud runui`

### Running Middle Tier Tests
You will still need a MarkLogic instance with no datahub installed.  
**WARNING:** This command will wipe out all your data. Do not point it at a production instance of MarkLogic.

`gradlew test`

### Access the DHCCE UI (development mode)
http://localhost:9999

You can log in with your MarkLogic username and password.

**Optional Config**  
If you want to debug the middle tier while you run the ui in development mode, you might want to use a port for the middle tier other than the default. This usually happens if the default port (9003) is already in use.  
Say you want to run the middle tier at 9004 by using `-Dserver.port=9004` when you run bootRun.

First, edit ui/.env:  
```
VUE_APP_MIDDLETIER_PORT=9004
```

Open two terminal windows. In one run the middle tier at the port you like (in this case, 9004):
`gradlew -Dserver.port=9004 -DdhfDir=/full/path/to/your/datahub -DmodelsDir=/full/path/to/your/models/dir -DdhfEnv=yourEnvironment bootRun`

In the other run the ui, as above:
`gradlew runui`

#### Running End to End UI Tests
Open a terminal in the top-level dhcce directory.

`gradlew testUi`

This will launch the Cypress UI. http://cypress.io is a testing framework we are using to test the UI e2e. When the Cypress UI launches you can click the run specs button to run the tests. Cypress will autowatch your files. If you make any changes the tests will re-run.

#### Production Mode
Open a terminal window:  

##### Build the jar

###### Without tests (single tenant)
`gradlew clean build -x test`

###### With tests (single tenant)
Note that you do need a running Data Hub Framework instance with a project folder  
**WARNING:** This command will wipe out all your data. Do not point it at a production instance.

`gradlew -DdhfDir=/full/path/to/your/datahub clean build`

###### Without tests (multi tenant)
`gradlew -Penv=cloud clean build -x test`

###### With tests (multi tenant)
Note that you do need a running Data Hub Framework instance with a project folder  
**WARNING:** This command will wipe out all your data. Do not point it at a production instance.

`gradlew -Penv=cloud -DdhfDir=/full/path/to/your/datahub clean build`


##### Run the jar
`java -jar middle-tier/build/lib/dhcce.jar`

The jar is configured so that you can drop it into a Data Hub Framework project dir and run it there.

`java -jar dhcce.jar`

However, if you need to point it at another folder where the Data Hub Framework is installed, run like so:

`java -DdhfDir=/full/path/to/your/datahub -jar dhcce.jar`

If you also need to specify a different models directory:

`java -DdhfDir=/full/path/to/your/datahub -DmodelsDir=/full/path/to/your/models/dir -jar dhcce.jar`

If you need to specify the Data Hub Framework environment (it defaults to local):

`java -DdhfEnv=prod -DdhfDir=/full/path/to/your/datahub -DmodelsDir=/full/path/to/your/models/dir -jar dhcce.jar`

#### Troubleshooting

##### Missing/Incorrect Concept Connector Model

* Are you seeing URIs on the Explore tab instead of meaningful labels?  
* Is the merge screen showing `   ,   ` instead of `Finn, Finn`?

You have not loaded the proper concept connector model for the current dataset in the Data Hub. To get working again, navigate to the "Connect" tab.  On the right panel, select "Load Model" and select the proper model for your dataset.  For the standard HR use case, pick "HR Employee 360".    

