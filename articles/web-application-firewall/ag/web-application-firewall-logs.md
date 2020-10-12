---
title: Monitorování protokolů pro Firewall webových aplikací Azure
description: Naučte se, jak povolit a spravovat protokoly a firewall webových aplikací Azure.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 10/25/2019
ms.author: victorh
ms.openlocfilehash: a84e48c7fbb6d63a4bf8946b66bd35f354643ccb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84753603"
---
# <a name="resource-logs-for-azure-web-application-firewall"></a>Protokoly prostředků pro Firewall webových aplikací Azure

Prostředky firewallu webových aplikací můžete monitorovat pomocí protokolů. Můžete ušetřit výkon, přístup a další data nebo je spotřebovat z prostředku pro účely monitorování.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="diagnostic-logs"></a>Diagnostické protokoly

Pomocí různých typů protokolů v Azure můžete spravovat a řešit potíže s aplikačními bránami. Některé z těchto protokolů jsou přístupné z webu Azure Portal. Všechny protokoly se dají extrahovat z úložiště objektů BLOB v Azure a zobrazovat v různých nástrojích, jako jsou [protokoly Azure monitor](../../azure-monitor/insights/azure-networking-analytics.md), excelu a Power BI. Další informace o různých typech protokolů najdete v následujícím seznamu:

* **Protokol aktivit**: k zobrazení všech operací odeslaných do vašeho předplatného Azure a jejich stavu můžete použít [protokoly aktivit Azure](../../azure-resource-manager/management/view-activity-logs.md) . Položky protokolu aktivit se ve výchozím nastavení shromažďují a můžete si je zobrazit na webu Azure Portal.
* **Přístup k protokolu prostředků**: pomocí tohoto protokolu můžete zobrazit vzory přístupu Application Gateway a analyzovat důležité informace. Patří sem IP adresa volajícího, požadovaná adresa URL, latence odpovědi, návratový kód a bajtů. Protokol přístupu se shromáždí každých 300 sekund. Tento protokol obsahuje jeden záznam na instanci Application Gateway. Instance Application Gateway je identifikována vlastností instanceId.
* **Protokol prostředku výkonu**: pomocí tohoto protokolu můžete zobrazit, jak probíhá Application Gateway instance. Tento protokol zachycuje informace o výkonu pro každou instanci, včetně celkového počtu zpracovaných požadavků, propustnosti v bajtech, celkových zpracovaných požadavků, počtu neúspěšných žádostí a stavu back-endu back-endu v pořádku. Protokol výkonu se shromáždí každých 60 sekund. Protokol výkonu je k dispozici pouze pro SKU v1. Pro SKU v2 použijte [metriky](../../application-gateway/application-gateway-metrics.md) pro data výkonu.
* **Protokol prostředků brány firewall**: pomocí tohoto protokolu můžete zobrazit požadavky, které jsou protokolovány buď pomocí režimu detekce, nebo prevence služby Application Gateway, která je nakonfigurována pomocí brány firewall webových aplikací.

> [!NOTE]
> Protokoly jsou k dispozici pouze pro prostředky nasazené v modelu nasazení Azure Resource Manager. Protokoly pro prostředky v modelu nasazení Classic nemůžete použít. Abyste lépe pochopili tyto dva modely, přečtěte si článek [principy správce prostředků nasazení a klasického nasazení](../../azure-resource-manager/management/deployment-models.md) .

Protokoly můžete ukládat třemi způsoby:

* **Učet úložiště**: Účty úložiště jsou nejvhodnější pro ukládání protokolů na delší dobu, které budete kontrolovat pouze v případě potřeby.
* **Centra událostí**: centra událostí jsou skvělou možností pro integraci s dalšími nástroji Siem (Security Information and Event Management), které vám umožní získat výstrahy na vaše prostředky.
* **Protokoly Azure monitor**: Azure monitor protokoly se nejlépe používají pro obecné monitorování vaší aplikace v reálném čase nebo při prohlížení trendů.

### <a name="enable-logging-through-powershell"></a>Povolit protokolování prostřednictvím PowerShellu

