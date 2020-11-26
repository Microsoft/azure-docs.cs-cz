---
title: Připojení dat analýzy hrozeb ke službě Azure Sentinel | Microsoft Docs
description: Přečtěte si, jak propojit data analýzy hrozeb s funkcí Sentinel Azure.
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/22/2019
ms.author: yelevin
ms.openlocfilehash: 205cc6eea5d1ac3be2d0e266621067dc8e20d2f9
ms.sourcegitcommit: b8a175b6391cddd5a2c92575c311cc3e8c820018
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96121752"
---
# <a name="connect-data-from-threat-intelligence-providers"></a>Připojení dat od zprostředkovatelů pro analýzu hrozeb

> [!IMPORTANT]
> Datové konektory pro analýzu hrozeb v Azure Sentinel jsou momentálně ve verzi Public Preview.
> Tato funkce se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Možnost Azure Sentinel umožňuje importovat ukazatele hrozeb, které vaše organizace používá, což může zlepšit schopnost analytiků zabezpečení detekovat známé hrozby a určit jejich prioritu. Několik funkcí z Azure Sentinel pak bude dostupných nebo vylepšených:

- **Analýza** zahrnuje sadu naplánovaných šablon pravidel, které můžete povolit pro generování výstrah a incidentů na základě shody událostí protokolu od indikátorů hrozeb.

- Pracovní **sešity** poskytují souhrnné informace o ukazatelích hrozeb importovaných do Azure Sentinel a všech výstrahách vygenerovaných z pravidel analýzy, které odpovídají indikátorům hrozeb.

- **Lovecké** dotazy umožňují vyšetřovacím zabezpečení používat indikátory hrozeb v rámci běžných loveckých scénářů.

- **Poznámkové bloky** můžou používat indikátory hrozeb při zkoumání anomálií a zjištění škodlivého chování.

Pomocí jednoho z produktů s integrovanými platformami pro analýzu hrozeb (TIP) uvedených v následující části, připojení k TAXII serverům nebo pomocí přímé integrace s [rozhraním API služby Microsoft Graph Security tiIndicators](/graph/api/resources/tiindicator)můžete streamovat ukazatele hrozeb do Azure Sentinel.

## <a name="integrated-threat-intelligence-platform-products"></a>Integrované produkty pro platformu pro analýzu hrozeb

