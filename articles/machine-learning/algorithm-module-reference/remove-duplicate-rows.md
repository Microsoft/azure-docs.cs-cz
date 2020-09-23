---
title: 'Odebrat duplicitní řádky: odkaz na modul'
titleSuffix: Azure Machine Learning
description: Naučte se, jak pomocí modulu odebrat duplicitní řádky v Azure Machine Learning odebrat možné duplicity z datové sady.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: bf35d08128aa8a3e8f545ed7184866694219f2cb
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90905221"
---
# <a name="remove-duplicate-rows-module"></a>Odebrat modul duplicitních řádků

Tento článek popisuje modul v Návrháři Azure Machine Learning.

Pomocí tohoto modulu můžete z datové sady odebrat možné duplicity.

Předpokládejme například, že vaše data vypadají jako následující a představují více záznamů pro pacienty. 

| PatientID | Iniciály| Pohlaví|Věk|Pustil|
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

V tomto příkladu je jasně uvedeno více sloupců s potenciálně duplicitními daty. Bez ohledu na to, jestli jsou ve skutečnosti duplicity, záleží na vašich znalostech dat. 

+ Například můžete zjistit, že mnoho pacientů má stejný název. Duplicity nebudete odstraňovat pomocí sloupců s názvy, a to pouze ve sloupci **ID** . Tímto způsobem jsou odfiltrovány pouze řádky s duplicitními hodnotami ID bez ohledu na to, zda mají pacienty stejný název.

+ Alternativně se můžete rozhodnout v poli ID k povolení duplicit a použít jinou kombinaci souborů k vyhledání jedinečných záznamů, jako je jméno, příjmení, věk a pohlaví.  

Chcete-li nastavit kritéria pro, zda je řádek duplicitní nebo nikoli, zadejte jeden sloupec nebo sadu sloupců, které mají být použity jako **klíče**. Dva řádky jsou považovány za duplicitní pouze v případě, že jsou hodnoty ve **všech** klíčových sloupcích stejné. Pokud některý z řádků postrádá hodnotu pro **klíče**, nepovažují se za duplicitní řádky. Pokud je například pohlaví a stáří nastavené jako klíče ve výše uvedené tabulce, řádky 6 a 7 nejsou duplicitní řádky, protože mají chybějící hodnotu v stáří.

Když spustíte modul, vytvoří se kandidátská datová sada a vrátí sadu řádků, které nemají žádné duplicity v rámci zadané sady sloupců.

> [!IMPORTANT]
> Zdrojová datová sada se nemění; Tento modul vytvoří novou datovou sadu, která je filtrována tak, aby vyloučila duplicity na základě kritérií, která zadáte.

## <a name="how-to-use-remove-duplicate-rows"></a>Použití funkce odebrat duplicitní řádky

1. Přidejte modul do svého kanálu. Modul **Odebrat duplicitní řádky** můžete najít v části **transformace dat**, **manipulace**.  

2. Připojte datovou sadu, u které chcete kontrolovat duplicitní řádky.

3. V podokně **vlastnosti** v části **výraz filtru výběru klíčového sloupce**klikněte na **Spustit selektor sloupců**a vyberte sloupce, které chcete použít při identifikaci duplicitních hodnot.

    V tomto kontextu **klíč** neznamená jedinečný identifikátor. Všechny sloupce, které vyberete pomocí selektoru sloupců, jsou označeny jako **klíčové sloupce**. Všechny sloupce bez výběru jsou považovány za neklíčové sloupce. Jedinečnost záznamů závisí na kombinaci sloupců, které vyberete jako klíče. (Můžete si ho představit jako příkaz SQL, který používá vícenásobné spojení se rovností.)

    Příklady:

    + "Chci zajistit, aby ID byla jedinečná": vyberte pouze sloupec ID.
    + "Chcete zajistit, aby byla kombinace jména, příjmení a ID jedinečná": vyberte všechny tři sloupce.

4. Chcete-li určit, který řádek, který se má vrátit, když budou nalezeny duplicity, použijte zaškrtávací políčko **zachovat první duplicitní řádek** :

    + Pokud je tato možnost vybrána, vrátí se první řádek a ostatní se zruší. 
    + Pokud zrušíte kontrolu této možnosti, bude poslední duplicitní řádek ve výsledcích uchováván a ostatní budou zahozeny. 

5. Odešlete kanál.

6. Chcete-li zkontrolovat výsledky, klikněte pravým tlačítkem na modul a vyberte **vizualizovat**. 

> [!TIP]
> Pokud jsou výsledky obtížné pochopit nebo pokud chcete vyloučit některé sloupce z úvah, můžete sloupce odebrat pomocí modulu [Výběr sloupců v datové sadě](./select-columns-in-dataset.md) .

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 