Protokolování aktivit je u každého prostředku Správce prostředků povolené automaticky. Chcete-li začít shromažďovat data dostupná prostřednictvím těchto protokolů, je nutné povolit protokolování přístupu a výkonu. Chcete-li povolit protokolování, použijte následující postup:

1. Poznamenejte si ID prostředku účtu úložiště, kam se data protokolu ukládají. Tato hodnota je ve formátu:/subscriptions/ \<subscriptionId\> /ResourceGroups/ \<resource group name\> /providers/Microsoft.Storage/storageAccounts/ \<storage account name\> . Můžete použít libovolný účet úložiště z vašeho předplatného. Tuto informaci najdete pomocí webu Azure Portal

    ![Portál: ID prostředku pro účet úložiště](../media/web-application-firewall-logs/diagnostics1.png)

2. Poznamenejte si ID prostředku brány Application Gateway, pro které je povolené protokolování. Tato hodnota je ve formátu:/subscriptions/ \<subscriptionId\> /ResourceGroups/ \<resource group name\> /providers/Microsoft.Network/applicationGateways/ \<application gateway name\> . Tuto informaci najdete pomocí webu Azure Portal.

    ![Portál: ID prostředku pro aplikační bránu](../media/web-application-firewall-logs/diagnostics2.png)

3. Pomocí následující rutiny prostředí PowerShell Povolte protokolování prostředků:

    ```powershell
    Set-AzDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name> -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> -Enabled $true     
    ```

> [!TIP]
>Protokoly aktivit nevyžadují samostatný účet úložiště. Za používání úložiště k protokolování přístupu a výkonu se účtují poplatky.

### <a name="enable-logging-through-the-azure-portal"></a>Povolit protokolování prostřednictvím webu Azure Portal

1. V Azure Portal vyhledejte prostředek a vyberte **nastavení diagnostiky**.

   Pro Application Gateway jsou k dispozici tři protokoly:

   * Přístup k protokolu
   * Protokol výkonu
   * Protokol brány firewall

2. Pokud chcete začít shromažďovat data, vyberte **zapnout diagnostiku**.

   ![Zapnutí diagnostiky][1]

3. Stránka **nastavení diagnostiky** poskytuje nastavení pro protokoly prostředků. V tomto příkladu Log Analytics ukládá protokoly. K uložení protokolů prostředků můžete použít taky centra událostí a účet úložiště.

   ![Spuštění procesu konfigurace][2]

5. Zadejte název nastavení, potvrďte nastavení a vyberte **Uložit**.

### <a name="activity-log"></a>Protokol aktivit

Azure ve výchozím nastavení generuje protokol aktivit. Protokoly se uchovávají po 90 dnech v úložišti protokolů událostí Azure. Další informace o těchto protokolech najdete v článku [zobrazení událostí a protokolu aktivit](../../azure-resource-manager/management/view-activity-logs.md) .

### <a name="access-log"></a>Přístup k protokolu

Protokol přístupu se vygeneruje jenom v případě, že jste ho povolili na každé instanci Application Gateway, jak je popsáno v předchozích krocích. Data se ukládají do účtu úložiště, který jste zadali při povolování protokolování. Každý přístup k Application Gateway je protokolován ve formátu JSON, jak je znázorněno v následujícím příkladu pro V1:

|Hodnota  |Popis  |
|---------|---------|
|instanceId     | Application Gateway instance, která zpracovala požadavek.        |
|IP adresa klienta     | Původní IP adresa pro požadavek.        |
|clientPort     | Původní port pro požadavek.       |
|httpMethod     | Metoda HTTP, kterou požadavek používá       |
|requestUri     | Identifikátor URI přijatého požadavku        |
|RequestQuery     | **Směrováno serverem**: instance fondu back-end, která odeslala požadavek.</br>**X-AzureApplicationGateway-log-ID**: ID korelace použité pro požadavek. Dá se použít k řešení problémů s přenosem na back-endové servery. </br>**Stav serveru**: kód odpovědi HTTP, který Application Gateway přijatý od back-endu.       |
|UserAgent     | Uživatelský agent z hlavičky požadavku HTTP.        |
|Stavu protokolu http     | Stavový kód HTTP se vrátil klientovi z Application Gateway.       |
|httpVersion     | Verze protokolu HTTP požadavku.        |
|receivedBytes     | Velikost přijatého paketu (v bajtech)        |
|sentBytes| Velikost odeslaného paketu (v bajtech).|
|timeTaken| Doba (v milisekundách), kterou vyžaduje zpracování žádosti a odpověď, která má být odeslána. Počítá se jako interval od času, kdy Application Gateway přijme první bajt požadavku HTTP do doby, kdy se dokončí operace odeslání odpovědi. Je důležité si uvědomit, že pole Time-Taken obvykle obsahuje čas, kdy se pakety požadavků a odpovědí cestují po síti. |
|sslEnabled| Zda komunikace s back-end fondy používala protokol TLS/SSL. Platné hodnoty jsou zapnuté a vypnuté.|
|Hostitel| Název hostitele, se kterým se odeslal požadavek na back-end Server. Pokud je přepsán back-end hostname, tento název bude odpovídat.|
|originalHost| Název hostitele, se kterým Application Gateway požadavek přijal z klienta.|
```json
{
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/PEERINGTEST/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
    "operationName": "ApplicationGatewayAccess",
    "timestamp": "2017-04-26T19:27:38Z",
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
V případě Application Gateway a WAF v2 se v protokolech zobrazí další informace:

|Hodnota  |Popis  |
|---------|---------|
|instanceId     | Application Gateway instance, která zpracovala požadavek.        |
|IP adresa klienta     | Původní IP adresa pro požadavek.        |
|clientPort     | Původní port pro požadavek.       |
|httpMethod     | Metoda HTTP, kterou požadavek používá       |
|requestUri     | Identifikátor URI přijatého požadavku        |
|UserAgent     | Uživatelský agent z hlavičky požadavku HTTP.        |
|Stavu protokolu http     | Stavový kód HTTP se vrátil klientovi z Application Gateway.       |
|httpVersion     | Verze protokolu HTTP požadavku.        |
|receivedBytes     | Velikost přijatého paketu (v bajtech)        |
|sentBytes| Velikost odeslaného paketu (v bajtech).|
|timeTaken| Doba (v milisekundách), kterou vyžaduje zpracování žádosti a odpověď, která má být odeslána. Počítá se jako interval od času, kdy Application Gateway přijme první bajt požadavku HTTP do doby, kdy se dokončí operace odeslání odpovědi. Je důležité si uvědomit, že pole Time-Taken obvykle obsahuje čas, kdy se pakety požadavků a odpovědí cestují po síti. |
|sslEnabled| Zda komunikace se fondy back-end používala protokol TLS. Platné hodnoty jsou zapnuté a vypnuté.|
|sslCipher| Šifrovací sada používaná pro komunikaci TLS (Pokud je povolený protokol TLS).|
|sslProtocol| Používá se protokol TLS (Pokud je povolený protokol TLS).|
|serverRouted| Back-end Server, na který Aplikační brána směruje požadavek.|
|serverStatus| Stavový kód HTTP back-end serveru.|
|serverResponseLatency| Latence odpovědi ze serveru back-end.|
|Hostitel| Adresa uvedená v hlavičce hostitele žádosti.|
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

### <a name="performance-log"></a>Protokol výkonu

Protokol výkonu je vygenerován pouze v případě, že jste jej povolili na každé instanci Application Gateway, jak je popsáno v předchozích krocích. Data se ukládají do účtu úložiště, který jste zadali při povolování protokolování. Data protokolu výkonu se generují v intervalu 1 minuty. Je k dispozici pouze pro SKU v1. Pro SKU v2 použijte [metriky](../../application-gateway/application-gateway-metrics.md) pro data výkonu. Zaprotokolují se následující data:


|Hodnota  |Popis  |
|---------|---------|
|instanceId     |  Instance Application Gateway, pro kterou se generují údaje o výkonu. Pro aplikační bránu s více instancemi je k dispozici jeden řádek na instanci.        |
|healthyHostCount     | Počet v nefunkčních hostitelích ve fondu back-end.        |
|unHealthyHostCount     | Počet nezdravých hostitelů ve fondu back-end.        |
|requestCount     | Počet zpracovaných požadavků.        |
|Latence | Průměrná latence (v milisekundách) požadavků z instance do back-endu, který obsluhuje požadavky. |
|failedRequestCount| Počet neúspěšných žádostí|
|throughput| Průměrná propustnost od posledního protokolu měřená v bajtech za sekundu|

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
> Latence se počítá od doby, kdy se první bajt požadavku HTTP přijme do doby, kdy se pošle poslední bajt odpovědi HTTP. Je to součet doby zpracování Application Gateway a navíc náklady na síť k back-endu a dobu, po kterou back-end zpracování žádosti vyžaduje.

### <a name="firewall-log"></a>Protokol brány firewall

Protokol brány firewall je vygenerován pouze v případě, že jste jej povolili pro každou bránu aplikace, jak je popsáno v předchozích krocích. Tento protokol také vyžaduje, aby byla na aplikační bráně nakonfigurovaná brána firewall webových aplikací. Data se ukládají do účtu úložiště, který jste zadali při povolování protokolování. Zaprotokolují se následující data:


|Hodnota  |Popis  |
|---------|---------|
|instanceId     | Instance Application Gateway, pro kterou se generují data brány firewall. Pro aplikační bránu s více instancemi je k dispozici jeden řádek na instanci.         |
|IP adresa klienta     |   Původní IP adresa pro požadavek.      |
|clientPort     |  Původní port pro požadavek.       |
|requestUri     | Adresa URL přijatého požadavku       |
|Typ rulesettype     | Typ sady pravidel Dostupná hodnota je OWASP.        |
|ruleSetVersion     | Použitá verze sady pravidel Dostupné hodnoty jsou 2.2.9 a 3,0.     |
|ruleId     | ID pravidla události triggeru        |
|zpráva     | Uživatelsky přívětivá zpráva pro aktivační událost. Další podrobnosti najdete v části Podrobnosti.        |
|akce     |  Akce prováděná na žádosti Dostupné hodnoty jsou blokované a povolené.      |
|webovém     | Lokalita, pro kterou se protokol vygeneroval. V současné době se v seznamu zobrazí pouze globální, protože pravidla jsou globální.|
|zobrazí     | Podrobnosti události aktivace.        |
|Podrobnosti. zpráva     | Popis pravidla        |
|Podrobnosti. data     | V žádosti, která se shodovala s pravidlem, se našla konkrétní data.         |
|details. File     | Konfigurační soubor, který obsahoval pravidlo.        |
|details. line     | Číslo řádku v konfiguračním souboru, který spustil událost.       |
|název hostitele   | Název hostitele nebo IP adresa Application Gateway.    |
|transactionId  | Jedinečné ID pro danou transakci, které pomáhá seskupovat více porušení pravidel, ke kterým došlo v rámci stejné žádosti.   |
|policyId   | Jedinečné ID zásady brány firewall přidružené k Application Gateway, naslouchacího procesu nebo cestě   |
|policyScope    | Umístěním hodnot zásad může být "globální", "naslouchací proces" nebo "umístění".   |
|policyScopeName   | Název objektu, na který se zásada aplikuje.    |

```json
{
  "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
  "operationName": "ApplicationGatewayFirewall",
  "time": "2017-03-20T15:52:09.1494499Z",
  "category": "ApplicationGatewayFirewallLog",
  "properties": {
      "instanceId": "ApplicationGatewayRole_IN_0",
      "clientIp": "52.161.109.147",
      "clientPort": "0",
      "requestUri": "/",
      "ruleSetType": "OWASP",
      "ruleSetVersion": "3.0",
      "ruleId": "920350",
      "ruleGroup": "920-PROTOCOL-ENFORCEMENT",
      "message": "Host header is a numeric IP address",
      "action": "Matched",
      "site": "Global",
      "details": {
        "message": "Warning. Pattern match \"^[\\\\d.:]+$\" at REQUEST_HEADERS:Host ....",
        "data": "127.0.0.1",
        "file": "rules/REQUEST-920-PROTOCOL-ENFORCEMENT.conf",
        "line": "791"
      },
      "hostname": "127.0.0.1",
      "transactionId": "16861477007022634343",
      "policyId": "/subscriptions/1496a758-b2ff-43ef-b738-8e9eb5161a86/resourceGroups/drewRG/providers/Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/perListener",
      "policyScope": "Listener",
      "policyScopeName": "httpListener1"
    }
  }
}

