---
title: Sledování protokolů brány firewall pro webové aplikace Azure
description: Zjistěte, jak povolit a spravovat protokoly a pro Azure Web Application Firewall
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 10/25/2019
ms.author: victorh
ms.openlocfilehash: cb1af86e04c0b4ba0b59398161fa111fd8065042
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81310055"
---
# <a name="diagnostic-logs-for-azure-web-application-firewall"></a>Diagnostické protokoly pro bránu firewall webových aplikací Azure

Prostředky brány firewall webových aplikací můžete sledovat pomocí protokolů. Můžete uložit výkon, přístup a další data nebo je využívat z prostředku pro účely monitorování.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="diagnostic-logs"></a>Diagnostické protokoly

Ke správě a odstraňování problémů s aplikačními bránami můžete v Azure použít různé typy protokolů. Některé z těchto protokolů jsou přístupné z webu Azure Portal. Všechny protokoly lze extrahovat z úložiště objektů Blob Azure a zobrazit v různých nástrojích, jako jsou [protokoly Azure Monitor ,](../../azure-monitor/insights/azure-networking-analytics.md)Excel a Power BI. Další informace o různých typech protokolů naleznete v následujícím seznamu:

* **Protokol aktivit**: [Protokoly aktivit Azure](../../azure-resource-manager/management/view-activity-logs.md) (dříve označované jako provozní protokoly a protokoly auditu) můžete zobrazit všechny operace, které jsou odeslány do předplatného Azure a jejich stav. Položky protokolu aktivit se ve výchozím nastavení shromažďují a můžete si je zobrazit na webu Azure Portal.
* **Protokol přístupu**: Tento protokol můžete použít k zobrazení vzorů přístupu brány aplikace a k analýze důležitých informací. To zahrnuje IP volajícího, požadovanou adresu URL, latenci odezvy, návratový kód a bajty dovnitř a ven. Protokol přístupu se shromažďuje každých 300 sekund. Tento protokol obsahuje jeden záznam na instanci aplikační brány. Instance Application Gateway je identifikována vlastností instanceId.
* **Protokol výkonu**: Tento protokol můžete použít k zobrazení výkonu instancí brány aplikace. Tento protokol zachycuje informace o výkonu pro každou instanci, včetně celkového počtu obsluhovaných požadavků, propustnost v bajtů, celkový počet obsluhovaných požadavků, počet neúspěšných požadavků a počet instancí v pořádku a není v pořádku. Protokol výkonu se shromažďuje každých 60 sekund. Protokol výkonu je k dispozici pouze pro skladovou položku v1. Pro skladovou položku v2 použijte [metriky](../../application-gateway/application-gateway-metrics.md) pro data o výkonu.
* **Protokol brány firewall**: Tento protokol můžete použít k zobrazení požadavků protokolovaných prostřednictvím režimu zjišťování nebo prevence aplikační brány, která je nakonfigurována pomocí brány firewall webové aplikace.

> [!NOTE]
> Protokoly jsou k dispozici jenom pro prostředky nasazené v modelu nasazení Azure Resource Manager. Protokoly nelze použít pro prostředky v modelu klasického nasazení. Pro lepší pochopení těchto dvou modelů, [najdete v článku Principy nasazení Resource Manager a klasické nasazení.](../../azure-resource-manager/management/deployment-models.md)

Protokoly můžete ukládat třemi způsoby:

* **Učet úložiště**: Účty úložiště jsou nejvhodnější pro ukládání protokolů na delší dobu, které budete kontrolovat pouze v případě potřeby.
* **Centra událostí**: Centra událostí jsou skvělou volbou pro integraci s dalšími nástroji pro správu informací o zabezpečení a událostí (SIEM), abyste získali upozornění na vaše prostředky.
* **Protokoly Azure Monitor**: Protokoly Azure Monitor se nejlépe používá pro obecné monitorování vaší aplikace v reálném čase nebo při pohledu na trendy.

### <a name="enable-logging-through-powershell"></a>Povolení protokolování pomocí PowerShellu

Protokolování aktivit je u každého prostředku Správce prostředků povolené automaticky. Chcete-li zahájit shromažďování dat dostupných prostřednictvím těchto protokolů, musíte povolit protokolování přístupu a výkonu. Chcete-li protokolování povolit, postupujte takto:

