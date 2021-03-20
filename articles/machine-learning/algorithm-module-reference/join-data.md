---
title: 'Data spojování: odkaz na modul'
titleSuffix: Azure Machine Learning
description: Naučte se používat modul JOIN data v Návrháři Azure Machine Learning ke sloučení dvou datových sad dohromady.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/19/2019
ms.openlocfilehash: c23dca40d50c5837bd9ff45bc3c3d7fb2581685b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "93420746"
---
# <a name="join-data"></a>Spojení dat

Tento článek popisuje, jak použít modul **Join data** v Návrháři Azure Machine Learning ke sloučení dvou datových sad pomocí operace join ve stylu databáze.  

## <a name="how-to-configure-join-data"></a>Postup konfigurace připojení k datům

Chcete-li provést spojení se dvěma datovými sadami, měly by se vztahovat na klíčový sloupec. Podporují se také složené klíče využívající více sloupců. 

1. Přidejte datové sady, které chcete zkombinovat, a pak přetáhněte modul **Join data** do kanálu. 

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
  
    * **Vnitřní spojení**: nejběžnější operace spojení je *vnitřní spojení* . Vrátí kombinované řádky pouze v případě, že se hodnoty klíčových sloupců shodují.  
  
    * **Levé vnější spojení**: *levé vnější spojení* vrátí připojené řádky pro všechny řádky z levé tabulky. Pokud řádek v levé tabulce nemá odpovídající řádky v pravé tabulce, vrácený řádek obsahuje chybějící hodnoty pro všechny sloupce, které pocházejí z pravé tabulky. Můžete také zadat náhradní hodnotu pro chybějící hodnoty.  
  
    * **Úplné vnější spojení**: *úplné vnější spojení* vrátí všechny řádky z levé tabulky (**Tabulka1**) a z pravé tabulky (**Tabulka2**).  
  
         Pro každý z řádků v obou tabulkách, které nemají odpovídající řádky v druhé, výsledek zahrnuje řádek obsahující chybějící hodnoty.  
  
    * **LEFT JOIN**: *levá polovina spojení* vrátí pouze hodnoty z levé tabulky, když se hodnoty klíčových sloupců shodují.  

1. Pro možnost **zachovat pravé klíčové sloupce v připojené tabulce**:

    * Tuto možnost vyberte, pokud chcete zobrazit klíče z obou vstupních tabulek.
    * Zrušte zaškrtnutí, pokud chcete vrátit pouze klíčové sloupce z levého vstupu.

1. Odešlete kanál.

1. Chcete-li zobrazit výsledky, klikněte pravým tlačítkem myši na **data JOIN** a vyberte **vizualizovat**.

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 