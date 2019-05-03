---
title: 'Odebere duplicitní řádky: Odkaz na modul'
titleSuffix: Azure Machine Learning service
description: Další informace o použití modulu odebrat duplicitní řádky ve službě Azure Machine Learning k odebrání potenciální duplicity z datové sady.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: dce90d911085c1f7330a2e0952bb9576c1d765fa
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029292"
---
# <a name="remove-duplicate-rows-module"></a>Odebrání modulu duplicitní řádky

Tento článek popisuje modulu rozhraní visual (preview) pro službu Azure Machine Learning.

Tento modul slouží k odebrání potenciální duplicity z datové sady.

Předpokládejme například, vaše data vypadá takto a představuje více záznamy o pacientech. 

| PatientID | Iniciály| Pohlaví|Věk|Přijaty|
|----|----|----|----|----|
|1|F.M.| M| 53| Led|
|2| F.A.M.| M| 53| Led|
|3| F.A.M.| M| 24| Led|
|3| F.M.| M| 24| Úno|
|4| F.M.| M| 23| Úno|
| | F.M.| M| 23| |
|5| F.A.M.| M| 53| |
|6| F.A.M.| M| NaN| |
|7| F.A.M.| M| NaN| |

Tento příklad jasně, má více sloupců s potenciálně duplicitní data. Určuje, zda jsou ve skutečnosti duplicitní závisí na svoje znalosti v oblasti data. 

+ Například je možné, že, že mnoho pacientů mají stejný název. Nebude vyloučení duplicit pomocí libovolný název sloupce, pouze **ID** sloupce. Tímto způsobem pouze řádky s duplicitními hodnotami ID, budou odfiltrovány, bez ohledu na to, zda pacientů mají stejný název, nebo ne.

+ Alternativně můžete se rozhodnout povolit vynechávání duplicit v poli ID a použití jiných kombinací soubory najít jedinečnou záznamů, například křestní jméno, název, stáří a poslední pohlaví.  

Chcete-li nastavit kritéria pro řádek je duplicitní nebo Ne, zadáte jeden sloupec nebo sadu sloupců, který se použije jako **klíče**. Dva řádky jsou považována za duplicitní pouze tehdy, když hodnoty v **všechny** klíčových sloupců jsou si rovny. Pokud chybí hodnota pro všechny řádky **klíče**, považují se za duplicitní řádky. Například pokud pohlaví nebo věku jsou nastavené klíče ve výše uvedené tabulky, řádek 6 a 7 nejsou uvedeny duplicitní řádky mají chybí hodnota v věku.

Při spuštění modulu vytvoří datovou sadu Release candidate a vrátí sadu řádků, které se mají žádné duplikáty sady sloupců, které jste zadali.

> [!IMPORTANT]
> Zdrojová datová sada se nezmění, Tento modul se vytvoří nová datová sada s filtrem k vyloučení duplicity, na základě kritérií, které zadáte.

## <a name="how-to-use-remove-duplicate-rows"></a>Jak používat odebrat duplicitní řádky

1. Přidáte modul do experimentu. Můžete najít **odebrat duplicitní řádky** modul v rámci **transformace dat**, **manipulaci s**.  

2. Připojte se datová sada, kterou chcete zkontrolovat duplicitní řádky.

3. V **vlastnosti** podokně v části **klíče výraz filtru pro výběr sloupce**, klikněte na tlačítko **spustit selektor sloupců**, vybrat sloupce, které chcete použít při identifikaci duplicitní položky.

    V tomto kontextu **klíč** neznamená jedinečný identifikátor. Všechny sloupce, které jste vybrali, přes výběr sloupců jsou určené jako **klíčové sloupce**. Všechny tak sloupce, které jsou považovány za neklíčových sloupců. Kombinace sloupců, které jste vybrali jako klíče určuje jedinečnost záznamy. (Si ho představit jako příkaz SQL, který používá více equalities spojení.)

    Příklady:

    + "Chceme se ujistit, že jsou jedinečné ID": Zvolte pouze sloupec ID.
    + "Chceme se ujistit, že je jedinečné kombinace křestního jména, příjmení a ID": Vyberte všechny tři sloupce.

4. Použití **první řádek duplicitní zachovat** zaškrtávací políčko a označit řádek, který se má vrátit v případě, že jsou nalezeny duplicitní hodnoty:

    + Pokud vybraná, vrátí první řádek a ostatní zahozeny. 
    + Pokud zrušíte zaškrtnutí této možnosti, poslední řádek duplicitní se ukládají ve výsledcích a ostatní jsou zahozeny. 

5. Spusťte experiment.

6. Chcete-li zkontrolovat výsledky kontroly, klikněte pravým tlačítkem na modul, vyberte **datovou sadu výsledků**a klikněte na tlačítko **vizualizovat**. 

> [!TIP]
> Pokud výsledky jsou těžko pochopitelné, nebo pokud chcete vyloučit některé sloupce v úvahu, můžete odebrat sloupce pomocí [výběr sloupců v datové sadě](./select-columns-in-dataset.md) modulu.

## <a name="next-steps"></a>Další postup

Zobrazit [sada modulů, které jsou k dispozici](module-reference.md) do služby Azure Machine Learning. 