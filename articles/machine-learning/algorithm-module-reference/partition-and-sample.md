---
title: 'Oddíl a ukázka: Odkaz na modul'
titleSuffix: Azure Machine Learning service
description: Naučte se používat oddíl a vzorový modul ve službě Azure Machine Learning, abyste mohli provádět vzorkování na datové sadě nebo vytvářet oddíly z vaší datové sady.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 79cd6fe9156a785d82e303007d02ce58506dcfcf
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128552"
---
# <a name="partition-and-sample-module"></a>Oddíl a vzorový modul

Tento článek popisuje modul vizuálního rozhraní (Preview) pro službu Azure Machine Learning.

Tento modul použijte k provedení vzorkování pro datovou sadu nebo k vytvoření oddílů z datové sady.

Vzorkování je důležitý nástroj ve strojovém učení, protože umožňuje zmenšit velikost datové sady a přitom zachovat stejný poměr hodnot. Tento modul podporuje několik souvisejících úloh, které jsou důležité ve strojovém učení: 

- Rozdělení dat do více dílčích částí stejné velikosti. 

    Oddíly můžete použít pro křížové ověřování nebo pro přiřazení případů k náhodným skupinám.

- Oddělení dat do skupin a následné práce s daty z konkrétní skupiny. 

    Po náhodném přiřazení případů do různých skupin budete možná muset upravit funkce, které jsou přidružené jenom k jedné skupině.

- Kontrol. 

    Můžete extrahovat procentuální podíl dat, použít náhodné vzorkování nebo zvolit sloupec, který se má použít k vyvážení datové sady a provádět vzorkování stratified na svých hodnotách.

- Vytváření menší datové sady pro testování. 

    Pokud máte velké množství dat, můžete při nastavování experimentu použít jenom prvních *n* řádků a pak při sestavování modelu přepnout na použití úplné datové sady. Vzorkování můžete také použít k vytvoření s menší datovou sadou pro použití při vývoji.

## <a name="configure-partition-and-sample"></a>Konfigurace oddílu a vzorku

Tento modul podporuje více metod, jak rozdělit data do oddílů nebo pro vzorkování. Zvolte nejprve metodu a pak nastavte další možnosti vyžadované metodou.

- Hlavní
- Vzorkování
- Přiřadit k skládání
- Vybrat skládání

### <a name="get-top-n-rows-from-a-dataset"></a>Získat HORNÍch N řádků z datové sady

Tento režim použijte k získání pouze prvních *n* řádků. Tato možnost je užitečná, pokud chcete otestovat experiment na malém počtu řádků a nepotřebujete, aby data byla vyrovnávána nebo odebírána jakýmkoli způsobem.

1. Přidejte **oddíl a vzorový** modul do svého experimentu do rozhraní a připojte datovou sadu.  

2. **Oddíl nebo vzorový režim**: Nastavte tuto možnost na **head**.

3. **Počet řádků, které se mají vybrat**: Zadejte počet řádků, které se mají vrátit.

    Počet řádků, které zadáte, musí být nezáporné celé číslo. Pokud je počet vybraných řádků větší než počet řádků v datové sadě, vrátí se celá datová sada.

4. Spusťte experiment.

Modul výstupuje jedinou datovou sadu obsahující pouze zadaný počet řádků. Řádky jsou vždy čteny z horní části datové sady.

### <a name="create-a-sample-of-data"></a>Vytvoření ukázkového data

Tato možnost podporuje jednoduché náhodné vzorkování nebo náhodné vzorkování stratified. To je užitečné, pokud chcete vytvořit menší reprezentativní ukázkovou datovou sadu pro testování.

1. Přidejte do experimentu **oddíl a vzorový** modul a připojte datovou sadu.

2. **Oddíl nebo vzorový režim**: Nastavte tuto hodnotu na **vzorkování**.

3. **Frekvence vzorkování**: Zadejte hodnotu mezi 0 a 1. Tato hodnota určuje procento řádků ze zdrojové datové sady, které by měly být zahrnuty do výstupní datové sady.

    Pokud například potřebujete jenom polovinu původní datové sady, zadejte `0.5` , že chcete, aby frekvence vzorkování byla 50%.

    Řádky vstupní datové sady jsou v režimu bez omezení a selektivně vloženy do výstupní datové sady podle zadaného poměru.

4. **Náhodné osazení pro vzorkování**: V případě potřeby zadejte celé číslo, které se má použít jako počáteční hodnota.

    Tato možnost je důležitá, pokud chcete, aby byly řádky v každém okamžiku rozděleny stejným způsobem. Výchozí hodnota je 0, což znamená, že počáteční počáteční hodnota se vygeneruje na základě systémových hodin. To může vést ke vzniku mírně odlišných výsledků pokaždé, když spustíte experiment.

5. **Stratified rozdělení pro vzorkování**: Tuto možnost vyberte, pokud je důležité, aby řádky v datové sadě byly před vzorkováním rovnoměrně rozděleny podle sloupce klíče.

    Pro **sloupec Key rozvrstvení pro vzorkování**vyberte jeden *sloupec vrstvy* , který se použije při rozdělování datové sady. Řádky v datové sadě se pak rozdělí takto:

    1. Všechny vstupní řádky jsou seskupené (stratified) podle hodnot v zadaném sloupci vrstvy.

    2. Řádky jsou v rámci každé skupiny v rozmísené.

    3. Každá skupina je selektivně přidaná do výstupní datové sady, aby splňovala zadaný poměr.


6. Spusťte experiment.

    Pomocí této možnosti modul výstupuje jedinou datovou sadu, která obsahuje reprezentativní vzorkování dat. Zbývající nevzorkovaná část DataSet není ve výstupu. 

