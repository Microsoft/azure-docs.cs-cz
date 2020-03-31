---
title: 'Oddíl a ukázka: Odkaz na modul'
titleSuffix: Azure Machine Learning
description: Zjistěte, jak pomocí oddílu a ukázkový modul v Azure Machine Learning provádět vzorkování na datové sady nebo vytvořit oddíly z datové sady.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 0392a05df485b45b1244f4542ed55af1837dca3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477506"
---
# <a name="partition-and-sample-module"></a>Oddíl ový a ukázkový modul

Tento článek popisuje modul v návrháři Azure Machine Learning (preview).

Pomocí oddílu a ukázkového modulu můžete provádět vzorkování v datové sadě nebo vytvářet oddíly z datové sady.

Vzorkování je důležitým nástrojem ve strojovém učení, protože umožňuje zmenšit velikost datové sady při zachování stejného poměru hodnot. Tento modul podporuje několik souvisejících úkolů, které jsou důležité ve strojovém učení: 

- Rozdělení dat do více podsekcí stejné velikosti. 

  Oddíly můžete použít pro křížové ověřování nebo k přiřazení případů náhodným skupinám.

- Oddělení dat do skupin a potom práce s daty z určité skupiny. 

  Po náhodném přiřazení případů různým skupinám může být nutné upravit funkce, které jsou přidruženy pouze k jedné skupině.

- Odběru vzorků. 

  Můžete extrahovat procento dat, použít náhodný odběr vzorků nebo zvolit sloupec, který chcete použít pro vyvážení datové sady a provést stratifikované vzorkování jejích hodnot.

- Vytvoření menší datové sady pro testování. 

  Pokud máte velké množství dat, můžete chtít použít pouze první *n* řádky při nastavování kanálu a potom přepnout na použití úplné datové sady při vytváření modelu. Vzorkování můžete také použít k vytvoření menší datové sady pro použití ve vývoji.

## <a name="configure-the-module"></a>Konfigurace modulu

Tento modul podporuje následující metody pro rozdělení dat do oddílů nebo pro vzorkování. Nejprve zvolte metodu a pak nastavte další možnosti, které metoda vyžaduje.

- Head
- Vzorkování
- Přiřadit k přeložením
- Vyskladnění přeložení

### <a name="get-top-n-rows-from-a-dataset"></a>Získání řádků TOP N z datové sady

Tento režim slouží k získání pouze prvních *n* řádků. Tato možnost je užitečná, pokud chcete otestovat kanál na malém počtu řádků a nepotřebujete data, která mají být vyvážená nebo vzorkována žádným způsobem.

1. Přidejte oddíl **a ukázkový** modul do kanálu v rozhraní a připojte datovou sadu.  

1. **Režim oddílu nebo ukázkového režimu**: Tuto možnost nastavte na **hodnotu Head**.

1. **Počet řádků, které chcete vybrat**: Zadejte počet řádků, které chcete vrátit.

   Počet řádků musí být nezáporné celé číslo. Pokud je počet vybraných řádků větší než počet řádků v datové sadě, je vrácena celá datová sada.

1. Odešlete potrubí.

Modul vyveze jednu datovou sadu, která obsahuje pouze zadaný počet řádků. Řádky jsou vždy číst z horní části datové sady.

### <a name="create-a-sample-of-data"></a>Vytvoření ukázky dat

Tato možnost podporuje jednoduché náhodné vzorkování nebo stratifikované náhodné vzorkování. Je to užitečné, pokud chcete vytvořit menší reprezentativní ukázkovou datovou sadu pro testování.

1. Přidejte oddíl **a ukázkový** modul do kanálu a připojte datovou sadu.

1. **Režim oddílu nebo ukázkového režimu**: Tuto možnost nastavte na **Vzorkování**.

1. **Rychlost odběru vzorků**: Zadejte hodnotu mezi 0 a 1. Tato hodnota určuje procento řádků ze zdrojové datové sady, které by měly být zahrnuty do výstupní datové sady.

   Chcete-li například pouze polovinu původní datové `0.5` sady, zadejte, že vzorkovací frekvence by měla být 50 procent.

   Řádky vstupní datové sady jsou zamíchány a selektivně umístěny do výstupní datové sady podle zadaného poměru.

1. **Náhodné osivo pro odběr vzorků**: Volitelně zadejte celé číslo, které se použije jako hodnota osiva.

   Tato možnost je důležitá, pokud chcete, aby byly řádky pokaždé rozděleny stejným způsobem. Výchozí hodnota je **0**, což znamená, že počáteční osiva je generována na základě systémových hodin. Tato hodnota může vést k mírně odlišné výsledky při každém spuštění kanálu.

1. **Stratifikované rozdělení pro vzorkování**: Tuto možnost vyberte, pokud je důležité, aby řádky v datové sadě byly před vzorkováním rovnoměrně rozděleny některým klíčovým sloupcem.

   Pro **sloupec stratifikačního klíče pro vzorkování**vyberte jeden *sloupec vrstev,* který se má použít při dělení datové sady. Řádky v datové sadě jsou pak rozděleny takto:

   1. Všechny vstupní řádky jsou seskupeny (stratifikované) podle hodnot v zadaném sloupci vrstev.

   1. Řádky jsou zamíchány v rámci každé skupiny.

   1. Každá skupina je selektivně přidána do výstupní datové sady, aby splňovala zadaný poměr.


