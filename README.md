# json-server-IIS
How to build your json-server to an IIS server


# YOU NEED TO INSTALL ON THE SERVER:

## -NODE JS (LTS)
https://nodejs.org/en/download

## -IISNode
https://github.com/azure/iisnode/releases/

#URL REWRITE
https://www.iis.net/downloads/microsoft/url-rewrite

Application request routing
https://www.iis.net/downloads/microsoft/application-request-routing

Then execute in the cmd / powershell:

net stop WAS /y

When the process done...:

net start WAS /y

**************************************************************************************************************************************************************
*****************************************WEB CONFIG START*****************************************
# CREATE A web.config file copy and paste the following code.

## -no copy- <add name="iisnode" path=""your file name" verb="*" modules="iisnode" />
## -no copy- <action type="Rewrite" url="/"your file name".cjs" />

**INIT COPY**

<?xml version="1.0" encoding="utf-8"?>
<configuration>
<system.webServer>
<iisnode loggingEnabled="false" debuggingEnabled="false" nodeProcessCommandLine="C:\Program Files\nodejs\node.exe" />
<handlers>
  <add name="iisnode" path="app.cjs" verb="*" modules="iisnode" />
</handlers>

	 <rewrite>
       <rules>
         <rule name="nodejs">
           <match url=".*" />
           <action type="Rewrite" url="/app.cjs" />
         </rule>
       </rules>
     </rewrite>


<security>
  <requestFiltering>
    <hiddenSegments>
      <add segment="node_modules" />
      <add segment="iisnode" />
    </hiddenSegments>
  </requestFiltering>
</security>


</system.webServer>
 </configuration>

**END COPY**

*****************************************WEB CONFIG END*****************************************

*****************************************JSON SERVER FILE START*****************************************
## CREATE A NEW FILE JS

app.js

## INSIDE FILE

const jsonServer = require("json-server");
const server = jsonServer.create();
const router = jsonServer.router("db.json"); // Your database file
const middlewares = jsonServer.defaults();

const PORT = 8080; // replace to -> process.env.PORT when the file is  on the server


// Add custom middleware for CORS
server.use((req, res, next) => {
  res.header("Access-Control-Allow-Origin", "*"); // ALLOW ANY ORIGIN, REPLACE * TO YOUR URL...
  res.header(
    "Access-Control-Allow-Methods",
    "GET, POST, PUT, DELETE, PATCH, OPTIONS"
  );
  res.header(
    "Access-Control-Allow-Headers",
    "Origin, X-Requested-With, Content-Type, Accept"
  );
  next();
});

server.use(router);
server.listen(PORT, () => {
  console.log("JSON Server is running");
});


*****************************************JSON SERVER FILE END*****************************************

## CREATE NEW APPLICATION ( NO MANAGEMENT CODE)

## COPY YOUR FILE TO THE ROOT APPLICATION FOLDER ON THE SERVER, THEN REPLACE THE EXTENSION FROM JS TO CJS: app.js -> app.cjs

## OPEN A TERMINAL OR POWERSHELL AND EXECUTE THE FOLLOWING COMMANDS:
npm init -y

npm install json-server (if didn't wor for the CORS problem... install the next version:  0.17.4 -> npm install json-server@0.17.4) 

npm install -> "your packages..."






