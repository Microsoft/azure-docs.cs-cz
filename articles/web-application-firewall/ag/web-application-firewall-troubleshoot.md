---
title: Řešení potíží – Firewall webových aplikací Azure
description: Tento článek poskytuje informace o řešení potíží pro Firewall webových aplikací (WAF) pro Azure Application Gateway
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: ant
ms.topic: conceptual
ms.openlocfilehash: 483d261a8cc107d01cfb7a405eac43667d7efcc6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92131832"
---
# <a name="troubleshoot-web-application-firewall-waf-for-azure-application-gateway"></a>Řešení potíží s firewallem webových aplikací (WAF) pro Azure Application Gateway

Existuje několik věcí, které můžete provést v případě, že požadavky, které by měly projít Firewall webových aplikací (WAF), jsou blokované.

Nejdřív se ujistěte, že jste si přečetli [Přehled WAF](ag-overview.md) a [konfigurační dokumenty WAF](application-gateway-waf-configuration.md) . Také se ujistěte, že jste povolili [monitorování WAF](../../application-gateway/application-gateway-diagnostics.md) tyto články vysvětlují, jak funguje funkce WAF, jak pravidlo WAF nastavuje práci a jak získat přístup k protokolům WAF.

OWASP RuleSets jsou navržené tak, aby byly velmi přísné, a vyladěny tak, aby vyhovovaly konkrétním potřebám aplikace nebo organizace pomocí WAF. Je zcela normální a ve skutečnosti se v mnoha případech očekává, že se mají vytvářet vyloučení, vlastní pravidla a dokonce i zakázat pravidla, která mohou způsobovat problémy nebo falešně pozitivní. Zásady vázané na lokalitu a pro identifikátor URI umožňují, aby tyto změny byly ovlivněny pouze na konkrétní lokality nebo identifikátory URI, takže jakékoli změny by neměly mít vliv na jiné lokality, které nemusí být spuštěny se stejnými problémy. 

## <a name="understanding-waf-logs"></a>Principy protokolů WAF

Účelem protokolu WAF je zobrazení všech požadavků, které jsou porovnány nebo blokovány rozhraním WAF. Je to hlavní kniha všech vyhodnocených požadavků, které jsou shodné nebo blokované. Pokud si všimnete, že WAF blokuje požadavek, který by neměl (falešně pozitivní), můžete provést několik věcí. Nejprve upřesněte a najděte konkrétní požadavek. Prohlédněte si protokoly a najděte konkrétní identifikátor URI, časové razítko nebo ID transakce žádosti. Když najdete přidružené položky protokolu, můžete začít působit na falešně pozitivních hodnot.

Řekněme například, že máte legitimní provoz obsahující řetězec *1 = 1* , který chcete předat vaší WAF. Pokud si vyzkoušíte požadavek, WAF blokuje přenos, který obsahuje váš *1 = 1* řetězec v jakémkoli parametru nebo poli. Toto je řetězec často spojený s útokem na injektáže SQL. Můžete procházet protokoly a zobrazit časové razítko požadavku a pravidla, která jsou blokovaná/spárována.