1. Odešlete potrubí.

   S touto volbou modul vyveze jednu datovou sadu, která obsahuje reprezentativní vzorkování dat. Zbývající nevzorkovaná část datové sady není výstupem. 

## <a name="split-data-into-partitions"></a>Rozdělení dat do oddílů

Tuto možnost použijte, pokud chcete datovou sadu rozdělit na podmnožiny dat. Tato možnost je také užitečná, pokud chcete vytvořit vlastní počet záhybů pro křížové ověření nebo rozdělit řádky do několika skupin.

1. Přidejte oddíl **a ukázkový** modul do kanálu a připojte datovou sadu.

1. V **režimu oddílu nebo ukázkového režimu**vyberte **Přiřadit přeložení**.

1. **Použít nahrazení v dělení**: Tuto možnost vyberte, pokud chcete, aby byl vzorkovaný řádek vložen zpět do fondu řádků pro potenciální opakované použití. V důsledku toho může být stejný řádek přiřazen k několika záhybům.

   Pokud nepoužijete nahrazení (výchozí možnost), vzorkovaný řádek není vložen zpět do fondu řádků pro potenciální opakované použití. V důsledku toho může být každý řádek přiřazen pouze k jednomu záhybu.

1. **Náhodné rozdělení**: Tuto možnost vyberte, pokud chcete, aby byly řádky náhodně přiřazeny k přeložením.

   Pokud tuto možnost nevyberete, řádky se přiřadí k přeložení metodou kruhového dotazování.

1. **Náhodné osivo**: Volitelně zadejte celé číslo, které se má použít jako hodnota osiva. Tato možnost je důležitá, pokud chcete, aby byly řádky pokaždé rozděleny stejným způsobem. V opačném případě výchozí hodnota **0** znamená, že bude použito náhodné počáteční osiva.

1. **Zadejte metodu partitioner**: Označte, jak mají být data rozdělena do každého oddílu, pomocí těchto možností:

   - **Oddíl rovnoměrně**: Pomocí této možnosti umístěte stejný počet řádků v každém oddílu. Chcete-li určit počet výstupních oddílů, zadejte celé číslo do **pole Zadejte počet záhybů, které chcete rovnoměrně rozdělit.**

   - **Oddíl s přizpůsobenými proporcemi**: Pomocí této možnosti můžete určit velikost každého oddílu jako seznamu odděleného čárkami.

     Předpokládejme například, že chcete vytvořit tři oddíly. První oddíl bude obsahovat 50 procent dat. Zbývající dva oddíly budou obsahovat 25 procent dat. Do **pole Seznam proporcí oddělených čárkou** zadejte tato čísla: **.5, .25, .25**.

     Součet všech velikostí oddílů musí přidat až přesně 1.

     Pokud zadáte čísla, která přidávají *méně než 1*, vytvoří se další oddíl pro uložení zbývajících řádků. Pokud například zadáte hodnoty **.2** a **.3**, vytvoří se třetí oddíl, který pojme zbývajících 50 procent všech řádků.
     
     Pokud zadáte čísla, která přidávají *více než 1*, je při spuštění kanálu vyvolána chyba.

1. **Stratifikované rozdělení**: Tuto možnost vyberte, pokud chcete, aby byly řádky při rozdělení stratifikovány, a pak zvolte _sloupec vrstev_.

1. Odešlete potrubí.

   S touto volbou modul vyveze více datových sad. Datové sady jsou rozděleny podle pravidel, která jste zadali.

### <a name="use-data-from-a-predefined-partition"></a>Použití dat z předdefinovaného oddílu  

Tuto možnost použijte, pokud jste rozdělili datovou sadu do více oddílů a nyní chcete načíst každý oddíl pro další analýzu nebo zpracování.

1. Přidejte oddíl **a ukázkový** modul do kanálu.

1. Připojte modul k výstupu předchozí instance **oddílu a ukázky**. Tato instance musí použít **možnost Přiřadit přeložení** ke generování určitého počtu oddílů.

1. **Režim oddílu nebo ukázkového režimu**: Vyberte **možnost Vybrat přeložení**.

1. **Určete, ze kterého přeložení se mají vzorkovat**: Zadáním jeho indexu vyberte oddíl, který se má použít. Indexy oddílů jsou založeny na 1. Pokud například rozdělíte datovou sadu na tři části, oddíly budou mít indexy 1, 2 a 3.

   Pokud zadáte neplatnou hodnotu indexu, je vyvolána chyba návrhu: "Chyba 0018: Datová sada obsahuje neplatná data."

   Kromě seskupení datové sady podle záhybů můžete datovou sadu rozdělit do dvou skupin: cílový záhyb a vše ostatní. Chcete-li to provést, zadejte index jednoho záhybu a pak vyberte možnost **Vybrat doplněk vybraného záhybu,** abyste získali vše kromě dat v zadaném záhybu.

1. Pokud pracujete s více oddíly, musíte přidat další instance **oddílu a ukázkový** modul pro zpracování každého oddílu.

   Například **oddíl a ukázkový** modul v druhém řádku je nastaven na **Přiřadit přeložení**a modul ve třetím řádku je nastaven na **Vyskladnění přeložení**.   

   ![Rozdělení a ukázka](./media/module/partition-and-sample.png)

1. Odešlete potrubí.

   Pomocí této možnosti modul vyveze jednu datovou sadu, která obsahuje pouze řádky přiřazené k tomuto přeložení.

> [!NOTE]
>  Označení přeložení nelze zobrazit přímo. Jsou k dispozici pouze v metadatech.

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 