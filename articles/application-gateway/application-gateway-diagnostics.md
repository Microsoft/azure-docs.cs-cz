---
title: Monitorování protokolů přístupu, protokolů výkonu, stavu back-endu a metrik pro Azure Application Gateway
description: Naučte se, jak povolit a spravovat protokoly přístupu a protokoly výkonu pro Azure Application Gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 3/28/2019
ms.author: victorh
ms.openlocfilehash: d9b0c551cdfb92b380a967aaa5bdce7c278fd39e
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2019
ms.locfileid: "70183574"
---
# <a name="back-end-health-diagnostic-logs-and-metrics-for-application-gateway"></a>Stav back-endu, diagnostické protokoly a metriky pro Application Gateway

Pomocí Azure Application Gateway můžete monitorovat prostředky následujícími způsoby:

* [Stav back-endu](#back-end-health): Application Gateway poskytuje možnost monitorovat stav serverů ve fondech back-endu prostřednictvím Azure Portal a prostředí PowerShell. V protokolech diagnostiky výkonu můžete také najít stav fondů back-end.

* [Protokoly](#diagnostic-logging): Protokoly umožňují, aby se pro účely monitorování uložily nebo využily údaje o výkonu, přístupu a dalších datech.

* [Metriky](#metrics): Application Gateway v současné době má sedm metrik pro zobrazení čítačů výkonu.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="back-end-health"></a>Stav back-endu

Application Gateway poskytuje možnost monitorování stavu jednotlivých členů fondů back-endu prostřednictvím portálu, PowerShellu a rozhraní příkazového řádku (CLI). Agregovaný souhrn stavu fondů back-end můžete také najít prostřednictvím protokolů diagnostiky výkonu. 

Zpráva o stavu back-endu odráží výstup Application Gateway sondy stavu do back-endové instance. Když je zjišťování úspěšné a back-end může přijímat přenosy, považuje se za dobrý stav. V opačném případě se považuje za není v pořádku.

> [!IMPORTANT]
> Pokud je v Application Gateway podsíti skupina zabezpečení sítě (NSG), otevřete rozsahy portů 65503-65534 v podsíti Application Gateway pro příchozí provoz. Tento rozsah portů je nutný pro komunikaci s infrastrukturou Azure. Jsou chráněny (uzamknuty) s použitím certifikátů Azure. Bez správných certifikátů nebudou externí entity, včetně zákazníků těchto bran, moci na těchto koncových bodech iniciovat žádné změny.


### <a name="view-back-end-health-through-the-portal"></a>Zobrazení stavu back-endu prostřednictvím portálu

V portálu je stav back-end k dispozici automaticky. V existující aplikační bráně vyberte **monitorování** > **stavu back-endu**. 

Každý člen ve fondu back-end je uveden na této stránce (bez ohledu na to, zda se jedná o síťovou kartu, IP adresu nebo plně kvalifikovaný název domény). Zobrazí se název záložního fondu, port, back-end nastavení HTTP a stav. Platné hodnoty pro stav stavu jsou **v pořádku**, nejsou v pořádku a nejsou **známy**.

> [!NOTE]
> Pokud vidíte stav back-endu **Neznámý**, zajistěte, aby byl přístup k back-endu zablokován pravidlem NSG, uživatelem definovanou trasou (udr) nebo vlastním DNS ve virtuální síti.

![Stav back-endu][10]

### <a name="view-back-end-health-through-powershell"></a>Zobrazení stavu back-endu prostřednictvím PowerShellu

Následující kód PowerShellu ukazuje, jak zobrazit stav back-endu pomocí `Get-AzApplicationGatewayBackendHealth` rutiny:

```powershell
Get-AzApplicationGatewayBackendHealth -Name ApplicationGateway1 -ResourceGroupName Contoso
```

### <a name="view-back-end-health-through-azure-cli"></a>Zobrazení stavu back-endu prostřednictvím rozhraní příkazového řádku Azure

```azurecli
az network application-gateway show-backend-health --resource-group AdatumAppGatewayRG --name AdatumAppGateway
```

### <a name="results"></a>Výsledky

Následující fragment kódu ukazuje příklad odpovědi:

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

## <a name="diagnostic-logging"></a>Diagnostické protokoly

Pomocí různých typů protokolů v Azure můžete spravovat a řešit potíže s aplikačními bránami. Některé z těchto protokolů jsou přístupné z webu Azure Portal. Všechny protokoly se dají extrahovat z úložiště objektů BLOB v Azure a zobrazovat v různých nástrojích, jako jsou [protokoly Azure monitor](../azure-monitor/insights/azure-networking-analytics.md), excelu a Power BI. Další informace o různých typech protokolů najdete v následujícím seznamu:

* **Protokol aktivit**: K zobrazení všech operací odeslaných do vašeho předplatného Azure a jejich stavu můžete použít [protokoly aktivit Azure](../monitoring-and-diagnostics/insights-debugging-with-events.md) (dřív označované jako operační protokoly a protokoly auditu). Položky protokolu aktivit se ve výchozím nastavení shromažďují a můžete si je zobrazit na webu Azure Portal.
* **Protokol přístupu**: Pomocí tohoto protokolu můžete zobrazit vzory přístupu Application Gateway a analyzovat důležité informace. Patří sem IP adresa volajícího, požadovaná adresa URL, latence odpovědi, návratový kód a bajtů. Protokol přístupu se shromáždí každých 300 sekund. Tento protokol obsahuje jeden záznam na instanci Application Gateway. Instance Application Gateway je identifikována vlastností instanceId.
* **Protokol výkonu**: Pomocí tohoto protokolu můžete zobrazit, jak probíhá Application Gateway instance. Tento protokol zachycuje informace o výkonu pro každou instanci, včetně celkového počtu zpracovaných požadavků, propustnosti v bajtech, celkových zpracovaných požadavků, počtu neúspěšných žádostí a stavu back-endu back-endu v pořádku. Protokol výkonu se shromáždí každých 60 sekund.
* **Protokol brány firewall**: Pomocí tohoto protokolu můžete zobrazit požadavky, které jsou protokolovány v režimu detekce nebo prevence služby Application Gateway, která je nakonfigurována pomocí brány firewall webových aplikací.

> [!NOTE]
> Protokoly jsou k dispozici pouze pro prostředky nasazené v modelu nasazení Azure Resource Manager. Protokoly pro prostředky v modelu nasazení Classic nemůžete použít. Abyste lépe pochopili tyto dva modely, přečtěte si článek [principy správce prostředků nasazení a klasického nasazení](../azure-resource-manager/resource-manager-deployment-model.md) .

Protokoly můžete ukládat třemi způsoby:

* **Účet úložiště**: Účty úložiště se nejlépe používají pro protokoly, když jsou protokoly uložené po delší dobu a v případě potřeby se přezkoumávají.
* **Centra událostí**: Centra událostí jsou skvělou možností pro integraci s dalšími nástroji SIEM (Security Information and Event Management), které vám umožní získat upozornění na vaše prostředky.
* **Protokoly Azure monitor**: Protokoly Azure Monitor se nejlépe používají pro obecné monitorování vaší aplikace v reálném čase nebo při prohlížení trendů.

### <a name="enable-logging-through-powershell"></a>Povolit protokolování prostřednictvím PowerShellu

Protokolování aktivit je u každého prostředku Správce prostředků povolené automaticky. Chcete-li začít shromažďovat data dostupná prostřednictvím těchto protokolů, je nutné povolit protokolování přístupu a výkonu. Chcete-li povolit protokolování, použijte následující postup:

1. Poznamenejte si ID prostředku účtu úložiště, kam se data protokolu ukládají. Tato hodnota má formát:/subscriptions/\<SubscriptionId\>/resourceGroups/\<název\>skupiny prostředků/Providers/Microsoft.Storage/storageAccounts/\<názevúčtuúložiště\>. Můžete použít libovolný účet úložiště z vašeho předplatného. Tuto informaci najdete pomocí webu Azure Portal

    ![Portál: ID prostředku pro účet úložiště](./media/application-gateway-diagnostics/diagnostics1.png)

2. Poznamenejte si ID prostředku brány Application Gateway, pro které je povolené protokolování. Tato hodnota je ve formátu:/subscriptions/\<SubscriptionId\>/resourceGroups/\<název\>skupiny prostředků/Providers/Microsoft.Network/applicationGateways/\<název aplikační brány. \>. Tuto informaci najdete pomocí webu Azure Portal.

    ![Portál: ID prostředku pro aplikační bránu](./media/application-gateway-diagnostics/diagnostics2.png)

3. Protokolování diagnostiky zapnete pomocí následující rutiny PowerShellu:

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

3. Stránka **Nastavení diagnostiky** obsahuje nastavení diagnostických protokolů. V tomto příkladu Log Analytics ukládá protokoly. Diagnostické protokoly můžete ukládat také pomocí služby center událostí a účtu úložiště.

   ![Spouští se proces konfigurace.][2]

5. Zadejte název nastavení, potvrďte nastavení a vyberte **Uložit**.

### <a name="activity-log"></a>Protokol aktivit

Azure ve výchozím nastavení generuje protokol aktivit. Protokoly se uchovávají po 90 dnech v úložišti protokolů událostí Azure. Další informace o těchto protokolech najdete v článku [zobrazení událostí a protokolu aktivit](../monitoring-and-diagnostics/insights-debugging-with-events.md) .

### <a name="access-log"></a>Přístup k protokolu

Protokol přístupu se vygeneruje jenom v případě, že jste ho povolili na každé instanci Application Gateway, jak je popsáno v předchozích krocích. Data se ukládají do účtu úložiště, který jste zadali při povolování protokolování. Každý přístup k Application Gateway je protokolován ve formátu JSON, jak je znázorněno v následujícím příkladu pro V1:

|Value  |Popis  |
|---------|---------|
|instanceId     | Application Gateway instance, která zpracovala požadavek.        |
|IP adresa klienta     | Původní IP adresa pro požadavek.        |
|clientPort     | Původní port pro požadavek.       |
|httpMethod     | Metoda HTTP, kterou požadavek používá       |
|requestUri     | Identifikátor URI přijatého požadavku        |
|RequestQuery     | **Server-Routed**: Instance fondu back-end, která odeslala požadavek.</br>**X-AzureApplicationGateway-LOG-ID**: ID korelace použité pro požadavek Dá se použít k řešení problémů s přenosem na back-endové servery. </br>**STAV SERVERU**: Kód odpovědi HTTP, který Application Gateway přijat z back-endu.       |
|UserAgent     | Uživatelský agent z hlavičky požadavku HTTP.        |
|httpStatus     | Stavový kód HTTP se vrátil klientovi z Application Gateway.       |
|httpVersion     | Verze protokolu HTTP požadavku.        |
|receivedBytes     | Velikost přijatého paketu (v bajtech)        |
|sentBytes| Velikost odeslaného paketu (v bajtech).|
|timeTaken| Doba (v milisekundách), kterou vyžaduje zpracování žádosti a odpověď, která má být odeslána. Počítá se jako interval od času, kdy Application Gateway přijme první bajt požadavku HTTP do doby, kdy se dokončí operace odeslání odpovědi. Je důležité si uvědomit, že časové pole obvykle zahrnuje dobu, po kterou se pakety požadavků a odpovědí cestují po síti. |
|sslEnabled| Zda komunikace s back-end fondy používala protokol SSL. Platné hodnoty jsou zapnuté a vypnuté.|
|host| Název hostitele, se kterým se odeslal požadavek na back-end Server. Pokud přeskočíte back-end hostname, tento název bude odpovídat.|
|originalHost| Název hostitele, se kterým Application Gateway požadavek přijal z klienta.|
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
V případě Application Gateway a WAF v2 se v protokolech zobrazí další informace:

|Value  |Popis  |
|---------|---------|
|instanceId     | Application Gateway instance, která zpracovala požadavek.        |
|IP adresa klienta     | Původní IP adresa pro požadavek.        |
|clientPort     | Původní port pro požadavek.       |
|httpMethod     | Metoda HTTP, kterou požadavek používá       |
|requestUri     | Identifikátor URI přijatého požadavku        |
|UserAgent     | Uživatelský agent z hlavičky požadavku HTTP.        |
|httpStatus     | Stavový kód HTTP se vrátil klientovi z Application Gateway.       |
|httpVersion     | Verze protokolu HTTP požadavku.        |
|receivedBytes     | Velikost přijatého paketu (v bajtech)        |
|sentBytes| Velikost odeslaného paketu (v bajtech).|
|timeTaken| Doba (v milisekundách), kterou vyžaduje zpracování žádosti a odpověď, která má být odeslána. Počítá se jako interval od času, kdy Application Gateway přijme první bajt požadavku HTTP do doby, kdy se dokončí operace odeslání odpovědi. Je důležité si uvědomit, že časové pole obvykle zahrnuje dobu, po kterou se pakety požadavků a odpovědí cestují po síti. |
|sslEnabled| Zda komunikace s back-end fondy používala protokol SSL. Platné hodnoty jsou zapnuté a vypnuté.|
|sslCipher| Šifrovací sada používaná pro komunikaci SSL (Pokud je povolený protokol SSL).|
|sslProtocol| Používá se protokol SSL (Pokud je povolený protokol SSL).|
|serverRouted| Back-end Server, na který Aplikační brána směruje požadavek.|
|serverStatus| Stavový kód HTTP back-end serveru.|
|serverResponseLatency| Latence odpovědi ze serveru back-end.|
|host| Adresa uvedená v hlavičce hostitele žádosti.|
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

Protokol výkonu je vygenerován pouze v případě, že jste jej povolili na každé instanci Application Gateway, jak je popsáno v předchozích krocích. Data se ukládají do účtu úložiště, který jste zadali při povolování protokolování. Data protokolu výkonu se generují v intervalu 1 minuty. Zaprotokolují se následující data:


|Value  |Popis  |
|---------|---------|
|instanceId     |  Instance Application Gateway, pro kterou se generují údaje o výkonu. Pro aplikační bránu s více instancemi je k dispozici jeden řádek na instanci.        |
|healthyHostCount     | Počet v nefunkčních hostitelích ve fondu back-end.        |
|unHealthyHostCount     | Počet nezdravých hostitelů ve fondu back-end.        |
|requestCount     | Počet zpracovaných požadavků.        |
|latence | Průměrná latence (v milisekundách) požadavků z instance do back-endu, který obsluhuje požadavky. |
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


|Value  |Popis  |
|---------|---------|
|instanceId     | Instance Application Gateway, pro kterou se generují data brány firewall. Pro aplikační bránu s více instancemi je k dispozici jeden řádek na instanci.         |
|IP adresa klienta     |   Původní IP adresa pro požadavek.      |
|clientPort     |  Původní port pro požadavek.       |
|requestUri     | Adresa URL přijatého požadavku       |
|ruleSetType     | Typ sady pravidel Dostupná hodnota je OWASP.        |
|ruleSetVersion     | Použitá verze sady pravidel Dostupné hodnoty jsou 2.2.9 a 3,0.     |
|ruleId     | ID pravidla události triggeru        |
|zpráva     | Uživatelsky přívětivá zpráva pro aktivační událost. Další podrobnosti najdete v části Podrobnosti.        |
|Akce     |  Akce prováděná na žádosti Dostupné hodnoty jsou blokované a povolené.      |
|Web     | Lokalita, pro kterou se protokol vygeneroval. V současné době se v seznamu zobrazí pouze globální, protože pravidla jsou globální.|
|details     | Podrobnosti události aktivace.        |
|Podrobnosti. zpráva     | Popis pravidla        |
|details.data     | V žádosti, která se shodovala s pravidlem, se našla konkrétní data.         |
|details. File     | Konfigurační soubor, který obsahoval pravidlo.        |
|details. line     | Číslo řádku v konfiguračním souboru, který spustil událost.       |

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
  }
} 

```

### <a name="view-and-analyze-the-activity-log"></a>Zobrazení a analýza protokolu aktivit

Data protokolu aktivit si můžete zobrazit použitím jedné z následujících metod:

* **Nástroje Azure**: Načtěte informace z protokolu aktivit prostřednictvím Azure PowerShell, Azure CLI, Azure REST API nebo Azure Portal. Podrobné pokyny k jednotlivým metodám najdete v článku o [operacích s protokoly aktivit ve Správci prostředků](../azure-resource-manager/resource-group-audit.md).
* **Power BI**: Pokud ještě nemáte účet [Power BI](https://powerbi.microsoft.com/pricing) , můžete si ho vyzkoušet zdarma. Díky [balíčku obsahu protokoly aktivit Azure pro Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/) můžete svá data analyzovat pomocí předkonfigurovaných řídicích panelů, které můžete použít okamžitě nebo si je upravit.

### <a name="view-and-analyze-the-access-performance-and-firewall-logs"></a>Zobrazení a analýza protokolů přístupu, výkonu a brány firewall

[Protokoly Azure monitor](../azure-monitor/insights/azure-networking-analytics.md) můžou shromažďovat soubory protokolů čítačů a událostí z vašeho účtu služby Blob Storage. Zahrnuje vizualizace a výkonné vyhledávací funkce k analýze protokolů.

Můžete se také připojit k účtu úložiště a načíst položky protokolu JSON s protokoly přístupu a výkonu. Po stažení souborů JSON je můžete převést do formátu CSV a zobrazit si je v Excelu, Power BI nebo jiném nástroji s vizualizací dat.

> [!TIP]
> Pokud znáte Visual Studio a máte představu, jak u konstant a proměnných v jazyce C# měnit hodnoty, můžete použít [nástroje pro převedení protokolů](https://github.com/Azure-Samples/networking-dotnet-log-converter), které jsou k dispozici na GitHubu.
> 
> 

#### <a name="analyzing-access-logs-through-goaccess"></a>Analýza protokolů přístupu prostřednictvím GoAccess

Publikovali jsme Správce prostředků šablonu, která nainstaluje a spustí oblíbený analyzátor protokolů [GoAccess](https://goaccess.io/) pro Application Gateway protokoly přístupu. GoAccess poskytuje cennou statistiku přenosů HTTP, jako jsou například jedineční návštěvníci, požadované soubory, hostitelé, operační systémy, prohlížeče, stavové kódy HTTP a další. Další podrobnosti najdete v [souboru Readme ve složce šablony Správce prostředků v GitHubu](https://aka.ms/appgwgoaccessreadme).

## <a name="metrics"></a>Metriky

Metriky jsou funkce pro určité prostředky Azure, kde můžete zobrazit čítače výkonu na portálu. Pro Application Gateway jsou k dispozici následující metriky:

- **Aktuální připojení**
- **Neúspěšné žádosti**
- **Počet hostitelů v pořádku**

   Můžete filtrovat podle fondu back-endu a zobrazit v něm v pořádku/poškozené hostitele v konkrétním back-end fondu.


- **Stav odpovědi**

   Distribuci stavového kódu odpovědi lze dále kategorizovat, aby zobrazovala odpovědi v kategoriích 2xx, 3xx, 4xx a 5xx.

- **Propustnost**
- **Celkový počet požadavků**
- **Počet hostitelů není v pořádku**

   Můžete filtrovat podle fondu back-endu a zobrazit v něm v pořádku/poškozené hostitele v konkrétním back-end fondu.

Přejděte na aplikační bránu a v části **monitorování** vyberte **metriky**. Chcete-li zobrazit dostupné hodnoty, vyberte rozevírací seznam **METRIKA**.

Na následujícím obrázku vidíte příklad se třemi metrikami zobrazenými za posledních 30 minut:

[![](media/application-gateway-diagnostics/figure5.png "Zobrazení metriky")](media/application-gateway-diagnostics/figure5-lb.png#lightbox)

Pokud chcete zobrazit aktuální seznam metrik, přečtěte si téma [podporované metriky s Azure monitor](../azure-monitor/platform/metrics-supported.md).

### <a name="alert-rules"></a>Pravidla výstrah

Můžete spustit pravidla upozornění založená na metrikách prostředku. Například výstraha může zavolat Webhook nebo poslat e-mailem správce, pokud je propustnost služby Application Gateway nad, níže nebo za stanovenou dobu.

Následující příklad vás provede vytvořením pravidla upozornění, které pošle e-mailem správci po porušení propustnosti prahovou hodnotu:

1. Vyberte **Přidat výstrahu metriky** a otevřete stránku **Přidat pravidlo** . Tuto stránku můžete také kontaktovat ze stránky metriky.

   ![Tlačítko Přidat upozornění na metriku][6]

2. Na stránce **Přidat pravidlo** vyplňte oddíly název, podmínka a Notify a vyberte **OK**.

   * V selektoru **podmínky** vyberte jednu ze čtyř hodnot: **Větší než**, **větší než nebo rovno**, **menší**nebo rovno nebo **menší než nebo rovno**.

   * V selektoru **období** vyberte období od pěti minut po 6 hodin.

   * Pokud vyberete možnost **vlastníci, přispěvatelé a čtenáři e-mailu**, může být e-mail dynamický v závislosti na uživatelích, kteří k tomuto prostředku mají přístup. Jinak můžete v poli **Další e-mailové zprávy správce** zadat čárkami oddělený seznam uživatelů.

   ![Přidat stránku pravidla][7]

Pokud dojde k porušení prahové hodnoty, přijde e-mail podobný tomu na následujícím obrázku.

![E-mail pro porušení prahové hodnoty][8]

Po vytvoření výstrahy metriky se zobrazí seznam výstrah. Poskytuje přehled o všech pravidlech výstrah.

![Seznam výstrah a pravidel][9]

Další informace o oznámeních výstrah najdete v tématu [přijímání oznámení](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)o výstrahách.

Další informace o webhookech a o tom, jak je můžete používat s výstrahami, najdete [v tématu Konfigurace Webhooku na upozornění metriky Azure](../azure-monitor/platform/alerts-webhooks.md).

## <a name="next-steps"></a>Další kroky

* Vizualizujte protokoly čítačů a událostí pomocí [Azure monitor protokolů](../azure-monitor/insights/azure-networking-analytics.md).
* [Vizualizujte si protokol aktivit Azure pomocí Power BI](https://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) Blogový příspěvek.
* Umožňuje [Zobrazit a analyzovat protokoly aktivit Azure v Power BI a další](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) Blogový příspěvek.

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
