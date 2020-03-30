---
title: Poradce při potížích – brána firewall webových aplikací Azure
description: Tento článek obsahuje informace o řešení potíží pro bránu FIREWALL webových aplikací (WAF) pro bránu aplikací Azure
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: ant
ms.topic: conceptual
ms.openlocfilehash: 33c85752903edd618044ccbab06aff7df9a791da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74046197"
---
# <a name="troubleshoot-web-application-firewall-waf-for-azure-application-gateway"></a>Poradce při potížích s bránou firewall webových aplikací (WAF) pro aplikační bránu Azure

Existuje několik věcí, které můžete udělat, pokud jsou blokovány požadavky, které by měly projít bránou firewall webové aplikace (WAF).

Nejprve se ujistěte, že jste si přečetli [přehled WAF](ag-overview.md) a [konfigurační dokumenty WAF.](application-gateway-waf-configuration.md) Také se ujistěte, že jste povolili [sledování WAF](../../application-gateway/application-gateway-diagnostics.md) Tyto články vysvětlují, jak waf funguje, jak fungují sady pravidel WAF a jak získat přístup k protokolům WAF.

## <a name="understanding-waf-logs"></a>Principy protokolů WAF

Účelem protokolů WAF je zobrazit každý požadavek, který je spárován nebo blokován WAF. Jedná se o hlavní knihu všech vyhodnocených požadavků, které jsou spárovány nebo blokovány. Pokud zjistíte, že WAF blokuje požadavek, že by neměl (falešně pozitivní), můžete udělat pár věcí. Nejprve zúžete a najděte konkrétní požadavek. Prohlédněte si protokoly a vyhledejte konkrétní identifikátor URI, časové razítko nebo ID transakce požadavku. Když najdete přidružené položky protokolu, můžete začít jednat na falešně pozitivních hodnot.

Řekněme například, že máte legitimní provoz obsahující řetězec *1 = 1,* který chcete projít waf. Pokud se pokusíte požadavek, WAF blokuje provoz, který obsahuje váš řetězec *1 = 1* v libovolném parametru nebo poli. Toto je řetězec často spojené s útokem injektáže SQL. Můžete se podívat do protokolů a zobrazit časové razítko požadavku a pravidla, která zablokovala/ uzavřeno.

