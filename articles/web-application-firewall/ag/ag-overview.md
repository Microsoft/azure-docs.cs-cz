---
title: Úvod do brány firewall webových aplikací Azure
titleSuffix: Azure Web Application Firewall
description: Tento článek obsahuje přehled brány firewall webových aplikací (WAF) v application gateway
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: victorh
ms.topic: overview
ms.openlocfilehash: e0e5c143e619b1c381a4a618a811883ad189719b
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81314358"
---
# <a name="azure-web-application-firewall-on-azure-application-gateway"></a>Brána firewall webových aplikací Azure v bráně aplikací Azure

Azure Web Application Firewall (WAF) ve službě Azure Application Gateway poskytuje centralizovanou ochranu webových aplikací před běžnými zneužitími a chybami zabezpečení. Webové aplikace jsou stále více terčem škodlivých útoků, které zneužívají běžně známé chyby zabezpečení. Vkládání SQL a skriptování mezi lokalitami patří mezi nejběžnější útoky.

WAF na aplikační bráně je založen na [základní sadě pravidel (CRS)](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.1, 3.0 nebo 2.2.9 z projektu zabezpečení otevřených webových aplikací (OWASP). WAF se automaticky aktualizuje tak, aby zahrnoval ochranu před novými chybami zabezpečení, bez další konfigurace. 

Všechny níže uvedené funkce WAF existují uvnitř zásad WAF. Můžete vytvořit více zásad a mohou být přidruženy k aplikační bráně, k jednotlivým naslouchaným posluchačům nebo k pravidlům směrování na základě cesty v bráně aplikace. Tímto způsobem můžete mít samostatné zásady pro každý web za vaší aplikační brány v případě potřeby. Další informace o zásadách WAF naleznete [v tématu Vytvoření zásad WAF](create-waf-policy-ag.md).

   > [!NOTE]
   > Zásady WAF pro vlastní web a uri jsou ve verzi Public Preview. To znamená, že tato funkce podléhá doplňkovým podmínkám použití společnosti Microsoft. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

![Diagram WAF aplikační brány](../media/ag-overview/waf1.png)

Aplikační brána funguje jako řadič pro doručování aplikací (ADC). Nabízí zabezpečení transportní vrstvy (TLS), dříve známé jako SSL (Secure Sockets Layer), ukončení, spřažení relací založené na souborech cookie, distribuci zatížení kruhového dotazování, směrování založené na obsahu, schopnost hostovat více webových stránek a vylepšení zabezpečení.

Vylepšení zabezpečení aplikační brány zahrnují správu zásad TLS a komplexní podporu TLS. Zabezpečení aplikací je posíleno integrací WAF do aplikační brány. Tato kombinace chrání webové aplikace před běžnými chybami zabezpečení. A poskytuje snadno konfigurovatelné centrální umístění pro správu.

## <a name="benefits"></a>Výhody

Tato část popisuje základní výhody, které waf na aplikační brány poskytuje.

### <a name="protection"></a>Ochrana

* Chraňte své webové aplikace před webovými chybami zabezpečení a útoky bez úprav y back-end kódu.

* Chraňte více webových aplikací současně. Instance Application Gateway může hostit až 40 webů, které jsou chráněny bránou firewall webové aplikace.

* Vytvoření vlastních zásad WAF pro různé weby za stejným WAF 

* Chraňte své webové aplikace před škodlivými roboty pomocí sady pravidel reputace IP (preview)

### <a name="monitoring"></a>Monitorování

* Sledujte útoky proti webovým aplikacím pomocí protokolu WAF v reálném čase. Protokol je integrovaný s [Azure Monitor](../../azure-monitor/overview.md) sledovat waf výstrahy a snadno sledovat trendy.

* Waf aplikační brány je integrovaný s Azure Security Center. Security Center poskytuje centrální zobrazení stavu zabezpečení všech vašich prostředků Azure.

### <a name="customization"></a>Přizpůsobení

* Přizpůsobte pravidla WAF a skupiny pravidel tak, aby vyhovovaly vašim požadavkům na aplikaci a eliminovaly falešně pozitivní výsledky.

* Přidružte zásady WAF pro každý web za waf, aby bylo možné konfiguraci specifické pro web

* Vytvoření vlastních pravidel podle potřeb vaší aplikace

## <a name="features"></a>Funkce

- Ochrana proti vstřikování SQL.
- Ochrana skriptování mezi pracemi.
- Ochrana proti dalším běžným webovým útokům, jako je například vkládání příkazů, pašování požadavků HTTP, rozdělení odpovědi HTTP a vzdálené zahrnutí souborů.
- Ochrana proti porušení protokolu HTTP.
- Ochrana proti anomáliím protokolu HTTP, jako je například chybějící hostitelský uživatelský agent a přijímat záhlaví.
- Ochrana proti prohledávačům a skenerům.
- Detekce běžných chybných konfigurací aplikací (například Apache a IIS).
- Konfigurovatelné limity velikosti požadavku s dolnía horní mezí.
- Seznamy vyloučení umožňují vynechat určité atributy požadavku z vyhodnocení WAF. Běžným příkladem jsou tokeny vložené službou Active Directory, které se používají pro pole ověřování nebo hesla.
- Vytvořte vlastní pravidla, která budou vyhovovat konkrétním potřebám vašich aplikací.
- Geofiltrovat provoz, který některým zemím umožní nebo zablokuje přístup k vašim aplikacím. (Preview)
- Chraňte své aplikace před roboty pomocí sady pravidel pro zmírnění botů. (Preview)

## <a name="waf-policy"></a>Zásady WAF

Chcete-li povolit bránu firewall webových aplikací v bráně aplikace, musíte vytvořit zásady WAF. Tyto zásady jsou místo, kde existují všechna spravovaná pravidla, vlastní pravidla, vyloučení a další vlastní nastavení, jako je limit pro odesílání souborů. 

### <a name="core-rule-sets"></a>Základní sady pravidel

Aplikační brána podporuje tři sady pravidel: CRS 3.1, CRS 3.0 a CRS 2.2.9. Tato pravidla chrání webové aplikace před škodlivými aktivitami.

Další informace naleznete v tématu [Skupiny a pravidla brány firewall webových aplikací firewall .](application-gateway-crs-rulegroups-rules.md)

### <a name="custom-rules"></a>Vlastní pravidla

Aplikační brána také podporuje vlastní pravidla. Pomocí vlastních pravidel můžete vytvořit vlastní pravidla, která jsou vyhodnocována pro každý požadavek, který prochází WAF. Tato pravidla mají vyšší prioritu než ostatní pravidla ve spravovaných sadách pravidel. Pokud je splněna sada podmínek, je provedena akce povolit nebo blokovat. 

Operátor geomatch je nyní k dispozici ve verzi Public Preview pro vlastní pravidla. Další informace naleznete v [tématu vlastní pravidla geomatch.](custom-waf-rules-overview.md#geomatch-custom-rules-preview)

> [!NOTE]
> Operátor geomatch pro vlastní pravidla je aktuálně ve verzi Public Preview a je vybaven předběžnou smlouvou o úrovni služeb. Některé funkce nemusí být podporované nebo můžou mít omezené možnosti. Podrobnosti najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Další informace o vlastních pravidlech najdete [v tématu Vlastní pravidla pro aplikační bránu.](custom-waf-rules-overview.md)

### <a name="bot-mitigation-preview"></a>Zmírnění botů (náhled)

Spravovanou sadu pravidel ochrany botů lze povolit tak, aby váš WAF blokoval nebo protokoloval požadavky ze známých škodlivých IP adres spolu se spravovanou sadou pravidel. IP adresy jsou získávány z informačního kanálu Microsoft Threat Intelligence. Inteligentní graf zabezpečení pohání microsoftovou analýzu hrozeb a používá ho několik služeb včetně Azure Security Center.

> [!NOTE]
> Sada pravidel ochrany botů je aktuálně ve verzi Public Preview a je vybavena předběžnou smlouvou o úrovni služeb. Některé funkce nemusí být podporované nebo můžou mít omezené možnosti. Podrobnosti najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pokud je povolena ochrana botů, příchozí požadavky, které odpovídají IP adresy klienta škodlivého robota, jsou zaznamenány v protokolu brány firewall, další informace naleznete níže. K protokolům WAF můžete přistupovat z účtu úložiště, centra událostí nebo analýzy protokolů. 

### <a name="waf-modes"></a>Režimy WAF

Waf aplikační brány lze nakonfigurovat tak, aby běžel v následujících dvou režimech:

* **Režim zjišťování**: Monitoruje a zaznamenává všechna upozornění na hrozby. Diagnostiku protokolování pro aplikační bránu zapnete v části **Diagnostika.** Musíte se také ujistit, že je vybrán a zapnutý protokol WAF. Brána firewall webové aplikace neblokuje příchozí požadavky, když pracuje v režimu zjišťování.
* **Režim prevence**: Blokuje vniknutí a útoky, které pravidla detekují. Útočník obdrží výjimku "403 neoprávněný přístup" a připojení je ukončeno. Režim prevence zaznamenává takové útoky v protokolech WAF.

> [!NOTE]
> Doporučujeme spustit nově nasazený WAF v režimu zjišťování na krátkou dobu v produkčním prostředí. To poskytuje možnost získat [protokoly brány firewall](../../application-gateway/application-gateway-diagnostics.md#firewall-log) a aktualizovat všechny výjimky nebo [vlastní pravidla](./custom-waf-rules-overview.md) před přechodem do režimu prevence. To může pomoci snížit výskyt neočekávaného blokovaného provozu.

### <a name="anomaly-scoring-mode"></a>Režim hodnocení anomálií

OWASP má dva režimy pro rozhodování, zda blokovat provoz: tradiční režim a režim hodnocení anomálií.

V tradičním režimu je provoz, který odpovídá libovolnému pravidlu, považován za nezávisle na jiných shodách pravidel. Tento režim je snadno pochopitelný. Nedostatek informací o tom, kolik pravidel odpovídá konkrétnímu požadavku, je však omezením. Takže byl zaveden režim hodnocení anomálií. Je to výchozí pro OWASP 3. *x*.

V režimu hodnocení anomálií není provoz, který odpovídá libovolnému pravidlu, okamžitě blokován, když je brána firewall v režimu prevence. Pravidla mají určitou závažnost: *Kritická*, *Chyba*, *Upozornění*nebo *Oznámení*. Tato závažnost ovlivňuje číselnou hodnotu požadavku, která se nazývá Skóre anomálií. Například jedna shoda pravidel *upozornění* přispívá ke skóre 3. Jedna *shoda kritických* pravidel přispívá 5.

|Severity  |Hodnota  |
|---------|---------|
|Kritická     |5|
|Chyba        |4|
|Upozornění      |3|
|Oznámení       |2|

Existuje prahová hodnota 5 pro skóre anomálií blokovat provoz. Takže jedna *shoda kritické* pravidlo je dost pro aplikační brány WAF blokovat požadavek, a to i v režimu prevence. Ale jedno *pravidlo upozornění* pouze zvyšuje skóre anomálií o 3, což samo o sobě nestačí k zablokování provozu.

> [!NOTE]
> Zpráva, která je zaznamenána, když pravidlo WAF odpovídá provozu, obsahuje hodnotu akce "Blokováno". Ale provoz je ve skutečnosti blokován pouze pro skóre anomálií 5 nebo vyšší.  

### <a name="waf-monitoring"></a>Monitorování WAF

Monitorování stavu službu Application Gateway je velmi důležité. Monitorování stavu waf a aplikace, které chrání, jsou podporovány integrací s Azure Security Center, Azure Monitor a Azure Monitor protokoly.

![Diagram diagnostiky WAF aplikační brány](../media/ag-overview/diagnostics.png)

#### <a name="azure-monitor"></a>Azure Monitor

Protokoly aplikační brány jsou integrované s [Azure Monitor](../../azure-monitor/overview.md). To umožňuje sledovat diagnostické informace, včetně výstrah WAF a protokolů. K této funkci se dostanete na kartě **Diagnostika** v prostředku aplikační brány na portálu nebo přímo prostřednictvím Azure Monitoru. Další informace o povolení protokolů naleznete v [tématu Diagnostika aplikační brány](../../application-gateway/application-gateway-diagnostics.md).

#### <a name="azure-security-center"></a>Azure Security Center

[Security Center](../../security-center/security-center-intro.md) vám pomůže předcházet hrozbám, zjišťovat je a reagovat na ně. Poskytuje lepší přehled o zabezpečení vašich prostředků Azure a kontrolu nad ním. Aplikační brána je [integrována s Security Center](../../application-gateway/application-gateway-integration-security-center.md). Security Center prohledá vaše prostředí a zjistí nechráněné webové aplikace. Může doporučit WAF aplikační brány k ochraně těchto ohrožených prostředků. Brány firewall vytváříte přímo z centra zabezpečení. Tyto instance WAF jsou integrovány s Security Center. Posílají výstrahy a informace o stavu do Centra zabezpečení pro vytváření sestav.

![Okno přehled centra zabezpečení](../media/ag-overview/figure1.png)

#### <a name="azure-sentinel"></a>Azure Sentinel

Microsoft Azure Sentinel je škálovatelné řešení pro nativní pro cloud, správu událostí zabezpečení (SIEM) a automatické odpovědi orchestrace zabezpečení (SOAR). Azure Sentinel poskytuje inteligentní analýzy zabezpečení a analýzy hrozeb v celém podniku a poskytuje jediné řešení pro detekci výstrah, viditelnost hrozeb, proaktivní lov a reakci na hrozby.

S integrovaným sešitem událostí brány firewall Azure WAF můžete získat přehled o událostech zabezpečení ve vašem WAF. To zahrnuje události, odpovídající a blokovaná pravidla a vše ostatní, co se zaznamená do protokolů brány firewall. Více informací o protokolování níže. 


![Sentinel](../media/ag-overview/sentinel.png)

#### <a name="logging"></a>Protokolování

Aplikace Gateway WAF poskytuje podrobné zprávy o každé hrozbě, kterou zjistí. Protokolování je integrované s protokoly Diagnostika Azure. Výstrahy jsou zaznamenávány ve formátu json. Tyto protokoly lze integrovat s [protokoly Azure Monitor](../../azure-monitor/insights/azure-networking-analytics.md).

![Okna protokolů diagnostiky aplikační brány](../media/ag-overview/waf2.png)

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

Cenové modely se liší pro WAF_v1 a WAF_v2 SKU. Další informace najdete na stránce [S cenami brány aplikací.](https://azure.microsoft.com/pricing/details/application-gateway/) 

## <a name="next-steps"></a>Další kroky

- Začínáme [vytvořením zásad WAF](create-waf-policy-ag.md)
- Další informace o [pravidlech spravovaných waf](application-gateway-crs-rulegroups-rules.md)
- Další informace o [vlastních pravidlech](custom-waf-rules-overview.md)
- Informace o [bráně firewall webových aplikací na Azure Front Door](../afds/afds-overview.md)

