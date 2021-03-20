---
title: 'Oddíl a ukázka: odkaz na modul'
titleSuffix: Azure Machine Learning
description: Naučte se, jak pomocí oddílu a ukázkového modulu v Azure Machine Learning provést vzorkování na datové sadě nebo vytvořit oddíly z datové sady.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: a1f0a0dff4eb8a1aad0cd5e142959a636827a541
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "90898483"
---
# <a name="partition-and-sample-module"></a>Oddíl a vzorový modul

Tento článek popisuje modul v Návrháři Azure Machine Learning.

Pomocí oddílu a vzorového modulu můžete provést vzorkování pro datovou sadu nebo vytvořit oddíly z datové sady.

Vzorkování je důležitý nástroj ve strojovém učení, protože umožňuje zmenšit velikost datové sady a přitom zachovat stejný poměr hodnot. Tento modul podporuje několik souvisejících úloh, které jsou důležité ve strojovém učení: 

- Rozdělení dat do více dílčích částí stejné velikosti. 

  Oddíly můžete použít pro křížové ověřování nebo pro přiřazení případů k náhodným skupinám.

- Oddělení dat do skupin a následné práce s daty z konkrétní skupiny. 

  Až náhodně přiřadíte případy různým skupinám, budete možná muset upravit funkce, které jsou přidružené jenom k jedné skupině.

- Kontrol. 

  Můžete extrahovat procentuální podíl dat, použít náhodné vzorkování nebo zvolit sloupec, který se má použít k vyvážení datové sady a provádět vzorkování stratified na svých hodnotách.

- Vytváření menší datové sady pro testování. 

  Pokud máte velké množství dat, můžete při nastavování kanálu použít jenom prvních *n* řádků a pak při sestavování modelu přepnout na použití úplné datové sady. Vzorkování můžete také použít k vytvoření menší datové sady pro použití při vývoji.

## <a name="configure-the-module"></a>Konfigurace modulu

Tento modul podporuje následující metody, jak rozdělit data do oddílů nebo pro vzorkování. Zvolte nejprve metodu a pak nastavte další možnosti, které metoda vyžaduje.

- Head
- Vzorkování
- Přiřadit k skládání
- Vybrat skládání

### <a name="get-top-n-rows-from-a-dataset"></a>Získat HORNÍch N řádků z datové sady

Tento režim použijte k získání pouze prvních *n* řádků. Tato možnost je užitečná v případě, že chcete testovat kanál na malém počtu řádků a vy nepotřebujete, aby byla data vyrovnávána nebo odebírána jakýmkoli způsobem.

1. Přidejte **oddíl a vzorový** modul do kanálu v rozhraní a připojte datovou sadu.  

1. **Oddíl nebo vzorový režim**: nastavte tuto možnost na **head**.

1. **Počet řádků, které se mají vybrat**: zadejte počet řádků, které se mají vrátit.

   Počet řádků musí být nezáporné celé číslo. Pokud je počet vybraných řádků větší než počet řádků v datové sadě, vrátí se celá datová sada.

1. Odešlete kanál.

Modul výstupuje jedinou datovou sadu, která obsahuje pouze zadaný počet řádků. Řádky jsou vždy čteny z horní části datové sady.

### <a name="create-a-sample-of-data"></a>Vytvoření ukázkového data

Tato možnost podporuje jednoduché náhodné vzorkování nebo náhodné vzorkování stratified. Je užitečné, pokud chcete vytvořit menší reprezentativní ukázkovou datovou sadu pro testování.

1. Přidejte do svého kanálu **oddíl a vzorový** modul a připojte datovou sadu.

1. **Oddíl nebo vzorový režim**: nastavte tuto možnost na  **vzorkování**.

1. **Frekvence vzorkování**: zadejte hodnotu mezi 0 a 1. Tato hodnota určuje procento řádků ze zdrojové datové sady, které by měly být zahrnuty do výstupní datové sady.

   Pokud například potřebujete jenom polovinu původní datové sady, zadejte, `0.5` abyste označili, že vzorkovací frekvence by měla být 50%.

   Řádky vstupní datové sady jsou v souladu se zapsaným poměrem a selektivním umístěním do výstupní datové sady.

1. **Náhodný základ pro vzorkování**: Volitelně zadejte celé číslo, které se má použít jako počáteční hodnota.

   Tato možnost je důležitá, pokud chcete, aby byly řádky v každém okamžiku rozděleny stejným způsobem. Výchozí hodnota je **0**, což znamená, že počáteční počáteční hodnota se vygeneruje na základě systémových hodin. Tato hodnota může vést ke mírně odlišným výsledkům při každém spuštění kanálu.

1. **Stratified rozdělení pro vzorkování**: tuto možnost vyberte, pokud je důležité, aby řádky v datové sadě byly před vzorkováním rovnoměrně rozděleny podle sloupce klíče.

   Pro **sloupec Key rozvrstvení pro vzorkování** vyberte jeden *sloupec vrstvy* , který se použije při rozdělování datové sady. Řádky v datové sadě se pak rozdělí takto:

   1. Všechny vstupní řádky jsou seskupené (stratified) podle hodnot v zadaném sloupci vrstvy.

   1. Řádky jsou v rámci každé skupiny v rozmísené.

   1. Každá skupina je selektivně přidaná do výstupní datové sady, aby splňovala zadaný poměr.


1. Odešlete kanál.

   Pomocí této možnosti modul výstupuje jedinou datovou sadu, která obsahuje reprezentativní vzorkování dat. Zbývající nevzorkovaná část DataSet není ve výstupu. 

