---
title: 'Rozdělení a vzorky: Odkaz na modul'
titleSuffix: Azure Machine Learning service
description: Další informace o použití modulu rozdělení a vzorky ve službě Azure Machine Learning provádět vzorkování pro datovou sadu nebo vytvoření oddílů z datové sady.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 72f9e11e3582d804eecc7479ea079276564bd12f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65029277"
---
# <a name="partition-and-sample-module"></a>Rozdělení a vzorky modulu

Tento článek popisuje modulu rozhraní visual (preview) pro službu Azure Machine Learning.

Tento modul slouží k provádění vzorkování pro datovou sadu nebo vytvoření oddílů z datové sady.

Vzorkování je důležitý nástroj v machine learning, protože to umožňuje snížit velikost datové sady při zachování stejné poměr hodnoty. Tento modul podporuje několik související úlohy, které jsou důležité ve službě machine learning: 

- Dělení dat do více podsekcí stejné velikosti. 

    Oddíly můžete použít pro křížové ověření nebo přiřazení případy do náhodných skupin.

- Oddělení dat do skupin a potom práce s daty z konkrétní skupiny. 

    Po přiřazení náhodně případy do různých skupin, je třeba upravit funkce, které jsou spojeny s pouze jednu skupinu.

- Vzorkování. 

    Můžete extrahovat procento dat, použije náhodné vzorkování nebo zvolte sloupec chcete použít pro vyrovnávání datovou sadu a provádět vrstveného vzorkování na jeho hodnoty.

- Vytvoření menší datové sady pro účely testování. 

    Pokud máte velké množství dat, můžete chtít použít pouze první *n* řádků při nastavení testu a pak přepínače na úplné datové sadě při vytváření modelu. Vzorkování můžete také použít k vytvoření s menší datové sady pro použití při vývoji.

## <a name="configure-partition-and-sample"></a>Konfigurace rozdělení a vzorky

Tento modul podporuje více metod pro rozdělení dat do oddílů nebo pro vzorkování. Nejprve zvolte metodu, a pak nastavit další možnosti požadovaný metodou.

- Hlavní uzel
- Vzorkování
- Přiřazení složení
- Vyberte si fold

### <a name="get-top-n-rows-from-a-dataset"></a>Získat prvních N řádky z datové sady

Tento režim použít k získání pouze první *n* řádků. Tato možnost je užitečná, pokud chcete otestovat experimentu na malý počet řádků které nevyžadují data, která mají balanced nebo vzorkovány žádným způsobem.

1. Přidat **rozdělení a vzorky** modulu do experimentu v rozhraní a připojit datové sady.  

2. **Oddíl nebo ukázkový režim**: Tuto možnost nastavte na **Head**.

3. **Počet řádků, které mají vybrat**: Zadejte počet vrácených řádků.

    Počet řádků, které zadáte, musí být nezáporné celé číslo. Pokud počet vybraných řádků je větší než počet řádků v datové sadě, je vrácena celá datová sada.

4. Spusťte experiment.

V modulu výstupy jedna datová sada obsahující pouze zadaný počet řádků. Řádky jsou vždy číst z horní části datové sady.

### <a name="create-a-sample-of-data"></a>Vytvořit ukázková data

Tato možnost podporuje prostý náhodný výběr nebo si náhodných vzorkování. To je užitečné, pokud chcete vytvořit datové sady menší reprezentativní vzorek pro testování.

1. Přidat **rozdělení a vzorky** modulu do svého experimentu a připojte se datová sada.

2. **Oddíl nebo ukázkový režim**: Nastavte na **vzorkování**.

3. **Míra vzorkování**: Zadejte hodnotu mezi 0 a 1. Tato hodnota určuje procento z jako zdrojovou datovou sadu, která mají být zahrnuta ve vstupní sadě řádků.

    Pokud chcete pouze polovinu původní datové sady, zadejte například `0.5` k označení, že vzorkovací frekvenci by měla být 50 %.

    Řádky vstupní datové sady jsou které se náhodně pomíchají a selektivně vytvořte výstupní datovou sadu podle zadaného poměr.

4. **Náhodný počáteční hodnotu pro vzorkování**: Volitelně můžete zadejte celé číslo, který se použije jako počáteční hodnoty.

    Je důležité, pokud chcete, aby řádků, které chcete rozdělit stejný způsobem pokaždé, když tuto možnost. Výchozí hodnota je 0, smyslu, že se vygeneruje počáteční počáteční hodnoty podle systémových hodin. To může vést k mírně odlišné výsledky při každém spuštění testu.

5. **Vrstveného rozdělení pro vzorkování**: Tuto možnost vyberte, pokud je to důležité, že řádky v datové sadě by měl rovnoměrně rozdělit podle některé klíčové sloupce před vzorkování.

    Pro **sloupec klíče rozdělení pro vzorkování**, vyberte jeden *setkání strata sloupec* při dělení datové sady. Řádky v datové sadě pak dělí takto:

    1. Všechny vstupní řádky jsou seskupené podle hodnot ve sloupci zadaného setkání strata (vrstveného).

    2. V rámci jednotlivých skupin jsou které řádky se náhodně pomíchají.

    3. Každá skupina se selektivně přidá do výstupní datovou sadu pro splnění zadané poměr.


6. Spusťte experiment.

    Tato možnost Modul výstupy jedna datová sada, která obsahuje reprezentativní data. Zbývající, nevzorkovaných část datové sady není výstup. 

## <a name="split-data-into-partitions"></a>Rozdělení dat do oddílů

