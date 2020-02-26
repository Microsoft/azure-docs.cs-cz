---
title: Správa výstrah zabezpečení ve službě Azure Security Center | Dokumentace Microsoftu
description: Tento dokument vám pomůže používat funkce služby Azure Security Center ke správě výstrah zabezpečení a reagování na ně.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: b88a8df7-6979-479b-8039-04da1b8737a7
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/27/2019
ms.author: memildin
ms.openlocfilehash: 203e3718cedca693dc029a4ba20a86ca50ab3e32
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/25/2020
ms.locfileid: "77603858"
---
# <a name="manage-and-respond-to-security-alerts-in-azure-security-center"></a>Správa a reakce na výstrahy zabezpečení v Azure Security Center

V tomto tématu se dozvíte, jak zobrazit a zpracovat výstrahy, které jste obdrželi za účelem ochrany vašich prostředků. 

* Další informace o různých typech výstrah najdete v tématu [typy výstrah zabezpečení](security-center-alerts-overview.md#security-alert-types).
* Přehled způsobu, jakým Security Center generuje výstrahy, najdete v tématu [jak Azure Security Center detekuje hrozby a reaguje na](security-center-alerts-overview.md#detect-threats)ně.

> [!NOTE]
> Pokud chcete povolit rozšířené detekce, upgradujte na Azure Security Center Standard. K dispozici je bezplatná zkušební verze. Pokud chcete provést upgrade, vyberte v [zásadách zabezpečení](tutorial-security-policy.md) cenovou úroveň. Další informace najdete v článku o [cenách Azure Security Center](security-center-pricing.md).

## <a name="what-are-security-alerts"></a>Co jsou výstrahy zabezpečení?
Security Center automaticky shromažďuje, analyzuje a integruje data protokolu z vašich prostředků Azure, sítě a připojených partnerských řešení, jako jsou brány firewall a řešení ochrany koncových bodů, aby se zjistily skutečné hrozby a snížil počet falešných poplachů. Seznam upřednostňovaných výstrah zabezpečení se zobrazí ve službě Security Center spolu s informacemi, které potřebujete k rychlému prozkoumání problému, a doporučeními týkajícími se řešení útoku.

> [!NOTE]
> Další informace o tom, jak funkce zjišťování Security Center fungují, najdete v tématu [jak Azure Security Center detekuje hrozby a reaguje na](security-center-alerts-overview.md#detect-threats)ně.

## <a name="manage-your-security-alerts"></a>Správa výstrah zabezpečení

1. Na řídicím panelu Security Center se na dlaždici **Ochrana před hrozbami** zobrazí a zobrazí přehled výstrah.

    ![Dlaždice Výstrahy zabezpečení ve službě Security Center](./media/security-center-managing-and-responding-alerts/security-center-dashboard-alert.png)

1. Pokud chcete zobrazit další podrobnosti o výstrahách, klikněte na dlaždici.

   ![Výstrahy zabezpečení ve službě Security Center](./media/security-center-managing-and-responding-alerts/security-center-manage-alerts.png)

1. Chcete-li filtrovat zobrazené výstrahy, klikněte na tlačítko **Filtr**a v okně **filtru** , které se otevře, vyberte možnosti filtru, které chcete použít. Seznam se aktualizuje podle vybraného filtru. Filtrování může být velmi užitečné. Například můžete chtít zabývat se výstrahami zabezpečení, k nimž došlo v posledních 24 hodinách, protože zjišťujete případný průnik do systému.

    ![Filtrování výstrah ve službě Security Center](./media/security-center-managing-and-responding-alerts/security-center-filter-alerts.png)

## <a name="respond-to-security-alerts"></a>Reakce na výstrahy zabezpečení

1. V seznamu **výstrahy zabezpečení** klikněte na výstrahu zabezpečení. Zobrazí se potřebné prostředky a kroky, které je třeba provést k nápravě útoku.

    ![Reakce na výstrahy zabezpečení](./media/security-center-managing-and-responding-alerts/security-center-alert.png)

1. Po kontrole informací klikněte na prostředek, který byl napadený.

    ![Návrhy, co dělat s výstrahami zabezpečení](./media/security-center-managing-and-responding-alerts/security-center-alert-remediate.png)

    Oddíl **Obecné informace** může nabídnout přehled o tom, co aktivovalo výstrahu zabezpečení. Zobrazuje informace, jako je cílový prostředek, zdrojová IP adresa (Pokud je k dispozici), pokud je výstraha stále aktivní, a doporučení k nápravě.  

    > [!NOTE]
    >V některých případech není zdrojová IP adresa k dispozici, některé protokoly událostí zabezpečení systému Windows neobsahují IP adresu.

1. Postup oprav, který navrhuje Security Center, se liší podle výstrahy zabezpečení. Sledujte je u každé výstrahy. V některých případech může být nutné použít jiné ovládací prvky nebo služby Azure k implementaci Doporučené nápravy. 

    Následující témata vás provedou různými výstrahami v závislosti na typech prostředků:
    
    * [Výstrahy IaaS virtuální počítače a servery](security-center-alerts-iaas.md)
    * [Nativní výpočetní výstrahy](security-center-alerts-compute.md)
    * [Výstrahy datových služeb](security-center-alerts-data-services.md)
    
    Následující témata vysvětlují, jak Security Center používá jinou telemetrii, kterou shromažďuje z integrace s infrastrukturou Azure, aby bylo možné použít další vrstvy ochrany pro prostředky nasazené v Azure:
    
    * [Výstrahy vrstvy služeb](security-center-alerts-service-layer.md)
    * [Detekce hrozeb pro Azure WAF a Azure DDoS Protection](security-center-alerts-integration.md)
    
## <a name="see-also"></a>Viz také:

V tomto dokumentu jste zjistili, jak ve službě Security Center konfigurovat zásady zabezpečení. Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Výstrahy zabezpečení v Azure Security Center](security-center-alerts-overview.md).
* [Zpracování incidentů zabezpečení](security-center-incident.md)
* [Průvodce plánováním a provozem služby Azure Security Center](security-center-planning-and-operations-guide.md)