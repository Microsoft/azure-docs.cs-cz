---
title: Přístup k protokolům monitorování, protokolování výkonu, stav back endu a metriky pro službu Application Gateway
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
ms.date: 6/20/2018
ms.author: amitsriva
ms.openlocfilehash: d8c652d75b01b3a13ef06475190ad81980f31e44
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53270361"
---
# <a name="back-end-health-diagnostic-logs-and-metrics-for-application-gateway"></a>Stav back endu, diagnostické protokoly a metriky pro službu Application Gateway

Pomocí Azure Application Gateway můžete monitorovat prostředky následujícími způsoby:

* [Stav back endu](#back-end-health): Služba Application Gateway poskytuje možnost sledovat stav serverů, fondů back-end na webu Azure portal a prostředí PowerShell. Můžete také vyhledat stavu back endové fondy prostřednictvím diagnostické protokoly výkonu.

* [Protokoly](#diagnostic-logging): Protokoly umožňují výkonu, přístupu a další data k ukládání nebo spotřebované prostředků pro účely monitorování.

* [Metriky](#metrics): Služba Application Gateway nyní má hlavní sedm metriky, chcete-li zobrazit čítače výkonu.

## <a name="back-end-health"></a>Stav back endu

Application Gateway poskytuje funkce pro sledování stavu jednotlivých členů fondu back-end pomocí portálu, Powershellu a rozhraní příkazového řádku (CLI). Můžete také vyhledat agregovaný stav souhrn fondy back-end pomocí protokolování diagnostiky výkonu. 

Sestava Stav back endu odráží výstup sondu stavu službě Application Gateway pro back endových instancí. Při zjišťování je úspěšné a back-endu může přijímat provoz, je považován za v pořádku. V opačném případě se považoval za poškozený.

> [!IMPORTANT]
> Pokud je skupina zabezpečení sítě (NSG) na podsíť Application Gateway, otevřete rozsahy portů 65503 65534 na podsítě Application Gateway pro příchozí provoz. Tento rozsah portů je nutné pro komunikaci infrastruktury Azure. Jsou chráněny (uzamknuty) s použitím certifikátů Azure. Bez správných certifikátů nemohou externí entity, včetně zákazníků těchto bran, nebude možné zahájit žádné změny na tyto koncové body.


### <a name="view-back-end-health-through-the-portal"></a>Zobrazit stav back endu pomocí portálu

Na portálu se automaticky poskytuje stav back endu. V existující aplikační bráně, vyberte **monitorování** > **stav back-endu**. 

Každý člen ve fondu back-end je uvedený na této stránce (ať už se jedná síťovou kartu, IP nebo plně kvalifikovaný název domény). Název back endového fondu, portu, názvu nastavení HTTP back-end a stav se zobrazí. Platné hodnoty pro stav jsou **pořádku**, **není v pořádku**, a **neznámý**.

> [!NOTE]
> Pokud se zobrazí stav back endu stav **neznámý**, ujistěte se, že přístup k back-end není blokován branou pravidlo NSG, trasy definované uživatelem (UDR) nebo vlastního DNS ve virtuální síti.

![Stav back endu][10]

### <a name="view-back-end-health-through-powershell"></a>Zobrazit stav back endu pomocí Powershellu

Následující kód Powershellu ukazuje, jak zobrazit stav back endu pomocí `Get-AzureRmApplicationGatewayBackendHealth` rutiny:

```powershell
Get-AzureRmApplicationGatewayBackendHealth -Name ApplicationGateway1 -ResourceGroupName Contoso
```

### <a name="view-back-end-health-through-azure-cli"></a>Zobrazit stav back endu pomocí rozhraní příkazového řádku Azure

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

Můžete použít různé typy protokolů v Azure pro správu a řešení potíží s application Gateway. Některé z těchto protokolů jsou přístupné z webu Azure Portal. Všechny protokoly je možné extrahovat z úložiště objektů Blob v Azure a zobrazit v různých nástrojů, jako například [Log Analytics](../azure-monitor/insights/azure-networking-analytics.md), Excelu a Power BI. Vám může Další informace o různých typech protokoly z následujícího seznamu:

* **Protokol aktivit**: Můžete použít [protokolů aktivit Azure](../monitoring-and-diagnostics/insights-debugging-with-events.md) (dříve označované jako provozní protokoly a protokoly auditu) Chcete-li zobrazit všechny operace, které se pošlou do vašeho předplatného Azure a jejich stav. Položky protokolu aktivit se ve výchozím nastavení shromažďují a můžete si je zobrazit na webu Azure Portal.
* **Přístup k protokolu**: Zobrazit vzory přístupu k Application Gateway a analyzovat důležité informace, včetně IP volajícího, požadovanou adresu URL, latence odpovědi, návratový kód a bajtů dovnitř a ven můžete použít tento protokol. Přístup k protokolu shromažďovaných každých 300 sekund. Tento protokol obsahuje jeden záznam za instance služby Application Gateway. Instance Application Gateway lze identifikovat podle vlastnosti ID instance.
* **V protokolu výkonu**: Chcete-li zobrazit, jaký výkon instancí Application Gateway můžete použít tento protokol. Tento protokol zaznamenává informace o výkonu pro každou instanci, včetně celkový počet požadavků, které jsou poskytovány, propustnost v bajtech, celkový počet požadavků obsluhovat, počet neúspěšných požadavků a počet instancí back-end v pořádku a není v pořádku. Protokolování výkonu shromažďovaných každých 60 sekund.
* **Brány firewall protokolu**: Tento protokol můžete zobrazit požadavky, které jsou zaznamenány pomocí detekce a ochrany před únikem informací režim služby application gateway, která je nakonfigurovaná s firewallem webových aplikací.

> [!NOTE]
> Protokoly jsou k dispozici pouze pro prostředky nasazené v modelu nasazení Azure Resource Manageru. Protokoly nelze použít pro prostředky v modelu nasazení classic. Lépe pochopit ze dvou modelů, najdete v článku [Principy nasazení Resource Manager a modelu nasazení classic](../azure-resource-manager/resource-manager-deployment-model.md) článku.

Protokoly můžete ukládat třemi způsoby:

* **Účet úložiště**: Účty úložiště jsou nejvhodnější pro protokoly protokoly ukládání delší dobu a zkontrolovat v případě potřeby.
* **Služba Event hubs**: Služba Event hubs jsou skvělou možností pro integraci s dalšími nástroji pro správu (SEIM) informace a události zabezpečení, abyste mohli dostávat upozornění na prostředky.
* **Log Analytics**: Log Analytics je nejvhodnější pro obecné v reálném čase sledování vaší aplikace nebo si chcete prohlédnout trendy.

### <a name="enable-logging-through-powershell"></a>Povolit protokolování pomocí Powershellu

Protokolování aktivit je u každého prostředku Správce prostředků povolené automaticky. Je nutné povolit přístup a výkon protokolování shromažďování dat, které jsou k dispozici prostřednictvím těchto protokolů. Povolení protokolování, použijte následující kroky:

1. Poznamenejte si ID prostředku účtu úložiště, kam se data protokolu ukládají. Tato hodnota je ve formátu: /subscriptions/\<subscriptionId\>/resourceGroups/\<název skupiny prostředků\>/providers/Microsoft.Storage/storageAccounts/\<název účtu úložiště\>. Můžete použít libovolný účet úložiště z vašeho předplatného. Tuto informaci najdete pomocí webu Azure Portal

    ![Portál: ID prostředku účtu úložiště](./media/application-gateway-diagnostics/diagnostics1.png)

2. Poznamenejte si ID prostředku application gateway, pro které je povoleno protokolování. Tato hodnota je ve formátu: /subscriptions/\<subscriptionId\>/resourceGroups/\<název skupiny prostředků\>/providers/Microsoft.Network/applicationGateways/\<název aplikace brány \>. Tuto informaci najdete pomocí webu Azure Portal.

    ![Portál: ID prostředku služby application gateway.](./media/application-gateway-diagnostics/diagnostics2.png)

3. Protokolování diagnostiky zapnete pomocí následující rutiny PowerShellu:

    ```powershell
    Set-AzureRmDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name> -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> -Enabled $true     
    ```
    
> [!TIP] 
>Protokoly aktivit nevyžadují samostatný účet úložiště. Za používání úložiště k protokolování přístupu a výkonu se účtují poplatky.

### <a name="enable-logging-through-the-azure-portal"></a>Povolit protokolování prostřednictvím webu Azure Portal

1. Na webu Azure Portal, najít váš prostředek a klikněte na tlačítko **diagnostické protokoly**.

   Pro službu Application Gateway jsou k dispozici tři protokoly:

   * Přístup k protokolu
   * V protokolu výkonu
   * Protokol brány firewall

2. Pokud chcete začít shromažďovat data, klikněte na **Zapnout diagnostiku**.

   ![Zapínání diagnostik][1]

3. **Nastavení diagnostiky** okno obsahuje nastavení pro diagnostické protokoly. V tomto příkladu služba Log Analytics ukládá protokoly. Klikněte na tlačítko **konfigurovat** pod **Log Analytics** proveďte konfiguraci pracovního prostoru. Diagnostické protokoly můžete ukládat také pomocí služby center událostí a účtu úložiště.

   ![Spouští se proces konfigurace][2]

4. Zvolte existující pracovní prostor Log Analytics nebo vytvořte novou. Tento příklad používá již existující.

   ![Možnosti pro pracovní prostory Log Analytics][3]

5. Potvrďte nastavení a klikněte na tlačítko **Uložit**.

   ![Okno nastavení diagnostiky se vybrané možnosti][4]

### <a name="activity-log"></a>Protokol aktivit

Ve výchozím nastavení vygeneruje protokol aktivit Azure. Protokoly jsou zachovány po dobu 90 dnů v úložišti Azure protokoly událostí. Další informace o těchto protokolů najdete [zobrazení událostí a protokolu aktivity](../monitoring-and-diagnostics/insights-debugging-with-events.md) článku.

### <a name="access-log"></a>Přístup k protokolu

Přístup protokolu se vytvoří pouze v případě, že jste ho povolili pro každou instanci Application Gateway, jak je uvedeno v předchozích krocích. Data se ukládají v účtu úložiště, který jste zadali při povolování protokolování. Každý přístup ke službě Application Gateway se protokoluje ve formátu JSON, jak je znázorněno v následujícím příkladu:


|Hodnota  |Popis  |
|---------|---------|
|instanceId     | Instance služby Application Gateway, který žádost zpracoval.        |
|Když     | Původní IP adresa pro žádost.        |
|clientPort     | Výchozí port pro daný požadavek.       |
|Vlastnost HttpMethod     | Metoda HTTP použitá v požadavku.       |
|requestUri     | Identifikátor URI byl přijat požadavek.        |
|RequestQuery     | **Server směrovat**: Instance back endového fondu, který byl odeslán požadavek.</br>**X-AzureApplicationGateway-LOG-ID**: ID korelace použitou pro danou žádost. Slouží k řešení potíží s přenosy na back-end serverech. </br>**STAV SERVERU**: Kód odpovědi HTTP, které služba Application Gateway přijaté z back-endu.       |
|UserAgent     | Uživatelský agent uveden v hlavičce požadavku HTTP.        |
|httpStatus     | Stavový kód HTTP vrácen do klienta z aplikační brány.       |
|httpVersion     | Verze protokolu HTTP žádosti.        |
|ReceivedBytes     | Velikost paketu přijaté v bajtech.        |
|SentBytes| Velikost v bajtech odeslaných paketů.|
|timeTaken| Časový interval (v milisekundách), která je potřebná pro zpracování požadavku a odpovědi k odeslání. Počítá se jako intervalu od okamžiku, kdy služba Application Gateway přijímá první bajt požadavku HTTP na čas při odesílání odpovědi operace dokončí. Je důležité si uvědomit, že pole Time-Taken obvykle zahrnuje dobu, po kterou jsou pakety žádostí a odpovědí přenášeny přes síť. |
|tuhle| Určuje, zda komunikace s back endové fondy používá protokol SSL. Platné hodnoty jsou zapnout a vypnout.|
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

### <a name="performance-log"></a>V protokolu výkonu

V protokolu výkonu se vygeneruje pouze v případě, že je povolená pro každou instanci Application Gateway, jak je uvedeno v předchozích krocích. Data se ukládají v účtu úložiště, který jste zadali při povolování protokolování. Data protokolu výkonu je generován v intervalech po 1 minutu. Se protokolují tato data:


|Hodnota  |Popis  |
|---------|---------|
|instanceId     |  Instance služby Application Gateway pro které výkonu data jsou generována. Pro bránu aplikace s více instancí je jeden řádek pro každou instanci.        |
|HealthyHostCount     | Počet v dobrém stavu hostitelů ve fondu back-end.        |
|unHealthyHostCount     | Počet není v pořádku hostitelů ve fondu back-end.        |
|RequestCount     | Počet požadavků, které obsluhují.        |
|latence | Latence (v milisekundách) požadavků z instance do back-endu, který obsluhuje požadavky. |
|failedRequestCount| Počet neúspěšných žádostí.|
|Propustnost| Průměrná propustnost od poslední protokolu v bajtech za sekundu.|

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
> Latence se počítá od doby, kdy se při přijetí první bajt požadavku HTTP na čas odeslání poslední bajt odpovědi protokolu HTTP. Jedná se o součet doba zpracování Application Gateway a náklady na síť na back-endu. navíc čas, který přebírá back-endu ke zpracování požadavku.

### <a name="firewall-log"></a>Protokol brány firewall

Brány firewall protokolu se vytvoří pouze v případě, že je povolená pro každou application gateway popsané v předchozích krocích. Tento protokol taky vyžaduje, aby firewall webových aplikací je konfigurovány ve službě application gateway. Data se ukládají v účtu úložiště, který jste zadali při povolování protokolování. Se protokolují tato data:


|Hodnota  |Popis  |
|---------|---------|
|instanceId     | Instance služby Application Gateway pro které firewall data jsou generována. Pro bránu aplikace s více instancí je jeden řádek pro každou instanci.         |
|Když     |   Původní IP adresa pro žádost.      |
|clientPort     |  Výchozí port pro daný požadavek.       |
|requestUri     | Adresa URL přijatý požadavek.       |
|ruleSetType     | Typ sady pravidel. Je k dispozici hodnota OWASP.        |
|ruleSetVersion     | Verze se používá sada pravidel. Dostupné jsou hodnoty 2.2.9 a 3.0.     |
|RuleId     | ID pravidla spouštěcí události.        |
|zpráva     | Uživatelsky přívětivé zprávu pro aktivační událost. Další podrobnosti jsou uvedeny v části Podrobnosti.        |
|action     |  Akce v požadavku. Dostupné jsou hodnoty blokováno a povolené.      |
|Web     | Web, pro který byl vygenerován v protokolu. V současné době pouze globální je uvedené, protože pravidla jsou globální.|
|Podrobnosti     | Podrobnosti o spouštěcí události.        |
|details.Message     | Popis pravidla.        |
|details.data     | Konkrétní data uvedená v požadavku, který odpovídá pravidlo.         |
|details.File     | Konfigurační soubor, který obsahoval pravidlo.        |
|details.Line     | Číslo řádku v konfiguračním souboru, který spustil danou událost.       |

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

* **Nástroje Azure**: Načíst informace z protokolu aktivit pomocí Azure Powershellu, rozhraní příkazového řádku Azure, rozhraní REST API služby Azure nebo na webu Azure portal. Podrobné pokyny k jednotlivým metodám najdete v článku o [operacích s protokoly aktivit ve Správci prostředků](../azure-resource-manager/resource-group-audit.md).
* **Power BI**: Pokud ještě nemáte [Power BI](https://powerbi.microsoft.com/pricing) účet, můžete zkusit ho zdarma. Díky [balíčku obsahu protokoly aktivit Azure pro Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/) můžete svá data analyzovat pomocí předkonfigurovaných řídicích panelů, které můžete použít okamžitě nebo si je upravit.

### <a name="view-and-analyze-the-access-performance-and-firewall-logs"></a>Zobrazení a analýza přístup, výkonu a protokoly brány firewall

Azure [Log Analytics](../azure-monitor/insights/azure-networking-analytics.md) může shromažďovat soubory čítač a protokol událostí z vašeho účtu úložiště objektů Blob. Zahrnuje vizualizace a výkonné vyhledávací funkce k analýze protokolů.

Můžete se také připojit k účtu úložiště a načíst položky protokolu JSON s protokoly přístupu a výkonu. Po stažení souborů JSON je můžete převést do formátu CSV a zobrazit si je v Excelu, Power BI nebo jiném nástroji s vizualizací dat.

> [!TIP]
> Pokud znáte Visual Studio a máte představu, jak u konstant a proměnných v jazyce C# měnit hodnoty, můžete použít [nástroje pro převedení protokolů](https://github.com/Azure-Samples/networking-dotnet-log-converter), které jsou k dispozici na GitHubu.
> 
> 

#### <a name="analyzing-access-logs-through-goaccess"></a>Analýza přístup k protokolům prostřednictvím GoAccess

Jsme publikovali šablony Resource Manageru, který nainstaluje a spustí Oblíbené [GoAccess](https://goaccess.io/) protokolu analyzátoru protokolů Application Gateway přístup. GoAccess poskytuje cenné statistiku provozu HTTP jako je například jedinečných návštěvníků, vyžádané soubory, hostitele, operační systémy, prohlížeče, stavové kódy HTTP a další. Další podrobnosti najdete [souboru Readme do složky šablony Resource Manageru v Githubu](https://aka.ms/appgwgoaccessreadme).

## <a name="metrics"></a>Metriky

Metriky jsou funkce pro určité prostředky Azure, kde můžete zobrazit čítače výkonu na portálu. Pro službu Application Gateway jsou dostupné následující metriky:

- **Aktuální počet připojení**
- **Neúspěšné požadavky**
- **V pořádku. počet hostitelů**

   Můžete filtrovat základě back-endový fond zobrazíte v pořádku a není v pořádku hostitelů v konkrétním back-endový fond.


- **Stav odpovědi**

   Rozdělení kódů odpovědí stavu lze dále rozdělit k zobrazení odpovědi v 2xx, 3xx, 4xx a 5xx kategorií.

- **Propustnost**
- **Celkový počet požadavků**
- **Není v pořádku. počet hostitelů**

   Můžete filtrovat základě back-endový fond zobrazíte v pořádku a není v pořádku hostitelů v konkrétním back-endový fond.

Přejděte do služby application gateway, v části **monitorování** klikněte na tlačítko **metriky**. Chcete-li zobrazit dostupné hodnoty, vyberte rozevírací seznam **METRIKA**.

Na následujícím obrázku uvidíte příklad tří metrik, které se zobrazí za posledních 30 minut:

[![](media/application-gateway-diagnostics/figure5.png "zobrazení metrik")](media/application-gateway-diagnostics/figure5-lb.png#lightbox)

Aktuální seznam metrik najdete v tématu [podporované metriky ve službě Azure Monitor](../monitoring-and-diagnostics/monitoring-supported-metrics.md).

### <a name="alert-rules"></a>Pravidla upozornění

Můžete začít pravidla upozornění na základě metrik pro prostředek. Výstrahu můžete například volání webhooku nebo e-mailu správce, pokud propustnosti služby application gateway je výše, níže nebo při prahové hodnotě během zadaného období.

Následující příklad vás provede procesem vytvoření pravidla upozornění, která odešle e-mail správce po propustnost porušení mezní hodnoty:

1. Klikněte na tlačítko **přidat upozornění metriky** otevřít **přidat pravidlo** okno. Můžete také kontaktovat tohoto okna v okně metriky.

   ![Tlačítko "Přidat upozornění metriky"][6]

2. Na **přidat pravidlo** okno, vyplňte název podmínky a upozornit části a klikněte na tlačítko **OK**.

   * V **podmínku** selektor, vyberte jednu ze čtyř hodnot: **Větší než**, **větší než nebo rovna**, **menší než**, nebo **menší než nebo rovno**.

   * V **období** selektor, vyberte období pět minut až šest hodin.

   * Pokud vyberete **e-mailu vlastníci, přispěvatelé a čtenáři**, e-mailu může být dynamické na základě uživatelů, kteří mají přístup do tohoto prostředku. V opačném případě můžete zadat čárkou oddělený seznam uživatelů v **další email(s) správce** pole.

   ![Přidat pravidlo okno][7]

Pokud překročení prahové hodnoty přijde nový e-mail, který je podobný tomu na následujícím obrázku:

![E-mailu pro porušení prahové hodnoty][8]

Po vytvoření upozornění na metriku se zobrazí seznam výstrah. Poskytuje přehled o všech pravidel upozornění.

![Seznam výstrah a pravidla][9]

Další informace o oznámeních výstrah najdete v tématu [doručování oznámení o upozorněních](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

Informace o tom Další informace o webhooky a jak je můžete využít s výstrahami, navštivte [konfigurace webhooku v upozornění Azure metriky](../azure-monitor/platform/alerts-webhooks.md).

## <a name="next-steps"></a>Další postup

* Vizualizujte čítače a protokoly událostí s využitím [Log Analytics](../azure-monitor/insights/azure-networking-analytics.md).
* [Vizualizujte váš protokol aktivit Azure v Power BI](https://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) blogový příspěvek.
* [Zobrazení a analýza protokolů aktivit Azure v Power BI a další](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) blogový příspěvek.

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
