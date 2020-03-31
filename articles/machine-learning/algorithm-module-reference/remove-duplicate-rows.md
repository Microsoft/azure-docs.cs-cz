---
title: 'Odstranit duplicitní řádky: Odkaz na modul'
titleSuffix: Azure Machine Learning
description: Zjistěte, jak pomocí modulu Odebrat duplicitní řádky v Azure Machine Learning odebrat potenciální duplikáty z datové sady.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 490d3305abcbcd906a0f727d736db8cab7e4287e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456017"
---
# <a name="remove-duplicate-rows-module"></a>Odstranit modul Duplicitní řádky

Tento článek popisuje modul v návrháři Azure Machine Learning (preview).

Tento modul slouží k odebrání potenciálních duplikátů z datové sady.

Předpokládejme například, že vaše data vypadají takto a představují více záznamů pro pacienty. 

| ID pacienta | Iniciály| Pohlaví|Věk|Přijaty|
|----|----|----|----|----|
|1|F.M.| M| 53| Jan|
|2| F.A.M.| M| 53| Jan|
|3| F.A.M.| M| 24| Jan|
|3| F.M.| M| 24| Feb|
|4| F.M.| M| 23| Feb|
| | F.M.| M| 23| |
|5| F.A.M.| M| 53| |
|6| F.A.M.| M| Není číslo| |
|7| F.A.M.| M| Není číslo| |

Je zřejmé, že tento příklad má více sloupců s potenciálně duplicitní data. Zda se skutečně jedná o duplikáty, závisí na vaší znalosti dat. 

+ Například, možná víte, že mnoho pacientů má stejný název. Neodstranili byste duplikáty pomocí sloupců názvů, pouze sloupce **ID.** Tímto způsobem jsou odfiltrovány pouze řádky s duplicitními hodnotami ID, bez ohledu na to, zda mají pacienti stejný název nebo ne.

+ Případně se můžete rozhodnout povolit duplikáty v poli ID a použít jinou kombinaci souborů k vyhledání jedinečných záznamů, například křestního jména, příjmení, věku a pohlaví.  

Chcete-li nastavit kritéria pro to, zda je řádek duplicitní či nikoli, zadejte jeden sloupec nebo sadu sloupců, které mají být **použity**jako klíče . Dva řádky jsou považovány za duplicitní pouze v případě, že hodnoty ve **všech** klíčových sloupcích jsou stejné. Pokud některý řádek má chybějící hodnotu pro **klíče**, nebudou považovány za duplicitní řádky. Pokud jsou například pohlaví a věk nastaveny jako klíče ve výše uvedené tabulce, řádek 6 a 7 nejsou duplicitní řádky vzhledem k tomu, že mají chybějící hodnotu v Age.

Při spuštění modulu vytvoří datovou sadu kandidátů a vrátí sadu řádků, které nemají žádné duplikáty v celé sadě sloupců, které jste zadali.

> [!IMPORTANT]
> Zdrojová datová sada se nezmění. Tento modul vytvoří novou datovou sadu, která je filtrována tak, aby vyloučila duplikáty, na základě zadaných kritérií.

## <a name="how-to-use-remove-duplicate-rows"></a>Použití použití funkce Odstranit duplicitní řádky

1. Přidejte modul do kanálu. Modul Odstranit **duplicitní řádky** najdete v části **Transformace dat**, **Manipulace**.  

2. Připojte datovou sadu, kterou chcete zkontrolovat pro duplicitní řádky.

3. V podokně **Vlastnosti** klikněte v části **Výraz filtru výběru sloupce klíče**na **selekátor sloupce spuštění**a vyberte sloupce, které chcete použít při identifikaci duplikátů.

    V tomto kontextu **Key** neznamená jedinečný identifikátor. Všechny sloupce, které vyberete pomocí voliče sloupců, jsou **označeny**jako klíčové sloupce . Všechny nevybrané sloupce jsou považovány za sloupce, které nejsou klíčové. Kombinace sloupců, které vyberete jako klíče, určuje jedinečnost záznamů. (Představte si to jako příkaz SQL, který používá více spojení rovnosti.)

    Příklady:

    + "Chci zajistit, aby ID byla jedinečná": Zvolte pouze sloupec ID.
    + "Chci zajistit, aby kombinace křestního jména, příjmení a ID byla jedinečná": Vyberte všechny tři sloupce.

4. Pomocí zaškrtávacího políčka **Zachovat první duplicitní řádek** označte, který řádek má být vrácen při nalezení duplicity:

    + Pokud je tato možnost vybrána, vrátí se první řádek a ostatní budou zahozeny. 
    + Pokud zrušíte zaškrtnutí této možnosti, poslední duplicitní řádek je zachován ve výsledcích a ostatní jsou zahozeny. 

5. Odešlete potrubí.

6. Chcete-li zkontrolovat výsledky, klepněte pravým tlačítkem myši na modul a vyberte **možnost Vizualizovat**. 

> [!TIP]
> Pokud jsou výsledky obtížně pochopitelné nebo pokud chcete vyloučit některé sloupce z úvahy, můžete sloupce odebrat pomocí modulu [Vybrat sloupce v datové sadě.](./select-columns-in-dataset.md)

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 