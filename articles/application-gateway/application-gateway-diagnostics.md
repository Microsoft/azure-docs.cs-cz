---
title: Přístup k protokolům monitorování a protokolování výkonu, stavu back-end, metriky pro službu Application Gateway
description: Zjistěte, jak povolit a spravovat přístup k protokolům a protokolování výkonu pro službu Application Gateway
services: application-gateway
author: amitsriva
manager: rossort
tags: azure-resource-manager
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 4/2/2018
ms.author: amitsriva
ms.openlocfilehash: 982ae712320cb390b1822de6a7a3980ebfb6251e
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="back-end-health-diagnostic-logs-and-metrics-for-application-gateway"></a>Stav back-end, diagnostické protokoly a metriky pro službu Application Gateway

Pomocí Azure Application Gateway můžete sledovat prostředky následujícími způsoby:

* [Back-end stavu](#back-end-health): Application Gateway poskytuje schopnost sledovat stav serverů v back endové fondy prostřednictvím portálu Azure a pomocí prostředí PowerShell. Můžete také získat stav fondu back-end prostřednictvím protokolování diagnostiky výkonu.

* [Protokoly](#diagnostic-logging): protokoly umožňují pro výkon, přístupu a další data ukládání nebo používán z prostředků pro účely monitorování.

* [Metriky](#metrics): Aplikační brána v současnosti má sedm metriky, chcete-li zobrazit čítače výkonu.

## <a name="back-end-health"></a>Back-end stavu

Application Gateway poskytuje možnost pro sledování stavu jednotlivých členů fondu back-end prostřednictvím portálu, prostředí PowerShell a rozhraní příkazového řádku (CLI). Můžete také vyhledat agregovaný stav souhrn back endové fondy prostřednictvím protokolování diagnostiky výkonu. 

Sestava stavu back-end odráží výstup test stavu Application Gateway na back-end instance. Při zjišťování úspěšné a back-end může přijímat provoz, bude považován za v pořádku. Jinak považuje není v pořádku.

> [!IMPORTANT]
> Pokud je skupina zabezpečení sítě (NSG) na podsíť aplikační bránu, otevřete rozsahy portů 65503 65534 na podsíť aplikační brány pro příchozí provoz. Tyto porty jsou povinné pro back-end stav rozhraní API pro práci.


### <a name="view-back-end-health-through-the-portal"></a>Zobrazit stav back-end prostřednictvím portálu

Na portálu se automaticky poskytuje stavu back-end. V existující aplikační brány, vyberte **monitorování** > **back-end stavu**. 

Každý člen ve fondu back-end je uvedený na této stránce (jestli je síťový adaptér, IP nebo plně kvalifikovaný název domény). Název fondu back-end, port, název nastavení HTTP back-end a stav se zobrazí. Platné hodnoty pro stav jsou **stavu v pořádku**, **není v pořádku**, a **neznámé**.

> [!NOTE]
> Pokud se zobrazí back-end stav **neznámé**, ujistěte se, zda není blokován přístup k back-end nastavením pravidlo NSG, trasy definované uživatelem (UDR) nebo vlastní DNS ve virtuální síti.

![Back-end stavu][10]

### <a name="view-back-end-health-through-powershell"></a>Zobrazit stav back-end pomocí prostředí PowerShell

Následující kód prostředí PowerShell ukazuje, jak zobrazit stav back-end pomocí `Get-AzureRmApplicationGatewayBackendHealth` rutiny:

```powershell
Get-AzureRmApplicationGatewayBackendHealth -Name ApplicationGateway1 -ResourceGroupName Contoso
```

### <a name="view-back-end-health-through-azure-cli-20"></a>Zobrazit stav back-end pomocí Azure CLI 2.0

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

Různé typy protokolů v Azure můžete použít ke správě a odstraňování potíží application Gateway. Některé z těchto protokolů můžete přistupovat prostřednictvím portálu. Všechny protokoly lze extrahovat z Azure Blob storage a zobrazit v různých nástrojů, jako například [analýzy protokolů](../log-analytics/log-analytics-azure-networking-analytics.md), Excel a Power BI. Se více o různých typech protokolů z následujícího seznamu:

* **Protokol aktivit**: můžete použít [protokoly Azure aktivity](../monitoring-and-diagnostics/insights-debugging-with-events.md) (dříve označovaný jako operační protokoly a protokoly auditu) Chcete-li zobrazit všechny operace, které se odešlou do vašeho předplatného Azure a jejich stav. Ve výchozím nastavení se shromažďují položky protokolu aktivity a lze je zobrazit na portálu Azure.
* **Přístup k protokolu**: můžete tento protokol zobrazit Application Gateway přístupové vzorce a analyzovat důležité informace, včetně IP volajícího, požadovanou adresu URL, latence odpovědi, návratový kód a bajtů a odhlášení. Protokol přístupu se shromažďují každých 300 sekund. Tento protokol obsahuje jeden záznam za instance aplikační brány. Instance aplikační brány lze identifikovat podle vlastnost ID instance.
* **Výkon protokolu**: Tento protokol můžete zobrazit, jak fungují instance aplikační brány. Tento protokol zaznamená informace o výkonu pro každou instanci, včetně celkový počet požadavků zpracovaných, propustnost v bajtech, celkový počet požadavků zpracovaných počet chybných požadavků a počet instancí back-end v pořádku a není v pořádku. Protokolu výkonu shromažďovaných každých 60 sekund.
* **Protokol brány firewall**: Tento protokol můžete zobrazit žádosti, které se protokolují prostřednictvím zjišťování nebo zabránění režim služby application gateway, která je konfigurovaná pomocí brány firewall webových aplikací.

> [!NOTE]
> Protokoly jsou k dispozici pouze pro prostředky nasazené v modelu nasazení Azure Resource Manager. Protokoly nelze použít pro prostředky v modelu nasazení classic. Lépe pochopit dva modely, najdete v článku [nasazení Resource Manager principy a nasazení classic](../azure-resource-manager/resource-manager-deployment-model.md) článku.

Máte tři možnosti pro ukládání protokolů:

* **Účet úložiště**: účty úložiště jsou nejvhodnější pro protokoly při protokoly jsou uloženy delší dobu a zkontrolovat v případě potřeby.
* **Služba Event hubs**: Event hubs je skvělou možnost pro integraci s další informace o zabezpečení a získat výstrahy na vaše prostředky nástroje pro správu událostí (SEIM).
* **Analýza protokolu**: analýzy protokolů je nejvhodnější pro obecné sledování v reálném čase vaší aplikace nebo při prohlížení trendy.

### <a name="enable-logging-through-powershell"></a>Povolit protokolování pomocí prostředí PowerShell

Pro každý prostředek Resource Manager je automaticky povolené protokolování aktivit. Je nutné povolit přístup a výkon protokolování spustit shromažďování dat, které jsou k dispozici prostřednictvím tyto protokoly. Chcete-li protokolování povolit, použijte následující kroky:

1. Poznamenejte si ID prostředku účtu úložiště, které jsou uložená data protokolu. Tato hodnota je ve formátu: /subscriptions/\<subscriptionId\>/resourceGroups/\<název skupiny prostředků\>/providers/Microsoft.Storage/storageAccounts/\<název účtu úložiště\>. Můžete použít libovolný účet úložiště v rámci vašeho předplatného. Na portálu Azure můžete najít tyto informace.

    ![Portál: ID prostředku pro účet úložiště](./media/application-gateway-diagnostics/diagnostics1.png)

2. Poznamenejte si ID prostředku Aplikační brána, pro které je povoleno protokolování. Tato hodnota je ve formátu: /subscriptions/\<subscriptionId\>/resourceGroups/\<název skupiny prostředků\>/providers/Microsoft.Network/applicationGateways/\<název brány aplikace \>. Na portálu můžete najít tyto informace.

    ![Portál: ID prostředku application Gateway.](./media/application-gateway-diagnostics/diagnostics2.png)

3. Povolte protokolování diagnostiky pomocí následující rutiny prostředí PowerShell:

    ```powershell
    Set-AzureRmDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name> -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> -Enabled $true     
    ```
    
> [!TIP] 
>Protokoly aktivity nevyžadují, aby účet samostatného úložiště. Používání úložiště pro přístup a protokolování výkonu způsobuje poplatky za služby.

### <a name="enable-logging-through-the-azure-portal"></a>Povolit protokolování prostřednictvím portálu Azure

1. Na portálu Azure najít prostředek a klikněte na tlačítko **diagnostické protokoly**.

   Pro službu Application Gateway jsou k dispozici tři protokoly:

   * Přístup k protokolu
   * Protokolu výkonu
   * Protokol brány firewall

2. Chcete-li spustit shromažďování dat, klikněte na tlačítko **zapněte diagnostiku**.

   ![Zapnutí diagnostiky][1]

3. **Nastavení diagnostiky** okno obsahuje nastavení pro diagnostické protokoly. V tomto příkladu analýzy protokolů ukládá protokoly. Klikněte na tlačítko **konfigurace** pod **analýzy protokolů** konfigurace pracovního prostoru. Můžete taky centrům událostí a účet úložiště pro uložení diagnostické protokoly.

   ![Spuštění procesu konfigurace][2]

4. Vyberte existující pracovní prostor analýzy protokolů nebo vytvořte novou. Tento příklad používá nějaký existující.

   ![Možnosti pro pracovní prostory analýzy protokolů][3]

5. Potvrďte nastavení a klikněte na tlačítko **Uložit**.

   ![Okno nastavení diagnostiky se výběry][4]

### <a name="activity-log"></a>Protokol aktivit

Ve výchozím nastavení vygeneruje Azure protokolu aktivit. Protokoly se zachovají 90 dní v úložišti Azure protokoly událostí. Další informace o tyto protokoly načtením [zobrazování událostí a protokolu aktivity](../monitoring-and-diagnostics/insights-debugging-with-events.md) článku.

### <a name="access-log"></a>Přístup k protokolu

Přístup k protokolu se vygeneruje pouze v případě, že jste ho povolili každé instance aplikační brány, jak je podrobně uvedeno v předchozích krocích. Data je uložený v účtu úložiště, které jste zadali při jste povolili protokolování. Každý přístup Application Gateway je zaznamenána ve formátu JSON, jak je znázorněno v následujícím příkladu:


|Hodnota  |Popis  |
|---------|---------|
|identifikátor instanceId     | Instance brány aplikace, který žádost zpracoval.        |
|Když     | Původní IP pro požadavek.        |
|clientPort     | Výchozí port pro požadavek.       |
|HttpMethod     | Metoda HTTP používaný žádosti.       |
|requestUri     | Identifikátor URI přijatý požadavek.        |
|RequestQuery     | **Server směrovat**: instance fond Back-end, který vám byl zaslán požadavek.</br>**X-AzureApplicationGateway-LOG-ID**: ID korelace použitou pro danou žádost. Může sloužit k řešení potíží provoz na back-end serverů. </br>**Stav serveru**: kód odpovědi HTTP, který Application Gateway dostali z back-end.       |
|UserAgent     | Agent u uživatele z hlavičky žádosti HTTP.        |
|httpStatus     | Stavový kód HTTP vrácen do klienta z aplikační brány.       |
|httpVersion     | Verze protokolu HTTP žádosti.        |
|ReceivedBytes     | Velikost paketu přijaté v bajtech.        |
|SentBytes| Velikost paket odeslaný v bajtech.|
|timeTaken| Délka dobu (v milisekundách), která je potřebná pro zpracování požadavku a odpovědi na odeslání. Počítá se jako interval od okamžiku, kdy Application Gateway přijímá první bajt požadavku HTTP na čas, kdy odpovědi odeslat dokončení operace. Je důležité si uvědomit, že pole Time-Taken obvykle zahrnuje čas, jsou pakety žádostí a odpovědí přenášeny po síti. |
|sslEnabled| Jestli komunikaci s back endové fondy používat protokol SSL. Platné hodnoty jsou zapnout a vypnout.|
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
        "sslEnabled": "off"
    }
}
```

### <a name="performance-log"></a>Protokolu výkonu

V protokolu výkonu se vygeneruje pouze v případě, že jste povolili každé instance aplikační brány, jak je podrobně uvedeno v předchozích krocích. Data je uložený v účtu úložiště, které jste zadali při jste povolili protokolování. Data protokolu výkonu je generován v intervalech 1 minutu. Se protokolují tato data:


|Hodnota  |Popis  |
|---------|---------|
|identifikátor instanceId     |  Instance brány aplikace, které výkonu je generován data. Pro bránu více instancí aplikace je jeden řádek pro každou instanci.        |
|healthyHostCount     | Počet pořádku hostitelích ve fondu back-end.        |
|unHealthyHostCount     | Počet není v pořádku hostitelích ve fondu back-end.        |
|RequestCount     | Počet požadavků zpracovaných.        |
|latence | Latence (v milisekundách) požadavků z instance back end, který obsluhuje žádosti. |
|failedRequestCount| Počet neúspěšných požadavků.|
|Propustnost| Průměrná propustnost od poslední protokolu měřená v bajtech za sekundu.|

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
> Latencí se počítá z čas, kdy je první bajt požadavku HTTP přijal čas odeslání poslední bajt odpovědi HTTP. Jedná se o součet bude čas zpracování aplikační brány a náklady na síť s back-end plus dobu, která back-end potřebná pro zpracování požadavku.

### <a name="firewall-log"></a>Protokol brány firewall

Protokol brány firewall se vygeneruje pouze v případě, že jste je povolili pro každý application gateway, jak je podrobně uvedeno v předchozích krocích. Tento protokol taky vyžaduje, aby brány firewall webových aplikací je nakonfigurovaný na aplikační brány. Data je uložený v účtu úložiště, které jste zadali při jste povolili protokolování. Se protokolují tato data:


|Hodnota  |Popis  |
|---------|---------|
|identifikátor instanceId     | Instance brány aplikace, které brány firewall data jsou generována. Pro bránu více instancí aplikace je jeden řádek pro každou instanci.         |
|Když     |   Původní IP pro požadavek.      |
|clientPort     |  Výchozí port pro požadavek.       |
|requestUri     | Adresa URL přijatý požadavek.       |
|ruleSetType     | Typ sady pravidel. K dispozici hodnota je OWASP.        |
|ruleSetVersion     | Verze použitá sady pravidel. Dostupné jsou hodnoty 2.2.9 a 3.0.     |
|RuleId     | ID pravidla spouštěcí události.        |
|zpráva     | Uživatelsky přívětivý zpráva pro aktivační událost. Další podrobnosti najdete v části Podrobnosti.        |
|akce     |  Akce v žádosti. Dostupné hodnoty jsou blokované a povolené.      |
|Web     | Web, pro které byla vygenerována v protokolu. V současné době pouze globální se má zobrazit, protože pravidla jsou globální.|
|Podrobnosti     | Podrobnosti o aktivační událost.        |
|details.Message     | Popis pravidla.        |
|details.data     | Konkrétní data uvedená v požadavek, který odpovídá pravidlo.         |
|details.File     | Konfigurační soubor, který obsahoval pravidlo.        |
|details.Line     | Číslo řádku v konfiguračním souboru, který spustil událost.       |

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

Můžete zobrazit a analyzovat data protokolu aktivit pomocí některé z následujících metod:

* **Nástroje Azure**: načtení informací z protokolu činnosti pomocí prostředí Azure PowerShell, rozhraní příkazového řádku Azure, REST API služby Azure nebo portálu Azure. Podrobné pokyny pro jednotlivé metody jsou podrobně popsané na [operací aktivit s Resource Managerem](../azure-resource-manager/resource-group-audit.md) článku.
* **Power BI**: Pokud ještě nemáte [Power BI](https://powerbi.microsoft.com/pricing) účet, můžete zkusit je zdarma. Pomocí [obsahu protokoly aktivity Azure pack pro Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/), můžete analyzovat svá data pomocí předkonfigurované řídicí panely, které můžete použít nebo přizpůsobit.

### <a name="view-and-analyze-the-access-performance-and-firewall-logs"></a>Zobrazit a analyzovat přístup, výkonu a protokoly brány firewall

Azure [analýzy protokolů](../log-analytics/log-analytics-azure-networking-analytics.md) z vašeho účtu úložiště objektů Blob můžete shromažďovat soubory protokolů událostí a čítače. Obsahuje vizualizace a výkonné možnosti vyhledávání k analýze protokolů.

Můžete také připojit k účtu úložiště a načítat položky protokolu JSON pro přístup a protokoly výkonu. Po stažení soubory JSON, můžete je převést na sdílený svazek clusteru a zobrazit je v aplikaci Excel, Power BI nebo jakýkoli jiný nástroj, vizualizace dat.

> [!TIP]
> Pokud jste obeznámeni s Visual Studio a základní koncepty změna hodnoty konstanty a proměnné v jazyce C#, můžete použít [protokolu nástroje Převaděč](https://github.com/Azure-Samples/networking-dotnet-log-converter) dostupné z Githubu.
> 
> 

## <a name="metrics"></a>Metriky

Metriky jsou funkce u některých prostředků Azure, kde můžete zobrazit čítače výkonu v portálu. Pro službu Application Gateway jsou k dispozici následující metriky:

- **Aktuální počet připojení**
- **Neúspěšné požadavky**
- **V pořádku počet hostitele**

   Můžete filtrovat podle za základ fond back-end pro zobrazení v pořádku nebo není v pořádku hostitelích v konkrétní back-endový fond.


- **Stav odpovědi**

   Rozdělení kódu stavu odpovědi můžete dále dělí zobrazíte odpovědí 2xx, 3xx, 4xx a 5xx kategorií.

- **Propustnost**
- **Celkový počet požadavků**
- **Chybný počet hostitele**

   Můžete filtrovat podle za základ fond back-end pro zobrazení v pořádku nebo není v pořádku hostitelích v konkrétní back-endový fond.

Přejděte do služby application gateway, v části **monitorování** klikněte na tlačítko **metriky**. Chcete-li zobrazit dostupné hodnoty, vyberte **METRIKA** rozevíracího seznamu.

Na následujícím obrázku najdete příklad tří metriky zobrazené pro posledních 30 minut:

[![](media/application-gateway-diagnostics/figure5.png "Zobrazení metriky")](media/application-gateway-diagnostics/figure5-lb.png#lightbox)

Pokud chcete zobrazit aktuální seznam metriky, najdete v části [podporované metriky s Azure monitorování](../monitoring-and-diagnostics/monitoring-supported-metrics.md).

### <a name="alert-rules"></a>Pravidla upozornění

Můžete spustit na základě metriky pro prostředek pravidla výstrah. Výstrahu můžete například volat webhook, jehož nebo e-mailu správce, pokud propustnost aplikační brány je výše, níže nebo na prahovou hodnotu v zadaném období.

Následující příklad vás provede vytvořením pravidlo výstrahy, která odešle e-mail na správce po narušení propustnost a prahové hodnoty:

1. Klikněte na tlačítko **přidat metriky upozornění** otevřete **přidat pravidlo** okno. Mohou být využity také toto okno, v okně metriky.

   ![Tlačítko "Přidat metriky upozornění"][6]

2. Na **přidat pravidlo** okno, zadejte název, stav a upozornit části a klikněte na tlačítko **OK**.

   * V **podmínku** selektor, vyberte jednu ze čtyř hodnot: **větší než**, **větší než nebo rovna**, **menší než**, nebo **Menší než nebo rovna hodnotě**.

   * V **období** selektor, vyberte období z pěti minut až šest hodin.

   * Pokud vyberete **e-mailu vlastníci, přispěvatelé a čtenáři**, e-mailu, může být dynamické podle uživatelů, kteří mají přístup k prostředku. Jinak, můžete zadat čárkami oddělený seznam uživatelů v **email(s) další správce** pole.

   ![Přidat pravidlo okno][7]

Pokud je prahová hodnota nedodržení, dorazí e-mail, který je podobný tomu na následujícím obrázku:

![E-mailu pro porušení prahové hodnoty][8]

Po vytvoření metriky upozornění se zobrazí seznam výstrah. Poskytuje přehled o všech pravidla výstrah.

![Seznam výstrah a pravidla][9]

Další informace o oznámeních výstrah najdete v tématu [dostávat oznámení o výstrahách](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

Bližší informace o webhooky a jak je možné používat s výstrahy, navštivte [konfigurace webhook, jehož na výstrahu Azure metriky](../monitoring-and-diagnostics/insights-webhooks-alerts.md).

## <a name="next-steps"></a>Další postup

* Vizualizovat čítač a protokoly událostí pomocí [analýzy protokolů](../log-analytics/log-analytics-azure-networking-analytics.md).
* [Aktivita Azure protokolu s Power BI vizualizovat](http://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) příspěvku na blogu.
* [Zobrazení a analýza protokolů Azure aktivity v Power BI a další](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) příspěvku na blogu.

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