1. Poznamenejte si ID prostředku účtu úložiště, kam se data protokolu ukládají. Tato hodnota má formát: /subscriptions/\<ID předplatného\>/resourceGroups/\<název skupiny prostředků\>/providers/Microsoft.Storage/storageAccounts/\<název účtu úložiště\>. Můžete použít libovolný účet úložiště z vašeho předplatného. Tuto informaci najdete pomocí webu Azure Portal

    ![Portál: ID prostředku pro účet úložiště](../media/web-application-firewall-logs/diagnostics1.png)

2. Poznamenejte si ID prostředku vaší brány aplikace, pro které je povoleno protokolování. Tato\<hodnota je formuláře: /subscriptions/\>subscriptionId\</resourceGroups/ název\>skupiny prostředků /providers/Microsoft.Network/applicationGateways/\<název\>brány aplikace . Tuto informaci najdete pomocí webu Azure Portal.

    ![Portál: ID prostředku pro aplikační bránu](../media/web-application-firewall-logs/diagnostics2.png)

3. Protokolování diagnostiky zapnete pomocí následující rutiny PowerShellu:

    ```powershell
    Set-AzDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name> -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> -Enabled $true     
    ```

> [!TIP]
>Protokoly aktivit nevyžadují samostatný účet úložiště. Za používání úložiště k protokolování přístupu a výkonu se účtují poplatky.

### <a name="enable-logging-through-the-azure-portal"></a>Povolit protokolování prostřednictvím webu Azure Portal

1. Na webu Azure Portal najděte svůj prostředek a vyberte **Nastavení diagnostiky**.

   Pro aplikační bránu jsou k dispozici tři protokoly:

   * Protokol přístupu
   * Protokol výkonu
   * Protokol brány firewall

2. Chcete-li začít shromažďovat data, vyberte **možnost Zapnout diagnostiku**.

   ![Zapnutí diagnostiky][1]

3. Stránka **Nastavení diagnostiky** obsahuje nastavení diagnostických protokolů. V tomto příkladu Log Analytics ukládá protokoly. Diagnostické protokoly můžete ukládat také pomocí služby center událostí a účtu úložiště.

   ![Spuštění procesu konfigurace][2]

5. Zadejte název nastavení, potvrďte nastavení a vyberte **Uložit**.

### <a name="activity-log"></a>Protokol aktivit

Azure ve výchozím nastavení generuje protokol aktivit. Protokoly jsou zachovány po dobu 90 dnů v úložišti protokolů událostí Azure. Další informace o těchto protokolech najdete v zobrazení [událostí a protokol aktivit](../../azure-resource-manager/management/view-activity-logs.md) článku.

### <a name="access-log"></a>Protokol přístupu

Protokol přístupu se generuje pouze v případě, že jste jej povolili v každé instanci aplikační brány, jak je podrobně popsáno v předchozích krocích. Data jsou uložena v účtu úložiště, který jste zadali při povolení protokolování. Každý přístup aplikační brány je zaznamenán ve formátu JSON, jak je znázorněno v následujícím příkladu pro v1:

|Hodnota  |Popis  |
|---------|---------|
|instanceId     | Instance Application Gateway, která obsluhovala požadavek.        |
|clientIP     | Původní IP pro požadavek.        |
|clientPort     | Původní port pro požadavek.       |
|httpMetoda     | Metoda HTTP použitá požadavkem.       |
|Requesturi     | Identifikátor URI přijatého požadavku.        |
|Požadavek na dotaz     | **Server-Routed**: Back-end pool instance, která byla odeslána požadavek.</br>**X-AzureApplicationGateway-LOG-ID**: ID korelace použité pro požadavek. Lze jej použít k řešení problémů s provozem na serverech back-end. </br>**STAV SERVERU:** Kód odpovědi HTTP, který aplikace gateway obdržela ze back-endu.       |
|Useragent     | Uživatelský agent z hlavičky požadavku HTTP.        |
|httpStatus     | Stavový kód HTTP vrácený klientovi z aplikační brány.       |
|httpVersion     | HTTP verze požadavku.        |
|přijaté bajty     | Velikost přijatého paketu v bajtech.        |
|sentBytes| Velikost odeslaného paketu v bajtech.|
|timeTaken| Doba (v milisekundách), která trvá pro zpracování požadavku a jeho odpověď, která má být odeslána. Vypočítá se jako interval od okamžiku, kdy brána aplikace obdrží první bajt požadavku HTTP, do okamžiku dokončení operace odeslání odpovědi. Je důležité si uvědomit, že časové pole obvykle zahrnuje čas, kdy pakety požadavku a odpovědi cestují po síti. |
|sslEnabled| Zda komunikace s back-end fondy používá TLS/SSL. Platné hodnoty jsou zapínat a vypínat.|
|host| Název hostitele, se kterým byl požadavek odeslán na server back-end. Pokud back-end název hostitele je přepsána, tento název bude odrážet.|
|originalHost| Název hostitele, se kterým byl požadavek přijat aplikační bránou od klienta.|
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
Pro aplikační bránu a WAF v2, protokoly zobrazit trochu více informací:

