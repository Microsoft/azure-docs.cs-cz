---
title: Připojení dat analýzy hrozeb k Azure Sentinelu| Dokumenty společnosti Microsoft
description: Přečtěte si, jak propojit data analýzy hrozeb s Azure Sentinelem.
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/22/2019
ms.author: yelevin
ms.openlocfilehash: 5c79642d287224cd15531701d7cc87ebfd72eb69
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588038"
---
# <a name="connect-data-from-threat-intelligence-providers"></a>Připojení dat od poskytovatelů analýzy hrozeb

> [!IMPORTANT]
> Datové konektory Threat Intelligence v Azure Sentinelu jsou aktuálně ve verzi Public Preview.
> Tato funkce je k dispozici bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Sentinel umožňuje importovat indikátory hrozeb, které vaše organizace používá, což může zvýšit schopnost analytiků zabezpečení zjišťovat a upřednostňovat známé hrozby. Několik funkcí z Azure Sentinelu pak k dispozici nebo jsou rozšířené:

- **Služba Analytics** obsahuje sadu naplánovaných šablon pravidel, které můžete povolit ke generování výstrah a incidentů na základě shod událostí protokolu z indikátorů hrozeb.

- **Sešity** poskytují souhrnné informace o indikátorech hrozeb importovaných do Azure Sentinelu a všech výstrah generovaných z analytických pravidel, která odpovídají vašim ukazatelům hrozeb.

- **Lovecké** dotazy umožňují bezpečnostním vyšetřovatelům používat indikátory hrozeb v kontextu běžných scénářů lovu.

- **Poznámkové bloky** mohou používat indikátory hrozeb, když zkoumáte anomálie a hledáte škodlivé chování.

Indikátory hrozeb můžete streamovat do Azure Sentinelu pomocí jednoho z produktů integrované platformy pro analýzu hrozeb (TIP), které jsou uvedeny v další části, připojení k serverům TAXII nebo pomocí přímé integrace s [rozhraním API microsoft graphsecurity tiindicators](https://aka.ms/graphsecuritytiindicators).

## <a name="integrated-threat-intelligence-platform-products"></a>Produkty integrované platformy pro analýzu hrozeb

- [MISP Open Source Threat Intelligence Platform](https://www.misp-project.org/)
    
    Ukázkový skript, který poskytuje klientům instance MISP pro migraci indikátorů hrozeb do rozhraní Microsoft Graph Security API, naleznete [v tématu MISP na Microsoft Graph Security Script](https://github.com/microsoftgraph/security-api-solutions/tree/master/Samples/MISP).

- [Palo Alto sítě MineMeld](https://www.paloaltonetworks.com/products/secure-the-network/subscriptions/minemeld)
    
    Pokyny s průvodcem naleznete [v tématu Odesílání iOC do rozhraní Microsoft Graph Security API pomocí MineMeld](https://live.paloaltonetworks.com/t5/MineMeld-Articles/Sending-IOCs-to-the-Microsoft-Graph-Security-API-using-MineMeld/ta-p/258540).

- [Platforma ThreatConnect](https://threatconnect.com/solution/)

    Další informace najdete v [tématu Integrace ThreatConnect](https://threatconnect.com/integrations/) a vyhledejte rozhraní Microsoft Graph Security API na stránce.


## <a name="connect-azure-sentinel-to-your-threat-intelligence-platform"></a>Připojení Azure Sentinelu k platformě pro analýzu hrozeb

## <a name="prerequisites"></a>Požadavky  

- Role Azure AD globálního správce nebo správce zabezpečení udělují oprávnění k produktu TIP nebo vlastní aplikaci, která používá přímou integraci s rozhraním API microsoft graph security tiindicators.

- Oprávnění ke čtení a zápisu do pracovního prostoru Azure Sentinel ukládají indikátory hrozeb.

## <a name="instructions"></a>Pokyny

1. [Zaregistrujte aplikaci](/graph/auth-v2-service#1-register-your-app) ve službě Azure Active Directory a získejte ID aplikace, tajný klíč aplikace a ID klienta Azure Active Directory. Tyto hodnoty potřebujete při konfiguraci integrovaného produktu nebo aplikace TIP, která používá přímou integraci s rozhraním Microsoft Graph Security tiIndicators API.

2. [Konfigurace oprávnění rozhraní API](/graph/auth-v2-service#2-configure-permissions-for-microsoft-graph) pro registrovanou aplikaci: Přidejte do registrované aplikace oprávnění aplikace Microsoft Graph **ThreatIndicators.ReadWrite.OwnedBy.**

3. Požádejte správce klienta služby Azure Active Directory, aby udělil souhlas správce registrované aplikace pro vaši organizaci. Z webu Azure Portal:**\<_Název_>** > aplikace **Azure Active Directory** > **App**Název > aplikace**Zobrazení oprávnění** > rozhraní API**Udělte souhlas správce pro \<název> _klienta_**.

4. Nakonfigurujte produkt nebo aplikaci TIP, která používá přímou integraci s rozhraním MICROSOFT Graph Security tiIndicators API k odesílání indikátorů do Azure Sentinelu zadáním následujícího:
    
    a. Hodnoty pro ID registrované aplikace, tajný klíč a ID klienta.
    
    b. Pro cílový produkt zadejte Azure Sentinel.
    
    c. Pro akci zadejte výstrahu.

5. Na webu Azure Portal přejděte na**datové konektory** **Azure Sentinel** > a pak vyberte konektor **Threat Intelligence Platforms (Preview).**

6. Vyberte **Otevřít stránku konektoru**a potom **připojit**.

7. Chcete-li zobrazit indikátory hrozeb importované do Azure Sentinelu, přejděte na **Azure Sentinel – protokoly** > **SecurityInsights**a rozbalte **ThreatIntelligenceIndicator**.

## <a name="connect-azure-sentinel-to-taxii-servers"></a>Připojení Azure Sentinelu k serverům TAXII

## <a name="prerequisites"></a>Požadavky  

- Oprávnění ke čtení a zápisu do pracovního prostoru Azure Sentinel ukládají indikátory hrozeb.

- TAXII 2.0 server URI a ID kolekce.

## <a name="instructions"></a>Pokyny

1. Na webu Azure Portal přejděte na**datové konektory** **Azure Sentinel** > a pak vyberte konektor **Threat Intelligence – TAXII (Preview).**

2. Vyberte **Otevřít stránku konektoru**.

3. Zadejte požadované a volitelné informace v textových polích.

4. Výběrem **možnosti Přidat** povolíte připojení k serveru TAXII 2.0.

5. Pokud máte další servery TAXII 2.0: Opakujte kroky 3 a 4.

6. Chcete-li zobrazit indikátory hrozeb importované do Azure Sentinelu, přejděte na **Azure Sentinel – protokoly** > **SecurityInsights**a rozbalte **ThreatIntelligenceIndicator**.

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste se dozvěděli, jak připojit poskytovatele analýzy hrozeb k Azure Sentinelu. Další informace o Azure Sentinelu najdete v následujících článcích.

- Přečtěte [si, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začínáme [s detekcí hrozeb pomocí Azure Sentinelu](tutorial-detect-threats.md).
