---
title: Řešení potíží s firewallu webových aplikací služby Azure Application Gateway
description: Tento článek obsahuje informace o odstraňování potíží pro Firewall webových aplikací (WAF) služby Azure Application Gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 6/11/2019
ms.author: ant
ms.topic: conceptual
ms.openlocfilehash: b81ffc5769fa5521094734da5d00aab77aa7cd35
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67082440"
---
# <a name="troubleshoot-web-application-firewall-waf-for-azure-application-gateway"></a>Řešení Firewall webových aplikací (WAF) služby Azure Application Gateway.

Existuje několik věcí, které můžete provést, pokud jsou blokované požadavky, které by měla předat prostřednictvím váš Firewall webových aplikací (WAF).

Nejprve, zkontrolujte, že jste si přečetli [WAF přehled](waf-overview.md) a [konfiguraci WAF](application-gateway-waf-configuration.md) dokumenty. Také se ujistěte, že jste povolili [monitorování WAF](application-gateway-diagnostics.md) tyto články vysvětlují, jak WAF funguje, jak sad pravidel WAF práci a jak získat přístup k protokolů WAF.

## <a name="understanding-waf-logs"></a>Principy WAF protokoly

Účelem protokolů WAF je zobrazíte všechny požadavky, které jsou porovnány nebo blokuje WAF. Je to hlavní knihy všechny vyhodnocené požadavků, které jsou porovnány nebo blokovaný. Pokud si všimnete, že WAF blokuje žádosti, nesmí (falešně pozitivní), můžete provést několik věcí. Nejprve zúžit a najít konkrétní žádost. Prohlédněte si protokoly a hledat konkrétní identifikátor URI, časové razítko nebo transakce ID požadavku. Když najdete položky protokolu přidružené můžete začít tak, aby fungoval na počet falešně pozitivních výsledků.

Řekněme například, že máte legitimní přenosy, obsahující řetězec *1 = 1* , které chcete předat prostřednictvím brány WAF. Pokud se pokusíte žádost, WAF blokuje provoz, který obsahuje vaše *1 = 1* řetězce v parametru nebo pole. Toto je řetězec často přidružený k útoku prostřednictvím injektáže SQL. Můžete si projít protokoly a zobrazit časové razítko požadavku a pravidla, která zablokuje/odpovídá.

