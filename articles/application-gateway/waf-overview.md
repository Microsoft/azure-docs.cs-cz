---
title: Úvod do firewallu webových aplikací (WAF) služby Azure Application Gateway
description: Tento článek obsahuje přehled firewallu webových aplikací (WAF) služby Application Gateway.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 11/16/2018
ms.author: amsriva
ms.openlocfilehash: 71990244db859a61885968a502d9849a36d81dce
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/27/2018
ms.locfileid: "52425157"
---
# <a name="web-application-firewall-waf"></a>Firewall webových aplikací (WAF)

Firewall webových aplikací (WAF) je funkce služby Application Gateway poskytující centralizovanou ochranu webových aplikací před běžným zneužitím a ohrožením zabezpečení.

Webové aplikace se čím dál častěji stávají cílem škodlivých útoků, které zneužívají běžně známé chyby zabezpečení. Společné tyto zneužití jsou útoky prostřednictvím injektáže SQL a skriptování mezi weby útoky pár. 

Předcházet takovým útokům v kódu aplikace může být náročné a může vyžadovat pečlivou údržbu, opravy a monitorování několika vrstev topologie aplikace. Centralizovaný firewall webových aplikací značně zjednodušuje správu zabezpečení a nabízí správcům lepší ochranu aplikací před hrozbami neoprávněného vniknutí. Řešení WAF může také reagovat na ohrožení zabezpečení, který je rychlejší, protože opravuje známé chyby zabezpečení v centrálním umístění, namísto zabezpečování jednotlivých webových aplikací. Stávající aplikační brány je možné jednoduše převést na aplikační brány doplněné webovým aplikačním firewallem.

