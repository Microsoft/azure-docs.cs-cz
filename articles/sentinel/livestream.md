---
title: Použití lovení Livestream u Azure Sentinelu k detekci hrozeb| Dokumenty společnosti Microsoft
description: Tento článek popisuje, jak používat lovení Livestream v Azure Sentinelu ke sledování dat.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77582122"
---
# <a name="use-hunting-livestream-in-azure-sentinel-to-detect-threats"></a>Použití živého streamování v Azure Sentinelu k detekci hrozeb

> [!IMPORTANT]
> Lovení živého přenosu v Azure Sentinelu je aktuálně ve verzi Public Preview a postupně se zavádí do tenantů.
> Tato funkce je k dispozici bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Pomocí živého vysílání hledání můžete vytvořit interaktivní relace, které vám umožní testovat nově vytvořené dotazy, když dojde k událostem, dostávat oznámení z relací při nalezení shody a v případě potřeby zahájit šetření. Můžete rychle vytvořit relaci živého přenosu pomocí libovolného dotazu Log Analytics.

- **Testování nově vytvořených dotazů při výskytu událostí**
    
    Můžete testovat a upravovat dotazy bez konfliktů aktuálních pravidel, která jsou aktivně aplikována na události. Po potvrzení těchto nových dotazů pracovat podle očekávání, je snadné je povýšit na vlastní pravidla výstrah výběrem možnost, která zvyšuje relace na výstrahu.

- **Získejte upozornění, když dojde k hrozbám**
    
    Zdroje dat o hrozbách můžete porovnat s agregovanými daty protokolu a být upozorněni, když dojde ke shodě. Informační kanály o hrozbách jsou průběžné datové proudy, které souvisejí s potenciálními nebo aktuálními hrozbami, takže oznámení může znamenat potenciální hrozbu pro vaši organizaci. Vytvořte relaci živého datového proudu namísto vlastního pravidla výstrahy, pokud chcete být upozorněni na potenciální problém bez režie udržování vlastního pravidla výstrahy.

- **Zahájit vyšetřování**
    
    Pokud existuje aktivní šetření, které zahrnuje prostředek, jako je například hostitele nebo uživatele, můžete zobrazit konkrétní (nebo jakékoli) aktivity v datech protokolu, jak k němu dochází na tomto prostředku. Můžete být upozorněni, když dojde k této aktivitě.


## <a name="create-a-livestream-session"></a>Vytvoření relace živého přenosu

Můžete vytvořit relaci živého datového proudu z existujícího dotazu pro lov nebo vytvořit relaci od začátku.

1. Na webu Azure najdete v části **Hledání** > **správu** > hrozeb Sentinel **.**

2. Chcete-li vytvořit relaci živého datového proudu z loveckého dotazu:
    
    1. Na kartě **Dotazy** vyhledejte vyhledávací dotaz, který chcete použít.
    2. Klikněte pravým tlačítkem myši na dotaz a vyberte **Přidat do živého datového proudu**. Například:
    
    > [!div class="mx-imgBorder"]
    > ![vytvoření relace Livestream z loveckého dotazu Azure Sentinelu](./media/livestream/livestream-from-query.png)

3. Vytvoření relace živého přenosu od začátku: 
    
    1. Výběr karty **Livestream**
    2. Vyberte **Přejít do živého přenosu**.
    
4. V podokně **Živého vysílání:**
    
    - Pokud jste spustili živý přenos z dotazu, zkontrolujte dotaz a proveďte změny, které chcete provést.
    - Pokud jste spustili živý přenos od začátku, vytvořte dotaz. 

5. Na panelu příkazů vyberte **Přehrát.**
    
    Stavový řádek pod panelem příkazů označuje, zda je relace živého datového proudu spuštěna nebo pozastavena. V následujícím příkladu je relace spuštěna:
    
    > [!div class="mx-imgBorder"]
    > ![vytvoření relace živého přenosu z lovu Azure Sentinelu](./media/livestream/livestream-session.png)

6. Z panelu příkazů vyberte **Uložit.**
    
    Pokud nevyberete **Pozastavit**, relace bude spuštěna, dokud se neodpojíte z webu Azure Portal.

## <a name="view-your-livestream-sessions"></a>Zobrazení relací živého přenosu

1. Na webu Azure portal přejděte na kartu **Správa** > **hrozeb** > Sentinel**Hunting** > **Livestream.**

2. Vyberte relaci živého datového proudu, kterou chcete zobrazit nebo upravit. Například:
    
    > [!div class="mx-imgBorder"]
    > ![vytvoření relace živého datového proudu z loveckého dotazu Azure Sentinelu](./media/livestream/livestream-tab.png)
    
    Vybraná relace živého přenosu se otevře, abyste mohli přehrávat, pozastavovat, upravovat a tak dále.

## <a name="receive-notifications-when-new-events-occur"></a>Příjem oznámení v případě výskytu nových událostí

Vzhledem k tomu, že oznámení livestream u nových událostí používají oznámení na portálu Azure, zobrazí se tato oznámení při každém použití portálu Azure. Například:

![Oznámení o portálu Azure pro livestream](./media/livestream/notification.png)

Výběrem oznámení otevřete podokno **Livestream.**
 
## <a name="elevate-a-livestream-session-to-an-alert"></a>Zvýšení stupně živého přenosu na výstrahu

Relaci živého přenosu můžete povýšit na novou výstrahu tak, že vyberete **možnost Zvýšit výstrahu** z panelu příkazů v příslušné relaci živého přenosu:

> [!div class="mx-imgBorder"]
> ![Zvýšení relace živého přenosu na výstrahu](./media/livestream/elevate-to-alert.png)

Tato akce otevře průvodce vytvořením pravidla, který je předem vyplněn dotazem, který je přidružen k relaci živého datového proudu.

## <a name="next-steps"></a>Další kroky

V tomto článku jste se naučili, jak používat lovení živého přenosu v Azure Sentinelu. Další informace o Azure Sentinelu najdete v následujících článcích:

- [Proaktivně lovit hrozby](hunting.md)
- [Používání automatických loveckých kampaní pomocí poznámkových bloků](notebooks.md)
