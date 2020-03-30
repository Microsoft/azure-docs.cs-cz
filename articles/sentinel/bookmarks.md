---
title: Použití loveckých záložek pro účely prozkoumání dat v Azure Sentinelu
description: Tento článek popisuje, jak používat záložky Azure Sentinel lov ke sledování dat.
author: yelevin
ms.author: yelevin
manager: rkarlin
ms.assetid: 320ccdad-8767-41f3-b083-0bc48f1eeb37
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.custom: mvc
ms.date: 10/24/2019
ms.openlocfilehash: eacc104bf7157732f91507f631bcda2cd166bfb3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588684"
---
# <a name="keep-track-of-data-during-hunting-with-azure-sentinel"></a>Sledování dat během lovu pomocí Azure Sentinelu

Hledání hrozeb obvykle vyžaduje kontrolu hory dat protokolu hledá důkazy o škodlivé chování. Během tohoto procesu vyšetřovatelé nacházejí události, které si chtějí pamatovat, znovu a analyzovat jako součást ověřování potenciálních hypotéz a pochopení celého příběhu kompromisu.

Lovení záložek v Azure Sentinelu vám to pomůže tím, že zachová dotazy, které jste spustili v **Azure Sentinel – protokoly**, spolu s výsledky dotazu, které považujete za relevantní. Můžete také zaznamenat kontextové pozorování a odkazovat na vaše zjištění přidáním poznámek a značek. Data se záložkami jsou viditelná pro vás a vaše spoluhráče pro snadnou spolupráci.

Data se záložkami můžete kdykoli znovu zobrazit na kartě **Záložky** v podokně **Lov.** Pomocí možností filtrování a vyhledávání můžete rychle najít konkrétní data pro aktuální šetření. Případně můžete zobrazit data se záložkami přímo v tabulce **HuntingBookmark** v pracovním prostoru Log Analytics. Například:

> [!div class="mx-imgBorder"]
> ![zobrazit tabulku HuntingBookmark](./media/bookmarks/bookmark-table.png)

Zobrazení záložek z tabulky umožňuje filtrovat, sumarizovat a spojit data se záložkami s jinými zdroji dat, což usnadňuje vyhledávání potvrzujících důkazů.

V současné době ve verzi Preview, pokud zjistíte něco, co naléhavě je třeba řešit při lovu v protokolech, v několika kliknutí, můžete vytvořit záložku a povýšit ji na incident, nebo přidat záložku k existujícímu incidentu. Další informace o incidentech najdete [v tématu Kurz: Vyšetřování incidentů s Azure Sentinel](tutorial-investigate-cases.md). 

Také ve verzi preview můžete vizualizovat data se záložkami kliknutím na **Prozkoumat** z podrobností záložky. Tím se spustí prostředí šetření, ve kterém můžete zobrazit, prozkoumat a vizuálně komunikovat vaše zjištění pomocí interaktivní entity graf diagramu a časové osy.

## <a name="add-a-bookmark"></a>Přidání záložky

1. Na webu Azure Portal přejděte na **službu Sledování** > **hrozeb** > **Sentinel,** kde můžete spouštět dotazy na podezřelé a neobvyklé chování.

2. Vyberte jeden z vyhledávacích dotazů a vpravo v podrobnostech vyhledávacího dotazu vyberte **spustit dotaz**. 

3. Vyberte **Zobrazit výsledky dotazu**. Například:
    
    > [!div class="mx-imgBorder"]
    > ![zobrazit výsledky dotazu z azure sentinelu lovu](./media/bookmarks/new-processes-observed-example.png)
    
    Tato akce otevře výsledky dotazu v podokně **Protokoly.**

4. V seznamu výsledků dotazu protokolu vyberte pomocí zaškrtávacích políček jeden nebo více řádků obsahujících informace, které vás zaujaly.

5. Vyberte **Přidat záložku**:
    
    > [!div class="mx-imgBorder"]
    > ![Přidání lovecké záložky do dotazu](./media/bookmarks/add-hunting-bookmark.png)