- [Platforma MISP Open Source Threat Intelligence](https://www.misp-project.org/)
    
    Vzorový skript, který poskytuje klientům MISP instance pro migraci indikátorů hrozeb do rozhraní API zabezpečení Microsoft Graph, najdete v tématu [MISP to Microsoft Graph Security Script](https://github.com/microsoftgraph/security-api-solutions/tree/master/Samples/MISP).

- [Anomálie ThreatStream](https://www.anomali.com/products/threatstream)

    Chcete-li stáhnout integrátor a rozšíření ThreatStream a pokyny pro připojení ThreatStream Intelligence k rozhraní API zabezpečení Microsoft Graph, přečtěte si stránku [ThreatStream Ke_stažení](https://ui.threatstream.com/downloads) .

- [Palo Alto Networks MineMeld](https://www.paloaltonetworks.com/products/secure-the-network/subscriptions/minemeld)
    
    Pokyny najdete v tématu [posílání IOCs do rozhraní API zabezpečení Microsoft Graph pomocí MineMeld](https://live.paloaltonetworks.com/t5/MineMeld-Articles/Sending-IOCs-to-the-Microsoft-Graph-Security-API-using-MineMeld/ta-p/258540).

- [Platforma ThreatConnect](https://threatconnect.com/solution/)

    Informace najdete v tématu věnovaném [integraci ThreatConnect](https://threatconnect.com/integrations/) a vyhledání rozhraní API pro Microsoft Graph zabezpečení na stránce.

- [Platforma EclecticIQ](https://www.eclecticiq.com/solutions)

- [Platforma ThreatQ Threat Intelligence](https://www.threatq.com/)

    Informace a pokyny najdete v tématu [Microsoft Sentinel Connector for ThreatQ Integration](https://appsource.microsoft.com/product/web-apps/threatquotientinc1595345895602.microsoft-sentinel-connector-threatq?src=health&tab=Overview).

## <a name="connect-azure-sentinel-to-your-threat-intelligence-platform"></a>Připojení Azure Sentinel k vaší platformě pro analýzu hrozeb

### <a name="prerequisites"></a>Předpoklady  

- Role Azure AD buď globálního správce nebo správce zabezpečení, aby udělila oprávnění k produktu TIP nebo vlastní aplikaci, která používá přímou integraci s rozhraním API Microsoft Graph tiIndicators Security.

- Oprávnění ke čtení a zápisu do pracovního prostoru Azure Sentinel pro ukládání indikátorů hrozeb.

### <a name="instructions"></a>Pokyny

1. [Zaregistrujte aplikaci](/graph/auth-v2-service#1-register-your-app) v Azure Active Directory, abyste získali ID aplikace, tajný klíč aplikace a id klienta Azure Active Directory. Tyto hodnoty budete potřebovat, když nakonfigurujete integrovaný produkt nebo aplikaci TIP, která používá přímou integraci s rozhraním API Microsoft Graph tiIndicators Security.

2. [Konfigurace oprávnění rozhraní API](/graph/auth-v2-service#2-configure-permissions-for-microsoft-graph) pro registrovanou aplikaci: přidejte do registrované aplikace oprávnění aplikace Microsoft Graph **ThreatIndicators. OwnedBy** .

3. Požádejte správce tenanta Azure Active Directory o udělení souhlasu správce k registrované aplikaci pro vaši organizaci. Z Azure Portal: **Azure Active Directory**  >  **App registrations**  >  **\<_app name_>**  >  **oprávnění k rozhraní API**  >  **pro \<_tenant name_> zobrazení Azure Active Directory registrace aplikací udělují správcům udělení souhlasu správce**.

4. Pomocí přímé integrace s rozhraním Microsoft Graph Security tiIndicators API pro odesílání ukazatelů do Azure Sentinel určete následující nastavení:
    
    a. Hodnoty pro ID registrované aplikace, tajný klíč a ID tenanta.
    
    b. Pro cílový produkt zadejte Azure Sentinel.
    
    c. Pro akci zadejte výstrahu.

5. V Azure Portal přejděte na **Azure Sentinel**  >  **datové konektory** Azure Sentinel a pak vyberte konektor **platformy pro analýzu hrozeb (Preview)** .

6. Vyberte **stránku otevřít konektor** a pak **připojit**.

7. Pokud chcete zobrazit ukazatele hrozeb importované do Azure Sentinel, přejděte do **Azure Sentinel-logs**  >  **SecurityInsights** a pak rozbalte **ThreatIntelligenceIndicator**.

## <a name="connect-azure-sentinel-to-taxii-servers"></a>Připojení Azure Sentinel k serverům TAXII

### <a name="prerequisites"></a>Předpoklady

- Oprávnění ke čtení a zápisu do pracovního prostoru Azure Sentinel pro ukládání indikátorů hrozeb.

- Identifikátor URI a ID kolekce TAXII 2,0 serveru.

### <a name="instructions"></a>Pokyny

1. V Azure Portal přejděte na **Azure Sentinel**  >  **datové konektory** Azure Sentinel a pak vyberte konektor **Threat Intelligence-TAXII (Preview)** .

2. Vyberte **stránku otevřít konektor**.

3. Zadejte požadované a volitelné informace do textových polí.

4. Vyberte **Přidat** , pokud chcete povolit připojení k serveru TAXII 2,0.

5. Pokud máte další servery TAXII 2,0: Opakujte kroky 3 a 4.

6. Pokud chcete zobrazit ukazatele hrozeb importované do Azure Sentinel, přejděte do **Azure Sentinel-logs**  >  **SecurityInsights** a pak rozbalte **ThreatIntelligenceIndicator**.

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste zjistili, jak připojit poskytovatele analýzy hrozeb ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích.

- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](./tutorial-detect-threats-built-in.md).