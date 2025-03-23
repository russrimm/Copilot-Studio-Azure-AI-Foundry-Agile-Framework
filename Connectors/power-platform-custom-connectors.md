# Power Platform Custom Connectors: A Comprehensive Guide

## Table of Contents
- [Introduction](#introduction)
- [API Testing Tools: Why and How to Use Them](#api-testing-tools-why-and-how-to-use-them)
- [Option 1: Creating a Custom Connector from a Postman Collection](#option-1-creating-a-custom-connector-from-a-postman-collection)
- [Option 2: Creating a Custom Connector Manually](#option-2-creating-a-custom-connector-manually)
- [Option 3: Creating a Custom Connector from Azure API Management](#option-3-creating-a-custom-connector-from-azure-api-management)
- [Authentication Options](#authentication-options)
- [Connecting to On-Premises APIs](#connecting-to-on-premises-apis)
- [Step-by-Step Example: Wikipedia API Custom Connector](#step-by-step-example-wikipedia-api-custom-connector)

## Introduction

Custom connectors in Power Platform enable you to connect to APIs that aren't available as standard connectors. They act as wrappers around REST APIs, allowing you to use them in Power Apps, Power Automate, and Power Virtual Agents.

This guide covers three primary methods for creating custom connectors and explains the available authentication options.

> **Important Licensing Note**: Custom connectors are classified as premium connectors in the Power Platform. This means they require a premium license (such as Power Apps Premium, Power Automate Premium, or per-app plan) beyond the standard Power Platform license included with Microsoft 365. Ensure you have the appropriate licensing before implementing custom connectors in production environments.

### Sample Custom Connectors

Microsoft provides several sample custom connectors that you can use as references or starting points for your own connectors. These samples demonstrate best practices and common patterns for different types of APIs.

Visit the [Microsoft Custom Connector Samples](https://learn.microsoft.com/en-us/connectors/custom-connectors/samples) page to explore these examples, including:

- Basic REST API connectors
- OAuth authentication examples
- Webhook implementations
- Connectors with complex data transformations
- Real-world API implementations

These samples can significantly accelerate your custom connector development by providing working templates that you can modify for your specific needs.

## API Testing Tools: Why and How to Use Them

### Why Use API Testing Tools?

API testing tools like Postman and Insomnia play a crucial role in developing custom connectors for several important reasons:

1. **Understanding API Behavior**: Before creating a custom connector, you need to understand how the API works—what endpoints are available, what parameters they accept, and what responses they return. API testing tools provide a user-friendly interface to explore these aspects.

2. **Validating Authentication**: API testing tools allow you to test different authentication methods to ensure you can successfully connect to the API before configuring your custom connector.

3. **Generating Documentation**: Tools like Postman can automatically generate documentation based on your requests, which serves as valuable reference material when creating your connector.

4. **Troubleshooting**: When your custom connector isn't working as expected, API testing tools help isolate whether the issue is with the API itself or your connector configuration.

5. **Collaboration**: Teams can share API configurations, enabling consistent implementation across different connectors and applications.

6. **Exporting to Custom Connectors**: Postman collections can be directly imported to create Power Platform custom connectors, saving significant development time.

### Installing and Using Insomnia on Windows

Insomnia is a popular, free, and user-friendly API testing tool. Here's how to install and use it with Wikipedia's API:

#### Installation Steps

1. **Download Insomnia**:
   - Open your web browser and navigate to the [Insomnia website](https://insomnia.rest/download)
   - Click the "Download" button for Insomnia Core (the free version)
   - Select the Windows installer (.exe file)

2. **Run the Installer**:
   - Locate the downloaded file (typically in your Downloads folder)
   - Double-click the installer file
   - If you see a security warning, click "Run" or "Yes" to proceed
   - The installer will launch with a welcome screen

3. **Complete the Installation**:
   - Follow the on-screen instructions
   - Accept the license agreement
   - Choose the installation location (the default is usually fine)
   - Select whether to create a desktop shortcut
   - Click "Install" to begin the installation
   - Wait for the installation to complete
   - Click "Finish" to exit the installer

4. **Launch Insomnia**:
   - Find Insomnia in your Start menu or desktop (if you created a shortcut)
   - Click to launch the application
   - On first launch, you may be asked to sign up for an account (this is optional and you can skip it)

#### Creating and Sending Wikipedia API Requests in Insomnia

Now let's create a request to Wikipedia's API to search for articles, following the official [Insomnia documentation](https://docs.insomnia.rest/insomnia/send-your-first-request):

1. **Create a New Request**:
   - After launching Insomnia, click "Create" in the top-left corner
   - Select "Request Collection" to create a new collection
   - Name it "Wikipedia API" and select "Create"
   - Now click the dropdown next to your new collection and select "New Request"
   - Name your request "Search Wikipedia"
   - Select "GET" as the method from the dropdown
   - Click "Create"
   
   *Explanation: A collection in Insomnia organizes related requests together. A GET request retrieves information without changing any data on the server.*

2. **Configure the Request URL**:
   - In the URL bar at the top of the request panel, enter:
     `https://en.wikipedia.org/w/api.php`
   
   *Explanation: This is the endpoint URL for Wikipedia's API. All API requests will use this base URL with different parameters.*

3. **Add Query Parameters**:
   - Click the "Query" tab below the URL field
   - For each parameter, click "Add" and enter the name and value:
     
     | Name | Value | Description |
     |------|-------|-------------|
     | action | query | Tells the API to perform a query operation |
     | format | json | Specifies that we want the results in JSON format |
     | list | search | Indicates we want to search for articles |
     | srsearch | Power Platform | The search term (you can change this to anything) |
     | srlimit | 5 | Limits the results to 5 articles |
   
   *Explanation: Query parameters customize your request. They appear in the URL after a "?" and are separated by "&" symbols.*

4. **Send the Request**:
   - Click the purple "Send" button
   - Insomnia will display the API response in the right panel
   
   *Explanation: When you send the request, Insomnia connects to Wikipedia's servers, passes your parameters, and displays the response.*

5. **View and Analyze the Response**:
   - The response appears in the right panel
   - You'll see the HTTP status code (typically 200 OK for successful requests)
   - Below that is the response body containing your search results
   - You can choose between "Preview" (formatted view) and "Raw" (unformatted) tabs
   
   *Explanation: A status code of 200 means your request was successful. The response body contains the actual data returned by the API.*

6. **Save Your Request**:
   - Your request is automatically saved in your "Wikipedia API" collection
   - You can click on it again in the left sidebar to run it again or modify it
   
   *Explanation: Saved requests allow you to quickly rerun the same API call without reconfiguring everything.*

7. **Create a Second Request for Article Content**:
   - Click the dropdown next to your collection and select "New Request" again
   - Name it "Get Article Content"
   - Keep "GET" as the method
   - Click "Create"
   - Enter the same URL: `https://en.wikipedia.org/w/api.php`
   - Add the following query parameters:
     
     | Name | Value | Description |
     |------|-------|-------------|
     | action | query | Performs a query operation |
     | format | json | Returns results in JSON format |
     | prop | extracts | Retrieves the content extract of the page |
     | pageids | [page ID from search results] | The page ID of an article from your search results |
     | explaintext | true | Returns plain text instead of HTML |
   
   *Explanation: This request uses different parameters to get the actual content of a specific article. For "pageids", use a number you found in the search results (in the "pageid" field).*

8. **Send and Review the Second Request**:
   - Click "Send" to execute the request
   - Review the response to see the article content
   
   *Explanation: This request retrieves the text content of a specific Wikipedia article based on its unique ID.*

9. **Use Environment Variables (Optional)**:
   - Click on "No Environment" in the top-left corner
   - Select "Manage Environments"
   - Create a new environment named "Wikipedia"
   - Add variables like `baseUrl` with value `https://en.wikipedia.org/w/api.php`
   - In your requests, change the URL to `{{ _.baseUrl }}`
   
   *Explanation: Environment variables let you reuse values across multiple requests and easily switch between different environments (like development and production).*

10. **Export Your Collection**:
    - Right-click on your "Wikipedia API" collection in the sidebar
    - Select "Export"
    - Choose "Insomnia v4 (JSON)" as the format
    - Save the file to your computer
    
    *Explanation: Exporting saves all your requests to a file that you can share with others or import into other tools.*

#### Exporting Your Requests for a Custom Connector

After creating and testing your requests, you can export them for use in a Power Platform custom connector:

1. **Export as a Collection**:
   - Right-click on your "Wikipedia API" collection
   - Select "Export"
   - Choose "Insomnia v4 (JSON)" format
   - Click "Export"
   - Choose a location to save the file
   
   *Explanation: This exports all your requests, including their URLs, parameters, and any authentication settings, into a single JSON file.*

2. **Convert to Postman Format (if needed)**:
   - Power Platform directly supports Postman collections
   - You can use tools like [Insomnia to Postman Converter](https://insomnia.rest/blog/exporting-to-postman) or online converters to change the format
   
   *Explanation: While Power Platform doesn't directly support Insomnia export files, you can convert them to Postman format, which is supported for creating custom connectors.*

3. **Use in Power Platform**:
   - Follow the steps in [Option 1: Creating a Custom Connector from a Postman Collection](#option-1-creating-a-custom-connector-from-a-postman-collection) to create your connector

#### Understanding API Concepts for Beginners

If you're new to APIs, here are some key concepts to understand:

- **API (Application Programming Interface)**: A set of rules that allows different software applications to communicate with each other. Think of it as a waiter in a restaurant - you (the client) give your order (request) to the waiter, who delivers it to the kitchen (server), and then brings back your food (response).

- **Endpoint**: A specific URL where an API can be accessed. In our example, `https://en.wikipedia.org/w/api.php` is the endpoint for Wikipedia's API.

- **HTTP Methods**: Different types of actions you can perform:
  - GET: Retrieve data (like searching for articles)
  - POST: Create new data
  - PUT/PATCH: Update existing data
  - DELETE: Remove data

- **Parameters**: Additional information sent with your request to customize what you want. There are several types:
  - Query parameters: Added to the URL after a question mark
  - Path parameters: Part of the URL path
  - Header parameters: Additional metadata about the request
  - Body parameters: Data sent with POST/PUT requests

- **Authentication**: How you prove to the API that you have permission to access it. Wikipedia's API is unusual because it doesn't require authentication for basic requests, but most APIs do.

- **Response Codes**: Numbers that indicate how your request was processed:
  - 200s: Success (200 = OK)
  - 300s: Redirections
  - 400s: Client errors (404 = Not Found)
  - 500s: Server errors

- **Response Format**: How the data is structured when returned. Common formats include JSON (what we used) and XML.

By understanding these basics and practicing with Insomnia, you'll be better prepared to create effective custom connectors in Power Platform.

## Option 1: Creating a Custom Connector from a Postman Collection

Postman collections provide a convenient way to define and test API requests. You can leverage existing collections to accelerate custom connector development.

### Prerequisites
- A Postman account
- A Postman collection with your API requests
- Power Apps or Power Automate environment access

### Step-by-Step Process

1. **Export Your Postman Collection**
   - Open Postman and navigate to your collection
   - Click on the "..." menu next to your collection
   - Select "Export"
   - Choose "Collection v2.1" format
   - Save the JSON file to your computer

2. **Create a New Custom Connector**
   - Sign in to [Power Apps](https://make.powerapps.com) or [Power Automate](https://flow.microsoft.com)
   - In the left navigation menu, select "Data" > "Custom connectors"
   - Click "New custom connector" and select "Import from > Postman collection"
   - Enter a name for your connector
   - Upload the Postman collection JSON file
   - Click "Continue"

3. **Configure General Information**
   - Add a description, icon, and background color for your connector
   - Review the host URL imported from Postman
   - Click "Security" to continue

4. **Configure Security**
   - Select the appropriate authentication type (see [Authentication Options](#authentication-options))
   - Configure the required authentication parameters
   - Click "Definition" to continue

5. **Review and Update Definitions**
   - Review the operations imported from your Postman collection
   - Edit operation details as needed (summary, description, visibility)
   - Add or modify request parameters if necessary
   - Configure response schemas for each operation
   - Click "Create connector" to finish

6. **Test Your Connector**
   - In the "Test" tab, select an operation
   - Enter the required parameters
   - Click "Test operation"
   - Verify the response

### Advantages
- Leverages existing API documentation in Postman
- Saves time by importing pre-configured requests
- Maintains consistency between testing and production environments

### Limitations
- May require manual adjustments after import
- Dynamic parameters may need additional configuration
- Authentication settings might need refinement

## Option 2: Creating a Custom Connector Manually

Creating a custom connector manually gives you full control over every aspect of the configuration.

### Prerequisites
- API documentation (endpoints, methods, request/response schemas)
- Power Apps or Power Automate environment access

### Step-by-Step Process

1. **Create a New Custom Connector**
   - Sign in to [Power Apps](https://make.powerapps.com) or [Power Automate](https://flow.microsoft.com)
   - In the left navigation menu, select "Data" > "Custom connectors"
   - Click "New custom connector" and select "Create from blank"
   - Enter a name for your connector

2. **Configure General Information**
   - Add a description, icon, and background color for your connector
   - Enter the host URL for your API (e.g., `api.example.com`)
   - Specify the base URL if different from the host
   - Click "Security" to continue

3. **Configure Security**
   - Select the appropriate authentication type (see [Authentication Options](#authentication-options))
   - Configure the required authentication parameters
   - Click "Definition" to continue

4. **Define Operations**
   - Click "New action" to add an API operation
   - Enter a summary and description for the operation
   - Define the operation ID (unique identifier)
   - Select the HTTP method (GET, POST, PUT, DELETE, etc.)
   - Enter the relative path for the endpoint

5. **Configure Request Parameters**
   - Add query parameters, headers, and body definitions
   - For each parameter, specify:
     - Name
     - Description
     - Whether it's required
     - Type (string, integer, boolean, etc.)
     - Location (query, header, path, body)
   - For body parameters, define the schema (JSON or XML)

6. **Configure Response Schemas**
   - Define the expected response for different status codes
   - For each response, specify:
     - HTTP status code
     - Description
     - Body schema in JSON format
   - Define any default response

7. **Create and Test Your Connector**
   - Click "Create connector" to save your configuration
   - In the "Test" tab, select an operation
   - Enter the required parameters
   - Click "Test operation"
   - Verify the response

### Advantages
- Complete control over connector configuration
- Can be tailored precisely to API requirements
- No need for existing Postman collections or OpenAPI definitions

### Limitations
- More time-consuming than other methods
- Requires detailed knowledge of the API
- Potential for human error in manual configuration

## Option 3: Creating a Custom Connector from Azure API Management

Azure API Management (APIM) provides a way to create, publish, and manage APIs. You can create a connector directly from an API in APIM.

### Prerequisites
- Azure subscription
- Existing API in Azure API Management
- Power Apps or Power Automate environment access

### Step-by-Step Process

1. **Configure Your API in Azure API Management**
   - Sign in to the [Azure portal](https://portal.azure.com)
   - Navigate to your API Management service
   - Select "APIs" from the left menu
   - Configure or verify your API settings:
     - Backend service URL
     - Operations (endpoints, methods, parameters)
     - Request/response schemas
     - Policies for authentication, transformation, etc.

2. **Export OpenAPI Definition**
   - In your API's overview page, click "Export" at the top
   - Select "OpenAPI" as the format
   - Choose "OpenAPI v2 (JSON)" or "OpenAPI v3 (JSON)"
   - Save the file to your computer

3. **Create a New Custom Connector in Power Platform**
   - Sign in to [Power Apps](https://make.powerapps.com) or [Power Automate](https://flow.microsoft.com)
   - In the left navigation menu, select "Data" > "Custom connectors"
   - Click "New custom connector" and select "Import from > OpenAPI file"
   - Enter a name for your connector
   - Upload the OpenAPI JSON file exported from APIM
   - Click "Continue"

4. **Review and Update General Information**
   - Verify the description, host URL, and other general settings
   - Make adjustments if necessary
   - Click "Security" to continue

5. **Review Security Settings**
   - The authentication type should match what's configured in APIM
   - Verify and update authentication parameters if needed
   - Click "Definition" to continue

6. **Review and Update Definitions**
   - Review the operations imported from APIM
   - Edit operation details as needed
   - Update request parameters and response schemas if necessary
   - Click "Create connector" to finish

7. **Test Your Connector**
   - In the "Test" tab, select an operation
   - Enter the required parameters
   - Click "Test operation"
   - Verify the response

### Alternative: Direct Export from APIM to Power Platform

Azure API Management offers a more direct way to export to Power Platform:

1. In your API's overview page in APIM, click "Export" at the top
2. Select "Power Platform" as the export target
3. Sign in with your Power Platform account if prompted
4. Choose the environment where you want to create the connector
5. Configure the connector name and other settings
6. Click "Export" to create the connector directly in Power Platform

### Advantages
- Leverages Azure API Management's robust API management capabilities
- Ensures consistency between API configuration and connector
- Simplifies management of API security and policies

### Limitations
- Requires Azure subscription and APIM instance
- May need additional configuration in Power Platform
- Changes in APIM require re-export or update of the connector

## Authentication Options

Power Platform custom connectors support several authentication methods:

### No Authentication

Use when your API doesn't require authentication.

- **Configuration**: Select "No authentication" in the Security tab
- **Best for**: Public APIs, development/testing environments
- **Example**: Wikipedia API (see [Step-by-Step Example](#step-by-step-example-wikipedia-api-custom-connector))

### API Key

Use when your API requires a key or token in the header, query, or body.

- **Configuration**:
  - Select "API Key" in the Security tab
  - Specify the parameter name (e.g., `api-key`, `x-api-key`)
  - Choose the parameter location (Header, Query, or Body)
- **Best for**: Simple API security, developer portals
- **Example**: Weather APIs, stock data services

### Basic Authentication

Use when your API requires username and password.

- **Configuration**:
  - Select "Basic authentication" in the Security tab
  - No additional parameters needed (username/password requested at connection time)
- **Best for**: Simple credential-based APIs
- **Example**: FTP services, simple web services

### OAuth 2.0

Use for APIs that implement OAuth 2.0 authorization.

- **Configuration**:
  - Select "OAuth 2.0" in the Security tab
  - Configure:
    - Identity provider (Generic OAuth 2.0)
    - Client ID and secret
    - Authorization URL
    - Token URL
    - Refresh URL (if applicable)
    - Scopes (if required)
- **Best for**: Cloud services, social media APIs
- **Example**: Microsoft Graph, Twitter, Salesforce

### Windows Authentication

Use for APIs that require Windows authentication.

- **Configuration**: Select "Windows authentication" in the Security tab
- **Best for**: Internal enterprise services
- **Example**: SharePoint on-premises, internal .NET services

### OAuth 2.0 with Azure Active Directory

Use for APIs secured with Azure AD.

- **Configuration**:
  - Select "OAuth 2.0" in the Security tab
  - Select "Azure Active Directory" as the identity provider
  - Configure:
    - Client ID
    - Tenant ID (directory ID)
    - Resource URL
- **Best for**: Microsoft services, Azure-protected APIs
- **Example**: Azure services, Microsoft 365 APIs

## Connecting to On-Premises APIs

Custom connectors can also connect to APIs hosted within your organization's network. This section covers the options for creating these connections.

### Option 1: On-Premises Data Gateway

The On-Premises Data Gateway acts as a bridge between Power Platform and your internal resources.

#### Setup Process

1. **Download and Install the Gateway**:
   - Download the On-Premises Data Gateway from the [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=53127)
   - Install it on a server or VM within your network that has access to the internal API
   - The server must have outbound internet connectivity to Azure Service Bus

2. **Register the Gateway**:
   - During installation, sign in with your Microsoft 365 account
   - Name your gateway and provide a recovery key
   - Complete the registration process

3. **Configure the Gateway in Power Platform**:
   - Sign in to [Power Apps](https://make.powerapps.com) or [Power Automate](https://flow.microsoft.com)
   - Navigate to "Data" > "Gateways" 
   - Your gateway should appear in the list
   - Select the gateway and click "Connect"

4. **Use the Gateway with Your Custom Connector**:
   - When creating or editing your custom connector, in the "Gateway settings" section
   - Select "Connect via on-premises data gateway"
   - Choose your gateway from the dropdown
   - Provide the internal host URL for your API

#### High Availability and Limitations

For production environments, it's strongly recommended to implement gateway clustering for high availability:

- Install multiple gateway instances on different servers
- Register them to the same gateway in Power Platform
- Traffic will be distributed across all available gateway instances

> **Important Clustering Note**: While gateway clustering provides some redundancy, it should not be mistaken for true load balancing. The gateway service uses a round-robin approach to distribute requests across nodes rather than intelligent load balancing. This means:
>
> - If a node in the cluster becomes unresponsive, requests may still be routed to it, causing failures
> - Recovery from a failed node may not be immediate
> - There is no automatic failover in the traditional sense
> - Connectivity issues can still occur during node failures or maintenance
>
> Organizations requiring true high availability should implement additional monitoring and have failover procedures in place.

However, it's important to understand the inherent limitations of gateway architecture:

> **Important**: On-premises data gateways function as proxies, meaning all requests from various Power Platform users appear to the target API as originating from a single client (the gateway server's IP address). This has several significant implications:
>
> - **API Throttling**: Your API may throttle or rate-limit requests from the gateway IP, affecting all users simultaneously
> - **Resource Contention**: Multiple concurrent requests share the gateway's resources and connection limits
> - **User Identity Masking**: The API endpoint cannot distinguish between different Power Platform users since all traffic comes from the same source
> - **Security Auditing Challenges**: This creates difficulties for security auditing as individual user actions cannot be traced at the API level
> - **Limited Scalability**: As usage grows, the gateway can become a bottleneck even with clustering
>
> For mission-critical applications or scenarios requiring user-specific API authentication, consider using Azure Virtual Network integration instead, which avoids many of these limitations. See the [Microsoft documentation on Virtual Network integration for Power Platform](https://learn.microsoft.com/en-us/power-platform/admin/vnet-support-overview) for more details.

#### Advantages

- No need to expose internal APIs to the internet
- Uses secure, encrypted connections via Azure Service Bus
- Supports most authentication methods
- Can be shared across multiple connectors and connections

#### Limitations

- Requires installation and maintenance of gateway software
- High availability requires multiple gateway installations
- Potential performance impact due to additional network hops
- Proxy architecture masks individual user identities to the API

### Option 2: Azure Virtual Network (VNET) Integration

For APIs hosted in Azure, you can use VNET integration to connect securely.

#### Setup Process

1. **Configure your API in Azure**:
   - Ensure your API is hosted in an Azure App Service or Functions app
   - Configure the service to use a Virtual Network

2. **Create a Virtual Network Connection**:
   - In Power Platform admin center, navigate to "Data" > "Virtual network connections"
   - Create a new connection to your Azure Virtual Network
   - Provide the necessary Azure subscription and network details

3. **Configure Your Custom Connector**:
   - When creating the connector, specify the Azure service URL
   - In the advanced settings, enable "Connect via VNET"
   - Select your VNET connection

#### Advantages

- Direct connection without additional gateway software
- Lower latency than using on-premises gateways
- Integrated with Azure security model
- Easier management for Azure-hosted APIs
- Preserves user identity context, avoiding the "single client" problem of on-premises gateways
- Better scalability for high-traffic scenarios
- More granular security control and auditing capabilities
- Eliminates throttling issues caused by proxy architecture

For comprehensive information on implementing VNet integration with Power Platform, refer to the official [Microsoft documentation on Virtual Network integration for Power Platform](https://learn.microsoft.com/en-us/power-platform/admin/vnet-support-overview).

#### Limitations

- Only works for APIs hosted in Azure
- Requires premium Power Platform environment
- Limited to specific Azure regions

### Security Considerations for On-Premises Connections

- **Gateway Updates**: Keep your On-Premises Data Gateway updated to receive security patches
- **Server Hardening**: Follow security best practices for the server hosting the gateway
- **Recovery Key**: Store the gateway recovery key securely
- **Least Privilege**: Use service accounts with minimal required permissions
- **Network Security**: Consider network segmentation and firewall rules
- **Monitoring**: Implement monitoring for gateway health and performance

## Step-by-Step Example: Wikipedia API Custom Connector

This example demonstrates creating a custom connector for Wikipedia's free API.

### Overview

The Wikipedia API allows you to search articles, retrieve content, and get page information without authentication.

### Step 1: Create a New Custom Connector Manually

1. Sign in to [Power Apps](https://make.powerapps.com)
2. In the left navigation, select "Data" > "Custom connectors"
3. Click "New custom connector" and select "Create from blank"
4. Enter "Wikipedia" as the connector name
5. Click "Continue"

### Step 2: Configure General Information

1. Add a description: "Connect to Wikipedia's REST API to search and retrieve article information"
2. Upload an icon (optional) or choose a background color
3. Host: `en.wikipedia.org`
4. Base URL: `/w/api.php`
5. Click "Security" to continue

### Step 3: Configure Security

1. Select "No authentication" (Wikipedia's API is publicly accessible)
2. Click "Definition" to continue

### Step 4: Define Operations

#### Add a Search Operation

1. Click "New action"
2. Summary: "Search Wikipedia"
3. Description: "Search Wikipedia for articles matching the given search term"
4. Operation ID: "Search"
5. HTTP method: GET
6. Relative path: Leave empty (will use query parameters)

#### Configure Search Request Parameters

1. Add query parameters:
   - Name: `action`
     - Description: "API action to perform"
     - Required: Yes
     - Type: string
     - Default value: "query"
   - Name: `format`
     - Description: "Response format"
     - Required: Yes
     - Type: string
     - Default value: "json"
   - Name: `list`
     - Description: "Type of list to generate"
     - Required: Yes
     - Type: string
     - Default value: "search"
   - Name: `srsearch`
     - Description: "Search term"
     - Required: Yes
     - Type: string
   - Name: `srlimit`
     - Description: "Number of results to return (max 50)"
     - Required: No
     - Type: integer
     - Default value: 10

#### Configure Search Response

1. Click "Add default response"
2. Import from sample:
```json
{
  "batchcomplete": "",
  "continue": {
    "sroffset": 10,
    "continue": "-||"
  },
  "query": {
    "searchinfo": {
      "totalhits": 1234
    },
    "search": [
      {
        "ns": 0,
        "title": "Article Title",
        "pageid": 12345,
        "size": 4321,
        "wordcount": 567,
        "snippet": "Article snippet with <span class=\"searchmatch\">highlighted</span> terms",
        "timestamp": "2022-01-01T00:00:00Z"
      }
    ]
  }
}
```

#### Add a Page Content Operation

1. Click "New action"
2. Summary: "Get Page Content"
3. Description: "Retrieve the content of a Wikipedia page by its page ID"
4. Operation ID: "GetPageContent"
5. HTTP method: GET
6. Relative path: Leave empty (will use query parameters)

#### Configure Page Content Request Parameters

1. Add query parameters:
   - Name: `action`
     - Description: "API action to perform"
     - Required: Yes
     - Type: string
     - Default value: "query"
   - Name: `format`
     - Description: "Response format"
     - Required: Yes
     - Type: string
     - Default value: "json"
   - Name: `prop`
     - Description: "Properties to retrieve"
     - Required: Yes
     - Type: string
     - Default value: "extracts"
   - Name: `pageids`
     - Description: "Page ID of the Wikipedia article"
     - Required: Yes
     - Type: string
   - Name: `explaintext`
     - Description: "Return extracts as plain text instead of HTML"
     - Required: No
     - Type: boolean
     - Default value: true
   - Name: `exintro`
     - Description: "Return only the content before the first section"
     - Required: No
     - Type: boolean
     - Default value: false

#### Configure Page Content Response

1. Click "Add default response"
2. Import from sample:
```json
{
  "batchcomplete": "",
  "query": {
    "pages": {
      "12345": {
        "pageid": 12345,
        "ns": 0,
        "title": "Article Title",
        "extract": "This is the full or partial content of the Wikipedia article."
      }
    }
  }
}
```

### Step 5: Create and Test the Connector

1. Click "Create connector" to save your configuration
2. Go to the "Test" tab

#### Test the Search Operation

1. Select the "Search" operation
2. Enter a search term in the `srsearch` parameter (e.g., "Power Platform")
3. Click "Test operation"
4. Verify that you receive search results in the response

#### Test the Page Content Operation

1. Select the "GetPageContent" operation
2. Enter a page ID in the `pageids` parameter (use one from the search results)
3. Click "Test operation"
4. Verify that you receive the article content in the response

### Step 6: Use the Connector in Power Apps or Power Automate

#### In Power Apps

1. Create a new app or open an existing one
2. In the Data panel, click "Add data"
3. Find and select your Wikipedia connector
4. Create a connection (no credentials needed)
5. Use the connector in your app:
   ```
   Search(WikipediaConnector, "Power Platform")
   ```

#### In Power Automate

1. Create a new flow or edit an existing one
2. Add a new action
3. Search for and select your Wikipedia connector
4. Choose the operation (Search or GetPageContent)
5. Configure the parameters
6. Use the output in subsequent flow steps

### Troubleshooting

If you encounter issues:

1. **404 Not Found**: Verify the host and base URL
2. **Invalid Parameters**: Check the required parameters and their values
3. **Response Parsing Errors**: Ensure your response schema matches the actual API response

This completes the Wikipedia API custom connector example.

## Conclusion

Power Platform custom connectors provide a flexible way to integrate with external APIs. Whether you start from a Postman collection, build manually, or leverage Azure API Management, you can create connectors tailored to your specific needs.

Choose the method that best aligns with your existing tools, expertise, and requirements. For simple APIs like Wikipedia, manual creation is straightforward. For complex APIs with extensive documentation in Postman, importing a collection saves time. For APIs already managed in Azure, leveraging APIM ensures consistency.

Remember to consider the appropriate authentication method based on your API's security requirements, and thoroughly test your connector before using it in production applications or flows.
