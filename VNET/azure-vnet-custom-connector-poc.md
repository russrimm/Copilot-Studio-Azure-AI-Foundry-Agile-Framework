# Azure VNET POC for On-Premise API Access from Power Platform

This POC guide outlines the steps to establish connectivity from Power Apps, Power Automate, and Copilot Studio to on-premise APIs using Azure Virtual Network and API Management.

## Phase 1: Setting Up Network Connectivity

### Step 1: Create a Virtual Network in Azure

1. Sign in to the Azure Portal (https://portal.azure.com)
2. Click on "Create a resource" > "Networking" > "Virtual Network"
3. Configure the basics:
   - **Subscription**: Select your subscription
   - **Resource Group**: Create a new one (e.g., `on-prem-api-rg`)
   - **Name**: Provide a name (e.g., `on-prem-api-vnet`)
   - **Region**: Select a region close to your on-premises location

4. Configure IP Addresses:
   - **IPv4 address space**: `10.0.0.0/16` (or as needed)
   - **Subnet name**: `default`
   - **Subnet address range**: `10.0.0.0/24`

5. Click "Review + create" and then "Create"

### Step 2: Set Up a Site-to-Site VPN or ExpressRoute

#### Option A: Site-to-Site VPN (more cost-effective)

1. Create a Virtual Network Gateway:
   - Go to "Create a resource" > "Networking" > "Virtual Network Gateway"
   - **Resource Group**: Select the existing resource group
   - **Name**: `on-prem-api-gateway`
   - **Region**: Same as your VNet
   - **Gateway type**: VPN
   - **VPN type**: Route-based
   - **SKU**: VpnGw1 (adjust based on your performance needs)
   - **Virtual network**: Select the VNet created earlier
   - **Gateway subnet address range**: `10.0.1.0/24`
   - **Public IP address**: Create new
   - Click "Review + create" and then "Create" (Note: This can take 30-45 minutes)

2. Configure your on-premises VPN device:
   - Download the VPN device configuration script from the Azure portal
   - Apply the configuration to your on-premises VPN device (router/firewall)

3. Create a Local Network Gateway:
   - Go to "Create a resource" > "Networking" > "Local Network Gateway"
   - **Name**: `on-prem-local-gateway`
   - **IP address**: Public IP address of your on-premises VPN device
   - **Address space**: IP address range of your on-premises network (e.g., `192.168.0.0/24`)
   - Click "Create"

4. Create a VPN Connection:
   - Navigate to your Virtual Network Gateway
   - Click "Connections" > "Add"
   - **Name**: `on-prem-connection`
   - **Connection type**: Site-to-site (IPsec)
   - **Local Network Gateway**: Select the one created
   - **Shared key (PSK)**: Create a secure pre-shared key
   - Click "OK"

#### Option B: ExpressRoute (for production environments with higher bandwidth requirements)

1. Order an ExpressRoute circuit through an Azure connectivity provider
2. Link your ExpressRoute circuit to your Azure VNet using an ExpressRoute Gateway
3. Configure ExpressRoute on your on-premises network devices

### Step 3: Configure Network Security Groups (NSGs)

1. Go to "Create a resource" > "Networking" > "Network security group"
2. **Name**: `on-prem-api-nsg`
3. Create rules to allow necessary traffic:
   - Allow HTTPS (port 443) from Azure to on-premises API servers
   - Allow API-specific ports needed for your application
4. Associate the NSG with the appropriate subnet in your VNet

### Step 4: Verify Connectivity

1. Deploy a test VM in the Azure VNet
2. Try to ping or connect to your on-premises API server
3. Troubleshoot any connectivity issues before proceeding

## Phase 2: Setting Up Azure API Management

### Step 1: Create an API Management Service

1. Go to "Create a resource" > "Integration" > "API Management"
2. Configure the basics:
   - **Resource Group**: Use the existing resource group
   - **Resource name**: `on-prem-api-apim`
   - **Region**: Same as your VNet
   - **Organization name**: Your company name
   - **Administrator email**: Your email
   - **Pricing tier**: Developer (for POC) or Premium (for production with VNET)

3. Click "Review + create" and then "Create" (deployment may take ~30 minutes)

### Step 2: Configure VNET Integration for API Management

1. Navigate to your API Management service
2. Go to "Settings" > "Virtual Network"
3. Set "Virtual Network" to "External"
4. Select your VNet and the appropriate subnet
5. Click "Apply"

### Step 3: Import/Create Your API

1. Go to your API Management service
2. Select "APIs" from the left menu
3. Click "Add API" and choose the appropriate option:
   - **OpenAPI**: If your on-premises API has an OpenAPI (Swagger) definition
   - **SOAP**: If your API is a SOAP service with a WSDL
   - **Blank API**: To manually configure endpoints

4. For a Blank API, configure:
   - **Display name**: Descriptive name for your API
   - **Name**: System name (e.g., `on-prem-api`)
   - **Web service URL**: Internal URL of your on-premises API (e.g., `http://192.168.1.100:8080/api`)
   - **URL scheme**: HTTPS
   - **API URL suffix**: Version or identifier (e.g., `v1`)

5. Click "Create"

### Step 4: Define API Operations

1. In your API, click "Add operation"
2. For each API endpoint, configure:
   - **Display name**: Human-readable name
   - **Name**: System identifier
   - **URL**: HTTP verb and relative path (e.g., `GET /customers`)
   - Add request parameters, headers, and body as needed
   - Configure response types

3. Test each operation using the "Test" tab

### Step 5: Configure Authentication and Policies

1. Go to the "Settings" tab of your API
2. Configure subscription requirements
3. Add policies as XML snippets:

```xml
<policies>
    <inbound>
        <base />
        <!-- Add authentication to backend -->
        <set-header name="Authorization" exists-action="override">
            <value>Bearer {{your-backend-token}}</value>
        </set-header>
        <!-- Add request validation if needed -->
    </inbound>
    <backend>
        <base />
    </backend>
    <outbound>
        <base />
        <!-- Modify response if needed -->
    </outbound>
    <on-error>
        <base />
    </on-error>
</policies>
```

4. Configure named values for secrets in the APIM settings

## Phase 3: Creating a Custom Power Platform Connector

### Step 1: Export the API from API Management

1. Go to your API in API Management
2. Select "Export" from the top menu
3. Choose "Power Platform" as the export format
4. Download the generated API definition file

### Step 2: Create a Custom Connector in Power Apps

1. Navigate to https://make.powerapps.com
2. Select your environment
3. Go to "Data" > "Custom connectors"
4. Click "+ New custom connector" > "Import an OpenAPI file"
5. Upload the exported API definition file
6. Configure the connector:
   - **General**: Verify the information and add an icon/description
   - **Security**: Configure authentication (typically OAuth 2.0 or API Key)
   - **Definition**: Review and update operations if needed
   - **Test**: Test the connector with your credentials

7. Click "Create connector"

### Step 3: Use the Connector in Power Apps

1. Create a new app or open an existing app
2. In the "Data" panel, click "Add data"
3. Find your custom connector under "Connectors"
4. Add the connector and provide any required authentication
5. Use the connector in your app:

```
// Example formula to call your API
ClearCollect(
    customers,
    YourConnector.GetCustomers()
)
```

### Step 4: Use the Connector in Power Automate

1. Create a new flow or edit an existing flow
2. Add a new step and search for your custom connector
3. Select an action from your connector
4. Configure the required parameters
5. Complete your flow by handling the response

### Step 5: Use the Connector in Copilot Studio

1. Navigate to Copilot Studio (https://copilotstudio.microsoft.com/)
2. Open or create a topic
3. Add an "Action" node to your conversation
4. Select "Power Automate flow" as the action type
5. Create a new flow that uses your custom connector
6. Configure the flow to pass relevant information from the conversation
7. Map the flow outputs to variables in Copilot Studio

## Testing and Validation

1. Test the end-to-end solution:
   - Verify connectivity from Power Apps to on-premises API
   - Test Power Automate flows with various inputs
   - Validate Copilot Studio interactions with the API

2. Monitor performance:
   - Check Azure API Management analytics
   - Monitor network traffic through the VPN/ExpressRoute
   - Watch for any throttling or latency issues

3. Troubleshoot common issues:
   - Network connectivity problems: Check NSGs, routing tables, and on-premises firewall
   - Authentication failures: Verify credentials and token configurations
   - Timeout errors: Check timeouts in APIM and Power Platform settings

## Next Steps After POC

1. Scale the solution:
   - Upgrade to Production tier for API Management
   - Implement high availability for the VPN/ExpressRoute connection
   - Consider geographic distribution if needed

2. Enhance security:
   - Implement Azure Private Link if applicable
   - Add API key rotation mechanisms
   - Configure more granular access controls

3. Operational considerations:
   - Set up monitoring and alerting
   - Create a backup and recovery plan
   - Document the architecture and operational procedures
