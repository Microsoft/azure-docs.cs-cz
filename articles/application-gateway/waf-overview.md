---
title: Úvod do firewallu webových aplikací služby Azure Application Gateway
description: Tento článek obsahuje přehled firewallu webových aplikací (WAF) služby Application Gateway.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 2/22/2019
ms.author: amsriva
ms.topic: conceptual
ms.openlocfilehash: 830513a03bd65ca14cb0938ae599a676f1bb3bca
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2019
ms.locfileid: "58518180"
---
# <a name="web-application-firewall-for-azure-application-gateway"></a>Firewall webových aplikací služby Azure Application Gateway

Azure Application Gateway nabízí firewallu webových aplikací (WAF), poskytující centralizovanou ochranu webových aplikací z běžných zneužitím a ohrožením zabezpečení. Webové aplikace stále častěji cílí útoky se zlými úmysly, které zneužívají běžně známé chyby zabezpečení. Mezi nejběžnější útoky způsoby útoku prostřednictvím injektáže SQL a skriptování napříč weby.

Předcházet takovým útokům v kódu aplikace není lehké. To může vyžadovat pečlivou údržbu, opravy a monitorování několika vrstev topologie aplikace. Centralizovaný firewall webových aplikací pomáhá bezpečnostní správu mnohem jednodušší. Brány WAF také nabízí správci aplikace lepší ochrany před hrozbami a vniknutí.

Řešení WAF může reagovat na ohrožení zabezpečení rychlejší centrálně opravuje známé chyby zabezpečení, místo zabezpečení každé jednotlivé webové aplikace. Stávající aplikační brány můžete snadno převést na fire aplikace s povolenými wall brány.

