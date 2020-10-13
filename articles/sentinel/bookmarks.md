---
title: Používejte lovecké záložky pro vyšetřování dat v Azure Sentinel
description: Tento článek popisuje, jak použít záložky pro lovecké funkce Azure Sentinel k udržení přehledu o datech.
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "77588684"
---
# <a name="keep-track-of-data-during-hunting-with-azure-sentinel"></a>Udržujte si přehled o datech během přilovu k Azure Sentinel

Lovecké hrozby obvykle vyžadují kontrolu Mountains dat protokolu a hledání důkazů o škodlivém chování. Během tohoto procesu prověřování vyhledá události, které chtějí pamatovat, znovu navštívit a analyzovat, jako součást ověřování potenciální hypotézy a porozumění celému příběhu ohrožení.

Rozkládání záložek v Azure Sentinel vám to uděláte tak, že zachováte dotazy, které jste spustili v **protokolech Azure Sentinel**, společně s výsledky dotazu, které považujete za relevantní. Můžete také zaznamenat kontextová pozorování a odkazovat na své závěry přidáním poznámek a značek. Data s záložkami jsou viditelná pro vás a vaše ostatními týmu pro jednoduchou spolupráci.

Data v záložkách můžete kdykoli znovu navštívit na kartě **záložky** v podokně pro **lov** . Pomocí možností filtrování a hledání můžete rychle najít konkrétní data pro vaše aktuální šetření. Případně můžete data z vaší záložky zobrazit přímo v tabulce **HuntingBookmark** v pracovním prostoru Log Analytics. Například:

> [!div class="mx-imgBorder"]
> ![Zobrazit tabulku HuntingBookmark](./media/bookmarks/bookmark-table.png)

Zobrazením záložek z tabulky můžete filtrovat, shrnout a spojovat data v záložkách s ostatními zdroji dat, což usnadňuje hledání corroborating legitimace.

Pokud zjistíte, že je v současnosti ve verzi Preview něco, co naléhavě musí být vyřešené při lovu do protokolů, můžete vytvořit záložku a povýšit ji na incident nebo přidat záložku k existujícímu incidentu. Další informace o incidentech najdete v tématu [kurz: prozkoumání incidentů pomocí služby Azure Sentinel](tutorial-investigate-cases.md). 

Také ve verzi Preview můžete vizualizovat data v záložkách kliknutím na tlačítko **prozkoumat** v podrobnostech záložky. Tím se spustí šetření, ve kterém si můžete prohlédnout, prozkoumat a vizuálně komunikovat vaše závěry pomocí interaktivního diagramu entit a Timeline grafu.

## <a name="add-a-bookmark"></a>Přidání záložky

1. V Azure Portal přejděte do části **Sentinel**  >  **Threat Management**  >  **lovu** a spusťte dotazy pro podezřelé a neobvyklé chování.

2. Vyberte jeden z loveckých dotazů a na pravé straně v části Podrobnosti o loveckém dotazu vyberte **Spustit dotaz**. 

3. Vyberte **Zobrazit výsledky dotazu**. Například:
    
    > [!div class="mx-imgBorder"]
    > ![zobrazení výsledků dotazu z hlediska pro lov Azure Sentinel](./media/bookmarks/new-processes-observed-example.png)
    
    Tato akce otevře výsledky dotazu v podokně **protokoly** .

4. V seznamu výsledků dotazu protokolu pomocí zaškrtávacích políček vyberte jeden nebo více řádků, které obsahují informace, které najdete zajímavě.

5. Vyberte **Přidat záložku**:
    
    > [!div class="mx-imgBorder"]
    > ![Přidat do dotazu záložku pro lov](./media/bookmarks/add-hunting-bookmark.png)

6. Napravo můžete v podokně **Přidat záložek** volitelně aktualizovat název záložky, přidat značky a poznámky, které vám pomůžou zjistit, co bylo pro danou položku zajímavé.

7. V části **informace o dotazu** můžete pomocí rozevíracích seznamů extrahovat informace z výsledků dotazu pro typ entity **účet**, **hostitele**a **IP adresy** . Tato akce mapuje vybraný typ entity na konkrétní sloupec z výsledku dotazu. Například:
    
    > [!div class="mx-imgBorder"]
    > ![Mapování typů entit pro lovecké záložky](./media/bookmarks/map-entity-types-bookmark.png)
    
    Chcete-li zobrazit záložku v grafu šetření (aktuálně ve verzi Preview), je nutné namapovat alespoň jeden typ entity, který je buď **účet**, **hostitel**, nebo **IP adresa**. 

5. Klikněte na **Uložit** a potvrďte provedené změny a přidejte záložku. Všechna data s záložkami se sdílejí s ostatními zkoušejícími a jedná se o první krok k prostředí šetření zaměřenému na spolupráci.

 
> [!NOTE]
> Výsledky dotazu protokolu podporují záložky vždy, když je toto podokno otevřeno z Azure Sentinel. Můžete například vybrat **Obecné**  >  **protokoly** z navigačního panelu, vybrat odkazy na události v grafu vyšetřování nebo vybrat ID výstrahy z úplných podrobností incidentu (aktuálně ve verzi Preview). Záložky nelze vytvořit, pokud je podokno **protokoly** otevřeno z jiných umístění, například přímo z Azure monitor.

## <a name="view-and-update-bookmarks"></a>Zobrazení a aktualizace záložek 

1. V Azure Portal přejděte do části **Sentinel**  >  **Threat Management**  >  **lov**. 

2. Vyberte kartu **záložky** a zobrazte tak seznam záložek.

3. Pokud vám pomůžete najít konkrétní záložku, použijte vyhledávací pole nebo možnosti filtrování.

