---
title: 'Připojte Data: Odkaz na modul'
titleSuffix: Azure Machine Learning service
description: Další informace o použití připojení k modulu dat ve službě Azure Machine Learning sloučit datové sady.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: peterclu
ms.date: 06/01/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 7e814f5ea4bd47ceb0697e860c946039ce39ae1f
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2019
ms.locfileid: "67518011"
---
# <a name="join-data"></a>Spojení dat

Tento článek popisuje způsob použití **spojení dat** modulu v rozhraní visual služby Azure Machine Learning, chcete-li sloučit dvě datové sady pomocí operace spojení stylu databáze.  

## <a name="how-to-configure-join-data"></a>Jak nakonfigurovat připojení dat

Pokud chcete provést spojení na dvě datové sady, by měl mít relace podle klíčový sloupec. Podporují také složené klíče pomocí více sloupců. 

1. Přidat datové sady, které chcete sloučit, a pak přetáhněte **připojení dat** modulu do svého experimentu. 

    Můžete najít v modulu **transformace dat** kategorie v části **manipulaci s**.

1. Připojit datové sady, které **připojení dat** modulu. 
 
1. Vyberte **spustit selektor sloupců** zvolit sloupce klíče. Nezapomeňte vybrat sloupce pro levé a pravé vstupy.

    Pro jeden klíč:

    Vyberte jeden klíčový sloupec pro obě vstupy.
    
    Složené klíče:

    Vyberte všechny sloupce klíče z levým vstupem a pravým vstupem ve stejném pořadí. **Spojení dat** modulu se připojí k tabulky, když všechny klíčové sloupce odpovídají. Zaškrtněte možnost **povolit vynechávání duplicit a zachovat pořadí sloupců v výběru** Pokud pořadí sloupců není stejný jako původní tabulky. 

    ![selektor sloupců](media/module/join-data-column-selector.png)


1. Vyberte **rozlišovat velikost písmen** možnost, pokud chcete zachovat rozlišování malých a velkých písmen na textový sloupec spojení. 
   
1. Použití **typ spojení** rozevírací seznam a určete, jak by měly být kombinované datové sady.  
  
    * **Vnitřní spojení**: *Vnitřní spojení* je nejběžnější operace spojení. Vrátí kombinovanou řádky jenom v případě, že hodnoty klíčových sloupců odpovídat.  
  
    * **Levé vnější spojení**: A *levé vnější spojení* vrátí připojený řádků pro všechny řádky z levé tabulky. Když řádek v levé tabulce neobsahuje žádné odpovídající řádky v pravé tabulce, vrácený řádek obsahuje chybějící hodnoty pro všechny sloupce, které pocházejí z pravé tabulky. Můžete také určit náhradní hodnotu pro chybějící hodnoty.  
  
    * **Úplné vnější spojení**: A *úplné vnější spojení* vrátí všechny řádky z levé tabulky (**Tabulka1**) a z pravé tabulky (**tabulka2**).  
  
         Pro všechny řádky z obou tabulek, které mají žádné odpovídající řádky v jiném výsledek bude obsahovat řádek obsahující chybějící hodnoty.  
  
    * **Left Join středníkem**: A *left join částečně* vrátí pouze hodnoty z levé tabulky, když odpovídají hodnot klíčových sloupců.  

1. Pro možnost **zachovat přímo klíčových sloupců v tabulce připojené k doméně**:

    * Tuto možnost použijte k zobrazení klíče z obou vstupní tabulky.
    * Zrušte výběr pro vrácení pouze klíčové sloupce z levým vstupem.

1. Spusťte experiment, nebo vyberte modul, dat a vybrané **spustit vybrané** k provedením příkazu join.

1. Zobrazit výsledky, klikněte pravým tlačítkem myši **připojení dat** > **datovou sadu výsledků** > **vizualizovat**.

## <a name="next-steps"></a>Další postup

Zobrazit [sada modulů, které jsou k dispozici](module-reference.md) do služby Azure Machine Learning. 