|Hodnota  |Popis  |
|---------|---------|
|instanceId     | Instance Application Gateway, která obsluhovala požadavek.        |
|clientIP     | Původní IP pro požadavek.        |
|clientPort     | Původní port pro požadavek.       |
|httpMetoda     | Metoda HTTP použitá požadavkem.       |
|Requesturi     | Identifikátor URI přijatého požadavku.        |
|Useragent     | Uživatelský agent z hlavičky požadavku HTTP.        |
|httpStatus     | Stavový kód HTTP vrácený klientovi z aplikační brány.       |
|httpVersion     | HTTP verze požadavku.        |
|přijaté bajty     | Velikost přijatého paketu v bajtech.        |
|sentBytes| Velikost odeslaného paketu v bajtech.|
|timeTaken| Doba (v milisekundách), která trvá pro zpracování požadavku a jeho odpověď, která má být odeslána. Vypočítá se jako interval od okamžiku, kdy brána aplikace obdrží první bajt požadavku HTTP, do okamžiku dokončení operace odeslání odpovědi. Je důležité si uvědomit, že časové pole obvykle zahrnuje čas, kdy pakety požadavku a odpovědi cestují po síti. |
|sslEnabled| Zda komunikace s back-end fondy používá TLS. Platné hodnoty jsou zapínat a vypínat.|
|sslCipher| Cipher suite se používá pro komunikaci TLS (pokud je povolena TLS).|
|protokol ssl| Používaný protokol TLS (pokud je povolen protokol TLS).|
|serverSměrováno| Back-endový server, na který brána aplikace směruje požadavek.|
|serverStatus| Stavový kód HTTP back-endového serveru.|
|serverResponseLatency| Latence odpovědi z back-endového serveru.|
|host| Adresa uvedená v hlavičce hostitele požadavku.|
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

Protokol výkonu je generován pouze v případě, že jste jej povolili v každé instanci aplikační brány, jak je podrobně popsáno v předchozích krocích. Data jsou uložena v účtu úložiště, který jste zadali při povolení protokolování. Data protokolu výkonu jsou generována v intervalech 1 minuty. Je k dispozici pouze pro skladovou položku v1. Pro skladovou položku v2 použijte [metriky](../../application-gateway/application-gateway-metrics.md) pro data o výkonu. Jsou zaznamenána následující data:


|Hodnota  |Popis  |
|---------|---------|
|instanceId     |  Instance application gateway, pro kterou jsou generována data o výkonu. Pro aplikační bránu s více instancemi je jeden řádek na instanci.        |
|healthyHostCount     | Počet zdravých hostitelů v back-endfondu.        |
|unHealthyHostCount     | Počet hostitelů není v pořádku v back-end fondu.        |
|requestCount     | Počet doručených žádostí.        |
|Latence | Průměrná latence (v milisekundách) požadavků z instance na back-end, který obsluhuje požadavky. |
|failedRequestCount| Počet neúspěšných požadavků.|
|throughput| Průměrná propustnost od posledního protokolu měřená v bajtech za sekundu.|

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
> Latence se počítá od okamžiku, kdy je přijat první bajt požadavku HTTP, do okamžiku odeslání posledního bajtu odpovědi HTTP. Je to součet doby zpracování aplikační brány plus náklady na síť do back-endu plus čas, který back-end trvá ke zpracování požadavku.

### <a name="firewall-log"></a>Protokol brány firewall

Protokol brány firewall je generován pouze v případě, že jste jej povolili pro každou aplikační bránu, jak je podrobně popsáno v předchozích krocích. Tento protokol také vyžaduje, aby brána firewall webové aplikace byla nakonfigurována v bráně aplikace. Data jsou uložena v účtu úložiště, který jste zadali při povolení protokolování. Jsou zaznamenána následující data:


