---
title: 'Rozdělit data: Odkaz na modul'
titleSuffix: Azure Machine Learning
description: Zjistěte, jak pomocí modulu Split Data v Azure Machine Learning rozdělit datovou sadu do dvou odlišných sad.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 9eba6f2c47629b708dde4a5a2888b76dbd24b4e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455889"
---
# <a name="split-data-module"></a>Modul Rozdělit data

Tento článek popisuje modul v návrháři Azure Machine Learning (preview).

Pomocí modulu Rozdělit data rozdělte datovou sadu na dvě odlišné sady.

Tento modul je užitečný, když potřebujete oddělit data do trénovacích a testovacích sad. Můžete také přizpůsobit způsob rozdělení dat. Některé možnosti podporují randomizaci dat. Jiné jsou přizpůsobeny pro určitý typ dat nebo typ modelu.

## <a name="configure-the-module"></a>Konfigurace modulu

> [!TIP]
> Než zvolíte režim rozdělení, přečtěte si všechny možnosti, abyste určili typ rozdělení, který potřebujete.
> Pokud změníte režim rozdělení, mohou být všechny ostatní možnosti resetovány.

1. Přidejte modul **Rozdělit data** do kanálu v návrháři. Tento modul najdete v části **Transformace dat**v kategorii **Ukázka a Rozdělení.**

1. **Režim rozdělení**: V závislosti na typu dat a způsobu jejich rozdělení zvolte jeden z následujících režimů. Každý režim rozdělení má jiné možnosti.

   - **Rozdělit řádky**: Tuto možnost použijte, pokud chcete data rozdělit na dvě části. Můžete určit procento dat, která se mají vložit do každého rozdělení. Ve výchozím nastavení jsou data rozdělena na 50/50.

     Můžete také náhodně vybrat řádky v každé skupině a použít stratifikované vzorkování. Při stratifikovaném vzorkování je nutné vybrat jeden sloupec dat, pro který mají být hodnoty rovnoměrně rozděleny mezi dvě datové sady výsledků.  

   - **Rozdělení regulárních výrazů**: Tuto možnost zvolte, pokud chcete rozdělit datovou sadu testováním hodnoty v jednom sloupci.

     Pokud například analyzujete mínění, můžete zkontrolovat přítomnost konkrétního názvu produktu v textovém poli. Potom můžete rozdělit datovou sadu do řádků s názvem cílového produktu a řádky bez názvu cílového produktu.

   - **Relativní rozdělení výrazu**: Tuto možnost použijte vždy, když chcete použít podmínku pro číselný sloupec. Číslem může být pole data a času, sloupec obsahující částky podle věku nebo v dolarech nebo dokonce procento. Můžete například rozdělit datovou sadu na základě nákladů na položky, skupiny osob podle věkových oblastí nebo samostatné ho data podle kalendářního data.

### <a name="split-rows"></a>Rozdělit řádky

1. Přidejte modul [Rozdělit data](./split-data.md) do kanálu v návrháři a připojte datovou sadu, kterou chcete rozdělit.
  
1. V **režimu Rozdělení**vyberte **rozdělit řádky**. 

1. **Zlomek řádků v první výstupní datové sadě**: Pomocí této možnosti určete, kolik řádků půjde do prvního výstupu (na levé straně). Všechny ostatní řádky přejdou do druhého (pravé strany) výstupu.

   Poměr představuje procento řádků odeslaných do první výstupní datové sady, takže je nutné zadat desetinné číslo mezi 0 a 1.
     
   Pokud například zadáte **hodnotu 0,75,** bude datová sada rozdělena na 75/25. V tomto rozdělení bude 75 procent řádků odesláno do první výstupní datové sady. Zbývajících 25 procent bude odesláno do druhé výstupní datové sady.
  
1. Pokud chcete do obou skupin náhodně vybrat možnost **Randomizované rozdělení.** Toto je upřednostňovaná možnost při vytváření trénovacích a testovacích datových sad.

1. **Náhodné osiva**: Zadejte nezápornou celočíselnou hodnotu pro spuštění pseudonáhodné sekvence instancí, které mají být použity. Toto výchozí osivo se používá ve všech modulech, které generují náhodná čísla. 

   Zadání osiva způsobí, že výsledky budou reprodukovatelné. Pokud potřebujete opakovat výsledky operace rozdělení, měli byste zadat osiva generátoru náhodných čísel. V opačném případě je náhodné osiva ve výchozím nastavení nastaveno na **hodnotu 0**, což znamená, že počáteční hodnota osiva je získána ze systémových hodin. V důsledku toho může být distribuce dat mírně odlišná při každém rozdělení. 

