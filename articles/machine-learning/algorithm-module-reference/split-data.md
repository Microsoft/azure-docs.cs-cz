---
title: 'Rozdělit data: odkaz na modul'
titleSuffix: Azure Machine Learning
description: Naučte se používat modul rozdělit data v Azure Machine Learning k rozdělení datové sady do dvou různých sad.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: a4c93b12ad654e54a7f3c7ee0e75507d5cb45e90
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "90907814"
---
# <a name="split-data-module"></a>Modul rozdělení dat

Tento článek popisuje modul v Návrháři Azure Machine Learning.

Použijte modul rozdělit data, chcete-li datovou sadu rozdělit do dvou různých sad.

Tento modul je užitečný v případě, že potřebujete oddělit data do sad pro školení a testování. Můžete také přizpůsobit způsob, jakým jsou data rozdělena. Některé možnosti podporují náhodnost dat. Jiné jsou přizpůsobené pro určitý typ dat nebo model.

## <a name="configure-the-module"></a>Konfigurace modulu

> [!TIP]
> Než zvolíte režim rozdělování, přečtěte si všechny možnosti a určete typ rozdělení, který potřebujete.
> Pokud změníte rozdělovací režim, všechny ostatní možnosti se můžou resetovat.

1. Přidejte modul **rozdělit data** do kanálu v návrháři. Tento modul můžete najít v části **transformace dat** v kategorii **Ukázka a rozdělení** .

1. **Režim rozdělení**: v závislosti na typu dat a způsobu, jakým je chcete rozdělit, vyberte jeden z následujících režimů. Každý režim rozdělování má různé možnosti.

   - **Rozdělit řádky**: tuto možnost použijte, pokud chcete pouze rozdělit data do dvou částí. Můžete určit procento dat, která se mají vložit do každého rozdělení. Ve výchozím nastavení jsou data rozdělena 50/50.

     Můžete také náhodně vybrat řádky v každé skupině a použít vzorkování stratified. V stratified vzorkování musíte vybrat jeden sloupec dat, pro který chcete hodnoty rozdělit rovnoměrně mezi dvě datové sady výsledků.  

   - **Rozdělení regulárního výrazu**: tuto možnost vyberte, pokud chcete datovou sadu rozdělit tak, že otestujete jeden sloupec s hodnotou.

     Pokud například analyzujete mínění, můžete zjistit přítomnost konkrétního názvu produktu v textovém poli. Datovou sadu pak můžete rozdělit do řádků s cílovým názvem produktu a řádky bez názvu cílového produktu.

   - **Rozdělení relativního výrazu**: tuto možnost použijte vždy, když chcete použít podmínku pro sloupec s čísly. Toto číslo může být pole Datum a čas, sloupec, který obsahuje stáří nebo částku dolaru, nebo dokonce i procento. Můžete například chtít rozdělit datovou sadu na základě nákladů na položky, seskupit lidi podle věkových rozsahů nebo rozdělit data podle kalendářního data.

### <a name="split-rows"></a>Rozdělit řádky

1. Přidejte modul [rozdělit data](./split-data.md) do kanálu v návrháři a připojte datovou sadu, kterou chcete rozdělit.
  
1. V případě **rozdělení režimu** vyberte **rozdělit řádky**. 

1. **Zlomek řádků v první výstupní sadě dat**: pomocí této možnosti určíte, kolik řádků bude přejít na první (levou stranu) výstup. Všechny ostatní řádky budou umístěny do druhého výstupu (na pravé straně).

   Poměr představuje procento řádků odeslaných na první výstupní datovou sadu, takže je nutné zadat desetinné číslo od 0 do 1.
     
   Pokud například zadáte **0,75** jako hodnotu, datová sada bude rozdělena 75/25. V tomto rozdělení se 75 procento řádků pošle na první výstupní datovou sadu. Zbývající 25 procent se pošle druhé výstupní datové sadě.
  
1. Vyberte možnost **náhodného rozdělení** , pokud chcete náhodně vybrat data do dvou skupin. Toto je upřednostňovaná možnost při vytváření školicích a testovacích datových sad.

1. **Random seed**: zadejte nezáporné celé číslo, pokud chcete spustit pseudonáhodných sekvenci instancí, které se mají použít. Toto výchozí osivo se používá ve všech modulech, které generují náhodná čísla. 

   Zadání počáteční hodnoty způsobí, že výsledky budou reprodukovatelné. Pokud potřebujete výsledky operace rozdělení zopakovat, měli byste pro generátor náhodných čísel zadat počáteční hodnotu. V opačném případě je náhodné osazení nastaveno na **hodnotu 0**, což znamená, že počáteční počáteční hodnota se získá ze systémových hodin. V důsledku toho může být distribuce dat mírně odlišná pokaždé, když provedete rozdělení. 