```

### <a name="view-and-analyze-the-activity-log"></a>Zobrazení a analýza protokolu aktivit

Data protokolu aktivit si můžete zobrazit použitím jedné z následujících metod:

* **Nástroje Azure**: Načtěte informace z protokolu aktivit prostřednictvím Azure PowerShellu, Azure CLI, rozhraní Azure REST API nebo webu Azure Portal. Podrobné pokyny k jednotlivým metodám najdete v článku o [operacích s protokoly aktivit ve Správci prostředků](../../azure-resource-manager/management/view-activity-logs.md).
* **Power BI**: Pokud ještě účet [Power BI](https://powerbi.microsoft.com/pricing) nemáte, můžete ho vyzkoušet zdarma. Pomocí [aplikací Power BI šablon](https://docs.microsoft.com/power-bi/service-template-apps-overview)můžete analyzovat data.

### <a name="view-and-analyze-the-access-performance-and-firewall-logs"></a>Zobrazení a analýza protokolů přístupu, výkonu a brány firewall

[Protokoly Azure monitor](../../azure-monitor/insights/azure-networking-analytics.md) můžou shromažďovat soubory protokolů čítačů a událostí z vašeho účtu služby Blob Storage. Zahrnuje vizualizace a výkonné vyhledávací funkce k analýze protokolů.

Můžete se také připojit k účtu úložiště a načíst položky protokolu JSON s protokoly přístupu a výkonu. Po stažení souborů JSON je můžete převést do formátu CSV a zobrazit si je v Excelu, Power BI nebo jiném nástroji s vizualizací dat.

> [!TIP]
> Pokud znáte Visual Studio a máte představu, jak u konstant a proměnných v jazyce C# měnit hodnoty, můžete použít [nástroje pro převedení protokolů](https://github.com/Azure-Samples/networking-dotnet-log-converter), které jsou k dispozici na GitHubu.
>
>

#### <a name="analyzing-access-logs-through-goaccess"></a>Analýza protokolů přístupu prostřednictvím GoAccess

Publikovali jsme Správce prostředků šablonu, která nainstaluje a spustí oblíbený analyzátor protokolů [GoAccess](https://goaccess.io/) pro Application Gateway protokoly přístupu. GoAccess poskytuje cennou statistiku přenosů HTTP, jako jsou například jedineční návštěvníci, požadované soubory, hostitelé, operační systémy, prohlížeče, stavové kódy HTTP a další. Další podrobnosti najdete v [souboru Readme ve složce šablony Správce prostředků v GitHubu](https://aka.ms/appgwgoaccessreadme).

## <a name="next-steps"></a>Další kroky

* Vizualizujte protokoly čítačů a událostí pomocí [Azure monitor protokolů](../../azure-monitor/insights/azure-networking-analytics.md).
* [Vizualizujte si protokol aktivit Azure pomocí Power BI](https://powerbi.microsoft.com/blog/monitor-azure-audit-logs-with-power-bi/) Blogový příspěvek.
* Umožňuje [Zobrazit a analyzovat protokoly aktivit Azure v Power BI a další](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) Blogový příspěvek.

[1]: ../media/web-application-firewall-logs/figure1.png
[2]: ../media/web-application-firewall-logs/figure2.png
[3]: ./media/application-gateway-diagnostics/figure3.png
[4]: ./media/application-gateway-diagnostics/figure4.png
[5]: ./media/application-gateway-diagnostics/figure5.png
[6]: ./media/application-gateway-diagnostics/figure6.png
[7]: ./media/application-gateway-diagnostics/figure7.png
[8]: ./media/application-gateway-diagnostics/figure8.png
[9]: ./media/application-gateway-diagnostics/figure9.png
[10]: ./media/application-gateway-diagnostics/figure10.png