1. **Stratifikované rozdělení**: Nastavte tuto možnost na **Hodnotu True,** abyste zajistili, že dvě výstupní datové sady obsahují reprezentativní vzorek hodnot ve *sloupci strata* nebo *ve sloupci stratifikačního klíče*. 

   Při stratifikovaném vzorkování jsou data rozdělena tak, že každá výstupní datová sada získá přibližně stejné procento každé cílové hodnoty. Můžete například chtít zajistit, aby vaše tréninkové a testovací sady byly zhruba vyvážené s ohledem na výsledek nebo na jiný sloupec (například pohlaví).

1. Odešlete potrubí.


## <a name="select-a-regular-expression"></a>Výběr regulárního výrazu

1. Přidejte modul [Rozdělit data](./split-data.md) do kanálu a připojte jej jako vstup k datové sadě, kterou chcete rozdělit.  
  
1. V **režimu Rozdělení**vyberte **možnost Rozdělení regulárního výrazu**.

1. Do pole **Regulární výraz** zadejte platný regulární výraz. 
  
   Regulární výraz by měl pro regulární výrazy postupovat podle syntaxe Pythonu.

1. Odešlete potrubí.

   Na základě zadaný regulárního výrazu je datová sada rozdělena do dvou sad řádků: řádky s hodnotami, které odpovídají výrazu, a všechny zbývající řádky. 

Následující příklady ukazují, jak rozdělit datovou sadu pomocí možnosti **Regulární výraz.** 

### <a name="single-whole-word"></a>Celé jedno slovo 

Tento příklad vloží do první datové sady `Gryphon` všechny řádky, které obsahují text ve sloupci `Text`. Vloží další řádky do druhého výstupu **Split Data**.

```text
    \"Text" Gryphon  
```

### <a name="substring"></a>Podřetězec

Tento příklad hledá zadaný řetězec v libovolné pozici v rámci druhého sloupce datové sady. Pozice je zde označena hodnotou indexu 1. Shoda rozlišuje malá a velká písmena.

```text
(\1) ^[a-f]
```

První datová sada výsledků obsahuje všechny řádky, ve kterých `a` `b`sloupec `c` `d`rejstříku začíná jedním z těchto znaků: , , , , `e`. `f` Všechny ostatní řádky jsou směrovány na druhý výstup.

## <a name="select-a-relative-expression"></a>Výběr relativního výrazu

1. Přidejte modul [Rozdělit data](./split-data.md) do kanálu a připojte jej jako vstup k datové sadě, kterou chcete rozdělit.
  
1. V **režimu rozdělení**vyberte **relativní výraz**.
  
1. Do pole **Relační výraz** zadejte výraz, který provádí operaci porovnání na jednom sloupci.

   Pro **číselný sloupec**:
   - Sloupec obsahuje čísla libovolného číselného datového typu, včetně datových typů data a času.
   - Výraz může odkazovat maximálně na jeden název sloupce.
   - Pro operaci AND použijte `&`znak ampersand , , . Použijte znak kanálu `|`, pro operaci OR.
   - Podporovány jsou následující `<` `>`operátory: , `<=`, , `>=` `==`, . `!=`.
   - Operace nelze seskupit pomocí `(` a . `)`
   
   Pro **sloupec String**:
   - Podporovány jsou následující `==` `!=`operátory: , .

1. Odešlete potrubí.

   Výraz rozděluje datovou sadu na dvě sady řádků: řádky s hodnotami, které splňují podmínku, a všechny zbývající řádky.

Následující příklady ukazují, jak rozdělit datovou sadu pomocí **možnosti Relativní výraz** v modulu **Rozdělit data.**  

### <a name="calendar-year"></a>Kalendářní rok

Běžným scénářem je rozdělení datové sady podle let. Následující výraz vybere všechny řádky, ve `Year` kterých `2010`jsou hodnoty ve sloupci větší než .

```text
\"Year" > 2010
```

Výraz data musí odpovídat všem částem kalendářních dat, které jsou zahrnuty ve sloupci dat. Formát kalendářních dat ve sloupci dat musí být konzistentní. 

Například ve sloupci data, `mmddyyyy`který používá formát , by měl být výraz přibližně takto:

```text
\"Date" > 1/1/2010
```

### <a name="column-index"></a>Index sloupce

Následující výraz ukazuje, jak můžete pomocí indexu sloupce vybrat všechny řádky v prvním sloupci datové sady, které obsahují hodnoty menší nebo rovné 30, ale nerovno 20.

```text
(\0)<=30 & !=20
```


## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 