V následujícím příkladu vidíte, že se během stejné žádosti spouštějí čtyři pravidla (pomocí pole TransactionId). První z nich odpovídá tomu, že uživatel použil pro požadavek číselnou adresu URL, která zvyšuje skóre anomálií o tři, protože se jedná o upozornění. Další pravidlo, které odpovídá, je 942130, což je ten, který hledáte. V poli můžete zobrazit *1 = 1* `details.data` . Tím se ještě třikrát zvyšuje skóre anomálií, protože se jedná i o upozornění. Obecně platí, že každé pravidlo, které **odpovídá** akci, zvyšuje skóre anomálií a v tomto okamžiku bude skóre anomálie šest. Další informace najdete v tématu [režim bodování anomálií](ag-overview.md#anomaly-scoring-mode).

Poslední dvě položky protokolu ukázaly, že žádost byla zablokována, protože skóre anomálie bylo dostatečně vysoké. Tyto položky mají jinou akci než druhá druhá. Ukazují, že jsou ve skutečnosti *zablokované* . Tato pravidla jsou povinná a nejde je zakázat. Nemusely být považovány za pravidla, ale více jako základní infrastruktura WAF interních.

```json
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "OWASP_CRS", 
        "ruleSetVersion": "3.0.0", 
        "ruleId": "920350", 
        "message": "Host header is a numeric IP address", 
        "action": "Matched", 
        "site": "Global", 
        "details": { 
            "message": "Warning. Pattern match \\\"^[\\\\\\\\d.:]+$\\\" at REQUEST_HEADERS:Host. ", 
            "data": "40.90.218.160", 
            "file": "rules\/REQUEST-920-PROTOCOL-ENFORCEMENT.conf\\\"", 
            "line": "791" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    } 
} 
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "OWASP_CRS", 
        "ruleSetVersion": "3.0.0", 
        "ruleId": "942130", 
        "message": "SQL Injection Attack: SQL Tautology Detected.", 
        "action": "Matched", 
        "site": "Global", 
        "details": { 
            "message": "Warning. Pattern match \\\"(?i:([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)([\\\\\\\\d\\\\\\\\w]++)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?:(?:=|\\u003c=\\u003e|r?like|sounds\\\\\\\\s+like|regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)\\\\\\\\2|(?:!=|\\u003c=|\\u003e=|\\u003c\\u003e|\\u003c|\\u003e|\\\\\\\\^|is\\\\\\\\s+not|not\\\\\\\\s+like|not\\\\\\\\s+regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?!\\\\\\\\2)([\\\\\\\\d\\\\\\\\w]+)))\\\" at ARGS:text1. ", 
            "data": "Matched Data: 1=1 found within ARGS:text1: 1=1", 
            "file": "rules\/REQUEST-942-APPLICATION-ATTACK-SQLI.conf\\\"", 
            "line": "554" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    } 
} 
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "", 
        "ruleSetVersion": "", 
        "ruleId": "0", 
        "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Score: 8)", 
        "action": "Blocked", 
        "site": "Global", 
        "details": { 
            "message": "Access denied with code 403 (phase 2). Operator GE matched 5 at TX:anomaly_score. ", 
            "data": "", 
            "file": "rules\/REQUEST-949-BLOCKING-EVALUATION.conf\\\"", 
            "line": "57" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    } 
} 
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "", 
        "ruleSetVersion": "", 
        "ruleId": "0", 
        "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Inbound Score: 8 - SQLI=5,XSS=0,RFI=0,LFI=0,RCE=0,PHPI=0,HTTP=0,SESS=0): SQL Injection Attack: SQL Tautology Detected.", 
        "action": "Blocked", 
        "site": "Global", 
        "details": { 
            "message": "Warning. Operator GE matched 5 at TX:inbound_anomaly_score. ", 
            "data": "", 
            "file": "rules\/RESPONSE-980-CORRELATION.conf\\\"", 
            "line": "73" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    }
}
```

## <a name="fixing-false-positives"></a>Oprava falešně pozitivních hodnot

S těmito informacemi a znalostmi, které pravidlo 942130 odpovídá řetězci *1 = 1* , můžete provést několik akcí, které zabrání v blokování provozu:

- Použití seznamu vyloučení

   Další informace o seznamech vyloučení najdete v tématu [Konfigurace WAF](application-gateway-waf-configuration.md#waf-exclusion-lists) .
- Zakáže pravidlo.

### <a name="using-an-exclusion-list"></a>Použití seznamu vyloučení

Aby se zajistilo, že bude mít informovaná rozhodnutí o zpracování falešně pozitivních hodnot, je důležité se seznámit s technologiemi, které vaše aplikace používá. Řekněme například, že ve vašem technologickém zásobníku není SQL Server a že se s těmito pravidly dostanete falešně pozitivní výsledky. Zakázání těchto pravidel neznamená nutně oslabení zabezpečení.

Jednou z výhod používání seznamu vyloučení je, že je zakázaná jenom určitá část požadavku. To ale znamená, že konkrétní vyloučení se vztahuje na veškerý provoz procházející vaším WAF, protože se jedná o globální nastavení. Například to může vést k problému, pokud *1 = 1* je platný požadavek v těle určité aplikace, ale ne pro jiné. Další výhodou je, že si můžete vybrat mezi textem, hlavičkou a soubory cookie, které se mají vyloučit, pokud je splněná určitá podmínka, a to na rozdíl od úplného požadavku.

V některých případech existují případy, kdy se konkrétní parametry předávají do WAF způsobem, který nemusí být intuitivní. Například je k dispozici token, který se předává při ověřování pomocí Azure Active Directory. Tento token, *__RequestVerificationToken*, obvykle se předává jako soubor cookie žádosti. Nicméně v některých případech, kdy jsou soubory cookie zakázané, je tento token také předán jako atribut Request nebo ARG. Pokud k tomu dojde, musíte zajistit, aby se *__RequestVerificationToken* do seznamu vyloučení Přidal také jako **název atributu žádosti** .

![Vyloučení](../media/web-application-firewall-troubleshoot/exclusion-list.png)

V tomto příkladu chcete vyloučit **název atributu žádosti** , který se rovná *Text1*. To je zřejmé, protože můžete zobrazit název atributu v protokolech brány firewall: **data: vyhovující data: 1 = 1 nalezeno v rámci argumentů: Text1:1 = 1**. Atribut je **Text1**. Tento název atributu můžete také najít několika dalšími způsoby, viz [hledání názvů atributů žádosti](#finding-request-attribute-names).

![Seznamy vyloučení WAF](../media/web-application-firewall-troubleshoot/waf-config.png)

### <a name="disabling-rules"></a>Zakázání pravidel

Dalším způsobem, jak získat falešně pozitivní hodnotu, je zakázat pravidlo, které se shoduje se vstupem, na který WAF mysleli. Vzhledem k tomu, že jste analyzovali protokoly WAF a zúžené pravidlo na 942130, můžete ho zakázat v Azure Portal. Viz [přizpůsobení pravidel firewallu webových aplikací pomocí Azure Portal](application-gateway-customize-waf-rules-portal.md).

Jednou z výhod zakázání pravidla je, že pokud víte, že veškerý provoz obsahující určitou podmínku, která bude normálně blokovaná, je platný provoz, můžete toto pravidlo zakázat pro celou WAF. Pokud se ale jenom jedná o platný provoz v konkrétním případu použití, otevřete chybu zabezpečení tím, že toto pravidlo zakážete pro celou WAF, protože se jedná o globální nastavení.

Pokud chcete použít Azure PowerShell, přečtěte si téma [přizpůsobení pravidel firewallu webových aplikací prostřednictvím PowerShellu](application-gateway-customize-waf-rules-powershell.md). Pokud chcete použít rozhraní příkazového řádku Azure, přečtěte si téma [přizpůsobení pravidel firewallu webových aplikací prostřednictvím rozhraní příkazového řádku Azure](application-gateway-customize-waf-rules-cli.md).

![Pravidla WAF](../media/web-application-firewall-troubleshoot/waf-rules.png)

## <a name="finding-request-attribute-names"></a>Hledání názvů atributů žádosti

Pomocí [Fiddler](https://www.telerik.com/fiddler)můžete zkoumat jednotlivé požadavky a určit, jaká konkrétní pole webové stránky jsou volána. To může pomoci při vyloučení určitých polí z kontroly pomocí seznamů vyloučení.

V tomto příkladu vidíte, že pole, kde byl zadán řetězec *1 = 1* , se nazývá **Text1**.

:::image type="content" source="../media/web-application-firewall-troubleshoot/fiddler-1.png" alt-text="Snímek obrazovky s průběhem webového ladicího programu Telerik Fiddler Na kartě Raw se po názvu Text1 zobrazuje 1 = 1." border="false":::

Toto pole můžete vyloučit. Další informace o seznamech vyloučení najdete v tématu [omezení velikosti požadavků firewallu webových aplikací a seznamů vyloučení](application-gateway-waf-configuration.md#waf-exclusion-lists). Vyhodnocování můžete v tomto případě vyloučit konfigurací následujícího vyloučení:

![WAF vyloučení](../media/web-application-firewall-troubleshoot/waf-exclusion-02.png)

Můžete také prostudovat protokoly brány firewall a získat informace, abyste viděli, co je třeba přidat do seznamu vyloučení. Pokud chcete povolit protokolování, přečtěte si téma [stav back-endu, protokoly prostředků a metriky pro Application Gateway](../../application-gateway/application-gateway-diagnostics.md).

Zkontrolujte protokol brány firewall a zobrazte PT1H.jsv souboru po dobu, po kterou došlo k žádosti, kterou chcete zkontrolovat.

V tomto příkladu vidíte, že máte čtyři pravidla se stejným TransactionID a že ke všem došlo ve stejnou dobu.

```json
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "OWASP_CRS",
-           "ruleSetVersion": "3.0.0",
-           "ruleId": "920350",
-           "message": "Host header is a numeric IP address",
-           "action": "Matched",
-           "site": "Global",
-           "details": {
-               "message": "Warning. Pattern match \\\"^[\\\\\\\\d.:]+$\\\" at REQUEST_HEADERS:Host. ",
-               "data": "40.90.218.160",
-               "file": "rules\/REQUEST-920-PROTOCOL-ENFORCEMENT.conf\\\"",
-               "line": "791"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "OWASP_CRS",
-           "ruleSetVersion": "3.0.0",
-           "ruleId": "942130",
-           "message": "SQL Injection Attack: SQL Tautology Detected.",
-           "action": "Matched",
-           "site": "Global",
-           "details": {
-               "message": "Warning. Pattern match \\\"(?i:([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)([\\\\\\\\d\\\\\\\\w]++)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?:(?:=|\\u003c=\\u003e|r?like|sounds\\\\\\\\s+like|regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)\\\\\\\\2|(?:!=|\\u003c=|\\u003e=|\\u003c\\u003e|\\u003c|\\u003e|\\\\\\\\^|is\\\\\\\\s+not|not\\\\\\\\s+like|not\\\\\\\\s+regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?!\\\\\\\\2)([\\\\\\\\d\\\\\\\\w]+)))\\\" at ARGS:text1. ",
-               "data": "Matched Data: 1=1 found within ARGS:text1: 1=1",
-               "file": "rules\/REQUEST-942-APPLICATION-ATTACK-SQLI.conf\\\"",
-               "line": "554"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "",
-           "ruleSetVersion": "",
-           "ruleId": "0",
-           "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Score: 8)",
-           "action": "Blocked",
-           "site": "Global",
-           "details": {
-               "message": "Access denied with code 403 (phase 2). Operator GE matched 5 at TX:anomaly_score. ",
-               "data": "",
-               "file": "rules\/REQUEST-949-BLOCKING-EVALUATION.conf\\\"",
-               "line": "57"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "",
-           "ruleSetVersion": "",
-           "ruleId": "0",
-           "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Inbound Score: 8 - SQLI=5,XSS=0,RFI=0,LFI=0,RCE=0,PHPI=0,HTTP=0,SESS=0): SQL Injection Attack: SQL Tautology Detected.",
-           "action": "Blocked",
-           "site": "Global",
-           "details": {
-               "message": "Warning. Operator GE matched 5 at TX:inbound_anomaly_score. ",
-               "data": "",
-               "file": "rules\/RESPONSE-980-CORRELATION.conf\\\"",
-               "line": "73"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
```

S vaším vědomím, jak pravidlo pro dodržování předpisů funguje a že počítačový RuleSet 3,0 funguje se systémem bodování anomálií (viz téma [Brána Firewall webových aplikací pro Azure Application Gateway](ag-overview.md)) víte, že dolní dvě pravidla s **akcí: blokovaná** vlastnost je blokována na základě celkového skóre anomálií. Mezi pravidla, která je potřeba zaměřit, patří horních dvou.

První položka je zaznamenána, protože uživatel použil číselnou IP adresu k přechodu na Application Gateway, která může být v tomto případě ignorována.

Druhá je zajímavá (pravidlo 942130). Můžete se podívat v podrobnostech, které se shodují se vzorem (1 = 1), a pole má název **Text1**. Použijte stejné předchozí kroky pro vyloučení **názvu atributu žádosti** , který **se rovná** **1 = 1**.

## <a name="finding-request-header-names"></a>Vyhledávají se názvy hlaviček požadavků.

Fiddler je užitečný nástroj znovu pro vyhledání názvů hlaviček požadavků. Na následujícím snímku obrazovky vidíte záhlaví této žádosti o získání, což zahrnuje *typ obsahu*, *uživatelský agent* atd.

:::image type="content" source="../media/web-application-firewall-troubleshoot/fiddler-2.png" alt-text="Snímek obrazovky s průběhem webového ladicího programu Telerik Fiddler Nezpracovaná karta obsahuje podrobnosti hlavičky požadavku, jako je připojení, typ obsahu a Agent pro uživatele." border="false":::

Další možností zobrazení hlaviček žádostí a odpovědí je prohledání v vývojářských nástrojích Chrome. Můžete stisknout klávesu F12 nebo kliknout pravým tlačítkem – > **zkontrolovat**  ->  **vývojářské nástroje** a vybrat kartu **síť** . načtěte webovou stránku a klikněte na žádost, kterou chcete zkontrolovat.

![Chrome F12](../media/web-application-firewall-troubleshoot/chrome-f12.png)

## <a name="finding-request-cookie-names"></a>Hledání názvů souborů cookie žádosti

Pokud požadavek obsahuje soubory cookie, může být vybrána karta **soubory cookie** , aby se zobrazila v Fiddler.

## <a name="restrict-global-parameters-to-eliminate-false-positives"></a>Omezení globálních parametrů pro odstranění falešně pozitivních hodnot

- Zakázat kontrolu textu žádosti

   Když nastavíte možnost **kontrolovat tělo požadavku** na vypnuto, texty požadavků všech přenosů nebudou vyhodnoceny vaším WAF. To může být užitečné, pokud víte, že těla žádosti nejsou škodlivá pro vaši aplikaci.

   Zakázáním této možnosti není kontrolováno pouze tělo žádosti. Záhlaví a soubory cookie zůstávají zkontrolovány, pokud nejsou vyloučeny pomocí funkce seznamu vyloučení.

- Omezení velikosti souborů

   Omezením velikosti souboru pro WAF omezíte možnost útoku, který se děje na vaše webové servery. Díky povolení nahrávání velkých souborů se zvyšuje riziko zahlceného back-endu. Omezení velikosti souboru na normálním případu použití pro vaši aplikaci je pouze jiný způsob, jak zabránit útokům.

   > [!NOTE]
   > Pokud víte, že vaše aplikace nebude nikdy potřebovat žádné nahrávání souborů nad danou velikost, můžete omezení omezit nastavením limitu.

## <a name="firewall-metrics-waf_v1-only"></a>Metriky brány firewall (jenom WAF_v1)

V případě bran firewall webových aplikací v1 jsou nyní na portálu k dispozici následující metriky: 

1. Počet zablokovaných požadavků firewallu webových aplikací na základě blokování
2. Blokované pravidlo brány firewall webových aplikací počítá všechna pravidla, která se shodovala, **a** požadavek byl zablokován.
3. Všechna pravidla, která se při vyhodnocování shodovala, se budou distribuovat všechna pravidla firewallu webových aplikací.
     
Pokud chcete povolit metriky, vyberte na portálu kartu **metriky** a vyberte jednu ze tří metrik.

## <a name="next-steps"></a>Další kroky

Viz [jak nakonfigurovat Firewall webových aplikací na Application Gateway](tutorial-restrict-web-traffic-powershell.md).
