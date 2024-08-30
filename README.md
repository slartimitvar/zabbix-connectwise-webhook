# ConnectWise Manage PSA webhook

This guide describes how to integrate a Zabbix 6.4 installation with ConnectWise Manage PSA using the Zabbix webhook feature. It provides instructions on setting up a media type, API authentication and an action in Zabbix.
Please note that recovery and update operations are supported only for trigger-based events.

# Setting up ConnectWise

1. First, create an APImember user for creating incidents.

2. Assign a role to this user which allows it to:

   - Create new tickets
   - Update ticket status
   - Add ticket notes
   - Set ticket resolution flag

3. Generate an API Keys set for the APIMember on its *API Keys* tab

4. Generate a ConnectWise Client ID to allow API interactivity

   - Go to https://developer.connectwise.com/ClientID
   - Click the Create new integration button and fill out the request details
   - Make sure to select Manage as your product
   - Wait for ConnectWise to provision the Client ID for you
   
# Setting up webhook in Zabbix

1. Under "Administration -> Media types", import the zbx_mediatype_cwpsa-6.4.yaml file.
   
2. Open the newly added *ConnectWise Manage PSA* media type and replace all Parameter value *<placeholders>* with your values.

   - Parameterss with names starting with *cwpsa_api_* are required for authentication
     - *cwpsa_api_clientid* is the Client ID string you generated from ConnectWise above
     - *cwpsa_api_user* is the concatenation of the APIMember's company name and public API key with a + symbol between (e.g. xyzcorp+7c21wqSxILBLqC98J)
     - *cwpsa_api_password* is the APIMember's private API key
     - *cwpsa_api_url* is the full path to the service tickets API on your ConnectWise Manage instsance (e.g. https://yourcw.com/v4_6_release/apis/3.0/service/tickets/)
   - Parameters starting with *cwpsa_cmpy_* are optionally used to map tickets to their correct companies in ConnectWise
      - This implementation is rather simplistic in that it uses the first three letters of the Zabbix Problem alert subject to identify the company
      - *cwpsa_cmpy_DEFAULT* must be used and must be set to the ConnectWise Client ID for the default company you wish to create tickets against (This is a Catchall scenario)
      - *cwpsa_cmpy_XXX*, as am example will contain the ConnectWise Client ID for the company you wish to assign a ticket who's Problem alert subject starts with XXX
         - You can add a new Parameter called *cwpsa_cmpy_ABC* with your Australian Broadcasting Corporation customer's Client ID as its value
         - Now if you trigger a Zabbix Problem with an alert subject of "ABC - Monitored host down", this webhook will create the ConnectWise ticket against the Australian Broadcasting Corporation company
      - Add as many *cwpsa_cmpy_???* Parameters as you need to map all your monitored customers to their ConnectWise company record
