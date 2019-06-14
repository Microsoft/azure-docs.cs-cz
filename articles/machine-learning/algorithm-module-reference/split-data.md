---
title: 'Rozdělení dat: Odkaz na modul'
titleSuffix: Azure Machine Learning service
description: Další informace o použití modulu rozdělení dat ve službě Azure Machine Learning datové sady rozdělit do dvou různých sad.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: a7395280ed9a2e9dcb94a081f0b3bf10a28da719
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65029397"
---
# <a name="split-data-module"></a>Modul rozdělení dat.

Tento článek popisuje modulu rozhraní visual (preview) pro službu Azure Machine Learning.

Tento modul slouží k rozdělení na dvě odlišné sady datové sady.

Tento modul je zvlášť užitečné, když budete chtít rozdělit data do trénování a testování sad. Můžete přizpůsobit tak, že data rozdělená také. Některé možnosti podporují náhodné dat; ostatní jsou přizpůsobené pro datový typ nebo typ modelu.

## <a name="how-to-configure"></a>Postup konfigurace

> [!TIP]
> Před výběrem rozdělení režimu, číst všechny možnosti, které určují typ rozdělení, které potřebujete.
> Pokud změníte rozdělení režimu, může resetovat další možnosti.

1. Přidat **rozdělení dat** modulu do experimentu v rozhraní. Můžete najít tento modul v rámci **transformace dat**v **ukázka a rozdělení** kategorie.

2. **Rozdělení režimu**: Vyberte jednu z následujících režimů, v závislosti na typu dat, která máte, a způsobu jeho rozdělení. Oba režimy dělení má různé možnosti. Klikněte na následující témata pro podrobné pokyny a příklady. 

    - **Rozdělit řádky**: Tuto možnost použijte, pokud chcete jenom data rozdělit do dvou částí. Můžete určit procento data vložit do jednotlivých rozdělit, ale ve výchozím nastavení, rozdělení dat. 50-50.

        Můžete také náhodně výběr řádků v jednotlivých skupinách a použití vrstveného vzorkování. V vrstveného vzorkování, musíte vybrat jednoho sloupce dat, pro které chcete hodnoty rozdělí rovnoměrně mezi dvěma výsledků datové sady.  

    - **Regulární výraz rozdělení** tuto možnost zvolte, pokud chcete rozdělit vaše datová sada při testování jednoho sloupce pro hodnoty.

        Například pokud je analýza mínění, můžete zkontrolovat přítomnost konkrétní produkt název do textového pole a datové sady rozdělit řádky s cílový název produktu a ta bez.

    - **Rozdělení relativní výraz**:  Tuto možnost použijte, kdykoli budete chtít použít podmínku pro číslo sloupce. Číslo může být pole data a času, obsahující stáří sloupec nebo korunách nebo dokonce i v procentech. Můžete například chtít rozdělit datovou sadu v závislosti na náklady na skupiny uživatelů podle věku rozsahů nebo samostatné dat podle kalendářního data položky.

### <a name="split-rows"></a>Rozdělit řádky
1.  Přidat [rozdělení dat](./split-data.md) modulu do experimentu v rozhraní a připojit datové sady, kterou chcete rozdělit.
  
2.  Pro **rozdělení režimu**, zvolte **rozdělit řádky**. 

3.  **Nastavte podíl řádků v první výstupní sadě dat**. Tuto možnost použijte, chcete-li zjistit, kolik řádků přejděte do první výstupu (vlevo). Všechny ostatní řádky se přejít na druhého výstupu (napravo).

    Poměr představuje procentuální podíl řádků odesílat první výstupní sadě dat, takže je nutné zadat desetinné číslo mezi 0 a 1.
     
     Například pokud jako hodnotu zadáte 0,75, datová sada by rozdělit pomocí poměrem 75:25 75 % řádků odesílat první výstupní sadě dat a 25 % odesílat druhý výstupní datovou sadu.
  
4. Vyberte **Randomized rozdělení** možnost, pokud chcete náhodně výběr data do dvou skupin. Toto je upřednostňovaná možnost, při vytváření datové sady pro trénování a testování.

5.  **Náhodné**: Zadejte hodnotu nezáporné celé číslo k inicializaci pseudonáhodné posloupnosti instancí, který se má použít. Tento počáteční výchozí hodnota se používá ve všech modulech, které generují náhodná čísla. 

     Určení základní hodnota díky výsledky obecně reprodukovatelné. Pokud je nutné opakovat výsledky operace rozdělení, měli byste zadat počáteční hodnoty pro generátor náhodných čísel. Jinak náhodná počáteční hodnota je standardně nastavena na 0, což znamená, že je počáteční počáteční hodnoty získané ze systémových hodin. V důsledku toho distribuci dat může být mírně odlišná pokaždé, když provedete rozdělení. 

6. **Vrstveného rozdělení**: Tuto možnost nastavte na **True** zajistit, že obsahují dva výstupní datové sady reprezentativní vzorek hodnot v *setkání strata sloupec* nebo *sloupec klíče rozdělení*. 

    S povoleným vzorkováním vrstveného, data rozdělená tak, aby každý výstupní datová sada získá zhruba procentní každou cílovou hodnotu. Například může mít jistotu, že trénování a testování sad přibližně vyvažují s ohledem na výsledek nebo s ohledem ot druhý sloupec, jako je například pohlaví.

7. Spusťte experiment.


## <a name="regular-expression-split"></a>Rozdělení regulárního výrazu

1.  Přidat [rozdělení dat](./split-data.md) modulu do svého experimentu a propojte jej jako vstup pro datovou sadu, kterou chcete rozdělit.  
  
2.  Pro **rozdělení režimu**vyberte **regulárního výrazu rozdělení**.

3. V **regulárního výrazu** zadejte platný regulární výraz. 
  
   Regulární výraz by měly dodržovat Python syntaxi regulárního výrazu.


4. Spusťte experiment.

    Podle regulárních výrazů, které zadáte, datová sada je rozdělen do dvou sad řádků: řádků s hodnotami, které odpovídají výrazu a všechny zbývající řádky. 

## <a name="relative-expression-split"></a>Rozdělení relativní výrazu.

1. Přidat [rozdělení dat](./split-data.md) modulu do svého experimentu a propojte jej jako vstup pro datovou sadu, kterou chcete rozdělit.
  
2. Pro **rozdělení režimu**vyberte **relativní výraz rozdělení**.
  
3. V **relační výraz** textového pole, typ, což je výraz, který provádí operace porovnání, na jeden sloupec:


 - Číselný sloupec:
    - Sloupec obsahuje počet číselných datových typů včetně datové typy data a času.

    - Výraz může odkazovat nejvýše jeden sloupec názvu.

    - Použít znak ampersand (&) pro operaci AND a použijte kanálu znak pro operace OR (|).

    - Jsou podporovány následující operátory: `<`, `>`, `<=`, `>=`, `==`, `!=`

    - Operace nejde skupin s použitím `(` a `)`.

 - Řetězcový sloupec: 
    - Jsou podporovány následující operátory: `==`, `!=`



4. Spusťte experiment.

    Výraz rozdělí dvě sady řádků datové sady: řádků s hodnotami, které splňují podmínku a všechny zbývající řádky.

## <a name="next-steps"></a>Další postup

Zobrazit [sada modulů, které jsou k dispozici](module-reference.md) do služby Azure Machine Learning. 