6. Vpravo v podokně **Přidat záložku** můžete volitelně aktualizovat název záložky, přidávat značky a poznámky, abyste zjistili, co bylo na položce zajímavé.

7. V části **Informace o dotazu** použijte rozevírací seznamy k extrahování informací z výsledků dotazu pro typy entit **Účet**, **Hostitel**a **IP adresa.** Tato akce mapuje vybraný typ entity na určitý sloupec z výsledku dotazu. Například:
    
    > [!div class="mx-imgBorder"]
    > ![Typy entit mapy pro loveckou záložku](./media/bookmarks/map-entity-types-bookmark.png)
    
    Chcete-li zobrazit záložku v grafu šetření (aktuálně ve verzi Preview), musíte namapovat alespoň jeden typ entity, který je buď **účet**, **hostitel**nebo **IP adresa**. 

5. Kliknutím na **Uložit** potvrďte změny a přidejte záložku. Všechna data se záložkami jsou sdílena s ostatními vyšetřovateli a jsou prvním krokem ke společnému vyšetřování.

 
> [!NOTE]
> Výsledky dotazu protokolu podporují záložky při každém otevření tohoto podokna z Azure Sentinelu. Například vyberete **obecné** > **protokoly** z navigačního panelu, vyberete odkazy na události v grafu vyšetřování nebo vyberete ID výstrahy z úplných podrobností incidentu (aktuálně ve verzi Preview). Při otevření podokna Protokoly z jiných umístění, například přímo z Azure Monitoru, nelze vytvořit **záložky.**

## <a name="view-and-update-bookmarks"></a>Zobrazení a aktualizace záložek 

1. Na webu Azure najdete v části **Hledání** > **správu** > hrozeb Sentinel **.** 

2. Chcete-li zobrazit seznam záložek, vyberte kartu **Záložky.**

3. Chcete-li najít konkrétní záložku, použijte vyhledávací pole nebo možnosti filtru.

4. Vyberte jednotlivé záložky a zobrazte podrobnosti záložky v pravém podokně podrobností.

5. Proveďte změny podle potřeby, které se automaticky uloží.

## <a name="exploring-bookmarks-in-the-investigation-graph"></a>Zkoumání záložek v grafu šetření

> [!IMPORTANT]
> Zkoumání záložek v grafu šetření a samotném grafu šetření jsou aktuálně ve verzi Public Preview.
> Tyto funkce jsou k dispozici bez smlouvy o úrovni služeb a nejsou doporučeny pro produkční úlohy.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

1. Na webu Azure portal přejděte > na kartu >  > **Hunting****Záložky** **pro správu hrozeb** **Sentinel**a vyberte záložku nebo záložky, které chcete prozkoumat.

2. V podrobnostech záložky zajistěte, aby byla mapována alespoň jedna entita. Například pro **entity**se zobrazí položky pro **IP**, **Stroj**nebo **Účet**.

3. Chcete-li zobrazit záložku v grafu šetření, klepněte na tlačítko **Prozkoumat.**

