---
title: Mějte přehled o data při typu v Azure Sentinelu Preview pomocí typu záložky | Dokumentace Microsoftu
description: Tento článek popisuje, jak použít ke sledování dat typu záložky Sentinelu Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: mbaldwin
editor: ''
ms.assetid: 320ccdad-8767-41f3-b083-0bc48f1eeb37
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 484acdc16f57f37098d669f03c1b51c087308289
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2019
ms.locfileid: "56993192"
---
# <a name="keep-track-of-data-during-hunting"></a>Mějte přehled o dat během myslivost

> [!IMPORTANT]
> Azure Sentinel je aktuálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 
Před internetovými útoky typu zpravidla vyžaduje, aby revize hory dat protokolu hledá známky škodlivého chování. Během tohoto procesu výzkumníků najít události, které chtějí mějte na paměti, opakování a analyzovat jako součást ověření potenciální hypotézy a pochopení celý příběh ohrožení zabezpečení.
Záložky typu můžete k tomu, zachování dotazy, že kterou jste spustili v Log Analytics, spolu s výsledky dotazu, které můžete považovat za relevantní. Můžete zaznamenat kontextové poznámky a svá zjištění odkazovat přidávání poznámek a značky. Označenou záložkou a doplňujte data jsou viditelná pro vás a odpovídáním pro snadnou spolupráci.   

Označenou záložkou a doplňujte dat v každém okamžiku může návštěvě na **záložku** karty **typu** stránky. Můžete použít filtrování a možnosti rychle najít konkrétní data pro aktuální šetření hledání. Alternativně můžete zobrazit přímo v označenou záložkou a doplňujte data **HuntingBookmark** tabulky ve službě Log Analytics. To vám umožňuje filtrovat, shrnout a připojte se k označenou záložkou a doplňujte dat s jinými zdroji dat, což usnadňuje vyhledání potvrzující důkazy.

Můžete také vizualizovat data označenou záložkou a doplňujte kliknutím **prošetření**. Tím se spustí šetření prostředí, ve kterém můžete zobrazit, prozkoumat a vizuálnímu svá zjištění použití interaktivní entity-graph diagram a časová osa.


## <a name="run-a-log-analytics-query-from-azure-sentinel"></a>Spuštění dotazu Log Analytics z Azure Sentinelu

1. Na portálu Azure Sentinelu, klikněte na tlačítko **typu** ke spouštění dotazů pro podezřelé a neobvyklé chování.

1. K vytvoření typu kampaně, vyberte jednu typu dotazů a na levém, zkontrolujte výsledky. 

1. Klikněte na tlačítko **zobrazit výsledky dotazu** v dotazu typu **podrobnosti** výsledky stránky o dotazu v Log Analytics. Tady je příklad, co vidíte Pokud jste spustili vlastního dotazu útoku bruteforce SSH.
  
   ![Zobrazit výsledky](./media/bookmarks/ssh-bruteforce-example.png)

## <a name="add-a-bookmark"></a>Přidat záložku

1. V seznamu výsledků dotazu Log Analytics rozbalte řádek, který obsahuje informace, které vás zajímají.

4. Vyberte tři tečky (...) na konci řádku a vyberte **přidávání záložek typu**.
5. Na pravé straně v **podrobnosti** stránce, aktualizujte název a přidat značky a poznámky, které vám pomůže identifikovat, co byl zajímavé položky.
6. Klikněte na tlačítko **Uložit** potvrďte provedené změny. Veškerá označenou záložkou a doplňujte data se sdílí s další výzkumníků a je první krok směrem k spolupráci prošetřování.

   ![Zobrazit výsledky](./media/bookmarks/add-bookmark-la.png)

 
> [!NOTE]
> Záložky můžete použít také s spustí ze stránky Azure ověřovací protokoly Log Analytics nebo dotazy na informačním ze stránky s Log Analytics a otevřené stránce typu vytvoří libovolný dotazy Log Analytics. Nebudete moct přidat záložku, pokud spustíte z mimo Sentinelu Azure Log Analytics. 

## <a name="view-and-update-bookmarks"></a>Zobrazit a aktualizovat záložky 

1. Na portálu Azure Sentinelu, klikněte na tlačítko **typu**. 
2. Klikněte na tlačítko **záložky** kartu uprostřed stránky zobrazíte seznam záložek.
3. Použijte možnosti vyhledávání pole nebo filtru k vyhledání konkrétní záložku.
4. Vyberte jednotlivé záložky v mřížce níže zobrazíte podrobnosti o záložku v pravém podokně Podrobnosti.
5. Aktualizace značky a poznámky, klikněte na upravitelná textová pole a klikněte na tlačítko **Uložit** zachovat vaše změny.

   ![Zobrazit výsledky](./media/bookmarks/view-update-bookmarks.png)

## <a name="view-bookmarked-data-in-log-analytics"></a>Zobrazení označenou záložkou a doplňujte dat v Log Analytics 

Zobrazení označenou záložkou a doplňujte dat v Log Analytics několika způsoby. 

Nejjednodušší způsob, jak zobrazit označenou záložkou a doplňujte dotazy, výsledky nebo z historie je tak, že vyberete požadovanou Záložka ve **záložky** tabulky a pomocí odkazů uvedených v podokně podrobností. Mezi možnosti patří: 
- Klikněte na **zobrazit dotaz** Chcete-li zobrazit dotaz na zdroj v Log Analytics.  
- Klikněte na **zobrazit historii záložku** zobrazíte vše (záložky) včetně metadata: kdo provedl aktualizace, aktualizovanými hodnotami a čas aktualizace došlo k chybě. 

- Záložka nezpracovaná data pro všechny záložky můžete zobrazit také kliknutím na **záložky protokoly** nad mřížkou záložku. Toto zobrazení se zobrazí všechny své záložky v tabulce typu záložky s příslušnými metadaty. KQL dotazy můžete vyfiltrovat nejnovější verzi, kterou jste hledali záložky.  


> [!NOTE]
> Může být poměrně dlouhodobému výpadku (měří se v minutách) mezi vytvořením záložku a když se objeví v **HuntingBookmark** tabulky. Doporučujeme nejprve vytvořte své záložky, pak po data ingestují jejich analýzu. 

## <a name="delete-a-bookmark"></a>Odstranit záložky
Pokud chcete odstranit záložky provést následující kroky: 
1.  Otevřete th **typu záložku** kartu. 
2.  Vyberte cíl záložky.
3.  Vyberte tři tečky (...) na konci řádku a vyberte **odstranit záložky**.
    
Odstraněním záložky dojde k odebrání záložky v seznamu **záložku** kartu.  Log Analytics "HuntingBookmark" tabulky budou i nadále obsahovat předchozí Záložka položky, ale poslední položka se změní **SoftDelete** hodnotu na true, usnadňuje odfiltrovat staré záložky.  Odstraňuje se záložka neodebere žádné entity z šetření prostředí, které jsou propojeny s jinými záložek nebo výstrahy. 


## <a name="next-steps"></a>Další postup

V tomto článku jste zjistili, jak spustit šetření typu používání záložek v ověřovacích Azure. Další informace o Azure Sentinelu, naleznete v následujících článcích:


- [Proaktivně hunt hrozby](hunting.md)
- [Použití poznámkových bloků ke spuštění automatizovaných typu kampaně](notebooks.md)