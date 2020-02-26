---
title: Správa incidentů zabezpečení v Azure Security Center | Microsoft Docs
description: Tento dokument vám pomůže s použitím Azure Security Center ke správě incidentů zabezpečení.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: memildin
ms.openlocfilehash: a600b17ce25a467b788671e12e3c2425ad20b809
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/25/2020
ms.locfileid: "77604080"
---
# <a name="manage-security-incidents-in-azure-security-center"></a>Správa incidentů zabezpečení v Azure Security Center

Třídění a vyšetřování výstrah zabezpečení může být časově náročné i pro ty z těch nejpravděpodobnějších analytiků zabezpečení a u mnoha míst je těžké i na tom, kde začít. Služba Security Center pomocí [analýzy](security-center-detection-capabilities.md) spojuje informace mezi odlišnými [výstrahami zabezpečení](security-center-managing-and-responding-alerts.md) a dokáže vám tak poskytnout ucelený přehled o útočné kampani a všech souvisejících výstrahách. Vy tak rychle porozumíte, jaké akce útočník podniká a které prostředky byly zasaženy.

Toto téma vysvětluje informace o incidentech v Security Center a o tom, jak používat nápravné výstrahy.

## <a name="what-is-a-security-incident"></a>Co je bezpečnostní incident?

Ve službě Security Center představuje bezpečnostní incident souhrn všech výstrah pro určitý prostředek, které odpovídají schématům [modelu kill chain](https://blogs.technet.microsoft.com/office365security/addressing-your-cxos-top-five-cloud-security-concerns/). Incidenty se zobrazí v seznamu [výstrahy zabezpečení](security-center-managing-and-responding-alerts.md) . Kliknutím na incident zobrazíte související výstrahy, které vám umožní získat další informace o každém výskytu.

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

Další informace o výstrahách, [správě a reakci na výstrahy zabezpečení](security-center-managing-and-responding-alerts.md).

Následující témata vás provedou různými výstrahami v závislosti na typech prostředků:

* [Výstrahy IaaS virtuální počítače a servery](security-center-alerts-iaas.md)
* [Nativní výpočetní výstrahy](security-center-alerts-compute.md)
* [Výstrahy datových služeb](security-center-alerts-data-services.md)

Následující témata vysvětlují, jak Security Center používá jinou telemetrii, kterou shromažďuje z integrace s infrastrukturou Azure, aby bylo možné použít další vrstvy ochrany pro prostředky nasazené v Azure:

* [Výstrahy vrstvy služeb](security-center-alerts-service-layer.md)
* [Detekce hrozeb pro Azure WAF a Azure DDoS Protection](security-center-alerts-integration.md)

## <a name="see-also"></a>Viz také
V tomto dokumentu jste zjistili, jak ve službě Azure Security Center používat funkci incidentů zabezpečení. Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Výstrahy zabezpečení v Azure Security Center](security-center-alerts-overview.md).
* [Správa výstrah zabezpečení](security-center-managing-and-responding-alerts.md)
* [Průvodce plánováním a provozem služby Azure Security Center](security-center-planning-and-operations-guide.md)
* [Blog o zabezpečení Azure](https://blogs.msdn.com/b/azuresecurity/) – Přečtěte si příspěvky o zabezpečení Azure a dodržování předpisů.