V následujícím příkladu uvidíte, že čtyři pravidla jsou spuštěny během stejného požadavku (pomocí transactionid pole). První z nich říká, že odpovídá, protože uživatel použil číselnou adresu IP pro požadavek, což zvyšuje skóre anomálií o tři, protože je to varování. Další pravidlo, které odpovídá, je 942130, což je jedno, které hledáte. Můžete vidět *1 =* 1 `details.data` v poli. Tím se skóre anomálií opět zvýší o tři, protože je to také varování. Obecně platí, že každé pravidlo, které má akci **Uzavřeno** zvyšuje skóre anomálie a v tomto okamžiku by skóre anomálie bylo šest. Další informace naleznete v [tématu Režim hodnocení anomálií](ag-overview.md#anomaly-scoring-mode).

Poslední dvě položky protokolu ukazují, že požadavek byl zablokován, protože skóre anomálií bylo dostatečně vysoké. Tyto položky mají jinou akci než ostatní dva. Ukazují, že vlastně *zablokovali* žádost. Tato pravidla jsou povinná a nelze je zakázat. Neměly by být považovány za pravidla, ale spíše jako základní infrastrukturu vnitřních waf.

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

## <a name="fixing-false-positives"></a>Oprava falešně pozitivních výsledků

S těmito informacemi a znalosti, že pravidlo 942130 je ten, který odpovídá řetězec *1 = 1,* můžete udělat několik věcí, aby to z blokování provozu:

- Použití seznamu vyloučení

   Další informace o seznamech vyloučení naleznete v [konfiguraci WAF.](application-gateway-waf-configuration.md#waf-exclusion-lists)
- Zakažte pravidlo.

### <a name="using-an-exclusion-list"></a>Použití seznamu vyloučení

Chcete-li učinit informované rozhodnutí o zpracování falešně pozitivní, je důležité seznámit se s technologiemi, které vaše aplikace používá. Řekněme například, že není sql server v zásobníku technologie a dochází k falešným poplachům souvisejícím s těmito pravidly. Zakázání těchto pravidel nemusí nutně oslabit vaši bezpečnost.

Jednou z výhod použití seznamu vyloučení je, že je zakázána pouze určitá část požadavku. To však znamená, že konkrétní vyloučení je použitelné pro veškerý provoz procházející waf, protože se jedná o globální nastavení. Například to může vést k problému, pokud *1 = 1* je platný požadavek v těle pro určitou aplikaci, ale ne pro ostatní. Další výhodou je, že si můžete vybrat mezi tělem, záhlavím a soubory cookie, které mají být vyloučeny, pokud je splněna určitá podmínka, na rozdíl od vyloučení celé žádosti.

V některých případech existují případy, kdy konkrétní parametry získat předány do WAF způsobem, který nemusí být intuitivní. Například je token, který získá předánpři ověřování pomocí služby Azure Active Directory. Tento *token, __RequestVerificationToken*, se obvykle předá jako soubor cookie požadavku. V některých případech, kdy jsou zakázány soubory cookie, je však tento token také předán jako atribut požadavku nebo "arg". Pokud k tomu dojde, je třeba zajistit, aby *__RequestVerificationToken* je přidán do seznamu vyloučení jako **název atributu Požadavek** také.

![Vyloučení](../media/web-application-firewall-troubleshoot/exclusion-list.png)

V tomto příkladu chcete vyloučit **název atributu Request,** který se rovná *textu1*. To je zřejmé, protože v protokolech brány firewall vidíte název atributu: **data: Spárovaná data: 1=1 nalezená v rámci ARGS:text1: 1=1**. Atribut je **text1**. Tento název atributu můžete najít také několika dalšími způsoby, viz [Hledání názvů atributů požadavku](#finding-request-attribute-names).

![Seznamy vyloučení WAF](../media/web-application-firewall-troubleshoot/waf-config.png)

### <a name="disabling-rules"></a>Zakázání pravidel

Dalším způsobem, jak obejít falešně pozitivní, je zakázat pravidlo, které odpovídalo vstupu, o kterém waf myslel, že je škodlivý. Vzhledem k tomu, že jste analyzovali protokoly WAF a zúžili pravidlo na 942130, můžete ho zakázat na webu Azure Portal. Viz [Přizpůsobení pravidel brány firewall webových aplikací prostřednictvím portálu Azure](application-gateway-customize-waf-rules-portal.md).

Jednou z výhod zakázání pravidla je, že pokud víte, že veškerý provoz, který obsahuje určitou podmínku, která bude normálně blokována, je platný provoz, můžete toto pravidlo zakázat pro celý WAF. Pokud se však jedná pouze o platný provoz v konkrétním případě použití, otevřete chybu zabezpečení zakázáním tohoto pravidla pro celý soubor WAF, protože se jedná o globální nastavení.

Pokud chcete používat Azure PowerShell, přečtěte si témat [u tématu Přizpůsobení pravidel brány firewall webových aplikací pomocí PowerShellu](application-gateway-customize-waf-rules-powershell.md). Pokud chcete používat Azure CLI, [přečtěte si témat uvlastního nastavení brány firewall pomocí azure CLI](application-gateway-customize-waf-rules-cli.md).

![Pravidla WAF](../media/web-application-firewall-troubleshoot/waf-rules.png)

## <a name="finding-request-attribute-names"></a>Hledání názvů atributů požadavku

Pomocí [Fiddler](https://www.telerik.com/fiddler), můžete zkontrolovat jednotlivé požadavky a určit, jaké konkrétní pole webové stránky se nazývají. To může pomoci vyloučit určitá pole z kontroly pomocí seznamů vyloučení.

V tomto příkladu uvidíte, že pole, ve kterém byl zadán řetězec *1=1,* se nazývá **text1**.

![Fiddler](../media/web-application-firewall-troubleshoot/fiddler-1.png)

Toto pole můžete vyloučit. Další informace o seznamech vyloučení naleznete v [tématu Omezení velikosti brány firewall webových aplikací a seznamy vyloučení](application-gateway-waf-configuration.md#waf-exclusion-lists). Hodnocení můžete v tomto případě vyloučit konfigurací následujícího vyloučení:

![Vyloučení WAF](../media/web-application-firewall-troubleshoot/waf-exclusion-02.png)

Můžete také zkontrolovat protokoly brány firewall a získat informace a zjistit, co je třeba přidat do seznamu vyloučení. Chcete-li povolit protokolování, naleznete [v tématu Stav back-endu, diagnostické protokoly a metriky pro aplikační bránu](../../application-gateway/application-gateway-diagnostics.md).

Zkontrolujte protokol brány firewall a zobrazte soubor PT1H.json po dobu, po kterou došlo k požadavku, který chcete zkontrolovat.

V tomto příkladu můžete vidět, že máte čtyři pravidla se stejným TransactionID a že všechny došlo ve stejnou dobu:

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

Se znalostí o tom, jak sady pravidel CRS fungují a že sada pravidel CRS 3.0 pracuje se systémem hodnocení anomálií (viz [Brána webových aplikací firewall pro bránu aplikací Azure)](ag-overview.md)víte, že dvě dolní pravidla s **akcí: Blokované** vlastnosti blokují na základě celkového skóre anomálií. Pravidla, na která se zaměřit, jsou první dvě.

První položka je zaznamenána, protože uživatel použil číselnou ADRESU IP k přechodu na aplikační bránu, kterou lze v tomto případě ignorovat.

Druhý (pravidlo 942130) je zajímavý. Můžete vidět v detailech, že odpovídá vzoru (1 = 1) a pole se nazývá **text1**. Stejným postupem vylučte **název atributu požadavku,** který **se rovná** **1=1**.

## <a name="finding-request-header-names"></a>Hledání názvů hlaviček požadavku

Fiddler je užitečný nástroj opět najít názvy hlavičky požadavku. Na následujícím snímku obrazovky můžete zobrazit záhlaví tohoto požadavku GET, které zahrnují *typ obsahu*, *user-agent*a tak dále.

![Fiddler](../media/web-application-firewall-troubleshoot/fiddler-2.png)

Dalším způsobem, jak zobrazit záhlaví požadavků a odpovědí, je podívat se do vývojářských nástrojů prohlížeče Chrome. Můžete stisknout klávesu F12 nebo klepnout pravým tlačítkem myši na příkaz -> **Zkontrolovat** -> **nástroje pro vývojáře**a vybrat kartu **Síť.** Načtěte webovou stránku a klikněte na požadavek, který chcete zkontrolovat.

![Chrom F12](../media/web-application-firewall-troubleshoot/chrome-f12.png)

## <a name="finding-request-cookie-names"></a>Hledání názvů souborů cookie požadavku

Pokud žádost obsahuje soubory cookie, lze kartu **Cookies** vybrat, aby se zobrazily v Šumaře.

## <a name="restrict-global-parameters-to-eliminate-false-positives"></a>Omezit globální parametry k odstranění falešných poplachů

- Zakázat kontrolu těla požadavku

   Nastavením **zkontrolovat tělo požadavku** na vypnuto, nebudou vaše WAF vyhodnocena těla požadavků všech přenosů. To může být užitečné, pokud víte, že těla požadavků nejsou škodlivé pro vaši aplikaci.

   Zakázáním této možnosti není zkontrolováno pouze tělo požadavku. Záhlaví a soubory cookie zůstávají kontrolovány, pokud nejsou jednotlivé vyloučeny pomocí funkce seznamu vyloučení.

- Omezení velikosti souboru

   Omezením velikosti souboru pro waf omezujete možnost útoku na vaše webové servery. Povolením nahrávání velkých souborů se zvyšuje riziko, že váš back-end bude zahlcen. Omezení velikosti souboru na normální případ použití aplikace je pouze další způsob, jak zabránit útokům.

   > [!NOTE]
   > Pokud víte, že vaše aplikace nikdy nebude potřebovat žádné nahrávání souborů nad danou velikost, můžete ji omezit nastavením limitu.

## <a name="firewall-metrics-waf_v1-only"></a>Metriky brány firewall (pouze WAF_v1)

Pro brány firewall webových aplikací v1 jsou nyní na portálu k dispozici následující metriky: 

1. Brána firewall pro blokované požadavky webové aplikace Počet požadavků Počet požadavků, které byly zablokovány
2. Brána firewall webových aplikací zablokovala počet pravidel Všechna pravidla, která byla spárována **a** požadavek byl zablokován
3. Distribuce celkového pravidla brány firewall webové aplikace Všechna pravidla, která byla spárována během hodnocení
     
Chcete-li povolit metriky, vyberte kartu **Metriky** na portálu a vyberte jednu ze tří metrik.

## <a name="next-steps"></a>Další kroky

Viz [Jak nakonfigurovat bránu firewall webových aplikací v application gateway](tutorial-restrict-web-traffic-powershell.md).