## <a name="split-data-into-partitions"></a>Rozdělit data na oddíly

Tuto možnost použijte, pokud chcete datovou sadu rozdělit na podmnožiny dat. Tato možnost je užitečná také v případě, že chcete vytvořit vlastní počet skládání pro křížové ověření, nebo rozdělit řádky do několika skupin.

1. Přidejte do experimentu **oddíl a vzorový** modul a připojte datovou sadu.

2. Pro **oddíl nebo vzorový režim**vyberte **přiřadit k skládání**.

3. **Použití náhrady při dělení**: Tuto možnost vyberte, pokud chcete, aby byl vzorový řádek vrácen zpět do fondu řádků pro potenciální opakované použití. V důsledku toho může být stejný řádek přiřazen několika skládáními.

    Pokud nepoužijete náhradu (výchozí možnost), vzorový řádek se nevrátí zpět do fondu řádků pro potenciální opakované použití. Výsledkem je, že každý řádek lze přiřadit pouze k jednomu skládání.

4. **Náhodné rozdělení**:  Tuto možnost vyberte, pokud chcete, aby byly řádky náhodně přiřazeny k skládání.

    Pokud tuto možnost nevyberete, řádky se přiřadí k skládání pomocí metody kruhového dotazování.

5. **Náhodné osazení**: Volitelně zadejte celé číslo, které se má použít jako hodnota počáteční hodnoty. Tato možnost je důležitá, pokud chcete, aby byly řádky v každém okamžiku rozděleny stejným způsobem. V opačném případě výchozí hodnota 0 znamená, že bude použito náhodné počáteční osivo.

6. **Zadejte metodu rozdělovače**: Určete, jak chcete rozdělit data na jednotlivé oddíly, a to pomocí těchto možností:

    - **Rozdělit rovnoměrně**: Tuto možnost použijte k umístění stejného počtu řádků do každého oddílu. Chcete-li zadat počet výstupních oddílů, zadejte celé číslo v poli **Zadejte počet přeložení, které chcete rozdělit rovnoměrně do** textového pole.

    - **Oddíl s**přizpůsobenými poměry: Tuto možnost použijte, chcete-li určit velikost každého oddílu jako seznam oddělený čárkami.

        Pokud například chcete vytvořit tři oddíly s prvním oddílem obsahujícím 50% dat a zbývající dva oddíly, které obsahují 25% dat, klikněte na pole se **seznamem proporcí oddělené čárkami** a zadejte tato čísla:`.5, .25, .25`

        Součet všech velikostí oddílů musí být přidán přesně na 1.

        - Pokud zadáte čísla, která přidají **méně než 1**, vytvoří se další oddíl, který bude obsahovat zbývající řádky. Pokud například zadáte hodnoty .2 a .3, vytvoří se třetí oddíl, který obsahuje zbývající 50% všech řádků.

        - Pokud zadáte čísla, která se dají přidat až do **1**, při spuštění experimentu se vyvolá chyba.

7. **Stratified rozdělení**: Tuto možnost vyberte, pokud chcete, aby se řádky stratified při rozdělení, a pak vyberte _sloupec vrstvy_.

8. Spusťte experiment.

    Pomocí této možnosti modul výstupuje více datových sad, které jsou rozdělené podle zadaných pravidel.

### <a name="use-data-from-a-predefined-partition"></a>Použití dat z předdefinovaného oddílu  

Tato možnost se používá, pokud jste rozdělili datovou sadu do několika oddílů a teď chcete načíst jednotlivé oddíly, a dále analyzovat nebo zpracovávat.

1. Přidejte do experimentu **oddíl a vzorový** modul.

2. Připojte ho k výstupu předchozí instance **oddílu a vzorku**. Aby bylo možné vygenerovat určitý počet oddílů, musí tato instance používat možnost **přiřadit k skládání** .

3. **Oddíl nebo vzorový režim**: Vyberte vybrat **skládání**.

4. **Určete, ze kterého skládání**se mají vzorkovat: Vyberte oddíl, který chcete použít, zadáním jeho indexu. Indexy oddílů jsou založené na 1. Například pokud jste datovou sadu rozdělili na tři části, oddíly budou mít indexy 1, 2 a 3.

    Pokud zadáte neplatnou hodnotu indexu, dojde k chybě v době návrhu: "Chyba 0018: Datová sada obsahuje neplatná data. "

    Kromě seskupení datové sady pomocí skládání lze datovou sadu Oddělit do dvou skupin: cílové skládání a všechno ostatní. Provedete to tak, že zadáte index jednoho skládání a potom vyberete možnost, vyberete **doplněk vybrané skládání**a získáte vše, ale data v zadaném skládání.

5. Pokud pracujete s více oddíly, je nutné přidat další instance **oddílu a vzorový** modul pro zpracování jednotlivých oddílů.

    Řekněme například, že dříve dělené pacienty do čtyř přeložení pomocí stáří. Chcete-li pracovat s jednotlivými skládáními, potřebujete čtyři kopie **oddílu a vzorového** modulu a v každém z nich vyberete jiné skládání, jak je uvedeno níže. Není správné použít přímo výstup **pro přiřazení k skládání** .  

    [![Oddíl a ukázka](./media/partition-and-sample/partition-and-sample.png)](./media/partition-and-sample/partition-and-sample-lg.png#lightbox)

5. Spusťte experiment.

    Pomocí této možnosti modul výstupuje jedinou datovou sadu obsahující pouze řádky přiřazené k tomuto přeložení.

> [!NOTE]
>  Nemůžete zobrazit označení skládání přímo; jsou k dispozici pouze v metadatech.

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning služby. 