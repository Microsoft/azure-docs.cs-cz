---
title: Správa incidentů zabezpečení v Azure Security Center | Microsoft Docs
description: Tento dokument vám pomůže s použitím Azure Security Center ke správě incidentů zabezpečení.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/15/2020
ms.author: memildin
ms.openlocfilehash: 98fc339e473ffb2bf54e7119634e93046cca1ef3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "79415671"
---
# <a name="manage-security-incidents-in-azure-security-center"></a>Správa incidentů zabezpečení v Azure Security Center

Třídění a vyšetřování výstrah zabezpečení může být časově náročné i pro ty z těch nejpravděpodobnějších analytiků zabezpečení a u mnoha míst je těžké i na tom, kde začít. Služba Security Center pomocí [analýzy](security-center-detection-capabilities.md) spojuje informace mezi odlišnými [výstrahami zabezpečení](security-center-managing-and-responding-alerts.md) a dokáže vám tak poskytnout ucelený přehled o útočné kampani a všech souvisejících výstrahách. Vy tak rychle porozumíte, jaké akce útočník podniká a které prostředky byly zasaženy.

Toto téma vysvětluje informace o incidentech v Security Center a o tom, jak používat nápravné výstrahy.

## <a name="what-is-a-security-incident"></a>Co je bezpečnostní incident?

Ve službě Security Center představuje bezpečnostní incident souhrn všech výstrah pro určitý prostředek, které odpovídají schématům [modelu kill chain](alerts-reference.md#intentions). Incidenty se zobrazí v seznamu [výstrahy zabezpečení](security-center-managing-and-responding-alerts.md) . Kliknutím na incident zobrazíte související výstrahy, které vám umožní získat další informace o každém výskytu.

## <a name="managing-security-incidents"></a>Správa incidentů zabezpečení

1. Na řídicím panelu Security Center klikněte na dlaždici **výstrahy zabezpečení** . Zobrazí se incidenty a výstrahy. Popis incidentu zabezpečení má v porovnání s ostatními výstrahami jinou ikonu.

    ![Zobrazit incidenty zabezpečení](./media/security-center-managing-and-responding-alerts/security-center-manage-alerts.png)

1. Chcete-li zobrazit podrobnosti, klikněte na incident. V okně **zjištěné incidenty zabezpečení** se zobrazí další podrobnosti. Oddíl **Obecné informace** může nabídnout přehled o tom, co aktivovalo výstrahu zabezpečení. Zobrazuje informace, jako je cílový prostředek, zdrojová IP adresa (Pokud je k dispozici), pokud je výstraha stále aktivní, a doporučení k nápravě.  

    ![Reakce na incidenty zabezpečení v Azure Security Center](./media/security-center-managing-and-responding-alerts/security-center-alert-incident.png)

1. Chcete-li získat další informace o jednotlivých výstrahách, klikněte na výstrahu. Náprava navrhovaná službou Security Center se liší podle výstrahy zabezpečení.

   > [!NOTE]
   > Stejná výstraha může existovat jako součást incidentu a také bude viditelná jako samostatná výstraha.

    ![Podrobnosti upozornění](./media/security-center-incident/security-center-incident-alert.png)

1. Postupujte podle kroků nápravy, které jsou pro každou výstrahu vydány.


## <a name="see-also"></a>Viz také
V tomto dokumentu jste zjistili, jak ve službě Azure Security Center používat funkci incidentů zabezpečení. Související informace najdete v následujících tématech:

* [Ochrana před hrozbami v Azure Security Center](threat-protection.md)
* [Výstrahy zabezpečení ve službě Azure Security Center](security-center-alerts-overview.md)
* [Správa výstrah zabezpečení](security-center-managing-and-responding-alerts.md)