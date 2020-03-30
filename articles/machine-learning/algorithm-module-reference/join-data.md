---
title: 'Data spojení: Odkaz na modul'
titleSuffix: Azure Machine Learning
description: Přečtěte si, jak pomocí modulu Join Data v Azure Machine Learning sloučit datové sady.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/19/2019
ms.openlocfilehash: 38606f424e38fc68519181f485b5b698d0705d6a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477574"
---
# <a name="join-data"></a>Spojení dat

Tento článek popisuje, jak použít modul **Join Data** v návrháři Azure Machine Learning (preview) ke sloučení dvou datových sad pomocí operace spojení ve stylu databáze.  

## <a name="how-to-configure-join-data"></a>Konfigurace dat spojení

Chcete-li provést spojení na dvě datové sady, měly by být spojeny klíčovým sloupcem. Podporovány jsou také složené klávesy používající více sloupců. 

1. Přidejte datové sady, které chcete zkombinovat, a přetáhněte modul **Join Data** do kanálu. 

    Modul najdete v kategorii **Transformace dat** v části **Manipulace**.

1. Připojte datové sady k modulu **Join Data.** 
 
1. Vyberte **Vybrat volič sloupce spuštění,** chcete-li vybrat klíčové sloupce. Nezapomeňte vybrat sloupce pro levý i pravý vstup.

    Pro jeden klíč:

    Vyberte jeden sloupec klíče pro oba vstupy.
    
    Pro složený klíč:

    Vyberte všechny klíčové sloupce z levého vstupu a pravý vstup ve stejném pořadí. Modul **Připojit data** se připojí k tabulkám, když se všechny klíčové sloupce shodují. Zaškrtněte možnost **Povolit duplikáty a zachovat pořadí sloupců ve výběru,** pokud pořadí sloupců není stejné jako původní tabulka. 

    ![volič sloupců](media/module/join-data-column-selector.png)


1. Pokud chcete zachovat rozlišování malých a velkých písmen na spojení textového sloupce, vyberte možnost **Srovnat malá** a velká písmena. 
   
1. Pomocí rozevíracího seznamu **Typ spojení** určete, jak mají být datové sady kombinovány.  
  
    * **Vnitřní spojení**: Nejběžnější operací spojení je *vnitřní spojení.* Vrátí kombinované řádky pouze v případě, že se hodnoty klíčových sloupců shodují.  
  
    * **Levé vnější spojení**: *Levé vnější spojení* vrátí spojené řádky pro všechny řádky z levé tabulky. Pokud řádek v levé tabulce nemá žádné odpovídající řádky v pravé tabulce, vrácený řádek obsahuje chybějící hodnoty pro všechny sloupce, které pocházejí z pravé tabulky. Můžete také zadat náhradní hodnotu chybějících hodnot.  
  
    * **Úplné vnější spojení**: *Úplné vnější spojení* vrátí všechny řádky z levé tabulky **(tabulka1)** a z pravé tabulky **(tabulka2).**  
  
         Pro každý z řádků v obou tabulkách, které nemají žádné odpovídající řádky v druhé, výsledek obsahuje řádek obsahující chybějící hodnoty.  
  
    * **Levé polospojení**: *Levé polospojení* vrátí pouze hodnoty z levé tabulky, pokud se hodnoty klíčových sloupců shodují.  

1. Pro volbu **Zachovat sloupce správných klíčů ve společné tabulce**:

    * Tuto možnost vyberte, chcete-li zobrazit klíče z obou vstupních tabulek.
    * Odznačte, chcete-li vrátit pouze klíčové sloupce z levého vstupu.

1. Odešlete potrubí.

1. Chcete-li zobrazit výsledky, klepněte pravým tlačítkem myši na **položku Spojit data** a vyberte možnost **Vizualizovat**.

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 