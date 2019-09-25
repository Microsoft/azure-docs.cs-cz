---
title: Připojení dat analýzy hrozeb ke službě Azure Sentinel | Microsoft Docs
description: Přečtěte si, jak propojit data analýzy hrozeb s funkcí Sentinel Azure.
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/24/2019
ms.author: rkarlin
ms.openlocfilehash: cbdf05c714971db5a618ca2a8bb35fe286d6804c
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240672"
---
# <a name="connect-data-from-threat-intelligence-providers---preview"></a>Připojení dat od zprostředkovatelů pro analýzu hrozeb – Preview

> [!IMPORTANT]
> Analýza hrozeb v Azure Sentinel je momentálně ve verzi Public Preview.
> Tato funkce se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Možnost Azure Sentinel umožňuje importovat ukazatele hrozeb, které vaše organizace používá, což může zlepšit schopnost vašich analytiků zabezpečení detekovat známé hrozby a určit jejich prioritu. Několik funkcí z Azure Sentinel pak bude dostupných nebo vylepšených:

- **Analýza** zahrnuje sadu naplánovaných šablon pravidel, které můžete povolit pro generování výstrah a incidentů, které jsou založené na odpovídajících událostech protokolu z vašich indikátorů hrozeb.

- **Sešity** poskytují souhrnné informace o ukazatelích hrozeb, které jsou importovány do Azure Sentinel, a všechny výstrahy vygenerované z pravidel analýzy, které odpovídají indikátorům hrozeb.

- **Lovecké** dotazy umožňují vyšetřovacím zabezpečení používat indikátory hrozeb v rámci běžných loveckých scénářů.

- **Poznámkové bloky** můžou používat indikátory hrozeb při zkoumání anomálií a zjištění škodlivého chování.

Pomocí jednoho z produktů s integrovanými platformami pro analýzu hrozeb (s POPISem), které jsou uvedené v další části, nebo pomocí přímé integrace s [rozhraním API Microsoft Graph Security tiIndicators](https://aka.ms/graphsecuritytiindicators)můžete streamovat indikátory hrozeb do Azure Sentinel.

## <a name="integrated-threat-intelligence-platform-products"></a>Integrované produkty pro platformu pro analýzu hrozeb

- [Platforma MISP Open Source Threat Intelligence](https://www.misp-project.org/)
    
    Vzorový skript, který poskytuje klientům MISP instance pro migraci indikátorů hrozeb do rozhraní API zabezpečení Microsoft Graph, najdete v tématu [MISP to Microsoft Graph Security Script](https://github.com/microsoftgraph/security-api-solutions/tree/master/Samples/MISP).

- [Palo Alto Networks MineMeld](https://www.paloaltonetworks.com/products/secure-the-network/subscriptions/minemeld)
    
    Pokyny najdete v tématu [posílání IOCs do rozhraní API zabezpečení Microsoft Graph pomocí MineMeld](https://live.paloaltonetworks.com/t5/MineMeld-Articles/Sending-IOCs-to-the-Microsoft-Graph-Security-API-using-MineMeld/ta-p/258540).

- [Platforma ThreatConnect](https://threatconnect.com/solution/)


## <a name="prerequisites"></a>Požadavky  

- Role Azure AD buď globálního správce nebo správce zabezpečení, aby udělila oprávnění k produktu TIP nebo vlastní aplikaci, která používá přímou integraci s rozhraním API Microsoft Graph tiIndicators Security.

- Oprávnění ke čtení a zápisu do pracovního prostoru Azure Sentinel pro ukládání indikátorů hrozeb.

## <a name="connect-azure-sentinel-to-your-threat-intelligence-provider"></a>Připojení Azure Sentinel k poskytovateli funkce Threat Intelligence

1. [Zaregistrujte aplikaci](/graph/auth-v2-service#1-register-your-app) v Azure Active Directory, abyste získali ID aplikace, tajný klíč aplikace a id klienta Azure Active Directory. Tyto hodnoty budete potřebovat, když nakonfigurujete integrovaný produkt nebo aplikaci TIP, která používá přímou integraci s rozhraním API Microsoft Graph tiIndicators Security.

2. [Konfigurace oprávnění rozhraní API](/graph/auth-v2-service#2-configure-permissions-for-microsoft-graph) pro registrovanou aplikaci: Přidejte do registrované aplikace oprávnění aplikace Microsoft Graph **ThreatIndicators. OwnedBy** .

3. Požádejte správce tenanta Azure Active Directory o udělení souhlasu správce k registrované aplikaci pro vaši organizaci. Z Azure Portal: >  > **Oprávnění** **\<rozhraní API pro zobrazení názvuaplikace>v**Azure Active Directory registrace aplikací udělení souhlasu správce pro >  >  **název tenanta\<>**

4. Pomocí přímé integrace s rozhraním Microsoft Graph Security tiIndicators API pro odesílání ukazatelů do Azure Sentinel určete následující nastavení:
    
    a. Hodnoty pro ID registrované aplikace, tajný klíč a ID tenanta.
    
    b. Pro cílový produkt zadejte Azure Sentinel.
    
    c. Pro akci zadejte výstrahu.

5. V Azure Portal přejděte na**datové konektory** **Azure Sentinel** > a pak vyberte konektor **platformy pro analýzu hrozeb (Preview)** .

6. Vyberte **stránku otevřít konektor**a pak **připojit**.

7. Pokud chcete zobrazit indikátory hrozeb importované do Azure Sentinel, přejděte do **Azure Sentinel-logs** > **SecurityInsights**a pak rozbalte **ThreatIntelligenceIndicator**.

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste zjistili, jak připojit poskytovatele analýzy hrozeb ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích.

- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats.md).