1. **Stratified Split**: tuto možnost nastavte na **hodnotu true** , chcete-li zajistit, aby dvě výstupní datové sady obsahovaly reprezentativní vzorek hodnot ve *sloupci vrstvy* nebo ve *sloupci klíč rozdělení* na více úrovní. 

   V případě vzorkování stratified jsou data rozdělena tak, že každá výstupní datová sada získá zhruba stejné procento každé cílové hodnoty. Například můžete chtít zajistit, aby vaše školicí a testovací sady byly zhruba vyvážené s ohledem na výsledek nebo do jiného sloupce (například pohlaví).

1. Odešlete kanál.


## <a name="select-a-regular-expression"></a>Vybrat regulární výraz

1. Přidejte do svého kanálu modul [rozdělení dat](./split-data.md) a připojte ho jako vstup k datové sadě, kterou chcete rozdělit.  
  
1. V případě **rozdělení režimu** vyberte možnost **rozdělení regulárního výrazu**.

1. V poli **regulární výraz** zadejte platný regulární výraz. 
  
   Regulární výraz by měl následovat za syntaxí Pythonu pro regulární výrazy.

1. Odešlete kanál.

   Na základě regulárního výrazu, který zadáte, je datová sada rozdělena do dvou sad řádků: řádků s hodnotami, které odpovídají výrazu a všem zbývajícím řádkům. 

Následující příklady ukazují, jak rozdělit datovou sadu pomocí možnosti **regulárního výrazu** . 

### <a name="single-whole-word"></a>Jedno celé slovo 

Tento příklad vloží do první datové sady všechny řádky, které obsahují text `Gryphon` ve sloupci `Text` . Vloží další řádky do druhého výstupu **rozdělených dat**.

```text
    \"Text" Gryphon  
```

### <a name="substring"></a>Podřetězec

Tento příklad hledá zadaný řetězec na libovolné pozici v druhém sloupci datové sady. Pozice je zde označena hodnotou indexu 1. Porovnávání rozlišuje velká a malá písmena.

```text
(\1) ^[a-f]
```

První výsledná datová sada obsahuje všechny řádky, ve kterých indexový sloupec začíná jedním z těchto znaků: `a` , `b` , `c` , `d` , `e` , `f` . Všechny ostatní řádky jsou směrovány na druhý výstup.

## <a name="select-a-relative-expression"></a>Výběr relativního výrazu

1. Přidejte do svého kanálu modul [rozdělení dat](./split-data.md) a připojte ho jako vstup k datové sadě, kterou chcete rozdělit.
  
1. V případě **rozdělení režimu** vyberte **relativní výraz**.
  
1. Do pole **relační výraz** zadejte výraz, který provádí operaci porovnání v jednom sloupci.

   Pro **číselný sloupec**:
   - Sloupec obsahuje čísla libovolného číselného datového typu, včetně datových typů data a času.
   - Výraz může odkazovat maximálně na jeden název sloupce.
   - `&`Pro operaci a použijte znak ampersand. Použijte `|` pro operaci nebo znak kanálu.
   - Podporovány jsou následující operátory: `<` , `>` , `<=` , `>=` , `==` , `!=` .
   - Operace nelze seskupit pomocí `(` a `)` .
   
   Pro **řetězcový sloupec**:
   - Podporovány jsou následující operátory: `==` , `!=` .

1. Odešlete kanál.

   Výraz rozdělí datovou sadu do dvou sad řádků: řádky s hodnotami, které splňují podmínku, a všechny zbývající řádky.

Následující příklady ukazují, jak rozdělit datovou sadu pomocí možnosti **relativního výrazu** v modulu **Split data** .  

### <a name="calendar-year"></a>Kalendářní rok

Běžným scénářem je rozdělit datovou sadu o roky. Následující výraz vybere všechny řádky, ve kterých jsou hodnoty ve sloupci `Year` větší než `2010` .

```text
\"Year" > 2010
```

Výraz data musí mít účet pro všechny části kalendářních dat zahrnuté do sloupce data. Formát dat v datovém sloupci musí být konzistentní. 

Například ve sloupci kalendářních dat, který používá formát `mmddyyyy` , by měl být výraz podobný tomuto:

```text
\"Date" > 1/1/2010
```

### <a name="column-index"></a>Index sloupce

Následující výraz ukazuje, jak můžete použít index sloupce pro výběr všech řádků v prvním sloupci datové sady, které obsahují hodnoty menší než nebo rovny 30, ale nerovnají se 20.

```text
(\0)<=30 & !=20
```


## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 
