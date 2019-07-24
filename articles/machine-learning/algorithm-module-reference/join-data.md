---
title: 'Připojit data: Odkaz na modul'
titleSuffix: Azure Machine Learning service
description: Naučte se používat datový modul Join join ve službě Azure Machine Learning ke sloučení datových sad.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: peterlu
ms.date: 06/01/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 1022bdc26a340b6b54ad840d1fe47674509fa865
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/15/2019
ms.locfileid: "67871659"
---
# <a name="join-data"></a>Spojení dat

Tento článek popisuje, jak použít modul **Join data** v vizuálním rozhraní služby Azure Machine Learning ke sloučení dvou datových sad pomocí operace join ve stylu databáze.  

## <a name="how-to-configure-join-data"></a>Postup konfigurace připojení k datům

Chcete-li provést spojení se dvěma datovými sadami, měly by se vztahovat na klíčový sloupec. Podporují se také složené klíče využívající více sloupců. 

1. Přidejte datové sady, které chcete zkombinovat, a pak přetáhněte modul **Join data** do experimentu. 

    Modul můžete najít v kategorii **transformace dat** v části **manipulace**.

1. Připojte datové sady k modulu **Join data** . 
 
1. Vyberte **Spustit selektor sloupců** a zvolte sloupce klíčů. Nezapomeňte zvolit sloupce pro vstupy vlevo a vpravo.

    Pro jeden klíč:

    Vyberte jeden klíčový sloupec pro oba vstupy.
    
    Pro složený klíč:

    Vyberte všechny klíčové sloupce z levého vstupního a pravého vstupu ve stejném pořadí. Modul **Join data** se spojí s tabulkami, pokud se všechny klíčové sloupce shodují. Pokud pořadí sloupců není stejné jako původní tabulka, **Zachovejte v výběru možnost povolující duplicity a zachovejte pořadí sloupců** . 

    ![výběr sloupců](media/module/join-data-column-selector.png)


1. Vyberte možnost **rozlišovat velká a malá písmena** , pokud chcete zachovat citlivost velkých a malých písmen u spojení s textovým sloupcem. 
   
1. Pomocí rozevíracího seznamu **typ spojení** můžete určit, jak se mají datové sady kombinovat.  
  
    * **Vnitřní spojení**: *Vnitřní spojení* je nejběžnější operace JOIN. Vrátí kombinované řádky pouze v případě, že se hodnoty klíčových sloupců shodují.  
  
    * **Levé vnější spojení**: *Levé vnější spojení* vrátí připojené řádky pro všechny řádky z levé tabulky. Pokud řádek v levé tabulce nemá odpovídající řádky v pravé tabulce, vrácený řádek obsahuje chybějící hodnoty pro všechny sloupce, které pocházejí z pravé tabulky. Můžete také zadat náhradní hodnotu pro chybějící hodnoty.  
  
    * **Úplné vnější spojení**: *Úplné vnější spojení* vrátí všechny řádky z levé tabulky (**Tabulka1**) a z pravé tabulky (**Tabulka2**).  
  
         Pro každý z řádků v obou tabulkách, které nemají odpovídající řádky v druhé, výsledek zahrnuje řádek obsahující chybějící hodnoty.  
  
    * **Levé spojení**: *Levé spojení* vrátí jenom hodnoty z levé tabulky, když se hodnoty klíčových sloupců shodují.  

1. Pro možnost **zachovat pravé klíčové sloupce v připojené tabulce**:

    * Tuto možnost vyberte, pokud chcete zobrazit klíče z obou vstupních tabulek.
    * Zrušte zaškrtnutí, pokud chcete vrátit pouze klíčové sloupce z levého vstupu.

1. Spusťte experiment nebo vyberte modul připojení dat a vybrané **spuštění** , aby se spojení provádělo.

1. Pokud chcete zobrazit výsledky, klikněte pravým tlačítkem  > myši na**vizualizaci**datové**sady** > výsledků spojování.

## <a name="next-steps"></a>Další postup

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning služby. 