Tuto možnost použijte, pokud chcete rozdělit podmnožiny dat datové sady. Tato možnost je také užitečné, pokud chcete vytvořit vlastní počet složení pro křížové ověření nebo chcete rozdělit do několika skupin řádků.

1. Přidat **rozdělení a vzorky** modulu do svého experimentu a připojte se datová sada.

2. Pro **oddílu nebo režim vzorkování**vyberte **přiřadit složení**.

3. **Použití nahrazení v rozdělení**: Tuto možnost vyberte, pokud chcete, aby vzorky řádku, který má být mělo vrátit do fondu řádků pro potenciální opakované použití. Na stejném řádku v důsledku toho může být přiřazeno několik složení.

    Pokud nepoužijete nahrazení (výchozí možnost), není vzorky řádku znovu umístěny do fondu řádků pro potenciální opakované použití. Každý řádek v důsledku toho je možné přiřadit do jediného fold.

4. **Náhodná rozdělení**:  Tuto možnost vyberte, pokud chcete, aby řádků, který má být náhodně přiřadí k přeložení.

    Pokud tuto možnost nevyberete, řádky jsou přiřazeny k přeložení pomocí metody kruhové dotazování.

5. **Náhodné**: Volitelně můžete zadejte celé číslo, který se použije jako počáteční hodnoty. Je důležité, pokud chcete, aby řádků, které chcete rozdělit stejný způsobem pokaždé, když tuto možnost. V opačném případě výchozí hodnota 0 znamená, že náhodnou od počáteční hodnoty se použije.

6. **Zadejte metodu rozdělovač**: Určete, jak mají dat se rozdělí do jednotlivých oddílů, pomocí těchto možností:

    - **Rovnoměrně rozdělit**: Pomocí této možnosti umístíte rovna počtu řádků v každém oddílu. Chcete-li určit počet oddílů výstup, zadejte celé číslo v **zadejte počet složení rovnoměrně rozdělit** textového pole.

    - **Oddíl s vlastní rozměry**: Tuto možnost použijte k určení velikosti jednotlivých oddílů jako seznam oddělený čárkami.

        Například pokud chcete vytvořit tři oddíly, první oddíl obsahující 50 % dat a zbývající dva oddíly každý obsahující 25 % dat, klikněte **seznam proporce oddělené čárkou** textového pole a Zadejte tyto hodnoty: `.5, .25, .25`

        Součet všech velikostí oddíl musí přidat až přesně 1.

        - Pokud zadáte čísla, které přidají **menší než 1**, další oddíl vytvořená k uložení zbývajících řádků. Například pokud zadáte hodnoty od 2 a.3, třetí oddíl se vytvoří, který obsahuje zbývající 50 procent všech řádků.

        - Pokud zadáte čísla, které přidají **více než 1**, je vyvolána chyba při spuštění testu.

7. **Vrstveného rozdělení**: Tuto možnost vyberte, pokud chcete řádky, které mají být si při rozdělení a klikněte na tlačítko _setkání strata sloupec_.

8. Spusťte experiment.

    Pomocí této možnosti modulu vyprodukuje více datových sad, rozdělit na oddíly pomocí pravidel, které jste zadali.

### <a name="use-data-from-a-predefined-partition"></a>Používat data z předdefinovaných oddílu  

Tato možnost se používá, když mají datové sady rozdělit do několika oddílů a teď chcete načíst všechny oddíly zase k další analýze nebo zpracování.

1. Přidat **rozdělení a vzorky** modulů na experiment.

2. Připojit k výstupu předchozí instanci **rozdělení a vzorky**. Tato instance musí mít použita **přiřadit složení** možnost k vygenerování některých počet oddílů.

3. **Oddíl nebo ukázkový režim**: Vyberte **vyberte Fold**.

4. **Určete, které fold se odeberou z**: Vyberte oddíl a použít tak, že zadáte jeho index. Oddíl indexy jsou založené na 1. Například pokud je datové sady rozdělit do tří částí, oddíly by mít 1, 2 a 3 indexy.

    Pokud zadáte hodnotu neplatný index, je vyvolána chyba návrhu: "Chyba 0018: Datová sada obsahuje neplatná data."

    Kromě seskupení podle složení datovou sadu, můžete oddělit datové sady do dvou skupin: fold cíl a všechno ostatní. Chcete-li to provést, zadejte index jedné fold a potom vyberte možnost, **vyberte doplněk z vybraného fold**, chcete-li získat vše kromě dat v zadané fold.

5. Pokud pracujete s několika oddílů, je nutné přidat další instance **rozdělení a vzorky** modul pro zpracování jednotlivých oddílů.

    Například Řekněme, že dříve dělené pacientů do čtyř složení pomocí věku. Postup při každé jednotlivé fold potřebujete čtyři kopie **rozdělení a vzorky** modulu, a v každém, vyberete jiný fold, jak je znázorněno níže. Není správné použít **přiřadit složení** výstup přímo.  

    [![Rozdělení a vzorky](./media/partition-and-sample/partition-and-sample.png)](./media/partition-and-sample/partition-and-sample-lg.png#lightbox)

5. Spusťte experiment.

    Tato možnost Modul výstupy jedna datová sada obsahující pouze řádky, které jsou přiřazeny k této fáze.

> [!NOTE]
>  Označení fold nelze zobrazit přímo. jsou k dispozici pouze v metadatech.

## <a name="next-steps"></a>Další postup

Zobrazit [sada modulů, které jsou k dispozici](module-reference.md) do služby Azure Machine Learning. 