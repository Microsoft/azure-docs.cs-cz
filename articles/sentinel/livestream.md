---
title: K detekci hrozeb použijte lovecké živě ve službě Azure Sentinel | Microsoft Docs
description: Tento článek popisuje, jak používat lovecké živě ve službě Azure Sentinel k udržení přehledu o datech.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/06/2019
ms.author: yelevin
ms.openlocfilehash: b392644e504fa8187e637278bef8718c9c2caa3f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "77582122"
---
# <a name="use-hunting-livestream-in-azure-sentinel-to-detect-threats"></a>K detekci hrozeb použijte lovecké živě ve službě Azure Sentinel.

> [!IMPORTANT]
> Lovecké živě ve službě Azure Sentinel jsou momentálně ve verzi Public Preview a postupně se vystavují klientům.
> Tato funkce se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


K vytváření interaktivních relací umožňujících testování nově vytvořených dotazů, když dojde k událostem, dostávat oznámení z relací, když se najde shoda, a v případě potřeby můžete spustit šetření. Relaci živě můžete rychle vytvořit pomocí libovolného dotazu Log Analytics.

- **Testování nově vytvořených dotazů při výskytu událostí**
    
    Dotazy můžete testovat a upravovat bez konfliktů k aktuálním pravidlům, která se aktivně používají pro události. Po potvrzení, že tyto nové dotazy fungují podle očekávání, je můžete snadno zvýšit na vlastní pravidla výstrah výběrem možnosti, která zvyšuje relaci na výstrahu.

- **Oznámení, když dojde k hrozbám**
    
    Můžete porovnat datové kanály hrozeb s agregovanými daty protokolů a při výskytu shody informovat. Datové kanály hrozeb jsou průběžné streamování dat, která se vztahují k potenciálním nebo současným hrozbám, takže oznámení můžou znamenat potenciální hrozbu pro vaši organizaci. Vytvořte relaci živě místo vlastního pravidla výstrahy, pokud chcete být upozorněni na potenciální problém bez nutnosti zachovat vlastní pravidlo výstrahy.

- **Spustit šetření**
    
    Pokud existuje aktivní šetření, které zahrnuje určitý prostředek, jako je například hostitel nebo uživatel, můžete v datech protokolu zobrazit konkrétní (nebo všechny) aktivity, ke kterým dojde na daném prostředku. Při výskytu této aktivity můžete být upozorněni.


## <a name="create-a-livestream-session"></a>Vytvoření relace živě

Můžete vytvořit relaci živě z existujícího loveckého dotazu nebo vytvořit svou relaci úplně od začátku.

1. V Azure Portal přejděte do části **Sentinel** > **Threat Management** > **lov**.

2. Vytvoření relace živě z loveckého dotazu:
    
    1. Na kartě **dotazy** vyhledejte lovecký dotaz, který chcete použít.
    2. Klikněte pravým tlačítkem na dotaz a vyberte **Přidat do živě**. Příklad:
    
    > [!div class="mx-imgBorder"]
    > ![vytvořit relaci živě z loveckého dotazu Azure Sentinel](./media/livestream/livestream-from-query.png)

3. Vytvoření relace živě od začátku: 
    
    1. Vyberte kartu **živě**
    2. Vyberte **Přejít na živě**.
    
4. V podokně **živě** :
    
    - Pokud jste spustili živě z dotazu, zkontrolujte dotaz a proveďte jakékoli změny, které chcete provést.
    - Pokud jste začali živě od začátku, vytvořte dotaz. 

5. Na panelu příkazů vyberte **Přehrát** .
    
    Stavový řádek pod panelem příkazů indikuje, jestli je relace živě spuštěná nebo pozastavená. V následujícím příkladu je spuštěná relace:
    
    > [!div class="mx-imgBorder"]
    > ![Vytvoření relace živě z loveckí Sentinel Azure](./media/livestream/livestream-session.png)

6. Na panelu příkazů vyberte **Uložit** .
    
    Pokud nevyberete **pozastavit**, relace zůstane spuštěná, dokud nebudete odhlášeni od Azure Portal.

## <a name="view-your-livestream-sessions"></a>Zobrazení živěch relací

1. V Azure Portal přejděte na kartu **Sentinel** > **Threat Management** > **lovecké** > **živě** .

2. Vyberte relaci živě, kterou chcete zobrazit nebo upravit. Příklad:
    
    > [!div class="mx-imgBorder"]
    > ![vytvořit relaci živě z loveckého dotazu Azure Sentinel](./media/livestream/livestream-tab.png)
    
    Vybraná relace živě se otevře, abyste ji mohli přehrát, pozastavit, upravit atd.

## <a name="receive-notifications-when-new-events-occur"></a>Dostávat oznámení, když dojde k novým událostem

Vzhledem k tomu, že živě oznámení o nových událostech používají Azure Portal oznámení, uvidíte tato oznámení vždy, když použijete Azure Portal. Příklad:

![Oznámení Azure Portal pro živě](./media/livestream/notification.png)

Vyberte oznámení a otevřete podokno **živě** .
 
## <a name="elevate-a-livestream-session-to-an-alert"></a>Zvýšení relace živě na výstrahu

Relaci živě můžete povýšit na nové upozornění výběrem možnosti **zvýšit úroveň na výstrahu** na panelu příkazů v příslušné relaci živě:

> [!div class="mx-imgBorder"]
> ![Zvýšení relace živě na výstrahu](./media/livestream/elevate-to-alert.png)

Tato akce otevře Průvodce vytvořením pravidla, který je předem vyplněný dotazem, který je přidružen k relaci živě.

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak používat lovecké živě v Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:

- [Proaktivní vylovení hrozeb](hunting.md)
- [Použití poznámkových bloků ke spouštění automatizovaných loveckých kampaní](notebooks.md)
