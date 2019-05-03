---
title: 'Výběr sloupců v datové sadě: Odkaz na modul'
titleSuffix: Azure Machine Learning service
description: Další informace o použití výběr sloupců v datové sadě modulu ve službě Azure Machine Learning vybrat podmnožinu sloupců pro použití v podřízené operace.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: e7407f62bd3401411d56076b298bd8cd134ece62
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028092"
---
# <a name="select-columns-in-dataset-module"></a>Výběr sloupců v datové sadě modulu

Tento článek popisuje modulu rozhraní visual (preview) pro službu Azure Machine Learning.

Tento modul slouží k vybrat podmnožinu sloupců pro použití v podřízené operace. V modulu neodebere fyzicky sloupce ze jako zdrojovou datovou sadu; Místo toho dojde k podmnožině sloupců, podobně jako databáze *zobrazení* nebo *projekce*.

Tento modul je užitečné, když budete chtít omezit sloupce, které jsou k dispozici pro příjem dat operaci, nebo pokud chcete zmenšit velikost datové sady, odebráním nepotřebných sloupců.

Sloupců v datové sadě jsou výstupem ve stejném pořadí jako původní data, i když je zadat v jiném pořadí.

## <a name="how-to-use"></a>Způsob použití

Tento modul nemá žádné parametry. Pomocí selektoru sloupců vyberte sloupce, které chcete zahrnout nebo vyloučit.

### <a name="choose-columns-by-name"></a>Zvolit sloupce podle názvu

V modulu pro výběr sloupců podle názvu několika způsoby: 

+ Filtrování a hledání

    Klikněte na tlačítko **podle názvu** možnost.

    Pokud jste se připojili datovou sadu, která je už vyplněné, by se zobrazit seznam dostupných sloupců. Pokud se nezobrazí žádné sloupce, můžete potřebovat ke spuštění nadřazeného moduly, chcete-li zobrazit seznam sloupců.

    Chcete-li filtrovat seznam, zadejte do vyhledávacího pole. Například, pokud zadáte písmeno `w` do vyhledávacího pole v seznamu vyfiltrují zobrazit názvy sloupců, které obsahují písmeno `w`.

    Vyberte sloupce a klikněte na tlačítko se šipkou vpravo vybrané sloupce přesunout do seznamu v pravém podokně.

    + Vybrat průběžné rozsah názvů sloupců, stiskněte klávesu **Shift + kliknutí myši**.
    + Chcete-li přidat jednotlivé sloupce do výběru, stiskněte **Ctrl + kliknutí**.

    Klikněte na tlačítko zaškrtnutí uložte a zavřete.

+ Použije názvy v kombinaci s jinými pravidly

    Klikněte na tlačítko **pravidla s** možnost.
    
    Vyberte pravidlo, jako je například zobrazení sloupců konkrétní datového typu.

    Potom klikněte na jednotlivé sloupce tohoto typu podle názvu, je přidat do seznamu výběru.

+ Zadejte nebo vložte čárkou oddělený seznam názvů sloupců

    Pokud vaše datová sada je rozsáhlá, může být jednodušší použít indexy nebo jednotlivě vygenerovat seznam názvů, nikoli výběr sloupce. Za předpokladu, že předem připravená seznamu:

    1. Klikněte na tlačítko **pravidla s** možnost. 
    2. Vyberte **žádné sloupce**vyberte **zahrnout**a potom klikněte do textového pole s červený vykřičník. 
    3. Vložte nebo zadejte čárkou oddělený seznam názvů sloupců dříve ověřené. Nelze uložit modulu Pokud libovolný sloupec má neplatný název, proto nezapomeňte zkontrolovat názvy předem.
    
    Tuto metodu můžete také zadat seznam sloupců pomocí jejich hodnoty indexu. 

### <a name="choose-by-type"></a>Zvolte podle typu

Pokud používáte **pravidla s** možnost, můžete použít více podmínek na vybrané sloupce. Například může být potřeba získat jenom funkce sloupce číselným datovým typem.

**Začít s** možnost určuje počáteční bod a je důležité pochopit, výsledky. 

+ Pokud vyberete **všechny sloupce** možnost, všechny sloupce se přidají do seznamu. Poté, je nutné použít **vyloučit** umožňuje *odebrat* sloupce, které splňují určité podmínky. 

    Například může začínat všechny sloupce a pak odeberte sloupce, podle názvu nebo podle typu.

+ Pokud vyberete **žádné sloupce** možnost, seznam sloupců na začátku prázdná. Zadejte podmínky, které *přidat* sloupců do seznamu. 

    Pokud použijete víc pravidel, je každá podmínka **sčítání**. Řekněme například, začněte s žádné sloupce a potom přidejte pravidlo zobrazíte všechny číselné sloupce. V datové sadě Automobile price bude výsledkem 16 sloupců. Potom klikněte **+** podepsat přidat novou podmínku a vyberte **zahrnují všechny funkce**. Výsledná datová sada obsahuje všechny číselné sloupce. navíc všechny funkce sloupce, včetně některých sloupců funkce řetězec.

### <a name="choose-by-column-index"></a>Zvolte podle sloupce indexu

Index sloupce odkazuje pořadí sloupců v rámci původní datové sady.

+ Sloupce jsou číslovány postupně počínaje 1.  
+ Oblast sloupců, použijte pomlčky. 
+ Konkrétní požadavky, jako `1-` nebo `-3` nejsou povoleny.
+ Duplicitní index hodnoty (nebo názvy sloupců) nejsou povoleny a může dojít k chybě.

Například za předpokladu, že vaše datová sada obsahuje alespoň osm sloupce, vám může vložit v některém z následujících příkladů vrátit víc nesousedících sloupců: 

+ `8,1-4,6`
+ `1,3-8`
+ `1,3-6,4` 

v posledním příkladu nemá za následek chybu; Nicméně, vrátí jednu instanci sloupce `4`.



### <a name="change-order-of-columns"></a>Změna pořadí sloupců

Možnost **povolit vynechávání duplicit a zachovat pořadí sloupců v výběru** začíná je seznam prázdný a přidá sloupce, které zadáte podle názvu nebo podle indexu. Na rozdíl od jiných možností, které vždy vrátí sloupců v jejich "přirozené pořadí", tato možnost výstup, který pojmenujete sloupce v pořadí nebo jejich seznam. 

V prvku dataset pomocí sloupců Sloupec1, Sloupec2, Col3 a Sloupec4 můžete například změnit pořadí sloupců a vynechte sloupec 2, tak, že určíte jednu z následujících seznamů:

+ `Col4, Col3, Col1`
+ `4,3,1`


## <a name="next-steps"></a>Další postup

Zobrazit [sada modulů, které jsou k dispozici](module-reference.md) do služby Azure Machine Learning. 