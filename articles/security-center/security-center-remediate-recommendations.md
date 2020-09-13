---
title: Opravit doporučení v Azure Security Center | Microsoft Docs
description: Tento článek vysvětluje, jak opravit doporučení v Azure Security Center k ochraně vašich prostředků a dodržování zásad zabezpečení.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 8be947cc-cc86-421d-87a6-b1e23077fd50
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/08/2020
ms.author: memildin
ms.openlocfilehash: 4bad3227e08c0fbe0d280967e45bbef9d477e1b3
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/09/2020
ms.locfileid: "89569131"
---
# <a name="remediate-recommendations-in-azure-security-center"></a>Zpracování doporučení ve službě Azure Security Center

Doporučení vám poskytnou návrhy na to, jak lépe zabezpečit prostředky. Doporučení můžete implementovat podle kroků pro nápravu, které jsou uvedené v doporučení.

## <a name="remediation-steps"></a>Postup odstranění problému <a name="remediation-steps"></a>

Po kontrole všech doporučení se rozhodněte, která z nich se má opravit jako první. Doporučujeme, abyste používali [dopad bezpečného skóre](security-center-recommendations.md#monitor-recommendations) , abyste lépe určili prioritu toho, co udělat jako první.

1. V seznamu klikněte na doporučení.

1. Postupujte podle pokynů v části **Postup odstranění problému** . Každé doporučení má svou vlastní sadu instrukcí. Následující snímek obrazovky ukazuje nápravný postup pro konfiguraci aplikací tak, aby povoloval jenom přenosy přes protokol HTTPS.

    ![Podrobnosti doporučení](./media/security-center-remediate-recommendations/security-center-remediate-recommendation.png)

1. Po dokončení se zobrazí oznámení o tom, jestli se oprava úspěšně provedla.

## <a name="quick-fix-remediation"></a>Rychlá oprava nápravy<a name="one-click"></a>

Rychlá oprava vám umožní rychle opravit doporučení pro víc prostředků. Je dostupná jenom pro konkrétní doporučení. Rychlá oprava zjednodušuje nápravu a umožňuje rychle zvýšit zabezpečení vašeho prostředí.

Postup implementace rychlé nápravy opravy:

1. Ze seznamu doporučení, která obsahují **rychlou opravu!** popisek, klikněte na doporučení.

    [![Vyberte Rychlá oprava!](media/security-center-remediate-recommendations/security-center-one-click-fix-select.png)](media/security-center-remediate-recommendations/security-center-one-click-fix-select.png#lightbox)

1. Na kartě **poškozené prostředky** vyberte prostředky, u kterých chcete doporučení implementovat, a klikněte na **opravit**.

    > [!NOTE]
    > Některé z uvedených prostředků můžou být zakázané, protože nemáte příslušná oprávnění k jejich úpravám.

1. V potvrzovacím poli si přečtěte podrobnosti o nápravě a důsledky.

    ![Rychlá oprava](./media/security-center-remediate-recommendations/security-center-one-click-fix-view.png)

    > [!NOTE]
    > Důsledky jsou uvedeny v poli šedé v okně **opravit prostředky** , které se otevře po kliknutí na **opravit**. Seznamte se s tím, k jakým změnám dochází při řešení potíží s rychlou opravou.

1. V případě potřeby vložte příslušné parametry a schvalte nápravu.

    > [!NOTE]
    > Tato operace může trvat několik minut, než se dokončí náprava, aby se na kartě **prostředky v pořádku** zobrazily prostředky. Chcete-li zobrazit nápravné akce, zkontrolujte [Protokol aktivit](#activity-log).

1. Po dokončení se zobrazí oznámení o tom, jestli se oprava úspěšně provedla.

## <a name="quick-fix-remediation-logging-in-the-activity-log"></a>Rychlá oprava protokolování nápravy v protokolu aktivit <a name="activity-log"></a>

Operace opravy používá volání rozhraní API pro nasazení šablony nebo opravu REST API k aplikování konfigurace na prostředku. Tyto operace se zaznamenávají do [protokolu aktivit Azure](../azure-resource-manager/management/view-activity-logs.md).


## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste si ukázali, jak opravit doporučení v Security Center. Další informace o Security Center najdete v následujících tématech:

* [Nastavení zásad zabezpečení v Azure Security Center](tutorial-security-policy.md) – Přečtěte si, jak nakonfigurovat zásady zabezpečení pro vaše předplatná Azure a skupiny prostředků.
* [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – Zjistěte, jak monitorovat stav svých prostředků Azure.
