---
title: Vlastní podrobnosti o ploše v upozorněních Sentinel Azure | Microsoft Docs
description: Extrakce a úprava vlastních podrobností události v upozorněních v pravidlech analýzy Azure pro lepší a úplnější informace o incidentu
services: sentinel
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
ms.date: 02/10/2021
ms.author: yelevin
ms.openlocfilehash: 45f0ef5366d97c275c40d4d436020dbaf3501d42
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "102456208"
---
# <a name="surface-custom-event-details-in-alerts-in-azure-sentinel"></a>Podrobnosti o vlastní události na ploše v upozorněních ve službě Azure Sentinel 

> [!IMPORTANT]
>
> - Funkce Custom Details je ve **verzi Preview**. Další právní podmínky, které se vztahují na funkce Azure, které jsou ve verzi beta, Preview nebo jinak ještě nedostupné ve všeobecné dostupnosti, najdete v tématu dodatečné [podmínky použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Preview.

## <a name="introduction"></a>Úvod

[Naplánovaná pravidla analýzy dotazů](tutorial-detect-threats-custom.md) analyzují **události** ze zdrojů dat připojených ke službě Azure Sentinel a vydávají **Upozornění** , když obsah těchto událostí bude významný z hlediska zabezpečení. Tyto výstrahy se dále analyzují, seskupují a filtrují podle různých modulů Azure Sentinel a přecházejí na **incidenty** , které mají na vědomí SOC analytika. Když ale analytik zobrazení incidentu, okamžitě se zobrazí jenom vlastnosti výstrah komponent. Získání aktuálního obsahu – informace obsažené v událostech vyžadují několik prozkoumá.

Pomocí funkce **Custom Details** v **Průvodci analytickým pravidlem** můžete v výstrahách vytvořených z těchto událostí vytvořit Surface dat událostí a učinit tak data události ve vlastnostech výstrahy. V důsledku toho vám poskytuje okamžitý přehled o obsahu událostí ve vašich incidentech a umožňuje vám obvěřit, prozkoumat, vykreslit závěry a reagovat s mnohem vyšší rychlostí a efektivitou.

Níže uvedený postup je součástí Průvodce vytvořením pravidla Analytics. Zpracovává se tady nezávisle na řešení scénáře přidávání nebo změny vlastních podrobností v existujícím analytickém pravidle.

## <a name="how-to-surface-custom-event-details"></a>Podrobnosti o tom, jak surfovat vlastní události

1. V navigační nabídce Azure Sentinel vyberte **Analytics**.

1. Vyberte pravidlo naplánovaného dotazu a klikněte na **Upravit**. Nebo vytvořte nové pravidlo kliknutím na **vytvořit &#10132; pravidlo plánovaného dotazu** v horní části obrazovky.

1. Klikněte na kartu **nastavit logiku pravidla** .

1. V části **vylepšení výstrah** vyberte **vlastní podrobnosti**.

    :::image type="content" source="media/surface-custom-details-in-alerts/alert-enhancement.png" alt-text="Hledání a výběr vlastních podrobností":::

1. V části nyní rozbalené **vlastní podrobnosti** přidejte páry klíč-hodnota odpovídající podrobnostem, které chcete naplochit:

    1. Do pole **klíč** zadejte název vašeho výběru, který se zobrazí jako název pole v okně výstrahy.

    1. V poli **hodnota** vyberte v rozevíracím seznamu parametr události, který chcete v upozorněních umístit. Tento seznam se vyplní hodnotami odpovídajícími polím v tabulkách, které jsou předmětem dotazu pravidla.
    
        :::image type="content" source="media/surface-custom-details-in-alerts/custom-details.png" alt-text="Přidat vlastní podrobnosti":::

1. Kliknutím na **Přidat nový přidejte** další podrobnosti a zopakováním posledních kroků definujte páry klíč-hodnota. 

    Pokud si to rozmyslíte, nebo pokud jste udělali omyly, můžete kliknutím na ikonu odpadkového koše vedle rozevíracího seznamu **hodnota** pro tuto podrobnost odebrat vlastní podrobnosti.

1. Po dokončení definování vlastních podrobností klikněte na kartu **Kontrola a vytvoření** . Po úspěšném ověření pravidla klikněte na **Uložit**.

    > [!NOTE]
    > 
    >**Omezení služby**
    > - V rámci jednoho analytického pravidla můžete definovat **až 20 vlastních podrobností** .
    >
    > - Omezení velikosti pro všechny vlastní podrobnosti je souhrnně nastaveno na **2 KB**.

## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste zjistili, jak surfovat vlastní podrobnosti v upozorněních pomocí pravidel Azure Sentinel Analytics. Další informace o Sentinel Azure najdete v následujících článcích:
- Získejte úplný přehled o [plánovaných pravidlech dotaz Analytics](tutorial-detect-threats-custom.md).
- Přečtěte si další informace o [entitách v Sentinel Azure](entities-in-azure-sentinel.md).