WAF je na základě pravidel ze [základní sady pravidel OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0 nebo 2.2.9. Automaticky aktualizuje zahrnout ochranu proti nová ohrožení zabezpečení, nepotřebují žádnou další konfiguraci.

![imageURLroute](./media/waf-overview/WAF1.png)

Application Gateway funguje jako kontroler doručování aplikací (ADC) a nabízí ukončení protokolu SSL, spřažení relace na základě souborů cookie, distribuci zatížení pomocí kruhového dotazování, směrování na základě obsahu, schopnost hostovat více webů a vylepšení zabezpečení.

Vylepšení zabezpečení nabízená službou Application Gateway zahrnují správu zásad protokolu SSL a podporu kompletního protokolu SSL. Zabezpečení aplikací je nyní posíleno integrací firewallu webových aplikací (WAF) přímo do nabídky ADC. Získáte tak snadno konfigurovatelné centrální umístění pro správu a ochranu svých webových aplikací před běžnými webovými chybami zabezpečení.

## <a name="benefits"></a>Výhody

Tady jsou základní výhody, které poskytují Application Gateway s firewallem webových aplikací:

### <a name="protection"></a>Ochrana

* Ochráníte své webové aplikace před webovými chybami zabezpečení a útoky bez nutnosti upravovat back-endový kód.

* Ochráníte více webových aplikací současně za službou Application Gateway. Služba Application Gateway podporuje za jednou bránou hostování až 20 webů, které můžete ochránit před webovými útoky pomocí WAF.

### <a name="monitoring"></a>Monitorování

* Můžete monitorovat útoky na své webové aplikace pomocí protokolu WAF generovaného v reálném čase. Tento protokol je integrovaný s [Azure Monitorem](../monitoring-and-diagnostics/monitoring-overview.md) a umožňuje sledování výstrah a protokolů WAF a snadnou identifikaci trendů.

* WAF je integrovaná s Azure Security Center. Azure Security Center vám poskytne centrální přehled o stavu zabezpečení všech vašich prostředků Azure.

### <a name="customization"></a>Přizpůsobení

* Díky možnosti přizpůsobit pravidla firewallu webových aplikací a pravidla skupin podle potřeb vaší aplikace můžete eliminovat falešně pozitivní nálezy.

## <a name="features"></a>Funkce

- Ochrana prostřednictvím injektáže SQL
- Mezi ochranou skriptování
- Běžné webové útoků ochranu, jako je injektáž příkazů, pronášení, rozdělování odpovědí protokolu HTTP požadavku HTTP a útok vzdáleného souboru
- Ochrana před narušením protokolu HTTP
- Ochrana proti anomálie protokolu HTTP například chybějící user-agent hostitele nebo hlavičky accept.
- Ochrana před roboty, prohledávacími moduly a skenery.
- Detekce běžných chyb v konfiguraci aplikací (například Apache, IIS atd.)

### <a name="public-preview-features"></a>Funkce ve verzi Public preview

Aktuální public preview WAF SKU zahrnuje následující funkce:

- **Požádat o omezení velikosti** – Firewall webových aplikací umožňuje uživatelům konfigurovat omezení velikosti požadavku v rámci dolní a horní hranice.
- **Seznamy vyloučení** – seznamy vyloučení WAF povolit uživatelům vynechání určité atributy žádosti ze zkušební verze WAF. Běžným příkladem je že vložen tokeny, které se používají pro ověřování nebo pole s heslem služby Active Directory.

Další informace o verzi public preview WAF najdete v tématu [webové omezení velikosti brány firewall na žádost o aplikaci a seznamy vyloučení (Public Preview)](application-gateway-waf-configuration.md).





### <a name="core-rule-sets"></a>Základní sady pravidel

Application Gateway podporuje dvě sady pravidel, CRS 3.0 a CRS 2.2.9. Tyto základní sady pravidel jsou kolekce pravidel, které chrání vaše webové aplikace před škodlivými aktivitami.

Firewall webových aplikací se poskytuje s předem nakonfigurovanou sadou pravidel CRS 3.0, nebo můžete použít verzi 2.2.9. CRS 3.0 dosahuje menšího počtu falešně pozitivních nálezů oproti verzi 2.2.9. Je zajištěna možnost [přizpůsobit si pravidla, podle vašich potřeb](application-gateway-customize-waf-rules-portal.md). Mezi běžné webové zranitelnosti, proti kterým firewall webových aplikací chrání, patří tyto:

- Ochrana prostřednictvím injektáže SQL
- Mezi ochranou skriptování
- Běžné webové útoků ochranu, jako je injektáž příkazů, pronášení, rozdělování odpovědí protokolu HTTP požadavku HTTP a útok vzdáleného souboru
- Ochrana před narušením protokolu HTTP
- Ochrana proti anomálie protokolu HTTP například chybějící user-agent hostitele nebo hlavičky accept.
- Ochrana před roboty, prohledávacími moduly a skenery.
- Detekce běžných chyb v konfiguraci aplikací (např. Apache, IIS atd.)

Podrobnější seznam pravidel a jejich ochrany najdete v tématu [základních sad pravidel](#core-rule-sets).


#### <a name="owasp30"></a>OWASP_3.0

Základní sada pravidel 3.0 má 13 skupin pravidel, shrnutých v následující tabulce. Každá z těchto skupin obsahuje několik pravidel, která mohou být zakázána.

|RuleGroup|Popis|
|---|---|
|**[REQUEST-911-METHOD-ENFORCEMENT](application-gateway-crs-rulegroups-rules.md#crs911)**|Obsahuje pravidla pro uzamčení metod (PUT, PATCH)|
|**[REQUEST-913-SCANNER-DETECTION](application-gateway-crs-rulegroups-rules.md#crs913)**| Obsahuje pravidla pro ochranu před skenery portů a prostředí.|
|**[REQUEST-920-PROTOCOL-ENFORCEMENT](application-gateway-crs-rulegroups-rules.md#crs920)**|Obsahuje pravidla pro ochranu před problémy s protokoly a kódováním.|
|**[REQUEST-921-PROTOCOL-ATTACK](application-gateway-crs-rulegroups-rules.md#crs921)**|Obsahuje pravidla s cílem zajistit ochranu proti vkládání záhlaví, podvržení požadavku a rozdělení odpovědi.|
|**[REQUEST-930-APPLICATION-ATTACK-LFI](application-gateway-crs-rulegroups-rules.md#crs930)**|Obsahuje pravidla pro ochranu před útoky na soubory a cesty.|
|**[REQUEST-931-APPLICATION-ATTACK-RFI](application-gateway-crs-rulegroups-rules.md#crs931)**|Obsahuje pravidla pro ochranu před útoky RFI (Remote File Inclusion).|
|**[REQUEST-932-APPLICATION-ATTACK-RCE](application-gateway-crs-rulegroups-rules.md#crs932)**|Obsahuje pravidla pro ochranu před vzdáleným spuštěním kódu.|
|**[REQUEST-933-APPLICATION-ATTACK-PHP](application-gateway-crs-rulegroups-rules.md#crs933)**|Obsahuje pravidla pro ochranu před útoky vkládáním kódu PHP.|
|**[REQUEST-941-APPLICATION-ATTACK-XSS](application-gateway-crs-rulegroups-rules.md#crs941)**|Obsahuje pravidla pro ochranu před křížovým skriptováním.|
|**[REQUEST-942-APPLICATION-ATTACK-SQLI](application-gateway-crs-rulegroups-rules.md#crs942)**|Obsahuje pravidla pro ochranu před injektáží SQL.|
|**[REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION](application-gateway-crs-rulegroups-rules.md#crs943)**|Obsahuje pravidla pro ochranu před útokem fixací na relaci.|

#### <a name="owasp229"></a>OWASP_2.2.9

Základní sada pravidel 2.2.9 má 10 skupin pravidel, shrnutých v následující tabulce. Každá z těchto skupin obsahuje několik pravidel, která mohou být zakázána.

|RuleGroup|Popis|
|---|---|
|**[crs_20_protocol_violations](application-gateway-crs-rulegroups-rules.md#crs20)**|Obsahuje pravidla pro ochranu před porušením protokolu (neplatné znaky, GET s tělem žádosti atd.)|
|**[crs_21_protocol_anomalies](application-gateway-crs-rulegroups-rules.md#crs21)**|Obsahuje pravidla pro ochranu před nesprávným obsahem záhlaví.|
|**[crs_23_request_limits](application-gateway-crs-rulegroups-rules.md#crs23)**|Obsahuje pravidla pro ochranu před argumenty nebo soubory, které překračují omezení.|
|**[crs_30_http_policy](application-gateway-crs-rulegroups-rules.md#crs30)**|Obsahuje pravidla pro ochranu před omezenými metodami, záhlavími a typy souborů. |
|**[crs_35_bad_robots](application-gateway-crs-rulegroups-rules.md#crs35)**|Obsahuje pravidla pro ochranu před roboty procházejícími web a před skenery.|
|**[crs_40_generic_attacks](application-gateway-crs-rulegroups-rules.md#crs40)**|Obsahuje pravidla pro ochranu před obecnými útoky (fixace na relaci, zahrnutí vzdáleného souboru, vkládání kódu PHP atd.)|
|**[crs_41_sql_injection_attacks](application-gateway-crs-rulegroups-rules.md#crs41sql)**|Obsahuje pravidla pro ochranu před injektáží SQL.|
|**[crs_41_xss_attacks](application-gateway-crs-rulegroups-rules.md#crs41xss)**|Obsahuje pravidla pro ochranu před křížovým skriptováním.|
|**[crs_42_tight_security](application-gateway-crs-rulegroups-rules.md#crs42)**|Obsahuje pravidla pro ochranu před útoky typu path traversal.|
|**[crs_45_trojans](application-gateway-crs-rulegroups-rules.md#crs45)**|Obsahuje pravidla pro ochranu před trojskými koni.|

### <a name="waf-modes"></a>Režimy WAF

Firewall webových aplikací (WAF) služby Application Gateway lze nakonfigurovat ke spuštění v těchto dvou režimech:

* **Režim detekce** – když je nakonfigurován ke spuštění v režimu detekce, waf služby Application Gateway monitoruje a protokoluje všechny výstrahy na hrozby do souboru protokolu. Diagnostika protokolování pro službu Application Gateway by měla být v části **Diagnostika** zapnutá. Bude také třeba zajistit, aby byl vybrán a zapnut protokol WAF. když je firewall webových aplikací spuštěný v režimu detekce, neblokuje příchozí požadavky.
* **Režim prevence** – Když je nakonfigurován ke spuštění v režimu prevence, služba Application Gateway aktivně blokuje vniknutí a útoky detekované pomocí svých pravidel. Útočník obdrží výjimku 403 – Neoprávněný přístup a připojení se ukončí. Režim prevence takové útoky nadále protokoluje do protokolů WAF.

### <a name="application-gateway-waf-reports"></a>Monitorování WAF

Monitorování stavu službu Application Gateway je velmi důležité. Monitorování stavu vašeho firewallu webových aplikací a aplikací, které chrání, je zajišťována protokolováním a integrací se službami Azure Monitor, Azure Security Center a Log Analytics.

![Diagnostika](./media/waf-overview/diagnostics.png)

#### <a name="azure-monitor"></a>Azure Monitor

Každý protokol aplikační brány je integrovaný do služby [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md).  To umožňuje sledovat diagnostické informace, včetně výstrah a protokolů WAF.  Tato možnost je poskytována v rámci prostředku Application Gateway na portálu na kartě **Diagnostika** nebo přímo prostřednictvím služby Azure Monitor. Další informace o povolení diagnostických protokolů pro službu application gateway najdete v tématu [diagnostice služby Application Gateway](application-gateway-diagnostics.md)

#### <a name="azure-security-center"></a>Azure Security Center

[Azure Security Center](../security-center/security-center-intro.md) pomáhá předcházet hrozbám, rozpoznávat je a reagovat na ně a nabízí lepší přehled o zabezpečení prostředků Azure a kontrolu nad nimi. Služba Application Gateway je nyní [integrována do služby Azure Security Center](application-gateway-integration-security-center.md). Azure Security Center prohledává vaše prostředí a rozpoznává nechráněné webové aplikace. Nyní může doporučovat firewall webových aplikací (WAF) služby Application Gateway k ochraně těchto citlivých prostředků. Firewall webových aplikací (WAF) služby Application Gateway můžete vytvořit přímo ve službě Azure Security Center.  Tyto instance firewallu webových aplikací (WAF) jsou integrovány se službou Azure Security Center a budou posílat upozornění a informace o stavu zpět do služby Azure Security Center za účelem vytváření sestav.

![Obrázek 1](./media/waf-overview/figure1.png)

#### <a name="logging"></a>Protokolování

Firewall webových aplikací (WAF) služby Application Gateway poskytuje podrobné generování sestav o každém útoky, který detekuje. Protokolování je integrované v protokolech diagnostiky Azure a výstrahy se zaznamenávají ve formátu JSON. Tyto protokoly je možné integrovat se službou [Log Analytics](../azure-monitor/insights/azure-networking-analytics.md).

![imageURLroute](./media/waf-overview/waf2.png)

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

Firewall webových aplikací je k dispozici jako nová položka WAF SKU. Tato položka SKU je k dispozici pouze v modelu zřizování Azure Resource Manager, nikoli v rámci klasického modelu nasazení. Kromě toho WAF SKU dostupný jen pro aplikace střední a velké velikosti instancí brány. Všechna omezení pro aplikační brány platí také pro WAF SKU.

Ceny jsou založeny na hodinové sazbě za instanci brány a na poplatcích za zpracování dat. Hodinová sazba za bránu se pro položku WAF SKU liší od sazby pro standardní položky SKU. Sazby jsou uvedeny v článku [Podrobnosti o cenách Application Gateway](https://azure.microsoft.com/pricing/details/application-gateway/). Poplatky za zpracování dat zůstávají stejné. Neexistuje žádné za pravidla nebo pravidla skupiny poplatky. Může chránit několik webových aplikací za stejnou bránou firewall webových aplikací a se vám neúčtují poplatky za podporu více aplikací.

## <a name="next-steps"></a>Další postup

Po získání informací o WAF, naleznete v tématu [postup konfigurace firewallu webových aplikací ve službě Application Gateway](tutorial-restrict-web-traffic-powershell.md).