|Hodnota  |Popis  |
|---------|---------|
|instanceId     | Instance Application Gateway, pro kterou jsou generována data brány firewall. Pro aplikační bránu s více instancemi je jeden řádek na instanci.         |
|clientIp     |   Původní IP pro požadavek.      |
|clientPort     |  Původní port pro požadavek.       |
|Requesturi     | URL přijaté žádosti.       |
|ruleSetType     | Typ sady pravidel. Dostupná hodnota je OWASP.        |
|ruleSetVersion     | Použitá verze sady pravidel. Dostupné hodnoty jsou 2.2.9 a 3.0.     |
|ruleId     | ID pravidla aktivační události.        |
|zpráva     | Uživatelsky přívětivá zpráva pro aktivační událost. Další podrobnosti jsou uvedeny v sekci podrobnosti.        |
|action     |  Opatření přijatá na základě žádosti. Dostupné hodnoty jsou blokovány a povoleny.      |
|Stránky     | Pracoviště, pro které byl protokol vygenerován. V současné době je uveden pouze globální, protože pravidla jsou globální.|
|Podrobnosti     | Podrobnosti o aktivační události.        |
|details.message     | Popis pravidla.        |
|details.data     | Konkrétní data nalezená v požadavku, která odpovídala pravidlu.         |
|podrobnosti.soubor     | Konfigurační soubor, který obsahoval pravidlo.        |
|details.line     | Číslo řádku v konfiguračním souboru, který událost spustil.       |
|Hostname   | Název hostitele nebo IP adresa aplikační brány.    |
|transactionId  | Jedinečné ID pro danou transakci, které pomáhá seskupit více porušení pravidel, ke kterým došlo v rámci stejnéžádosti.   |
|policyId   | Jedinečné ID zásad brány firewall přidružené k bráně aplikace, naslouchací proces nebo cesta.   |
|policyScope    | Umístění zásady - hodnoty mohou být "Globální", "Naslouchací proces" nebo "Umístění".   |
|policyScopeName   | Název objektu, kde je použita zásada.    |

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
* **Power BI**: Pokud ještě účet [Power BI](https://powerbi.microsoft.com/pricing) nemáte, můžete ho vyzkoušet zdarma. Pomocí [aplikací pro šablony Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview)můžete data analyzovat.

### <a name="view-and-analyze-the-access-performance-and-firewall-logs"></a>Zobrazení a analýza protokolů přístupu, výkonu a brány firewall

[Protokoly Azure Monitor](../../azure-monitor/insights/azure-networking-analytics.md) můžete shromažďovat soubory protokolu čítače a události z vašeho účtu úložiště objektů Blob. Zahrnuje vizualizace a výkonné vyhledávací funkce k analýze protokolů.

Můžete se také připojit k účtu úložiště a načíst položky protokolu JSON s protokoly přístupu a výkonu. Po stažení souborů JSON je můžete převést do formátu CSV a zobrazit si je v Excelu, Power BI nebo jiném nástroji s vizualizací dat.

> [!TIP]
> Pokud znáte Visual Studio a máte představu, jak u konstant a proměnných v jazyce C# měnit hodnoty, můžete použít [nástroje pro převedení protokolů](https://github.com/Azure-Samples/networking-dotnet-log-converter), které jsou k dispozici na GitHubu.
>
>

#### <a name="analyzing-access-logs-through-goaccess"></a>Analýza protokolů přístupu prostřednictvím aplikace GoAccess

Publikovali jsme šablonu Správce prostředků, která nainstaluje a spustí populární analyzátor protokolů [GoAccess](https://goaccess.io/) pro protokoly přístupu k aplikační bráně. GoAccess poskytuje cenné statistiky http provozu, jako jsou unikátní návštěvníci, požadované soubory, hostitelé, operační systémy, prohlížeče, http stavové kódy a další. Další podrobnosti najdete [v souboru Readme ve složce šablony Správce prostředků na GitHubu](https://aka.ms/appgwgoaccessreadme).

## <a name="next-steps"></a>Další kroky

* Vizualizujte čítač a protokoly událostí pomocí [protokolů Azure Monitor](../../azure-monitor/insights/azure-networking-analytics.md).
* [Vizualizujte svůj protokol aktivit Azure pomocí](https://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) příspěvku blogu Power BI.
* [Zobrazte a analyzujte protokoly aktivit Azure v Power BI a další](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) blogové matné příspěvky.

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
