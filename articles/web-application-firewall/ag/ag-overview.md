---
title: Co je firewall webových aplikací Azure v Azure Application Gateway?
titleSuffix: Azure Web Application Firewall
description: Tento článek poskytuje přehled firewallu webových aplikací (WAF) na Application Gateway
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 09/16/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 659e7fcdbd2284110282d14fc89bd4d8d5ac2472
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91267019"
---
# <a name="what-is-azure-web-application-firewall-on-azure-application-gateway"></a>Co je firewall webových aplikací Azure v Azure Application Gateway?

Firewall webových aplikací Azure (WAF) v Azure Application Gateway poskytuje centralizovanou ochranu webových aplikací před běžnými zneužitími a chybami zabezpečení. Webové aplikace jsou stále více zaměřené na škodlivé útoky, které využívají často známá ohrožení zabezpečení. Vkládání SQL a skriptování mezi weby patří mezi nejběžnější útoky.

WAF on Application Gateway vychází ze [základní sady pravidel (počítačový systém)](https://owasp.org/www-project-modsecurity-core-rule-set/) 3,1, 3,0 nebo 2.2.9 z otevřeného projektu webové aplikace Security (OWASP). WAF se automaticky aktualizuje, aby zahrnovala ochranu proti novým chybám zabezpečení bez nutnosti další konfigurace. 

Všechny níže uvedené funkce WAF existují v zásadách WAF. Můžete vytvořit více zásad a můžete je přidružit k Application Gateway, jednotlivým posluchačům nebo k pravidlům směrování na základě cesty na Application Gateway. Tímto způsobem můžete v případě potřeby mít v případě potřeby samostatné zásady pro každou lokalitu za vaším Application Gateway. Další informace o zásadách WAF najdete v tématu [Vytvoření zásady WAF](create-waf-policy-ag.md).

   > [!NOTE]
   > Zásady WAF podle identifikátoru URI jsou v Public Preview. To znamená, že tato funkce podléhá dodatečným podmínkám používání společnosti Microsoft. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

![Diagram Application Gateway WAF](../media/ag-overview/waf1.png)

Application Gateway funguje jako kontroler doručování aplikací (ADC). Nabízí zabezpečení TLS (Transport Layer Security), dříve označované jako SSL (Secure Sockets Layer) (SSL), ukončení, spřažení relací na základě souborů cookie, distribuce zatížení kruhového dotazování, směrování na základě obsahu, schopnost hostovat více webů a vylepšení zabezpečení.

Mezi Application Gateway vylepšení zabezpečení patří Správa zásad TLS a kompletní podpora protokolu TLS. Zabezpečení aplikací je posílené integrací WAF do Application Gateway. Tato kombinace chrání webové aplikace před běžnými chybami zabezpečení. A nabízí snadnou konfiguraci centrálního umístění pro správu.

## <a name="benefits"></a>Výhody

Tato část popisuje základní výhody, které WAF Application Gateway poskytuje.

### <a name="protection"></a>Ochrana

* Chraňte své webové aplikace proti webovým chybám zabezpečení a útokům bez nutnosti úprav kódu back-endu.

* Chraňte více webových aplikací současně. Instance Application Gateway může hostovat až 40 webů, které jsou chráněné bránou firewall webových aplikací.

* Vytváření vlastních zásad WAF pro různé weby za stejným WAF 

* Chraňte své webové aplikace před škodlivými robotyy pomocí IP reputace RuleSet (Preview).

### <a name="monitoring"></a>Monitorování

* Sledujte útoky na webové aplikace pomocí protokolu WAF v reálném čase. Protokol je integrovaný do [Azure monitor](../../azure-monitor/overview.md) a umožňuje sledovat výstrahy WAF a snadno sledovat trendy.

* Application Gateway WAF je integrována s Azure Security Center. Security Center poskytuje centrální pohled na stav zabezpečení všech vašich prostředků Azure.

### <a name="customization"></a>Přizpůsobení

* Přizpůsobte pravidla WAF a skupiny pravidel tak, aby vyhovovaly vašim požadavkům na aplikace a vyloučily falešně pozitivní výsledky.

* Přiřaďte zásady WAF pro každou lokalitu za vaší WAF, aby bylo možné nakonfigurovat konkrétní lokalitu.

* Vytváření vlastních pravidel podle potřeb vaší aplikace

## <a name="features"></a>Funkce

- Ochrana injektáže SQL.
- Ochrana skriptování mezi weby.
- Ochrana proti jiným běžným webovým útokům, jako je vkládání příkazů, vkládání požadavků HTTP, rozdělování odpovědí HTTP a vzdálené zahrnutí souborů.
- Ochrana proti narušení protokolu HTTP.
- Ochrana proti anomáliím protokolu HTTP, například chybějícímu uživatelskému agentovi a hlavičkám přijetí hostitele.
- Ochrana proti prohledávacím modulům a skenerům
- Detekce běžných neobvyklých konfigurací aplikací (například Apache a IIS).
- Konfigurovatelné omezení velikosti žádostí s použitím dolních a horních mezí.
- Seznamy vyloučení umožňují vynechat určité atributy žádosti z WAF vyhodnocení. Běžným příkladem jsou vložené tokeny vložené službou Active Directory, které se používají pro pole ověřování nebo hesla.
- Vytvářejte vlastní pravidla, aby vyhovovala konkrétním potřebám vašich aplikací.
- Přenos geografického filtru, který umožňuje nebo blokuje určité země nebo oblasti, aby získal přístup k vašim aplikacím. (Preview)
- Chraňte své aplikace z roboty pomocí RuleSet zmírnění ochrany bot. (Preview)

## <a name="waf-policy-and-rules"></a>Zásady a pravidla WAF

Pokud chcete povolit bránu firewall webových aplikací na Application Gateway, musíte vytvořit zásadu WAF. Tato zásada je tam, kde existují všechna spravovaná pravidla, vlastní pravidla, vyloučení a další přizpůsobení, jako je například limit nahrávání souborů.

Můžete nakonfigurovat zásady WAF a přidružit tuto zásadu k jedné nebo více aplikačním branám, které se mají chránit. Zásady WAF se skládají ze dvou typů pravidel zabezpečení:

- Vlastní pravidla, která vytvoříte

- Spravované sady pravidel, které jsou kolekcí předem nakonfigurovaných sad pravidel spravovaných Azure

Pokud jsou přítomny obě, před zpracováním pravidel v sadě spravovaných pravidel se zpracují vlastní pravidla. Pravidlo se skládá z podmínky shody, priority a akce. Podporované typy akcí: ALLOW, BLOCK a LOG. Můžete vytvořit plně přizpůsobené zásady, které vyhovují konkrétním požadavkům na ochranu aplikací, a to kombinováním spravovaných a vlastních pravidel.

Pravidla v rámci zásad jsou zpracovávána v pořadí podle priority. Priorita je jedinečné celé číslo, které definuje pořadí pravidel pro zpracování. Menší celočíselná hodnota znamená vyšší prioritu a tato pravidla se vyhodnocují před pravidly s vyšší celočíselnou hodnotou. Po porovnání pravidla se na žádost aplikuje odpovídající akce, která byla definována v pravidle. Po zpracování této shody se pravidla s nižšími prioritami nezpracují dále.

Webová aplikace, kterou poskytuje Application Gateway, může mít přidruženou zásadu WAF na globální úrovni, na úrovni jednotlivých lokalit nebo na úrovni jednotlivých identifikátorů URI.

### <a name="core-rule-sets"></a>Základní sady pravidel

Application Gateway podporuje tři sady pravidel: počítačový rezervační 3,1, počítačový počítač 3,0 a počítačový 2.2.9. Tato pravidla chrání vaše webové aplikace před škodlivými aktivitami.

Další informace najdete v tématu [skupiny pravidel a pravidla pro počítačový rezervační Firewall webových aplikací](application-gateway-crs-rulegroups-rules.md).

### <a name="custom-rules"></a>Vlastní pravidla

Application Gateway podporuje také vlastní pravidla. Pomocí vlastních pravidel můžete vytvořit vlastní pravidla, která se vyhodnotí pro každý požadavek, který projde přes WAF. Tato pravidla mají vyšší prioritu než zbývající pravidla ve spravovaných sadách pravidel. Je-li splněna sada podmínek, je provedena akce pro povolení nebo blokování. 

Operátor inshodě je teď dostupný ve verzi Public Preview pro vlastní pravidla. Další informace najdete v tématu [vlastní pravidla pro inshodě](custom-waf-rules-overview.md#geomatch-custom-rules-preview) .

> [!NOTE]
> Operátor porovnávání pro vlastní pravidla je aktuálně ve verzi Public Preview a je k dispozici ve verzi Preview smlouvy o úrovni služeb. Některé funkce nemusí být podporované nebo můžou mít omezené možnosti. Podrobnosti najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Další informace o vlastních pravidlech najdete v tématu [vlastní pravidla pro Application Gateway.](custom-waf-rules-overview.md)

### <a name="bot-mitigation-preview"></a>Omezení na robota (Preview)

Sada pravidel spravované ochrany robotů může být povolená pro WAF k blokování nebo protokolování požadavků ze známých škodlivých IP adres společně se spravovaným RuleSet. Zdrojem těchto IP adres je kanál analýzy hrozeb Microsoftu. Analýza hrozeb Microsoftu využívá systém Intelligent Security Graph, který využívá celá řada služeb, včetně služby Azure Security Center.

> [!NOTE]
> Sada pravidel ochrany robota je aktuálně ve verzi Public Preview a poskytuje verzi Preview smlouvy o úrovni služeb. Některé funkce nemusí být podporované nebo můžou mít omezené možnosti. Podrobnosti najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pokud je zapnutá ochrana robota, do protokolu brány firewall se zaprotokolují příchozí požadavky, které se shodují s IP adresami klientů škodlivou bot, další informace najdete níže. K protokolům WAF můžete přistupovat z účtu úložiště, centra událostí nebo Log Analytics. 

### <a name="waf-modes"></a>WAF režimy

Application Gateway WAF lze nakonfigurovat tak, aby běžela v následujících dvou režimech:

* **Detekční režim**: monitoruje a protokoluje všechny výstrahy hrozeb. Diagnostiku protokolování pro Application Gateway zapnete v části **Diagnostika** . Musíte se také ujistit, že je vybraný a zapnutý protokol WAF. Firewall webových aplikací neblokuje příchozí požadavky, když pracuje v režimu detekce.
* **Režim prevence**: blokuje vniknutí a útoky, které pravidla zjišťují. Útočník obdrží výjimku "403 neoprávněný přístup" a připojení je zavřené. Režim prevence zaznamenává takové útoky do protokolů WAF.

> [!NOTE]
> Doporučuje se spouštět nově nasazené WAF v režimu detekce po krátkou dobu v produkčním prostředí. Díky tomu získáte možnost získat [protokoly brány firewall](../../application-gateway/application-gateway-diagnostics.md#firewall-log) a aktualizovat všechny výjimky nebo [vlastní pravidla](./custom-waf-rules-overview.md) před přechodem do režimu prevence. To může přispět k omezení výskytu neočekávaného blokovaného provozu.

### <a name="anomaly-scoring-mode"></a>Režim bodování anomálií

OWASP má dva režimy pro rozhodnutí, jestli se má blokovat provoz: režim vyhodnocování tradičních režimů a anomálií.

V tradičním režimu se provoz, který odpovídá libovolnému pravidlu, považuje za nezávisle na ostatních shodách pravidla. Tento režim je snadno srozumitelný. Ale nedostatek informací o tom, kolik pravidel odpovídá konkrétní žádosti, je omezení. Proto byl zaveden režim bodování anomálií. Ve výchozím nastavení je to pro OWASP 3. *x*.

V režimu bodování anomálií není přenos, který odpovídá libovolnému pravidlu, okamžitě zablokován, pokud je brána firewall v režimu prevence. Pravidla mají určitou závažnost: *kritická*, *Chyba*, *Upozornění* *nebo upozornění*. Tato závažnost má vliv na číselnou hodnotu pro požadavek, který se označuje jako skóre anomálií. Například jedno pravidlo *Upozornění* bude odpovídat 3 do skóre. Jedna *kritická* shoda pravidla přispívá 5.

|Závažnost  |Hodnota  |
|---------|---------|
|Kritické     |5|
|Chyba        |4|
|Upozornění      |3|
|Šestiměsíční       |2|

Pro skóre anomálií pro blokování provozu existuje prahová hodnota 5. Proto je pro Application Gateway WAF k blokování požadavku jedna *kritická* pravidla dostatečná, a to i v režimu prevence. Ale jedna shoda pravidla *Upozornění* zvyšuje jenom skóre anomálií o 3, což není dostačující pro blokování provozu.

> [!NOTE]
> Zpráva zaznamenaná v případě, že pravidlo WAF odpovídá provozu, zahrnuje hodnotu akce "blokováno". Provoz je ale ve skutečnosti zablokovaný jenom pro skóre anomálií 5 nebo vyšší.  

### <a name="waf-monitoring"></a>Monitorování WAF

Monitorování stavu službu Application Gateway je velmi důležité. Monitorování stavu WAF a aplikací, které chrání, jsou podporovány integrací s protokoly Azure Security Center, Azure Monitor a Azure Monitor.

![Diagram diagnostiky Application Gateway WAF](../media/ag-overview/diagnostics.png)

#### <a name="azure-monitor"></a>Azure Monitor

Protokoly Application Gateway jsou integrovány s [Azure monitor](../../azure-monitor/overview.md). To vám umožní sledovat diagnostické informace, včetně výstrah a protokolů WAF. K této funkci můžete přistupovat na kartě **Diagnostika** v prostředku Application Gateway na portálu nebo přímo prostřednictvím Azure monitor. Další informace o povolení protokolů najdete v tématu [diagnostika Application Gateway](../../application-gateway/application-gateway-diagnostics.md).

#### <a name="azure-security-center"></a>Azure Security Center

[Security Center](../../security-center/security-center-intro.md) pomáhá předcházet hrozbám, zjišťovat je a reagovat na ně. Poskytuje lepší přehled o zabezpečení prostředků Azure a kontrolu nad nimi. Application Gateway je [integrována s Security Center](../../application-gateway/application-gateway-integration-security-center.md). Security Center prohledá vaše prostředí a detekuje nechráněné webové aplikace. Může doporučit Application Gateway WAF k ochraně těchto ohrožených prostředků. Brány firewall vytvoříte přímo z Security Center. Tyto instance WAF jsou integrované s Security Center. Odesílají výstrahy a informace o stavu Security Center pro vytváření sestav.

![Okno přehledu Security Center](../media/ag-overview/figure1.png)

#### <a name="azure-sentinel"></a>Azure Sentinel

Microsoft Azure Sentinel je škálovatelná, cloudová, nativní, SIEM (Security Information Management) a automatizované řešení pro orchestraci zabezpečení (společnosti). Služba Azure Sentinel nabízí inteligentní analýzu zabezpečení a analýzu hrozeb napříč podnikem a poskytuje jediné řešení pro detekci výstrah, viditelnost hrozeb, proaktivní lov a reakci na hrozby.

S integrovaným sešitem událostí brány firewall Azure WAF můžete získat přehled událostí zabezpečení na WAF. Patří sem události, odpovídající a blokovaná pravidla a všechno ostatní, co se přihlásí do protokolů brány firewall. Další informace najdete v části protokolování níže. 


![Sešit událostí brány firewall Azure WAF](../media/ag-overview/sentinel.png)


#### <a name="azure-monitor-workbook-for-waf"></a>Azure Monitor sešitu pro WAF

Tento sešit umožňuje vlastní vizualizaci událostí WAF souvisejících se zabezpečením napříč několika panely, které lze filtrovat. Funguje se všemi WAF typy, včetně Application Gateway, frontových dveří a CDN a dá se filtrovat podle typu WAF nebo konkrétní instance WAF. Import přes šablonu nebo šablonu ARM. Pokud chcete nasadit tento sešit, přečtěte si článek [WAF Workbook](https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20WAF/Azure%20Monitor%20Workbook).

#### <a name="logging"></a>protokolování

Application Gateway WAF poskytuje podrobné hlášení o každé zjištěné hrozbě. Protokolování je integrované s protokoly Azure Diagnostics. Výstrahy se zaznamenávají ve formátu. JSON. Tyto protokoly je možné integrovat s [protokoly Azure monitor](../../azure-monitor/insights/azure-networking-analytics.md).

![Application Gateway diagnostické protokoly Windows](../media/ag-overview/waf2.png)

```json
{
  "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupId}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{appGatewayName}",
  "operationName": "ApplicationGatewayFirewall",
  "time": "2017-03-20T15:52:09.1494499Z",
  "category": "ApplicationGatewayFirewallLog",
  "properties": {
    {
      "instanceId": "ApplicationGatewayRole_IN_0",
      "clientIp": "52.161.109.145",
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
      "transactionId": "16861477007022634343"
      "policyId": "/subscriptions/1496a758-b2ff-43ef-b738-8e9eb5161a86/resourceGroups/drewRG/providers/Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/globalWafPolicy",
      "policyScope": "Global",
      "policyScopeName": " Global "
    }
  }
} 

```

## <a name="application-gateway-waf-sku-pricing"></a>Ceny SKU WAF služby Application Gateway

Cenové modely se liší od WAF_v1 a WAF_v2 SKU. Další informace najdete na stránce s [cenami Application Gateway](https://azure.microsoft.com/pricing/details/application-gateway/) . 

## <a name="whats-new"></a>Co je nového

Další informace o tom, co je nového u brány firewall webových aplikací Azure, najdete v tématu [Aktualizace Azure](https://azure.microsoft.com/updates/?category=networking&query=Web%20Application%20Firewall).

## <a name="next-steps"></a>Další kroky

- Další informace o [spravovaných pravidlech WAF](application-gateway-crs-rulegroups-rules.md)
- Další informace o [vlastních pravidlech](custom-waf-rules-overview.md)
- Přečtěte si o [bráně firewall webových aplikací v Azure front-dveří](../afds/afds-overview.md)
