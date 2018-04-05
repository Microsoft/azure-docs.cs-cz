---
title: Úvod do Firewallu webových aplikací (WAF) služby Azure Application Gateway | Microsoft Docs
description: Na této stránce najdete přehled firewallu webových aplikací (WAF) služby Application Gateway.
documentationcenter: na
services: application-gateway
author: amsriva
manager: rossort
editor: amsriva
ms.assetid: 04b362bc-6653-4765-86f6-55ee8ec2a0ff
ms.service: application-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2017
ms.author: amsriva
ms.openlocfilehash: d3945436a369f624ccb1d862b9623b8cfb1ed376
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="web-application-firewall-waf"></a>Firewall webových aplikací (WAF)

Firewall webových aplikací (WAF) je funkce služby Application Gateway poskytující centralizovanou ochranu webových aplikací před běžným zneužitím a ohrožením zabezpečení. 

Firewall webových aplikací je založený na základě [základní sady pravidel OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0 nebo 2.2.9. Webové aplikace se čím dál častěji stávají cílem škodlivých útoků, které zneužívají běžně známé chyby zabezpečení. Mezi tyto běžné typy zneužití patří mimo jiné například útoky prostřednictvím injektáže SQL nebo skriptování mezi weby. Předcházet takovým útokům v kódu aplikace může být náročné a může vyžadovat pečlivou údržbu, opravy a monitorování několika vrstev topologie aplikace. Centralizovaný firewall webových aplikací značně zjednodušuje správu zabezpečení a nabízí správcům lepší ochranu aplikací před hrozbami neoprávněného vniknutí. Řešení Firewall webových aplikací (WAF) může také rychleji reagovat na ohrožení zabezpečení, protože opravuje známé chyby zabezpečení v centrálním umístění, namísto zabezpečování jednotlivých webových aplikací. Stávající aplikační brány je možné jednoduše převést na aplikační brány doplněné webovým aplikačním firewallem.

![imageURLroute](./media/application-gateway-web-application-firewall-overview/WAF1.png)

Application Gateway funguje jako řadič doručování aplikací a zajišťuje zakončení protokolu SSL, spřažení relace na základě souborů cookie, distribuci zatížení pomocí kruhového dotazování, směrování na základě obsahu, schopnost hostovat více webů a vylepšení zabezpečení. Vylepšení zabezpečení nabízená službou Application Gateway zahrnují správu zásad protokolu SSL a podporu koncového šifrování protokolu SSL. Zabezpečení aplikací je nyní posíleno integrací firewallu webových aplikací (WAF) přímo do nabídky ADC. Získáte tak snadno konfigurovatelné centrální umístění pro správu a ochranu svých webových aplikací před běžnými webovými chybami zabezpečení.

## <a name="benefits"></a>Výhody

Tady jsou základní výhody, které poskytují Application Gateway s firewallem webových aplikací:

### <a name="protection"></a>Ochrana

* Ochráníte své webové aplikace před webovými chybami zabezpečení a útoky bez nutnosti upravovat back-endový kód.

* Ochráníte více webových aplikací současně za službou Application Gateway. Služba Application Gateway podporuje za jednou bránou hostování až 20 webů, které můžete ochránit před webovými útoky pomocí WAF.

### <a name="monitoring"></a>Monitorování

* Můžete monitorovat útoky na své webové aplikace pomocí protokolu WAF generovaného v reálném čase. Tento protokol je integrovaný s [Azure Monitorem](../monitoring-and-diagnostics/monitoring-overview.md) a umožňuje sledování výstrah a protokolů WAF a snadnou identifikaci trendů.

* WAF se integruje se službou Azure Security Center. Azure Security Center vám poskytne centrální přehled o stavu zabezpečení všech vašich prostředků Azure.

### <a name="customization"></a>Přizpůsobení

* Díky možnosti přizpůsobit pravidla firewallu webových aplikací a pravidla skupin podle potřeb vaší aplikace můžete eliminovat falešně pozitivní nálezy.

## <a name="features"></a>Funkce

Firewall webových aplikací se poskytuje s předem nakonfigurovanou sadou pravidel CRS 3.0, nebo můžete použít verzi 2.2.9. CRS 3.0 dosahuje menšího počtu falešně pozitivních nálezů oproti verzi 2.2.9. Je zajištěna možnost [přizpůsobit si pravidla, podle vašich potřeb](application-gateway-customize-waf-rules-portal.md). Mezi běžné webové zranitelnosti, proti kterým firewall webových aplikací chrání, patří tyto:

* Ochrana před útoky prostřednictvím injektáže SQL.
* Ochrana před skriptováním mezi weby.
* Ochrana před běžnými webovými útoky, jako je například injektáž příkazů, pronášení požadavků HTTP, rozdělování odpovědí protokolu HTTP a útok pomocí vložení vzdáleného souboru.
* Ochrana před narušením protokolu HTTP.
* Ochrana před anomáliemi protokolu HTTP, jako například chybějící údaj user-agent hostitele nebo hlavičky Accept.
* Ochrana před roboty, prohledávacími moduly a skenery.
* Detekce běžných chyb v konfiguraci aplikací (tj. Apache, IIS atd.).

Podrobnější seznam pravidel a ochrany, kterou poskytují, naleznete v popisu [základních sad pravidel](#core-rule-sets).

### <a name="core-rule-sets"></a>Základní sady pravidel

Application Gateway podporuje dvě sady pravidel, CRS 3.0 a CRS 2.2.9. Tyto základní sady pravidel jsou kolekce pravidel, které chrání vaše webové aplikace před škodlivými aktivitami.

#### <a name="owasp30"></a>OWASP_3.0

Základní sada pravidel 3.0 má 13 skupin pravidel, shrnutých v následující tabulce. Každá z těchto skupin obsahuje několik pravidel, která mohou být zakázána.

|RuleGroup|Popis|
|---|---|
|**[REQUEST-910-IP-REPUTATION](application-gateway-crs-rulegroups-rules.md#crs910)**|Obsahuje pravidla chránící před známými spammery a škodlivou aktivitou.|
|**[REQUEST-911-METHOD-ENFORCEMENT](application-gateway-crs-rulegroups-rules.md#crs911)**|Obsahuje pravidla pro uzamčení metod (PUT, PATCH< ..)|
|**[REQUEST-912-DOS-PROTECTION](application-gateway-crs-rulegroups-rules.md#crs912)**| Obsahuje pravidla pro ochranu před útoky DoS (Denial of Service).|
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

* **Režim detekce** – Když je nakonfigurován ke spuštění v režimu detekce, WAF služby Application Gateway monitoruje a protokoluje všechny výstrahy na hrozby do souboru protokolu. Diagnostika protokolování pro službu Application Gateway by měla být v části **Diagnostika** zapnutá. Bude také třeba zajistit, aby byl vybrán a zapnut protokol WAF. když je firewall webových aplikací spuštěný v režimu detekce, neblokuje příchozí požadavky.
* **Režim prevence** – Když je nakonfigurován ke spuštění v režimu prevence, služba Application Gateway aktivně blokuje vniknutí a útoky detekované pomocí svých pravidel. Útočník obdrží výjimku 403 – Neoprávněný přístup a připojení se ukončí. Režim prevence takové útoky nadále protokoluje do protokolů WAF.

### <a name="application-gateway-waf-reports"></a>Monitorování WAF

Monitorování stavu službu Application Gateway je velmi důležité. Monitorování stavu vašeho firewallu webových aplikací a aplikací, které chrání, je zajišťována protokolováním a integrací se službami Azure Monitor, Azure Security Center a Log Analytics.

![Diagnostika](./media/application-gateway-web-application-firewall-overview/diagnostics.png)

#### <a name="azure-monitor"></a>Azure Monitor

Každý protokol aplikační brány je integrovaný do služby [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md).  To umožňuje sledovat diagnostické informace, včetně výstrah a protokolů WAF.  Tato možnost je poskytována v rámci prostředku Application Gateway na portálu na kartě **Diagnostika** nebo přímo prostřednictvím služby Azure Monitor. Další informace o povolení diagnostických protokolů pro službu Application Gateway najdete na stránkách o [diagnostice služby Application Gateway](application-gateway-diagnostics.md)

#### <a name="azure-security-center"></a>Azure Security Center

[Azure Security Center](../security-center/security-center-intro.md) pomáhá předcházet hrozbám, rozpoznávat je a reagovat na ně a nabízí lepší přehled o zabezpečení prostředků Azure a kontrolu nad nimi. Služba Application Gateway se [integruje do služby Azure Security Center](application-gateway-integration-security-center.md). Azure Security Center prohledává vaše prostředí a rozpoznává nechráněné webové aplikace. Nyní může doporučovat firewall webových aplikací (WAF) služby Application Gateway k ochraně těchto citlivých prostředků. Firewall webových aplikací (WAF) služby Application Gateway můžete vytvořit přímo ve službě Azure Security Center.  Tyto instance firewallu webových aplikací (WAF) jsou integrovány se službou Azure Security Center a budou posílat upozornění a informace o stavu zpět do služby Azure Security Center za účelem vytváření sestav.

![Obrázek 1](./media/application-gateway-web-application-firewall-overview/figure1.png)

#### <a name="logging"></a>Protokolování

Firewall webových aplikací (WAF) služby Application Gateway poskytuje podrobné generování sestav o každém útoky, který detekuje. Protokolování je integrované v protokolech diagnostiky Azure a výstrahy se zaznamenávají ve formátu JSON. Tyto protokoly je možné integrovat se službou [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md).

![imageURLroute](./media/application-gateway-web-application-firewall-overview/waf2.png)

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

Firewall webových aplikací je k dispozici jako nová položka WAF SKU. Tato položka SKU je k dispozici pouze v modelu zřizování Azure Resource Manager, nikoli v rámci klasického modelu nasazení. WAF SKU je navíc dostupný jen pro střední a velké instance aplikační brány. Všechna omezení pro aplikační brány platí také pro WAF SKU. Ceny jsou založeny na hodinové sazbě za instanci brány a na poplatcích za zpracování dat. Hodinová sazba za bránu se pro položku WAF SKU liší od sazby pro standardní položky SKU. Sazby jsou uvedeny v článku [Podrobnosti o cenách Application Gateway](https://azure.microsoft.com/pricing/details/application-gateway/). Poplatky za zpracování dat zůstávají stejné. Neexistují žádné poplatky za pravidla nebo skupiny pravidel. Jedna aplikační brána může chránit několik webových aplikací a další aplikace nejsou zpoplatněny. 

Fakturace za WAF bude zahájena 5. 5. 2017, do té doby budou položky WAF SKU fakturovány podle standardních sazeb.

## <a name="next-steps"></a>Další kroky

Až se podrobněji seznámíte se schopnostmi WAF, přečtěte si článek [Postup konfigurace Firewallu webových aplikací ve službě Application Gateway](application-gateway-web-application-firewall-portal.md).