V následujícím příkladu vidíte, že čtyři pravidla se spouštějí během stejné požadavku (s použitím pole identifikátor TransactionId). První z nich uvádí, že ji namapovat, protože uživatel použil číselné nebo IP adresu URL pro požadavek, který zvýší o tři hodnocení anomálie, protože jde o upozornění. Další pravidlo, které neodpovídají je 942130, což je ta, kterou hledáte. Zobrazí se *1 = 1* v `details.data` pole. Dále zvyšuje úroveň hodnocení anomálie tři znovu, protože to je taky upozornění. Obecně platí, každé pravidlo, které má akce **shodné** zvýšení skóre anomálii a hodnocení anomálie v tomto okamžiku by šest. Další informace najdete v tématu [bodování režimu anomálií](waf-overview.md#anomaly-scoring-mode).

Poslední položky protokolu dvě zobrazují že žádost se zablokovala, protože byla dostatečně vysoké hodnocení anomálie. Tyto položky mají různé akce než další dvě. Zobrazují se ve skutečnosti *blokované* požadavku. Tato pravidla jsou povinné a nedá se zakázat. Se nesmí představit jako pravidla, ale více jako základní infrastruktury interních WAF.

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

## <a name="fixing-false-positives"></a>Oprava počet falešně pozitivních výsledků

Pomocí těchto informací a znalostí, které pravidlo 942130 je ten, který odpovídá *1 = 1* řetězec, vám pomůžou zabránit blokování provozu několik věcí:

- Pomocí seznamu vyloučení

   Zobrazit [konfiguraci WAF](application-gateway-waf-configuration.md#waf-exclusion-lists) uvádí další informace o vyloučení.
- Zakážete pravidlo.

### <a name="using-an-exclusion-list"></a>Pomocí seznamu vyloučení

Chcete-li provést informované rozhodnutí o manipulaci s falešnou kladné, je potřeba seznámit se s technologií, které vaše aplikace používá. Řekněme například, není ve vaší technologiích systému SQL server, a získáte počet falešně pozitivních výsledků související s těmito pravidly. Zakázání pravidla není oslabit nutně zabezpečení.

Jednou z výhod použití seznamu vyloučení je, že určitou část požadavku je zakázána. To ale znamená, že konkrétní vyloučení se vztahuje na veškerý provoz procházející svou bránu WAF, protože se jedná o globální nastavení. Například to může vést k problém pokud *1 = 1* žádost platná v textu pro určité aplikace, ale ne pro ostatní uživatele. Další výhodou je, že mají být vyloučeny, pokud je splněna určitá podmínka, na rozdíl od s výjimkou celý požadavek můžete mezi subjektem, hlaviček a soubory cookie.

V některých případech jsou případy, ve kterém se konkrétní parametry předat do WAF způsobem, který nemusí být intuitivní. Například je token, který bude předána při ověřování pomocí Azure Active Directory. Tento token *__RequestVerificationToken*, obvykle získat předaný jako soubor Cookie požadavku. Nicméně v některých případech, kdy jsou zakázané soubory cookie, tento token také předána jako atribut žádosti nebo "argument". Pokud k tomu dojde, budete muset zajistit, aby *__RequestVerificationToken* se přidá do seznamu vyloučení **žádost o název atributu** také.

![Vyloučení](media/waf-tshoot/exclusion-list.png)

V tomto příkladu, které chcete vyloučit **žádost o název atributu** , který se rovná *text1*. Důvodem je zřejmé, že se zobrazí název atributu v protokolech brány firewall: **dat: Odpovídající Data: 1 = 1, v rámci ARGS:text1 nebyl nalezen: 1=1**. Atribut je **text1**. Můžete také vyhledat tento název atributu několik dalších způsobů, jak, naleznete v tématu [hledání požadavek názvy atributů](#finding-request-attribute-names).

![Seznamy vyloučení WAF](media/waf-tshoot/waf-config.png)

### <a name="disabling-rules"></a>Zákaz pravidel

Dalším způsobem, jak získat kolem falešnou kladné je postup při zakázání pravidla, která odpovídá na tím vstup WAF se zlými úmysly. Protože jste analyzovat protokolů WAF a zúžené pravidlo dolů 942130, zakážete ho na webu Azure Portal. Zobrazit [přizpůsobení pravidel firewallu webových aplikací na webu Azure portal](application-gateway-customize-waf-rules-portal.md).

Jednou z výhod zakázání pravidla je, že pokud víte, že veškerý provoz, který obsahuje určitou podmínku, která se obvykle zablokuje je platný provoz, můžete zakázat toto pravidlo pro celý WAF. Ale pokud se jedná pouze platné provozu v případě konkrétní použití, můžete otevřít chybu zakázáním tohoto pravidla pro celý WAF, protože se jedná o globální nastavení.

Pokud chcete pomocí Azure Powershellu, přečtěte si téma [přizpůsobení pravidel firewallu webových aplikací pomocí prostředí PowerShell](application-gateway-customize-waf-rules-powershell.md). Pokud chcete použít rozhraní příkazového řádku Azure, přečtěte si téma [přizpůsobení pravidel firewallu webových aplikací pomocí Azure CLI](application-gateway-customize-waf-rules-cli.md).

![Pravidla firewallu webových aplikací](media/waf-tshoot/waf-rules.png)

## <a name="finding-request-attribute-names"></a>Názvy atributů žádosti o zjištění

Díky [Fiddler](https://www.telerik.com/fiddler), zkontrolujte jednotlivé požadavky a určit, jaké konkrétní pole webové stránky jsou volány. To může pomoct vyloučit určitá pole z kontroly pomocí obsahuje seznam vyloučení.

V tomto příkladu vidíte, že pole ve kterém *1 = 1* byl zadán řetězec se nazývá **text1**.

![Fiddler](media/waf-tshoot/fiddler-1.png)

Toto je pole, které se můžete vyloučit. Další informace o seznamy vyloučení, naleznete v tématu [webových aplikací brány firewall na žádost o omezení velikosti a seznamy vyloučení](application-gateway-waf-configuration.md#waf-exclusion-lists). V tomto případě můžete vyloučit hodnocení nakonfigurováním následující vyloučení:

![Vyloučení WAF](media/waf-tshoot/waf-exclusion-02.png)

Můžete také zkontrolovat protokoly brány firewall, chcete-li získat informace, které chcete zobrazit, co je potřeba přidat do seznamu vyloučení. Povolení protokolování, najdete v článku [stav Back endu, diagnostické protokoly a metriky pro službu Application Gateway](application-gateway-diagnostics.md).

Vyhledejte v protokolu brány firewall a zobrazení souboru PT1H.json hodina, po kterou došlo k požadavku, který chcete zkontrolovat.

V tomto příkladu vidíte, že máte čtyři pravidla s stejný identifikátor TransactionID a že všechny došlo k chybě ve stejnou dobu:

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

Se vaše znalosti o tom, sad pravidel CRS práce a pravidel CRS 3.0 pracující s anomálií hodnocení systému (naleznete v tématu [firewallu webových aplikací služby Azure Application Gateway](waf-overview.md)) víte, že dolní dvě pravidla se  **akce: Blokované** je vlastnost blokování na základě skóre celkový anomálií. Jsou pravidla a zaměřte se na horní dva.

První položka je protokolována, protože uživatel použili číselné IP adresu pro navigaci ke službě Application Gateway, která můžete v tomto případě ignorovat.

Druhá jeden (pravidlo 942130) je zajímavé. Můžete vidět v podrobnostech, odpovídá vzoru (1 = 1) a se pole jmenuje **text1**. Postupujte stejným způsobem předchozí vyloučit **žádost o název atributu** , který **rovná** **1 = 1**.

## <a name="finding-request-header-names"></a>Názvy záhlaví žádosti o zjištění

Fiddler je užitečný nástroj znovu k vyhledání názvů záhlaví požadavku. Na následujícím snímku obrazovky vidíte záhlaví pro tento požadavek GET, které zahrnují *Content-Type*, *User-Agent*, a tak dále.

![Fiddler](media/waf-tshoot/fiddler-2.png)

Dalším způsobem, jak zobrazit hlaviček žádostí a odpovědí se vás pod rouškou uvnitř nástroje pro vývojáře v chromu. Stisknutím klávesy F12 nebo klikněte pravým tlačítkem -> **zkontrolujte, jestli se** -> **vývojářské nástroje**a vyberte **sítě** kartu. Načtení webové stránky a klikněte na žádost, u které chcete zkontrolovat.

![Chrome F12](media/waf-overview/chrome-f12.png)

## <a name="finding-request-cookie-names"></a>Názvy souborů cookie požadavku hledání

Pokud požadavek obsahuje soubory cookie, **soubory cookie** kartě je možné vybrat a zobrazit je v aplikaci Fiddler.

## <a name="restrict-global-parameters-to-eliminate-false-positives"></a>Omezení globálních parametrů eliminovat falešně pozitivních výsledků

- Zakázat kontrolu tělo požadavku

   Nastavením **zkontrolujte, jestli se text požadavku** vypnutý, nebude se vyhodnocovat žádosti subjektů veškerý provoz pomocí brány WAF. To může být užitečné, pokud víte, že nejsou těla požadavku škodlivý do vaší aplikace.

   Tím, že zakážete tuto možnost, není zkontroloval pouze text požadavku. Hlavičkové soubory a soubory cookie zůstanou zkontrolovanou, pokud jednotlivé značky jsou vyloučeny pomocí funkce seznam vyloučení.

- Omezení velikosti souborů

   Tím, že omezíte velikost souboru pro svou bránu WAF, jste omezení možností útoku děje na webové servery. Tím, že velké soubory k odeslání, zvyšuje riziko zahlcením back-endu. Omezení velikosti souboru normální použití případu pro vaše aplikace je jenom další způsob, jak zabránit útokům.

   > [!NOTE]
   > Pokud víte, že vaše aplikace bude potřebovat nikdy nahrávání všech souborů výše danou velikost můžete omezit, která nastavením omezení.

## <a name="next-steps"></a>Další postup

Zobrazit [postup konfigurace firewallu webových aplikací ve službě Application Gateway](tutorial-restrict-web-traffic-powershell.md).
