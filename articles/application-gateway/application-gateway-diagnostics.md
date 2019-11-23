---
title: Back-end health and diagnostic logs
titleSuffix: Azure Application Gateway
description: Learn how to enable and manage access logs and performance logs for Azure Application Gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/22/2019
ms.author: victorh
ms.openlocfilehash: bfae540af1c501c09ec026b97ac11e8a14b177a9
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326553"
---
# <a name="back-end-health-and-diagnostic-logs-for-application-gateway"></a>Back-end health and diagnostic logs for Application Gateway

You can monitor Azure Application Gateway resources in the following ways:

* [Back-end health](#back-end-health): Application Gateway provides the capability to monitor the health of the servers in the back-end pools through the Azure portal and through PowerShell. You can also find the health of the back-end pools through the performance diagnostic logs.

* [Logs](#diagnostic-logging): Logs allow for performance, access, and other data to be saved or consumed from a resource for monitoring purposes.

* [Metrics](application-gateway-metrics.md): Application Gateway has several metrics which help you verify that your system is performing as expected.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="back-end-health"></a>Back-end health

Application Gateway provides the capability to monitor the health of individual members of the back-end pools through the portal, PowerShell, and the command-line interface (CLI). You can also find an aggregated health summary of back-end pools through the performance diagnostic logs. 

The back-end health report reflects the output of the Application Gateway health probe to the back-end instances. When probing is successful and the back end can receive traffic, it's considered healthy. Otherwise, it's considered unhealthy.

> [!IMPORTANT]
> If there is a network security group (NSG) on an Application Gateway subnet, open port ranges 65503-65534 for v1 SKUs, and 65200-65535 for v2 SKUs on the Application Gateway subnet for inbound traffic. This port range is required for Azure infrastructure communication. Jsou chráněny (uzamknuty) s použitím certifikátů Azure. Without proper certificates, external entities, including the customers of those gateways, will not be able to initiate any changes on those endpoints.


### <a name="view-back-end-health-through-the-portal"></a>View back-end health through the portal

In the portal, back-end health is provided automatically. In an existing application gateway, select **Monitoring** > **Backend health**. 

Each member in the back-end pool is listed on this page (whether it's a NIC, IP, or FQDN). Back-end pool name, port, back-end HTTP settings name, and health status are shown. Valid values for health status are **Healthy**, **Unhealthy**, and **Unknown**.

> [!NOTE]
> If you see a back-end health status of **Unknown**, ensure that access to the back end is not blocked by an NSG rule, a user-defined route (UDR), or a custom DNS in the virtual network.

![Back-end health][10]

### <a name="view-back-end-health-through-powershell"></a>View back-end health through PowerShell

The following PowerShell code shows how to view back-end health by using the `Get-AzApplicationGatewayBackendHealth` cmdlet:

```powershell
Get-AzApplicationGatewayBackendHealth -Name ApplicationGateway1 -ResourceGroupName Contoso
```

### <a name="view-back-end-health-through-azure-cli"></a>View back-end health through Azure CLI

```azurecli
az network application-gateway show-backend-health --resource-group AdatumAppGatewayRG --name AdatumAppGateway
```

### <a name="results"></a>Výsledky

The following snippet shows an example of the response:

```json
{
"BackendAddressPool": {
    "Id": "/subscriptions/00000000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/appGatewayBackendPool"
},
"BackendHttpSettingsCollection": [
    {
    "BackendHttpSettings": {
        "Id": "/00000000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings"
    },
    "Servers": [
        {
        "Address": "hostname.westus.cloudapp.azure.com",
        "Health": "Healthy"
        },
        {
        "Address": "hostname.westus.cloudapp.azure.com",
        "Health": "Healthy"
        }
    ]
    }
]
}
```

## <a name="diagnostic-logging"></a>Diagnostic logs

You can use different types of logs in Azure to manage and troubleshoot application gateways. Některé z těchto protokolů jsou přístupné z webu Azure Portal. All logs can be extracted from Azure Blob storage and viewed in different tools, such as [Azure Monitor logs](../azure-monitor/insights/azure-networking-analytics.md), Excel, and Power BI. You can learn more about the different types of logs from the following list:

* **Activity log**: You can use [Azure activity logs](../monitoring-and-diagnostics/insights-debugging-with-events.md) (formerly known as operational logs and audit logs) to view all operations that are submitted to your Azure subscription, and their status. Položky protokolu aktivit se ve výchozím nastavení shromažďují a můžete si je zobrazit na webu Azure Portal.
* **Access log**: You can use this log to view Application Gateway access patterns and analyze important information. This includes the caller's IP, requested URL, response latency, return code, and bytes in and out. An access log is collected every 300 seconds. This log contains one record per instance of Application Gateway. The Application Gateway instance is identified by the instanceId property.
* **Performance log**: You can use this log to view how Application Gateway instances are performing. This log captures performance information for each instance, including total requests served, throughput in bytes, total requests served, failed request count, and healthy and unhealthy back-end instance count. A performance log is collected every 60 seconds. The Performance log is available only for the v1 SKU. For the v2 SKU, use [Metrics](application-gateway-metrics.md) for performance data.
* **Firewall log**: You can use this log to view the requests that are logged through either detection or prevention mode of an application gateway that is configured with the web application firewall.

> [!NOTE]
> Logs are available only for resources deployed in the Azure Resource Manager deployment model. You cannot use logs for resources in the classic deployment model. For a better understanding of the two models, see the [Understanding Resource Manager deployment and classic deployment](../azure-resource-manager/resource-manager-deployment-model.md) article.

Protokoly můžete ukládat třemi způsoby:

* **Učet úložiště**: Účty úložiště jsou nejvhodnější pro ukládání protokolů na delší dobu, které budete kontrolovat pouze v případě potřeby.
* **Event hubs**: Event hubs are a great option for integrating with other security information and event management (SIEM) tools to get alerts on your resources.
* **Azure Monitor logs**: Azure Monitor logs is best used for general real-time monitoring of your application or looking at trends.

### <a name="enable-logging-through-powershell"></a>Enable logging through PowerShell

Protokolování aktivit je u každého prostředku Správce prostředků povolené automaticky. You must enable access and performance logging to start collecting the data available through those logs. To enable logging, use the following steps:

1. Poznamenejte si ID prostředku účtu úložiště, kam se data protokolu ukládají. This value is of the form: /subscriptions/\<subscriptionId\>/resourceGroups/\<resource group name\>/providers/Microsoft.Storage/storageAccounts/\<storage account name\>. Můžete použít libovolný účet úložiště z vašeho předplatného. Tuto informaci najdete pomocí webu Azure Portal

    ![Portal: resource ID for storage account](./media/application-gateway-diagnostics/diagnostics1.png)

2. Note your application gateway's resource ID for which logging is enabled. This value is of the form: /subscriptions/\<subscriptionId\>/resourceGroups/\<resource group name\>/providers/Microsoft.Network/applicationGateways/\<application gateway name\>. Tuto informaci najdete pomocí webu Azure Portal.

    ![Portal: resource ID for application gateway](./media/application-gateway-diagnostics/diagnostics2.png)

3. Protokolování diagnostiky zapnete pomocí následující rutiny PowerShellu:

    ```powershell
    Set-AzDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name> -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> -Enabled $true     
    ```
    
> [!TIP] 
>Activity logs do not require a separate storage account. Za používání úložiště k protokolování přístupu a výkonu se účtují poplatky.

### <a name="enable-logging-through-the-azure-portal"></a>Povolit protokolování prostřednictvím webu Azure Portal

1. In the Azure portal, find your resource and select **Diagnostic settings**.

   For Application Gateway, three logs are available:

   * Access log
   * Performance log
   * Firewall log

2. To start collecting data, select **Turn on diagnostics**.

   ![Turning on diagnostics][1]

3. Stránka **Nastavení diagnostiky** obsahuje nastavení diagnostických protokolů. In this example, Log Analytics stores the logs. Diagnostické protokoly můžete ukládat také pomocí služby center událostí a účtu úložiště.

   ![Starting the configuration process][2]

5. Type a name for the settings, confirm the settings, and select **Save**.

### <a name="activity-log"></a>Protokol aktivit

Azure generates the activity log by default. The logs are preserved for 90 days in the Azure event logs store. Learn more about these logs by reading the [View events and activity log](../monitoring-and-diagnostics/insights-debugging-with-events.md) article.

### <a name="access-log"></a>Access log

The access log is generated only if you've enabled it on each Application Gateway instance, as detailed in the preceding steps. The data is stored in the storage account that you specified when you enabled the logging. Each access of Application Gateway is logged in JSON format, as shown in the following example for v1:

|Hodnota  |Popis  |
|---------|---------|
|instanceId     | Application Gateway instance that served the request.        |
|clientIP     | Originating IP for the request.        |
|clientPort     | Originating port for the request.       |
|httpMethod     | HTTP method used by the request.       |
|requestUri     | URI of the received request.        |
|RequestQuery     | **Server-Routed**: Back-end pool instance that was sent the request.</br>**X-AzureApplicationGateway-LOG-ID**: Correlation ID used for the request. It can be used to troubleshoot traffic issues on the back-end servers. </br>**SERVER-STATUS**: HTTP response code that Application Gateway received from the back end.       |
|UserAgent     | User agent from the HTTP request header.        |
|httpStatus     | HTTP status code returned to the client from Application Gateway.       |
|httpVersion     | HTTP version of the request.        |
|receivedBytes     | Size of packet received, in bytes.        |
|sentBytes| Size of packet sent, in bytes.|
|timeTaken| Length of time (in milliseconds) that it takes for a request to be processed and its response to be sent. This is calculated as the interval from the time when Application Gateway receives the first byte of an HTTP request to the time when the response send operation finishes. It's important to note that the Time-Taken field usually includes the time that the request and response packets are traveling over the network. |
|sslEnabled| Whether communication to the back-end pools used SSL. Valid values are on and off.|
|host| The hostname with which the request has been sent to the backend server. If backend hostname is being overridden, this name will reflect that.|
|originalHost| The hostname with which the request was received by the Application Gateway from the client.|
```json
{
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/PEERINGTEST/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
    "operationName": "ApplicationGatewayAccess",
    "time": "2017-04-26T19:27:38Z",
    "category": "ApplicationGatewayAccessLog",
    "properties": {
        "instanceId": "ApplicationGatewayRole_IN_0",
        "clientIP": "191.96.249.97",
        "clientPort": 46886,
        "httpMethod": "GET",
        "requestUri": "/phpmyadmin/scripts/setup.php",
        "requestQuery": "X-AzureApplicationGateway-CACHE-HIT=0&SERVER-ROUTED=10.4.0.4&X-AzureApplicationGateway-LOG-ID=874f1f0f-6807-41c9-b7bc-f3cfa74aa0b1&SERVER-STATUS=404",
        "userAgent": "-",
        "httpStatus": 404,
        "httpVersion": "HTTP/1.0",
        "receivedBytes": 65,
        "sentBytes": 553,
        "timeTaken": 205,
        "sslEnabled": "off",
        "host": "www.contoso.com",
        "originalHost": "www.contoso.com"
    }
}
```
For Application Gateway and WAF v2, the logs show a little more information:

|Hodnota  |Popis  |
|---------|---------|
|instanceId     | Application Gateway instance that served the request.        |
|clientIP     | Originating IP for the request.        |
|clientPort     | Originating port for the request.       |
|httpMethod     | HTTP method used by the request.       |
|requestUri     | URI of the received request.        |
|UserAgent     | User agent from the HTTP request header.        |
|httpStatus     | HTTP status code returned to the client from Application Gateway.       |
|httpVersion     | HTTP version of the request.        |
|receivedBytes     | Size of packet received, in bytes.        |
|sentBytes| Size of packet sent, in bytes.|
|timeTaken| Length of time (in **seconds**) that it takes for a request to be processed and its response to be sent. This is calculated as the interval from the time when Application Gateway receives the first byte of an HTTP request to the time when the response send operation finishes. It's important to note that the Time-Taken field usually includes the time that the request and response packets are traveling over the network. |
|sslEnabled| Whether communication to the back-end pools used SSL. Valid values are on and off.|
|sslCipher| Cipher suite being used for SSL communication (if SSL is enabled).|
|sslProtocol| SSL/TLS protocol being used (if SSL is enabled).|
|serverRouted| The backend server that application gateway routes the request to.|
|serverStatus| HTTP status code of the backend server.|
|serverResponseLatency| Latency of the response from the backend server.|
|host| Address listed in the host header of the request.|
```json
{
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/PEERINGTEST/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
    "operationName": "ApplicationGatewayAccess",
    "time": "2017-04-26T19:27:38Z",
    "category": "ApplicationGatewayAccessLog",
    "properties": {
        "instanceId": "appgw_1",
        "clientIP": "191.96.249.97",
        "clientPort": 46886,
        "httpMethod": "GET",
        "requestUri": "/phpmyadmin/scripts/setup.php",
        "userAgent": "-",
        "httpStatus": 404,
        "httpVersion": "HTTP/1.0",
        "receivedBytes": 65,
        "sentBytes": 553,
        "timeTaken": 205,
        "sslEnabled": "off",
        "sslCipher": "",
        "sslProtocol": "",
        "serverRouted": "104.41.114.59:80",
        "serverStatus": "200",
        "serverResponseLatency": "0.023",
        "host": "www.contoso.com",
    }
}
```

### <a name="performance-log"></a>Performance log

The performance log is generated only if you have enabled it on each Application Gateway instance, as detailed in the preceding steps. The data is stored in the storage account that you specified when you enabled the logging. The performance log data is generated in 1-minute intervals. It is available only for the v1 SKU. For the v2 SKU, use [Metrics](application-gateway-metrics.md) for performance data. The following data is logged:


|Hodnota  |Popis  |
|---------|---------|
|instanceId     |  Application Gateway instance for which performance data is being generated. For a multiple-instance application gateway, there is one row per instance.        |
|healthyHostCount     | Number of healthy hosts in the back-end pool.        |
|unHealthyHostCount     | Number of unhealthy hosts in the back-end pool.        |
|requestCount     | Number of requests served.        |
|latency | Average latency (in milliseconds) of requests from the instance to the back end that serves the requests. |
|failedRequestCount| Number of failed requests.|
|throughput| Average throughput since the last log, measured in bytes per second.|

```json
{
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
    "operationName": "ApplicationGatewayPerformance",
    "time": "2016-04-09T00:00:00Z",
    "category": "ApplicationGatewayPerformanceLog",
    "properties":
    {
        "instanceId":"ApplicationGatewayRole_IN_1",
        "healthyHostCount":"4",
        "unHealthyHostCount":"0",
        "requestCount":"185",
        "latency":"0",
        "failedRequestCount":"0",
        "throughput":"119427"
    }
}
```

> [!NOTE]
> Latency is calculated from the time when the first byte of the HTTP request is received to the time when the last byte of the HTTP response is sent. It's the sum of the Application Gateway processing time plus the network cost to the back end, plus the time that the back end takes to process the request.

### <a name="firewall-log"></a>Firewall log

The firewall log is generated only if you have enabled it for each application gateway, as detailed in the preceding steps. This log also requires that the web application firewall is configured on an application gateway. The data is stored in the storage account that you specified when you enabled the logging. The following data is logged:


|Hodnota  |Popis  |
|---------|---------|
|instanceId     | Application Gateway instance for which firewall data is being generated. For a multiple-instance application gateway, there is one row per instance.         |
|clientIp     |   Originating IP for the request.      |
|clientPort     |  Originating port for the request.       |
|requestUri     | URL of the received request.       |
|ruleSetType     | Rule set type. The available value is OWASP.        |
|ruleSetVersion     | Rule set version used. Available values are 2.2.9 and 3.0.     |
|ruleId     | Rule ID of the triggering event.        |
|zpráva     | User-friendly message for the triggering event. More details are provided in the details section.        |
|action     |  Action taken on the request. Available values are Matched and Blocked.      |
|site     | Site for which the log was generated. Currently, only Global is listed because rules are global.|
|details     | Details of the triggering event.        |
|details.message     | Description of the rule.        |
|details.data     | Specific data found in request that matched the rule.         |
|details.file     | Configuration file that contained the rule.        |
|details.line     | Line number in the configuration file that triggered the event.       |
|hostname   | Hostname or IP address of the Application Gateway.    |
|transactionId  | Unique ID for a given transaction which helps group multiple rule violations that occurred within the same request.   |

```json
{
  "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
  "operationName": "ApplicationGatewayFirewall",
  "time": "2017-03-20T15:52:09.1494499Z",
  "category": "ApplicationGatewayFirewallLog",
  "properties": {
    "instanceId": "ApplicationGatewayRole_IN_0",
    "clientIp": "104.210.252.3",
    "clientPort": "4835",
    "requestUri": "/?a=%3Cscript%3Ealert(%22Hello%22);%3C/script%3E",
    "ruleSetType": "OWASP",
    "ruleSetVersion": "3.0",
    "ruleId": "941320",
    "message": "Possible XSS Attack Detected - HTML Tag Handler",
    "action": "Blocked",
    "site": "Global",
    "details": {
      "message": "Warning. Pattern match \"<(a|abbr|acronym|address|applet|area|audioscope|b|base|basefront|bdo|bgsound|big|blackface|blink|blockquote|body|bq|br|button|caption|center|cite|code|col|colgroup|comment|dd|del|dfn|dir|div|dl|dt|em|embed|fieldset|fn|font|form|frame|frameset|h1|head|h ...\" at ARGS:a.",
      "data": "Matched Data: <script> found within ARGS:a: <script>alert(\\x22hello\\x22);</script>",
      "file": "rules/REQUEST-941-APPLICATION-ATTACK-XSS.conf",
      "line": "865"
    }
    "hostname": "40.90.218.100", 
    "transactionId": "AYAcUqAcAcAcAcAcASAcAcAc"
  }
} 

```

### <a name="view-and-analyze-the-activity-log"></a>Zobrazení a analýza protokolu aktivit

Data protokolu aktivit si můžete zobrazit použitím jedné z následujících metod:

* **Nástroje Azure**: Načtěte informace z protokolu aktivit prostřednictvím Azure PowerShellu, Azure CLI, rozhraní Azure REST API nebo webu Azure Portal. Podrobné pokyny k jednotlivým metodám najdete v článku o [operacích s protokoly aktivit ve Správci prostředků](../azure-resource-manager/resource-group-audit.md).
* **Power BI**: Pokud ještě účet [Power BI](https://powerbi.microsoft.com/pricing) nemáte, můžete ho vyzkoušet zdarma. By using the [Power BI template apps](https://docs.microsoft.com/power-bi/service-template-apps-overview), you can analyze your data.

### <a name="view-and-analyze-the-access-performance-and-firewall-logs"></a>View and analyze the access, performance, and firewall logs

[Azure Monitor logs](../azure-monitor/insights/azure-networking-analytics.md) can collect the counter and event log files from your Blob storage account. Zahrnuje vizualizace a výkonné vyhledávací funkce k analýze protokolů.

Můžete se také připojit k účtu úložiště a načíst položky protokolu JSON s protokoly přístupu a výkonu. Po stažení souborů JSON je můžete převést do formátu CSV a zobrazit si je v Excelu, Power BI nebo jiném nástroji s vizualizací dat.

> [!TIP]
> Pokud znáte Visual Studio a máte představu, jak u konstant a proměnných v jazyce C# měnit hodnoty, můžete použít [nástroje pro převedení protokolů](https://github.com/Azure-Samples/networking-dotnet-log-converter), které jsou k dispozici na GitHubu.
> 
> 

#### <a name="analyzing-access-logs-through-goaccess"></a>Analyzing Access logs through GoAccess

We have published a Resource Manager template that installs and runs the popular [GoAccess](https://goaccess.io/) log analyzer for Application Gateway Access Logs. GoAccess provides valuable HTTP traffic statistics such as Unique Visitors, Requested Files, Hosts, Operating Systems, Browsers, HTTP Status codes and more. For more details, please see the [Readme file in the Resource Manager template folder in GitHub](https://aka.ms/appgwgoaccessreadme).

## <a name="next-steps"></a>Další kroky

* Visualize counter and event logs by using [Azure Monitor logs](../azure-monitor/insights/azure-networking-analytics.md).
* [Visualize your Azure activity log with Power BI](https://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) blog post.
* [View and analyze Azure activity logs in Power BI and more](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) blog post.

[1]: ./media/application-gateway-diagnostics/figure1.png
[2]: ./media/application-gateway-diagnostics/figure2.png
[3]: ./media/application-gateway-diagnostics/figure3.png
[4]: ./media/application-gateway-diagnostics/figure4.png
[5]: ./media/application-gateway-diagnostics/figure5.png
[6]: ./media/application-gateway-diagnostics/figure6.png
[7]: ./media/application-gateway-diagnostics/figure7.png
[8]: ./media/application-gateway-diagnostics/figure8.png
[9]: ./media/application-gateway-diagnostics/figure9.png
[10]: ./media/application-gateway-diagnostics/figure10.png
