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
ms.openlocfilehash: 3e831e58b47d53e2924956cab13568c69bc1432e
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2020
ms.locfileid: "77153736"
---
# <a name="split-data-module"></a>Modul rozdělení dat

Tento článek popisuje modul v Návrháři Azure Machine Learning (Preview).

Pomocí tohoto modulu můžete rozdělit datovou sadu do dvou různých sad.

Tento modul je zvláště užitečný v případě, že potřebujete oddělit data do sad pro školení a testování. Můžete přizpůsobit také způsob, jakým jsou data rozdělena. Některé možnosti podporují náhodnost dat; jiné jsou přizpůsobené pro určitý typ dat nebo model.

## <a name="how-to-configure"></a>Jak nakonfigurovat

> [!TIP]
> Než zvolíte režim rozdělování, přečtěte si všechny možnosti a určete typ rozdělení, který potřebujete.
> Pokud změníte rozdělovací režim, můžete obnovit všechny ostatní možnosti.

1. Přidejte modul **rozdělit data** do kanálu v návrháři. Tento modul můžete najít v části **transformace dat**v kategorii **Ukázka a rozdělení** .

2. **Režim rozdělení**: v závislosti na typu dat a způsobu, jakým je chcete rozdělit, vyberte jeden z následujících režimů. Každý režim rozdělování má různé možnosti. Podrobné pokyny a příklady získáte kliknutím na následující témata. 

    - **Rozdělit řádky**: tuto možnost použijte, pokud chcete pouze rozdělit data do dvou částí. Můžete určit procento dat, která mají být vložena do každého rozdělení, ale ve výchozím nastavení jsou data rozdělena 50-50.

        Můžete také náhodně vybrat řádky v každé skupině a použít vzorkování stratified. V stratified vzorkování musíte vybrat jeden sloupec dat, pro který chcete hodnoty rozdělit rovnoměrně mezi dvě datové sady výsledků.  

    - **Rozdělení regulárního výrazu**  Tuto možnost vyberte, pokud chcete datovou sadu rozdělit tak, že otestujete jeden sloupec s hodnotou.

        Pokud například analyzujete mínění, mohli byste vyhledat přítomnost konkrétního názvu produktu v textovém poli a potom datovou sadu rozdělit do řádků s cílovým názvem produktu a bez.

    - **Rozdělení relativního výrazu**: tuto možnost použijte vždy, když chcete použít podmínku pro sloupec s čísly. Toto číslo může být pole Datum a čas, sloupec obsahující věkové nebo dolarové částky nebo dokonce i procento. Například můžete chtít rozdělit datovou sadu podle nákladů na položky, seskupit lidi podle věkových rozsahů nebo jednotlivé údaje podle kalendářního data.

### <a name="split-rows"></a>Rozdělit řádky

1.  Přidejte modul [rozdělit data](./split-data.md) do kanálu v návrháři a připojte datovou sadu, kterou chcete rozdělit.
  
2.  V **režimu rozdělení**vyberte **rozdělit řádky**. 

3.  **Zlomek řádků v první výstupní datové sadě**. Tuto možnost použijte k určení, kolik řádků přejde do prvního (levého) výstupu. Všechny ostatní řádky budou přejít na druhý (pravá ruka) výstup.

    Poměr představuje procento řádků odeslaných na první výstupní datovou sadu, takže je nutné zadat desetinné číslo od 0 do 1.
     
     Pokud například zadáte 0,75 jako hodnotu, datová sada bude rozdělena pomocí poměru 75:25 a 75% řádků odeslaných do první výstupní datové sady a 25% odesláno druhé výstupní datové sadě.
  
4. Vyberte možnost **náhodného rozdělení** , pokud chcete náhodně vybrat data do dvou skupin. Toto je upřednostňovaná možnost při vytváření školicích a testovacích datových sad.

5.  **Random seed**: zadejte nezápornou celočíselnou hodnotu pro inicializaci pseudonáhodných sekvence instancí, které mají být použity. Toto výchozí osivo se používá ve všech modulech, které generují náhodná čísla. 

     Určením počáteční hodnoty se výsledky obvykle rereprodukovatelní. Pokud potřebujete výsledky operace rozdělení zopakovat, měli byste pro generátor náhodných čísel zadat počáteční hodnotu. V opačném případě je náhodné osazení nastaveno na hodnotu 0, což znamená, že počáteční počáteční hodnota se získá ze systémových hodin. V důsledku toho může být distribuce dat mírně odlišná pokaždé, když provedete rozdělení. 

6. **Stratified Split**: tuto možnost nastavte na **hodnotu true** , chcete-li zajistit, aby dvě výstupní datové sady obsahovaly reprezentativní vzorek hodnot ve *sloupci vrstvy* nebo ve *sloupci klíč rozdělení*na více úrovní. 

    V případě vzorkování stratified jsou data rozdělena tak, že každá výstupní datová sada získá zhruba stejné procento každé cílové hodnoty. Například můžete chtít zajistit, aby vaše školicí a testovací sady byly zhruba vyvážené s ohledem na výsledek nebo s ohledem na jiný sloupec, například pohlaví.

7. Spuštění kanálu


## <a name="regular-expression-split"></a>Rozdělení regulárního výrazu

1.  Přidejte do svého kanálu modul [rozdělení dat](./split-data.md) a připojte ho jako vstup k datové sadě, kterou chcete rozdělit.  
  
2.  V případě **rozdělení režimu**vyberte možnost **rozdělení regulárního výrazu**.

3. Do pole **regulární výraz** zadejte platný regulární výraz. 
  
   Regulární výraz by měl následovat po syntaxi regulárního výrazu jazyka Python.


4. Spuštění kanálu

    Na základě regulárního výrazu, který zadáte, je datová sada rozdělena do dvou sad řádků: řádků s hodnotami, které odpovídají výrazu a všem zbývajícím řádkům. 

## <a name="relative-expression-split"></a>Rozdělení relativního výrazu

1. Přidejte do svého kanálu modul [rozdělení dat](./split-data.md) a připojte ho jako vstup k datové sadě, kterou chcete rozdělit.
  
2. V případě **rozdělení režimu**vyberte **relativní rozdělení výrazu**.
  
3. Do textového pole **relační výraz** zadejte výraz, který provádí operaci porovnání, v jednom sloupci:


 - Číselný sloupec:
    - Sloupec obsahuje čísla libovolného číselného datového typu, včetně datových typů data a času.

    - Výraz může odkazovat maximálně na jeden název sloupce.

    - Použijte znak ampersand (&) pro operaci AND a použijte pro operaci nebo znak svislé čáry (|).

    - Podporovány jsou následující operátory: `<`, `>`, `<=`, `>=`, `==`, `!=`

    - Operace nelze seskupit pomocí `(` a `)`.

 - Sloupec řetězce: 
    - Podporovány jsou následující operátory: `==`, `!=`



4. Spuštění kanálu

    Výraz rozdělí datovou sadu do dvou sad řádků: řádky s hodnotami, které splňují podmínku, a všechny zbývající řádky.

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 