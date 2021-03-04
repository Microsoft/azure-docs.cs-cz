---
title: Opravit doporučení v Azure Security Center | Microsoft Docs
description: Tento článek vysvětluje, jak reagovat na doporučení v Azure Security Center chránit vaše prostředky a vyhovět zásadám zabezpečení.
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
ms.openlocfilehash: 6222491d35317e549a3d21f29395b9c25813ea9f
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102100351"
---
# <a name="remediate-recommendations-in-azure-security-center"></a>Zpracování doporučení ve službě Azure Security Center

Doporučení vám poskytnou návrhy na to, jak lépe zabezpečit prostředky. Doporučení můžete implementovat podle kroků pro nápravu, které jsou uvedené v doporučení.

## <a name="remediation-steps"></a>Postup odstranění problému <a name="remediation-steps"></a>

Po kontrole všech doporučení se rozhodněte, která z nich se má opravit jako první. Doporučujeme, abyste pro zvýšení vašeho zabezpečeného skóre určili prioritní kontrolu zabezpečení na nejvyšší potenciál.

1. V seznamu vyberte doporučení.

1. Postupujte podle pokynů v části **Postup odstranění problému** . Každé doporučení má svou vlastní sadu instrukcí. Následující snímek obrazovky ukazuje nápravný postup pro konfiguraci aplikací tak, aby povoloval jenom přenosy přes protokol HTTPS.

    :::image type="content" source="./media/security-center-remediate-recommendations/security-center-remediate-recommendation.png" alt-text="Postup ruční nápravy pro doporučení" lightbox="./media/security-center-remediate-recommendations/security-center-remediate-recommendation.png":::

1. Po dokončení se zobrazí oznámení o tom, jestli se problém vyřeší.

## <a name="quick-fix-remediation"></a>Rychlá oprava nápravy

K zjednodušení nápravy a vylepšení zabezpečení vašeho prostředí (a zvýšení zabezpečeného skóre) obsahuje mnoho doporučení možnost rychlé opravy.

Rychlá oprava vám pomůže rychle opravit doporučení pro víc prostředků.

> [!TIP]
> Rychlá oprava řešení je k dispozici pouze pro konkrétní doporučení. Pokud chcete najít doporučení s dostupnou rychlou opravou, použijte filtr **akcí odpovědí** pro seznam doporučení:
> 
> :::image type="content" source="media/security-center-remediate-recommendations/quick-fix-filter.png" alt-text="Pomocí filtrů nad seznamem doporučení najdete doporučení s možností Rychlá oprava.":::

Implementace řešení rychlé opravy:

1. Ze seznamu doporučení, která obsahují **rychlou opravu!** Označte doporučení.

    [![Vyberte Rychlá oprava!](media/security-center-remediate-recommendations/security-center-quick-fix-select.png)](media/security-center-remediate-recommendations/security-center-quick-fix-select.png#lightbox)

1. Na kartě **poškozené prostředky** vyberte prostředky, u kterých chcete doporučení implementovat, a vyberte **opravit**.

    > [!NOTE]
    > Některé z uvedených prostředků můžou být zakázané, protože nemáte příslušná oprávnění k jejich úpravám.

1. V potvrzovacím poli si přečtěte podrobnosti o nápravě a důsledky.

    ![Rychlá oprava](./media/security-center-remediate-recommendations/security-center-quick-fix-view.png)

    > [!NOTE]
    > Důsledky jsou uvedeny v poli šedé v okně **opravit prostředky** , které se otevře po kliknutí na **opravit**. Seznamte se s tím, k jakým změnám dochází při řešení potíží s rychlou opravou.

1. V případě potřeby vložte příslušné parametry a schvalte nápravu.

    > [!NOTE]
    > Tato operace může trvat několik minut, než se dokončí náprava, aby se na kartě **prostředky v pořádku** zobrazily prostředky. Chcete-li zobrazit nápravné akce, zkontrolujte [Protokol aktivit](#activity-log).

1. Po dokončení se zobrazí oznámení o tom, jestli se oprava úspěšně provedla.

## <a name="quick-fix-remediation-logging-in-the-activity-log"></a>Rychlá oprava protokolování nápravy v protokolu aktivit <a name="activity-log"></a>

Operace opravy používá volání rozhraní API pro nasazení šablony nebo opravu REST API k aplikování konfigurace na prostředku. Tyto operace se zaznamenávají do [protokolu aktivit Azure](../azure-resource-manager/management/view-activity-logs.md).


## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste si ukázali, jak opravit doporučení v Security Center. Další informace o Security Center najdete na následujících stránkách:

* [Nastavení zásad zabezpečení v Azure Security Center](tutorial-security-policy.md) – Přečtěte si, jak nakonfigurovat zásady zabezpečení pro vaše předplatná Azure a skupiny prostředků.
* [Co jsou zásady zabezpečení, iniciativy a doporučení?](security-policy-concept.md)