## <a name="split-data-into-partitions"></a>Rozdělit data na oddíly

Tuto možnost použijte, pokud chcete datovou sadu rozdělit na podmnožiny dat. Tato možnost je užitečná také v případě, že chcete vytvořit vlastní počet skládání pro křížové ověření, nebo rozdělit řádky do několika skupin.

1. Přidejte do svého kanálu **oddíl a vzorový** modul a připojte datovou sadu.

1. Pro **oddíl nebo vzorový režim** vyberte **přiřadit k skládání**.

1. **Použití náhrady při dělení**: tuto možnost vyberte, pokud chcete, aby byl vzorový řádek vrácen zpět do fondu řádků pro potenciální opakované použití. V důsledku toho může být stejný řádek přiřazen několika skládáními.

   Pokud nepoužijete náhradu (výchozí možnost), vzorový řádek se nevrátí zpět do fondu řádků pro potenciální opakované použití. Výsledkem je, že každý řádek lze přiřadit pouze k jednomu skládání.

1. **Náhodné rozdělení**: tuto možnost vyberte, pokud chcete, aby byly řádky náhodně přiřazeny k skládání.

   Pokud tuto možnost nevyberete, řádky se přiřadí k skládání prostřednictvím metody kruhového dotazování.

1. **Random seed**: Volitelně zadejte celé číslo, které se má použít jako hodnota počáteční hodnoty. Tato možnost je důležitá, pokud chcete, aby byly řádky v každém okamžiku rozděleny stejným způsobem. V opačném případě výchozí hodnota **0** znamená, že bude použito náhodné počáteční osivo.

1. **Zadejte metodu rozdělovače**: Určete, jak mají být data rozdělena do jednotlivých oddílů pomocí těchto možností:

   - **Rozdělit rovnoměrně**: tuto možnost použijte, pokud chcete umístit stejný počet řádků do každého oddílu. Chcete-li zadat počet výstupních oddílů, zadejte celé číslo v poli **Zadejte počet přeložení, které chcete rozdělit rovnoměrně do** boxu.

   - **Oddíl s přizpůsobenými poměry**: tuto možnost použijte, pokud chcete určit velikost každého oddílu jako seznam oddělený čárkami.

     Předpokládejme například, že chcete vytvořit tři oddíly. První oddíl bude obsahovat 50% dat. Zbývající dva oddíly budou obsahovat 25 procent dat. V poli se **seznamem portů, které jsou odděleny čárkou** , zadejte tyto hodnoty: **0,5,. 25,. 25**.

     Součet všech velikostí oddílů musí být přidán přesně na 1.

     Pokud zadáte čísla, která přidají *méně než 1*, vytvoří se další oddíl, který bude obsahovat zbývající řádky. Pokud například zadáte hodnoty **.2** a **.3**, vytvoří se třetí oddíl, který bude uchovávat zbývající 50% všech řádků.
     
     Pokud zadáte čísla, která se dají přidat až do *1*, při spuštění kanálu dojde k chybě.

1. **Stratified rozdělení**: tuto možnost vyberte, pokud chcete, aby se řádky Stratified při rozdělení, a pak vyberte _sloupec vrstvy_.

1. Odešlete kanál.

   Pomocí této možnosti modul výstupuje více datových sad. Datové sady jsou rozdělené podle pravidel, která jste zadali.

### <a name="use-data-from-a-predefined-partition"></a>Použití dat z předdefinovaného oddílu  

Tuto možnost použijte, pokud jste datovou sadu rozdělili do několika oddílů a teď chcete načíst jednotlivé oddíly, a pak provést další analýzu nebo zpracování.

1. Přidejte do kanálu **oddíl a vzorový** modul.

1. Připojte modul k výstupu předchozí instance **oddílu a vzorku**. Aby bylo možné vygenerovat určitý počet oddílů, musí tato instance používat možnost **přiřadit k skládání** .

1. **Oddíl nebo vzorový režim**: vyberte vybrat **skládání**.

1. **Určete, ze kterého skládání** se mají vzorkovat: vyberte oddíl, který se má použít, zadáním jeho indexu. Indexy oddílů jsou založené na 1. Například pokud jste datovou sadu rozdělili na tři části, oddíly budou mít indexy 1, 2 a 3.

   Pokud zadáte neplatnou hodnotu indexu, dojde k chybě v době návrhu: "Error 0018: DataSet obsahuje neplatná data."

   Kromě seskupení datové sady pomocí skládání lze datovou sadu Oddělit do dvou skupin: cílové skládání a všechno ostatní. To provedete tak, že zadáte index jednoho skládání a potom vyberete možnost **Vybrat doplněk vybraného skládání** a získáte vše, ale data v zadaném skládání.

1. Pokud pracujete s více oddíly, je nutné přidat další instance **oddílu a vzorového** modulu pro zpracování jednotlivých oddílů.

   Například **oddíl a vzorový** modul ve druhém řádku je nastaven na hodnotu **přiřadit k skládání** a modul ve třetím řádku je nastaven na **skládání výběru**.   

   ![Oddíl a ukázka](./media/module/partition-and-sample.png)

1. Odešlete kanál.

   Pomocí této možnosti modul výstupuje jedinou datovou sadu, která obsahuje pouze řádky přiřazené k tomuto přeložení.

> [!NOTE]
>  Nemůžete zobrazit označení skládání přímo. Jsou k dispozici pouze v metadatech.

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 