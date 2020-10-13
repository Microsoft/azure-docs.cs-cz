---
title: Správa incidentů zabezpečení v Azure Security Center | Microsoft Docs
description: Tento dokument vám pomůže s použitím Azure Security Center ke správě incidentů zabezpečení.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 06/15/2020
ms.author: memildin
ms.openlocfilehash: c31d3136ec4bcdf6da7680d41de078f8bd03d256
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91447097"
---
# <a name="manage-security-incidents-in-azure-security-center"></a>Správa incidentů zabezpečení v Azure Security Center

Třídění a vyšetřování výstrah zabezpečení může být časově náročné i pro ty z těch nejpravděpodobnějších analytiků zabezpečení. V mnoha případech je obtížné zjistit, kde začít. 

Security Center používá [Analytics](security-center-detection-capabilities.md) k propojení informací mezi různými [výstrahami zabezpečení](security-center-managing-and-responding-alerts.md). Pomocí těchto připojení Security Center může poskytnout jediné zobrazení kampaně útoku a související výstrahy, které vám pomohou pochopit akce útočníka a ovlivněné prostředky.

Tato stránka poskytuje přehled o incidentech v Security Center.

## <a name="what-is-a-security-incident"></a>Co je bezpečnostní incident?

Ve službě Security Center představuje bezpečnostní incident souhrn všech výstrah pro určitý prostředek, které odpovídají schématům [modelu kill chain](alerts-reference.md#intentions). Incidenty se zobrazí na stránce [výstrahy zabezpečení](security-center-managing-and-responding-alerts.md) . Pokud chcete zobrazit související výstrahy a získat další informace, vyberte incident.

## <a name="managing-security-incidents"></a>Správa incidentů zabezpečení

1. Na stránce Přehled Security Center vyberte dlaždici **výstrahy zabezpečení** . Zobrazí se incidenty a výstrahy. Všimněte si, že bezpečnostní incidenty mají jinou ikonu výstrah zabezpečení.

    ![Zobrazit incidenty zabezpečení](./media/security-center-managing-and-responding-alerts/security-center-manage-alerts.png)

1. Pokud chcete zobrazit podrobnosti, vyberte incident. Na stránce **incident zabezpečení** se zobrazí další podrobnosti. 

    [![Reakce na incidenty zabezpečení v Azure Security Center](media/security-center-incident/incident-details.png)](media/security-center-incident/incident-details.png#lightbox)

    V levém podokně stránky incident zabezpečení se zobrazují informace vysoké úrovně incidentu zabezpečení: název, závažnost, stav, čas aktivity, popis a ovlivněný prostředek. Vedle ovlivněného prostředku vidíte relevantní značky Azure. Tyto značky použijte k odvození organizačního kontextu prostředku při zkoumání výstrahy.

    Pravé podokno obsahuje kartu **výstrahy** s výstrahami zabezpečení, které byly korelační jako součást tohoto incidentu. 

    >[!TIP]
    > Chcete-li získat další informace o konkrétní výstraze, vyberte ji. 

    [![Karta akce při provedení incidentu](media/security-center-incident/incident-take-action-tab.png)](media/security-center-incident/incident-take-action-tab.png#lightbox)

    Chcete-li přepnout na kartu **provést akci** , vyberte kartu nebo tlačítko v dolní části pravého podokna. Tato karta slouží k provedení dalších akcí, jako například:
    - *Zmírnění hrozby* – poskytuje postup ruční nápravy pro tento bezpečnostní incident.
    - *Prevence budoucích útoků* – poskytuje doporučení zabezpečení, která vám pomůžou snížit plochu pro útoky, zvýšit stav zabezpečení a zabránit budoucím útokům.
    - *Aktivovat automatizovanou odezvu* – poskytuje možnost aktivovat aplikaci logiky jako reakci na tento bezpečnostní incident.
    - *Potlačit podobné výstrahy* – poskytuje možnost potlačit budoucí výstrahy s podobnými charakteristikami, pokud výstraha není relevantní pro vaši organizaci. 

   > [!NOTE]
   > Stejná výstraha může existovat jako součást incidentu a také bude viditelná jako samostatná výstraha.

1. Chcete-li napravit hrozby v incidentu, postupujte podle kroků pro nápravu, které jsou k dispozici u jednotlivých výstrah.


## <a name="next-steps"></a>Další kroky

Tato stránka vysvětluje možnosti incidentu zabezpečení Security Center. Související informace najdete na následujících stránkách:

- [Výstrahy zabezpečení v Security Center](security-center-alerts-overview.md)
- [Správa a reakce na výstrahy zabezpečení](security-center-managing-and-responding-alerts.md)