Pokyny k použití grafu šetření naleznete v [tématu Použití grafu šetření k podrobnému prozkoumání](tutorial-investigate-cases.md#use-the-investigation-graph-to-deep-dive).

## <a name="add-bookmarks-to-a-new-or-existing-incident"></a>Přidání záložek k novému nebo existujícímu incidentu

> [!IMPORTANT]
> Přidání záložek k novému nebo existujícímu incidentu je aktuálně ve verzi Public Preview.
> Tato funkce je k dispozici bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

1. Na webu Azure portal přejděte > na kartu >  > **Hunting****Záložky** **pro správu hrozeb** **Sentinel**a vyberte záložku nebo záložky, které chcete přidat k incidentu.

2. Na panelu příkazů vyberte **akce incidentu (náhled):**
    
    > [!div class="mx-imgBorder"]
    > ![Přidání záložek k incidentu](./media/bookmarks/incident-actions.png)

3. Podle potřeby vyberte **možnost Vytvořit nový incident** nebo Přidat k **existujícímu incidentu**. Potom:
    
    - Pro nový incident: Volitelně aktualizujte podrobnosti incidentu a pak vyberte **Vytvořit**.
    - Přidání záložky k existujícímu incidentu: Vyberte jeden incident a pak vyberte **Přidat**. 

Chcete-li zobrazit záložku v **Sentinel** > rámci incidentu: Přejděte na**Incidenty** **správy** > hrozeb Sentinel a vyberte incident pomocí záložky. Vyberte **Zobrazit úplné podrobnosti**a pak vyberte kartu **Záložky.**

> [!TIP]
> Jako alternativu k možnosti **Akce incidentu (Náhled)** na panelu příkazů můžete použít kontextovou nabídku (**...**) pro jednu nebo více záložek a vybrat možnosti pro **vytvoření nového incidentu**, Přidat k **existujícímu incidentu**a **Odebrat z incidentu**. 

## <a name="view-bookmarked-data-in-logs"></a>Zobrazení dat se záložkami v protokolech

Chcete-li zobrazit dotazy, výsledky nebo jejich historii se záložkami, vyberte záložku na kartě**Záložky** **lovu** > a použijte odkazy uvedené v podokně podrobností: 

- **Zobrazení zdrojového dotazu** pro zobrazení zdrojového dotazu v podokně **Protokoly**

- **Zobrazením protokolů záložek** zobrazíte všechna metadata záložek, která zahrnují, kdo provedl aktualizaci, aktualizované hodnoty a čas aktualizace.

Data nezpracovaná záložky pro všechny záložky můžete také zobrazit výběrem **položek Protokoly záložek** z panelu příkazů na kartě**Záložky** **lovu:** > 

> [!div class="mx-imgBorder"]
> ![Záložky protokoly](./media/bookmarks/bookmark-logs.png)

Toto zobrazení zobrazuje všechny záložky s přidruženými metadaty. Pomocí [dotazů pro dotazy na klíčové slovo](https://docs.microsoft.com/sharepoint/dev/general-development/keyword-query-language-kql-syntax-reference) (KQL) můžete filtrovat na nejnovější verzi konkrétní záložky, kterou hledáte.

> [!NOTE]
> Může dojít k významnému zpoždění (měřeno v minutách) mezi časem vytvoření záložky a zobrazením na kartě **Záložky.**

## <a name="delete-a-bookmark"></a>Odstranění záložky
 
1.  Na webu Azure portal přejděte > na kartu >  > **Hunting****Záložky** **pro správu hrozeb** **Sentinel**a vyberte záložku nebo záložky, které chcete odstranit. 

2. Klikněte pravým tlačítkem myši na výběry a vyberte možnost odstranění záložky nebo záložek. Například **Odstranit záložku,** pokud jste vybrali pouze jednu záložku, a **Odstranit 2 záložky,** pokud jste vybrali dvě záložky.
    
Odstraněním záložky odstraníte záložku ze seznamu na kartě **Záložka.** Tabulka **HuntingBookmark** pro pracovní prostor Log Analytics bude nadále obsahovat předchozí položky záložek, ale poslední položka změní hodnotu **SoftDelete** na hodnotu true, což usnadňuje filtrování starých záložek. Odstraněním záložky neodeberete žádné entity ze zkušenosti s šetřením, které jsou přidruženy k jiným záložkám nebo výstrahám. 


## <a name="next-steps"></a>Další kroky

V tomto článku jste se naučili, jak spustit hledání šetření pomocí záložek v Azure Sentinelu. Další informace o Azure Sentinelu najdete v následujících článcích:


- [Proaktivně lovit hrozby](hunting.md)
- [Používání automatických loveckých kampaní pomocí poznámkových bloků](notebooks.md)