4. Vyberte jednotlivé záložky a zobrazte podrobnosti o záložce v podokně podrobností na pravé straně.

5. Proveďte změny podle potřeby, které se automaticky uloží.

## <a name="exploring-bookmarks-in-the-investigation-graph"></a>Zkoumání záložek v grafu šetření

> [!IMPORTANT]
> Zkoumání záložek v grafu šetření a samotného grafu šetření jsou momentálně ve verzi Public Preview.
> Tyto funkce se poskytují bez smlouvy o úrovni služeb a nedoporučují se pro produkční úlohy.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

1. V Azure Portal přejděte na kartu **Sentinel**  >  **Threat Management**–  >  karta**lovecké**  >  **záložky** a vyberte záložku nebo záložky, které chcete prozkoumat.

2. V podrobnostech záložky se ujistěte, že je namapovaná aspoň jedna entita. Například u **entit**se zobrazí položky pro **IP adresu**, **počítač**nebo **účet**.

3. Kliknutím na **prozkoumat** Zobrazte záložku v grafu šetření.

Pokyny k používání grafu šetření najdete v tématu [použití grafu šetření k hloubkové podrobně](tutorial-investigate-cases.md#use-the-investigation-graph-to-deep-dive).

## <a name="add-bookmarks-to-a-new-or-existing-incident"></a>Přidat záložky do nového nebo existujícího incidentu

> [!IMPORTANT]
> Přidávání záložek do nového nebo existujícího incidentu je aktuálně ve verzi Public Preview.
> Tato funkce se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

1. V Azure Portal přejděte na kartu **Sentinel**  >  **Threat Management**–  >  karta**lovecké**  >  **záložky** a vyberte záložku nebo záložky, které chcete přidat k incidentu.

2. Na panelu příkazů vyberte **Akce incidentu (Preview)** :
    
    > [!div class="mx-imgBorder"]
    > ![Přidat záložky k incidentu](./media/bookmarks/incident-actions.png)

3. V případě potřeby vyberte možnost **vytvořit nový incident** nebo **Přidat k existujícímu incidentu**. Pak:
    
    - Pro nový incident: volitelně aktualizujte podrobnosti incidentu a potom vyberte **vytvořit**.
    - Přidání záložky do existujícího incidentu: vyberte jeden incident a pak vyberte **Přidat**. 

Zobrazení záložky v rámci incidentu: přejděte na incidenty se správou hrozeb s **ověřovacími**  >  **hrozbami**  >  **Incidents** a vyberte incident s vaší záložkou. Vyberte **Zobrazit úplné podrobnosti**a potom vyberte kartu **záložky** .

> [!TIP]
> Jako alternativu k možnosti **Akce incidentu (Preview)** na panelu příkazů můžete použít kontextovou nabídku (**...**) pro jednu nebo více záložek a vybrat možnosti pro **Vytvoření nového incidentu**, **přidání do existujícího incidentu**a **Odebrat z incidentu**. 

## <a name="view-bookmarked-data-in-logs"></a>Zobrazit data v záložkách v protokolech

Pokud chcete zobrazit záložky, výsledky nebo jejich historii, vyberte záložku na kartě **lovecké**  >  **záložky** a použijte odkazy v podokně podrobností: 

- **Zobrazit zdrojový dotaz** pro zobrazení zdrojového dotazu v podokně **protokoly** .

- **Zobrazením protokolů záložky** zobrazíte všechna metadata záložky, včetně toho, kdo provedl aktualizaci, aktualizované hodnoty a čas, kdy k aktualizaci došlo.

Nezpracované data záložky pro všechny záložky můžete zobrazit také tak, že na panelu příkazů na kartě **lovecké**záložky vyberete možnost **protokoly záložek**  >  **Bookmarks** :

> [!div class="mx-imgBorder"]
> ![Protokoly záložek](./media/bookmarks/bookmark-logs.png)

V tomto zobrazení se zobrazují všechny záložky s přidruženými metadaty. Pomocí dotazů jazyka KQL ( [klíč Query Language](https://docs.microsoft.com/sharepoint/dev/general-development/keyword-query-language-kql-syntax-reference) ) můžete filtrovat dolů na nejnovější verzi konkrétní záložky, kterou hledáte.

> [!NOTE]
> Mezi časem vytvoření záložky a jejím zobrazením na kartě **záložky** může být významná prodleva (měřeno v minutách).

## <a name="delete-a-bookmark"></a>Odstranění záložky
 
1.  V Azure Portal přejděte na kartu **Sentinel**  >  **Threat Management**–  >  karta**lovecké**  >  **záložky** a vyberte záložku nebo záložky, které chcete odstranit. 

2. Klikněte pravým tlačítkem na své výběry a vyberte možnost odstranění záložky nebo záložek. Pokud jste například vybrali jenom jednu záložku, **odstraňte záložku** , a pokud jste vybrali dvě záložky, odstraňte **2 záložky** .
    
Odstranění záložky odebere záložku ze seznamu na kartě **Záložka** . Tabulka **HuntingBookmark** pro váš pracovní prostor Log Analytics bude dál obsahovat položky předchozí záložky, ale nejnovější položka změní hodnotu **SoftDelete** na true, což usnadňuje filtrování starých záložek. Odstraněním záložky se neodstraní žádné entity z prostředí pro šetření, které jsou přidruženy k jiným záložkám nebo výstrahám. 


## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak spustit lovecké šetření pomocí záložek ve službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:


- [Proaktivní vylovení hrozeb](hunting.md)
- [Použití poznámkových bloků ke spouštění automatizovaných loveckých kampaní](notebooks.md)
