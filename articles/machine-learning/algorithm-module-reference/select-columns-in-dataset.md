---
title: 'Vybrat sloupce v datové sadě: odkaz na modul'
titleSuffix: Azure Machine Learning
description: Naučte se používat modul vybrat sloupce v datové sadě v Azure Machine Learning k výběru podmnožiny sloupců pro použití v rámci navazujících operací.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 7926d5dd33df5538713f8de152dbbace2bc29864
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "90890298"
---
# <a name="select-columns-in-dataset-module"></a>Výběr sloupců v modulu DataSet

Tento článek popisuje modul v Návrháři Azure Machine Learning.

Pomocí tohoto modulu můžete zvolit podmnožinu sloupců, které se mají použít v rámci podřízených operací. Modul neodebere fyzické sloupce ze zdrojové datové sady. místo toho vytvoří podmnožinu sloupců, podobně jako *zobrazení* nebo *projekce* databáze.

Tento modul je užitečný v případě, že potřebujete omezit sloupce dostupné pro operaci pro příjem dat, nebo pokud chcete zmenšit velikost datové sady odebráním nepotřebných sloupců.

Sloupce v datové sadě jsou výstupy ve stejném pořadí jako v původních datech, a to i v případě, že je zadáte v jiném pořadí.

## <a name="how-to-use"></a>Způsob použití

Tento modul nemá žádné parametry. K výběru sloupců, které se mají zahrnout nebo vyloučit, použijte selektor sloupců.

### <a name="choose-columns-by-name"></a>Zvolit sloupce podle názvu

V modulu je více možností pro výběr sloupců podle názvu: 

+ Filtrovat a Hledat

    Klikněte na možnost **podle názvu** .

    Pokud jste připojili datovou sadu, která je již naplněna, zobrazí se seznam dostupných sloupců. Pokud se nezobrazí žádné sloupce, může být nutné spustit moduly pro odesílání dat, aby se zobrazil seznam sloupců.

    Chcete-li filtrovat seznam, zadejte do vyhledávacího pole. Pokud například zadáte písmeno `w` do vyhledávacího pole, seznam se vyfiltruje tak, aby zobrazoval názvy sloupců, které obsahují písmeno `w` .

    Vyberte sloupce a kliknutím na tlačítko se šipkou doprava přesuňte vybrané sloupce do seznamu v pravém podokně.

    + Chcete-li vybrat souvislý rozsah názvů sloupců, stiskněte klávesy **SHIFT + kliknutí**.
    + Chcete-li přidat jednotlivé sloupce do výběru, stiskněte klávesy **CTRL + kliknutí**.

    Kliknutím na tlačítko zaškrtnutí se uložte a zavřete.

+ Použití názvů v kombinaci s jinými pravidly

    Klikněte na možnost **s pravidly** .
    
    Vyberte pravidlo, jako je například zobrazení sloupců určitého datového typu.

    Pak klikněte na jednotlivé sloupce daného typu podle názvu a přidejte je do seznamu výběru.

+ Zadejte nebo vložte čárkami oddělený seznam názvů sloupců.

    Pokud je vaše datová sada rozsáhlá, může být jednodušší použít indexy nebo vygenerované seznamy názvů místo výběru sloupců jednotlivě. Za předpokladu, že jste si seznam připravili předem:

    1. Klikněte na možnost **s pravidly** . 
    2. Vyberte **žádné sloupce**, vyberte  **Zahrnout** a pak klikněte do textového pole s červeným vykřičníkem. 
    3. Vložte nebo zadejte čárkami oddělený seznam dříve ověřených názvů sloupců. Modul nemůžete uložit, pokud má nějaký sloupec neplatný název, proto nezapomeňte zkontrolovat názvy předem.
    
    Tuto metodu můžete také použít k určení seznamu sloupců pomocí jejich hodnot indexu. 

### <a name="choose-by-type"></a>Zvolit podle typu

Použijete-li možnost **with Rules** , můžete pro výběr sloupců použít více podmínek. Například může být nutné získat pouze sloupce funkce číselného datového typu.

Možnost **začít s** určuje výchozí bod a je důležité pro porozumění výsledkům. 

+ Pokud vyberete možnost **všechny sloupce** , do seznamu se přidají všechny sloupce. Pak je nutné pomocí možnosti **vyloučit** *Odebrat* sloupce, které splňují určité podmínky. 

    Můžete například začít se všemi sloupci a pak odebrat sloupce podle názvu nebo podle typu.

+ Pokud vyberete možnost **žádné sloupce** , seznam sloupců začne být prázdný. Pak zadáte podmínky pro *Přidání* sloupců do seznamu. 

    Pokud použijete více pravidel, každá podmínka je **aditivní**. Řekněme například, že začnete bez sloupců a pak přidáte pravidlo, které získá všechny číselné sloupce. V datové sadě cen automobilu je výsledkem 16 sloupců. Pak klikněte na **+** znaménko a přidejte novou podmínku a vyberte **Zahrnout všechny funkce**. Výsledná datová sada zahrnuje všechny číselné sloupce a všechny sloupce funkcí včetně některých sloupců funkcí řetězce.

### <a name="choose-by-column-index"></a>Vybrat podle indexu sloupce

Index sloupce odkazuje na pořadí sloupce v rámci původního objektu DataSet.

+ Sloupce se číslují sekvenčně od 1.  
+ Chcete-li získat rozsah sloupců, použijte spojovník. 
+ Specifikace Open-konec, například `1-` nebo, nejsou `-3` povoleny.
+ Duplicitní hodnoty indexu (nebo názvy sloupců) nejsou povoleny a mohou mít za následek chybu.

Například za předpokladu, že vaše datová sada má alespoň osm sloupců, můžete vložit do kteréhokoli z následujících příkladů, které vrátí více nesouvislých sloupců: 

+ `8,1-4,6`
+ `1,3-8`
+ `1,3-6,4` 

poslední příklad nevede k chybě; Vrátí ale jednu instanci sloupce `4` .



### <a name="change-order-of-columns"></a>Změnit pořadí sloupců

Možnost **povolující duplicity a zachovat pořadí sloupců v výběru** začíná prázdným seznamem a přidává sloupce, které určíte podle názvu nebo indexu. Na rozdíl od jiných možností, které vždy vracejí sloupce v jejich "přirozeném pořadí", tato možnost vypíše sloupce v pořadí, ve kterém je napíšete nebo je vypíšete. 

Například v datové sadě se sloupci Sloupec1, col2, Col3 a Col4 můžete změnit pořadí sloupců a nechat sloupec 2 zadáním jednoho z následujících seznamů:

+ `Col4, Col3, Col1`
+ `4,3,1`


## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 