Waf služby Application Gateway je založen na [základní pravidlo nastavte Rezervačním](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0 nebo 2.2.9 z Open Web Application zabezpečení projektu (OWASP). WAF se automaticky aktualizuje obsahující ochranu proti nová ohrožení zabezpečení, nepotřebují žádnou další konfiguraci.

![Diagram Application Gateway WAF](./media/waf-overview/WAF1.png)

Služba Application Gateway funguje jako kontroler doručování aplikací (ADC). Nabízí ukončení vrstvy SSL (Secure Sockets), spřažení relace na základě souborů cookie, distribuci zatížení pomocí kruhového dotazování, směrování na základě obsahu, schopnost hostovat více webů a vylepšení zabezpečení.

Vylepšení zabezpečení Application Gateway zahrnují správu zásad protokolu SSL a podporu-kompletního protokolu SSL. Zabezpečení aplikací je posíleno integrací integrace waf služby Application Gateway. Kombinace chrání vaše webové aplikace proti známých chyb zabezpečení. A poskytuje snadno nakonfigurovat centrální umístění služby pro správu.

## <a name="benefits"></a>Výhody

Tato část popisuje základní výhody, které poskytují Application Gateway a jejich brány WAF.

### <a name="protection"></a>Ochrana

* Chrání vaše webové aplikace před webovými chybami zabezpečení a útoky bez nutnosti upravovat back endový kód.

* Ochráníte více webových aplikací ve stejnou dobu. Instance služby Application Gateway může být hostitelem až 20 webů, které jsou chráněné bránou firewall webových aplikací.

### <a name="monitoring"></a>Monitorování

* Monitorovat útoky na vaše webové aplikace pomocí v reálném čase protokol WAF. Je integrován do protokolu [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md) pro sledování výstrah WAF a snadnou identifikaci trendů.

* Waf služby Application Gateway je integrovaná s Azure Security Center. Security Center poskytuje přehled o stavu zabezpečení všech vašich prostředků Azure.

### <a name="customization"></a>Přizpůsobení

* Můžete přizpůsobit pravidla firewallu webových aplikací a pravidla skupin podle potřeb vaší aplikace a eliminovat falešně pozitivních výsledků.

## <a name="features"></a>Funkce

- Ochrana útok prostřednictvím injektáže SQL.
- Ochrana skriptování napříč weby.
- Ochrana před další běžné webovými útoky, jako je například injektáž příkazů, pronášení, rozdělování odpovědí protokolu HTTP požadavku HTTP a zahrnutí vzdáleného souboru.
- Ochrana před narušením protokolu HTTP.
- Ochrana proti anomálie protokolu HTTP, jako je například chybějící user-agent hostitele nebo hlavičky accept.
- Ochrana před roboty, prohledávacími moduly a skenery.
- Detekce běžných chyb v konfiguraci aplikací (například Apache a IIS).
- Omezení velikosti konfigurovatelné požadavek s dolní a horní mez.
- Seznamy vyloučení umožňují vynechat určité atributy žádosti ze zkušební verze WAF. Běžným příkladem jsou služby Active Directory vložené tokeny, které se používají pro ověření nebo pole s heslem.

### <a name="core-rule-sets"></a>Základní sady pravidel

Služba Application Gateway podporuje dvě sady pravidel, CRS 3.0 a CRS 2.2.9. Tato pravidla chrání vaše webové aplikace ze škodlivých aktivit.

Waf služby Application Gateway obsahuje předkonfigurovanou CRS 3.0 ve výchozím nastavení. Ale můžete místo toho použít CRS 2.2.9. CRS 3.0 nabídky snížit počet falešně pozitivních výsledků ve srovnání s CRS 2.2.9. Můžete také [přizpůsobit pravidla tak, aby odpovídala vašim potřebám](application-gateway-customize-waf-rules-portal.md).

WAF chrání proti následujícím webovým ohrožením zabezpečení:

- Útoky prostřednictvím injektáže SQL
- Útoky skriptování napříč weby
- Další běžné útoky, jako je například injektáž příkazů, HTTP požadavku pašování, rozdělování odpovědí protokolu HTTP a zahrnutí vzdáleného souboru
- Narušením protokolu HTTP.
- Anomálie protokolu HTTP, jako je například chybějící user-agent hostitele nebo hlavičky accept.
- Roboty, prohledávacími moduly a skenery.
- Časté nesprávné konfigurace aplikace (například Apache a IIS)

#### <a name="owasp-crs-30"></a>OWASP CRS 3.0

CRS 3.0 obsahuje 13 skupin pravidel, shrnutých v následující tabulce. Každá skupina obsahuje více pravidel, která mohou být zakázána.

|Pravidlo skupiny|Popis|
|---|---|
|**[REQUEST-911-METHOD-ENFORCEMENT](application-gateway-crs-rulegroups-rules.md#crs911)**|Uzamčení metod (PUT, PATCH)|
|**[REQUEST-913-SCANNER-DETECTION](application-gateway-crs-rulegroups-rules.md#crs913)**|Ochranu před skenery portů a prostředí|
|**[REQUEST-920-PROTOCOL-ENFORCEMENT](application-gateway-crs-rulegroups-rules.md#crs920)**|Ochrana proti protokoly a kódováním problémy|
|**[REQUEST-921-PROTOCOL-ATTACK](application-gateway-crs-rulegroups-rules.md#crs921)**|Ochranu proti vkládání záhlaví, podvržení požadavku a rozdělení odpovědi.|
|**[REQUEST-930-APPLICATION-ATTACK-LFI](application-gateway-crs-rulegroups-rules.md#crs930)**|Ochrana před útoky na soubory a cesty|
|**[REQUEST-931-APPLICATION-ATTACK-RFI](application-gateway-crs-rulegroups-rules.md#crs931)**|Ochrana před útoky na vzdálený soubor zahrnutí (RFI)|
|**[REQUEST-932-APPLICATION-ATTACK-RCE](application-gateway-crs-rulegroups-rules.md#crs932)**|Ochranu před útoky provádění vzdáleného kódu|
|**[REQUEST-933-APPLICATION-ATTACK-PHP](application-gateway-crs-rulegroups-rules.md#crs933)**|Ochrana před útoky vkládání kódu PHP|
|**[REQUEST-941-APPLICATION-ATTACK-XSS](application-gateway-crs-rulegroups-rules.md#crs941)**|Ochranu před útoky skriptování napříč weby|
|**[REQUEST-942-APPLICATION-ATTACK-SQLI](application-gateway-crs-rulegroups-rules.md#crs942)**|Ochrana před útoky prostřednictvím injektáže SQL|
|**[REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION](application-gateway-crs-rulegroups-rules.md#crs943)**|Ochrana před útoky na záznam relace|

#### <a name="owasp-crs-229"></a>OWASP CRS 2.2.9

CRS 2.2.9 zahrnuje 10 skupin pravidel, shrnutých v následující tabulce. Každá skupina obsahuje více pravidel, která mohou být zakázána.

|Pravidlo skupiny|Popis|
|---|---|
|**[crs_20_protocol_violations](application-gateway-crs-rulegroups-rules.md#crs20)**|Ochranu před porušením protokolu (jako jsou neplatné znaky nebo GET s tělem žádosti)|
|**[crs_21_protocol_anomalies](application-gateway-crs-rulegroups-rules.md#crs21)**|Ochranu před nesprávným obsahem záhlaví|
|**[crs_23_request_limits](application-gateway-crs-rulegroups-rules.md#crs23)**|Ochranu před argumenty nebo soubory, které překračují omezení|
|**[crs_30_http_policy](application-gateway-crs-rulegroups-rules.md#crs30)**|Ochranu před omezenými metodami, záhlaví a typy souborů|
|**[crs_35_bad_robots](application-gateway-crs-rulegroups-rules.md#crs35)**|Ochranu před roboty procházejícími web a skenery.|
|**[crs_40_generic_attacks](application-gateway-crs-rulegroups-rules.md#crs40)**|Ochranu před obecnými útoky (například fixace na relaci, zahrnutí vzdáleného souboru a vkládání kódu PHP)|
|**[crs_41_sql_injection_attacks](application-gateway-crs-rulegroups-rules.md#crs41sql)**|Ochrana před útoky prostřednictvím injektáže SQL|
|**[crs_41_xss_attacks](application-gateway-crs-rulegroups-rules.md#crs41xss)**|Ochranu před útoky skriptování napříč weby|
|**[crs_42_tight_security](application-gateway-crs-rulegroups-rules.md#crs42)**|Ochrana před útoky na cestu procházení|
|**[crs_45_trojans](application-gateway-crs-rulegroups-rules.md#crs45)**|Ochranu před trojskými koni|

### <a name="waf-modes"></a>Režimy WAF

Waf služby Application Gateway může být nakonfigurován pro spouštění v těchto dvou režimech:

* **Režim detekce**: Monitoruje a protokoluje všechna upozornění na hrozby. Zapnutí protokolování diagnostiky pro službu Application Gateway v **diagnostiky** oddílu. Musí také zajistíte, že nebude vybrána a zapnutý protokol WAF. Firewall webových aplikací při provozu v režimu detekce nebrání v příchozí požadavky.
* **Režim ochrany před únikem informací**: Blokuje vniknutí a útoky, které pravidla zjišťování. Útočník obdrží výjimku "403 – neoprávněný přístup" a připojení se ukončí. Režim ochrany před únikem informací zaznamenává do protokolů WAF takové útoky.

### <a name="anomaly-scoring-mode"></a>Režim bodování anomálií
 
OWASP má dva režimy pro rozhodování o tom, jestli se mají blokovat provoz: Tradiční režim a režim vyhodnocování anomálií.

V tradičním režimu se považuje za provozu, který odpovídá jakékoli pravidlo nezávisle na jiných pravidla shody. Tento režim je snadno pochopitelný. Ale chybí informace o tom, kolik pravidla odpovídají konkrétní žádost je omezení. Ano byla zavedena anomálií vyhodnocování režimu. Je výchozí nastavení pro OWASP 3. *x*.

V režimu vyhodnocení anomálií provoz, který odpovídá jakékoli pravidlo není blokované okamžitě brána firewall je v režimu ochrany před únikem informací. Pravidla mají určité závažnosti: *Kritické*, *chyba*, *upozornění*, nebo *Všimněte si, že*. Které ovlivňuje závažnost číselnou hodnotu pro žádosti, která se nazývá hodnocení anomálie. Například jeden *upozornění* pravidla shody přispívá 3 na skóre. Jeden *kritický* pravidla shody přispívá 5.

Je prahová hodnota 5 anomálií skóre blokuje komunikaci. Tak jediné *kritický* pravidlo je dostatečná pro waf služby Application Gateway blokovat žádost, i v režimu ochrany před únikem informací. Krom jedné *upozornění* pravidla shody jenom zvýší anomálií 3 skóre, která není k dispozici dostatek samostatně k blokování provozu.

> [!NOTE]
> Zpráva, která je zaznamenána při pravidlo WAF odpovídá provoz obsahuje hodnotu akce "Uzavřeno". Ale provoz je ve skutečnosti jenom blokovaných pro hodnocení anomálie 5 nebo vyšší.  

### <a name="waf-monitoring"></a>Monitorování WAF

Monitorování stavu službu Application Gateway je velmi důležité. Monitorování stavu brány WAF a aplikace, které chrání podporuje integraci s Azure Security Center, Azure Monitor a protokoly Azure monitoru.

![Diagram diagnostiky waf služby Application Gateway](./media/waf-overview/diagnostics.png)

#### <a name="azure-monitor"></a>Azure Monitor

Protokolů Application Gateway je integrovaná s [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md). To umožňuje sledovat diagnostické informace, včetně výstrah a protokolů WAF. Tato funkce je k dispozici na **diagnostiky** kartu v prostředku Application Gateway na portálu nebo přímo prostřednictvím služby Azure Monitor. Další informace o povolení protokolů najdete v tématu [diagnostice služby Application Gateway](application-gateway-diagnostics.md).

#### <a name="azure-security-center"></a>Azure Security Center

[Security Center](../security-center/security-center-intro.md) pomáhá zabránit, detekci a reakce na hrozby. Poskytuje zvýšené viditelnosti a kontroly nad zabezpečením vašich prostředků Azure. Služba Application Gateway je [integrované se službou Security Center](application-gateway-integration-security-center.md). Security Center prohledává vaše prostředí a rozpoznává nechráněné webové aplikace. Můžete ho doporučujeme waf služby Application Gateway k ochraně těchto citlivých prostředků. Vytvořit bránu firewall přímo ze služby Security Center. Tyto instance WAF jsou integrované pomocí služby Security Center. Odesílají oznámení a informací o stavu do služby Security Center pro vytváření sestav.

![Okno Security Center – přehled](./media/waf-overview/figure1.png)

#### <a name="logging"></a>Protokolování

Waf služby Application Gateway poskytuje podrobné generování sestav o každém útoky, který detekuje. Protokolování je integrované v protokolech diagnostiky Azure. Výstrahy se zaznamenávají ve formátu .json. Tyto protokoly je možné integrovat se službou [protokoly Azure monitoru](../azure-monitor/insights/azure-networking-analytics.md).

![Application Gateway diagnostické protokoly systému windows](./media/waf-overview/waf2.png)

```json
{
  "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupId}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{appGatewayName}",
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

## <a name="application-gateway-waf-sku-pricing"></a>Ceny SKU WAF služby Application Gateway

Waf služby Application Gateway je k dispozici jako nová skladová položka. Tato skladová položka je k dispozici pouze v modelu zřizování Azure Resource Manageru, ne v modelu nasazení classic. WAF SKU je navíc k dispozici pouze ve střední a velké velikosti instancí Application Gateway. Všechna omezení pro službu Application Gateway platí také pro WAF SKU.

Cena je založená na hodinové sazbě za instanci brány a poplatek za zpracování dat. [Ceny Application Gateway](https://azure.microsoft.com/pricing/details/application-gateway/) pro skladovou Položku WAF se liší od standardní poplatky za SKU. Poplatky za zpracování dat jsou stejné. Neúčtují žádné poplatky za pravidla nebo skupiny pravidel. Může chránit několik webových aplikací za stejnou bránou firewall webových aplikací. Se vám neúčtují poplatky za podporu více aplikací.

## <a name="next-steps"></a>Další postup

Zobrazit [postup konfigurace firewallu webových aplikací ve službě Application Gateway](tutorial-restrict-web-traffic-powershell.md).
