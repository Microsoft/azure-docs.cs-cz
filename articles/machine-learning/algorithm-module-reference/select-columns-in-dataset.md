---
title: 'Vybrat sloupce v datové sadě: Odkaz na modul'
titleSuffix: Azure Machine Learning
description: Zjistěte, jak použít modul Vybrat sloupce v datové sadě v Azure Machine Learning k výběru podmnožiny sloupců, které se mají použít v podřízených operacích.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 714148815fa4ff543ecbf86ab21e8d51fe775543
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77153770"
---
# <a name="select-columns-in-dataset-module"></a>Vybrat sloupce v modulu Datové sady

Tento článek popisuje modul v návrháři Azure Machine Learning (preview).

Tento modul slouží k výběru podmnožiny sloupců, které se mají použít v následných operacích. Modul fyzicky neodebere sloupce ze zdrojové datové sady. místo toho vytvoří podmnožinu sloupců, podobně jako *zobrazení* databáze nebo *projekce*.

Tento modul je užitečný, když potřebujete omezit sloupce, které jsou k dispozici pro následnou operaci, nebo pokud chcete zmenšit velikost datové sady odebráním nepotřebných sloupců.

Sloupce v datové sadě jsou výstupem ve stejném pořadí jako v původních datech, i když je zadáte v jiném pořadí.

## <a name="how-to-use"></a>Způsob použití

Tento modul nemá žádné parametry. Pomocí voliče sloupců můžete vybrat sloupce, které chcete zahrnout nebo vyloučit.

### <a name="choose-columns-by-name"></a>Výběr sloupců podle názvu

V modulu je několik možností pro výběr sloupců podle názvu: 

+ Filtrování a vyhledávání

    Klepněte na možnost **NÁZEV.**

    Pokud jste připojili datovou sadu, která je již naplněna, měl by se zobrazit seznam dostupných sloupců. Pokud se nezobrazí žádné sloupce, bude pravděpodobně nutné spustit upstream moduly pro zobrazení seznamu sloupců.

    Chcete-li seznam filtrovat, zadejte do vyhledávacího pole. Pokud například zadáte písmeno `w` do vyhledávacího pole, seznam se vyfiltruje `w`tak, aby zobrazoval názvy sloupců, které obsahují písmeno .

    Vyberte sloupce a kliknutím na tlačítko se šipkou vpravo přesuňte vybrané sloupce do seznamu v pravém podokně.

    + Chcete-li vybrat souvislou oblast názvů sloupců, stiskněte **klávesu Shift + Klepněte na tlačítko**.
    + Chcete-li do výběru přidat jednotlivé sloupce, stiskněte **kombinaci kláves Ctrl + Klepněte na tlačítko**.

    Kliknutím na tlačítko zaškrtnutí uložte a zavřete.

+ Použití názvů v kombinaci s jinými pravidly

    Klepněte na možnost **S PRAVIDLY.**
    
    Zvolte pravidlo, například zobrazení sloupců určitého datového typu.

    Potom klikněte na jednotlivé sloupce tohoto typu podle názvu a přidejte je do seznamu výběru.

+ Zadání nebo vložení seznamu názvů sloupců oddělených čárkami

    Pokud je datová sada široká, může být jednodušší použít indexy nebo generované seznamy názvů, než vybírat sloupce jednotlivě. Za předpokladu, že jste si seznam připravili předem:

    1. Klepněte na možnost **S PRAVIDLY.** 
    2. Vyberte **Žádné sloupce**, vyberte **Zahrnout**a pak klepněte do textového pole s červeným vykřičníkem. 
    3. Vložte nebo zadejte seznam dříve ověřených názvů sloupců oddělených čárkami. Modul nelze uložit, pokud má libovolný sloupec neplatný název, proto nezapomeňte předem zkontrolovat názvy.
    
    Tuto metodu můžete také použít k určení seznamu sloupců pomocí jejich hodnot indexu. 

### <a name="choose-by-type"></a>Výběr podle typu

Pokud použijete možnost **S RULES,** můžete na výběr sloupců použít více podmínek. Může být například nutné získat pouze sloupce funkcí číselného datového typu.

Možnost **BEGIN WITH** určuje počáteční bod a je důležitá pro pochopení výsledků. 

+ Pokud vyberete možnost **Všechny sloupce,** budou do seznamu přidány všechny sloupce. Potom musíte použít **možnost Vyloučit** k *odebrání* sloupců, které splňují určité podmínky. 

    Můžete například začít se všemi sloupci a potom sloupce odebrat podle názvu nebo podle typu.

+ Pokud vyberete možnost **BEZ SLOUPCŮ,** seznam sloupců začne prázdný. Potom určíte podmínky pro *přidání* sloupců do seznamu. 

    Pokud použijete více pravidel, každá podmínka je **aditivní**. Řekněme například, že začnete bez sloupců a pak přidáte pravidlo, které získá všechny číselné sloupce. V datové sadě ceny automobilu, která má za následek 16 sloupců. Potom klepnutím na **+** znaménko přidáte novou podmínku a vyberete **Zahrnout všechny funkce**. Výsledná datová sada obsahuje všechny číselné sloupce a všechny sloupce funkcí, včetně některých sloupců funkce řetězce.

### <a name="choose-by-column-index"></a>Výběr podle indexu sloupce

Index sloupce odkazuje na pořadí sloupce v původní datové sadě.

+ Sloupce jsou číslovány postupně začínající na 1.  
+ Chcete-li získat rozsah sloupců, použijte pomlčku. 
+ Otevřené specifikace, například `1-` `-3` nebo nejsou povoleny.
+ Duplicitní hodnoty indexu (nebo názvy sloupců) nejsou povoleny a může dojít k chybě.

Například za předpokladu, že vaše datová sada obsahuje alespoň osm sloupců, můžete vložit do některého z následujících příkladů vrátit více nesouvislých sloupců: 

+ `8,1-4,6`
+ `1,3-8`
+ `1,3-6,4` 

konečný příklad nevede k chybě; však vrátí jednu instanci sloupce `4`.



### <a name="change-order-of-columns"></a>Změna pořadí sloupců

Možnost **Povolit duplikáty a zachovat pořadí sloupců ve výběru** začíná prázdným seznamem a přidá sloupce, které zadáte podle názvu nebo indexu. Na rozdíl od jiných možností, které vždy vracejí sloupce v jejich "přirozeném pořadí", tato možnost vypíše sloupce v pořadí, ve kterém je pojmenujete nebo vypíšete. 

Například v datové sadě se sloupci Col1, Col2, Col3 a Col4 můžete obrátit pořadí sloupců a vynechat sloupec 2 zadáním některého z následujících seznamů:

+ `Col4, Col3, Col1`
+ `4,3,1`


## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 