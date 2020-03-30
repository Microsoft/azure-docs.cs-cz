---
title: 'Ruční zadání dat: Odkaz na modul'
titleSuffix: Azure Machine Learning
description: Zjistěte, jak pomocí modulu Zadat data ručně v Azure Machine Learning vytvořit malou datovou sadu zadáním hodnot. Datová sada může mít více sloupců.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 35e31e5ace53654e8aad794dd3e25fc04bd9a088
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367512"
---
# <a name="enter-data-manually-module"></a>Zadat modul Data ručně

Tento článek popisuje modul v návrháři Azure Machine Learning (preview).

Pomocí modulu **Zadat data ručně** vytvořte malou datovou sadu zadáním hodnot. Datová sada může mít více sloupců.
  
Tento modul může být užitečný ve scénářích, jako jsou:  
  
- Generování malé sady hodnot pro testování.  
- Vytvoření krátkého seznamu štítků.  
- Zadáním seznamu názvů sloupců, které chcete vložit do datové sady.

## <a name="create-a-dataset"></a>Vytvoření datové sady 
  
1. Přidejte modul [Zadejte data ručně](./enter-data-manually.md) do kanálu. Tento modul najdete v kategorii **Vstup a výstup dat** v Azure Machine Learning. 
  
1. V **části DataFormat**vyberte jednu z následujících možností. Tyto možnosti určují, jak by měla být analyzována data, která zadáte. Požadavky pro každý formát se značně liší, proto si přečtěte související témata.  
  
   - **ARFF**: Formát souboru vztah atributu používaný weka.   
   - **CSV**: Formát hodnot oddělených čárkami. Další informace naleznete v [tématu Převod na soubor CSV](./convert-to-csv.md).    
   - **SVMLight**: Formát používaný Vowpal Wabbit a dalšími rámci strojového učení.    
   - **TSV**: Formát hodnot oddělených tabulátory.

   Pokud zvolíte formát a neposkytnete data, která splňují specifikace formátu, dojde k chybě za běhu.
  
1. Kliknutím do textového pole **Data** začněte zadávat data. Následující formáty vyžadují zvláštní pozornost:  
  
   - **CSV**: Chcete-li vytvořit více sloupců, vložte text oddělený čárkami nebo zadejte více sloupců pomocí čárek mezi polemi.
  
     Pokud vyberete možnost **HasHeader,** můžete jako záhlaví sloupce použít první řádek hodnot.  
  
     Pokud zrušíte výběr této volby, budou použity názvy sloupců (Col1, Col2 a tak dále). Názvy sloupců můžete později přidat nebo změnit pomocí funkce [Upravit metadata](./edit-metadata.md).  
  
   - **TSV**: Chcete-li vytvořit více sloupců, vložte do textu odděleného kartami nebo zadejte více sloupců pomocí karet mezi poli.  
  
     Pokud vyberete možnost **HasHeader,** můžete jako záhlaví sloupce použít první řádek hodnot.  
  
     Pokud zrušíte výběr této volby, budou použity názvy sloupců (Col1, Col2 a tak dále). Názvy sloupců můžete později přidat nebo změnit pomocí funkce [Upravit metadata](./edit-metadata.md).  
  
   - **ARFF**: Vložte do existujícího souboru formátu ARFF. Pokud zadáváte hodnoty přímo, nezapomeňte na začátek dat přidat volitelná pole záhlaví a požadovaných atributů. 

     Například následující řádky záhlaví a atributů lze přidat do jednoduchého seznamu. Záhlaví sloupce bude `SampleText`. Všimněte si, že typ String není podporován.
    
     ```text
     % Title: SampleText.ARFF  
     % Source: Enter Data module  
     @ATTRIBUTE SampleText NUMERIC  
     @DATA  
     \<type first data row here>  
     ```

   - **SVMLight**: Zadejte nebo vložte hodnoty pomocí formátu SVMLight.  
  
     Například následující ukázka představuje prvních pár řádků datové sady darování krve ve formátu SVMLight:  
  
     ```text  
     # features are [Recency], [Frequency], [Monetary], [Time]  
     1 1:2 2:50 3:12500 4:98   
     1 1:0 2:13 3:3250 4:28   
     ```  
  
     Při spuštění modulu [Zadat data ručně](./enter-data-manually.md) jsou tyto řádky převedeny na datovou sadu sloupců a hodnot indexu následujícím způsobem:  
  
     |Sloupec 1|Sloupec 2|Kol3|Kol4|Popisky|  
     |-|-|-|-|-|  
     |0.00016|0.004|0.999961|0.00784|1|  
     |0|0.004|0.999955|0.008615|1|  
  
1. Chcete-li za každým řádkem spustit nový řádek, vyberte klávesu Zadat.      
     
   Pokud vyberete zadat vícekrát přidat více prázdných koncových řádků, prázdné řádky budou odstraněny nebo oříznuty.  
  
   Pokud vytvoříte řádky s chybějícími hodnotami, můžete je vždy později odfiltrovat.  
  
1. Připojte výstupní port k ostatním modulům a spusťte kanál.  
  
   Chcete-li zobrazit datovou sadu, klepněte pravým tlačítkem myši na modul a vyberte možnost **Vizualizovat**.

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 