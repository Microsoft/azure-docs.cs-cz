---
title: 'Zadejte Data ručně: Odkaz na modul'
titleSuffix: Azure Machine Learning service
description: Další informace o použití modulu ručně zadejte Data ve službě Azure Machine Learning vytvořit na malou sadu dat tak, že zadáte hodnoty. Datová sada může mít více sloupců.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: ee15b6fb7160ece907d55e790b0ae38ee458ab96
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028602"
---
# <a name="enter-data-manually-module"></a>Zadejte Data ručně modulu

Tento článek popisuje modulu rozhraní visual (preview) pro službu Azure Machine Learning.

Tento modul slouží k vytvoření malé datové sady zadáním hodnot. Datová sada může mít více sloupců.
  
Tento modul může být užitečné v situacích, jako je například tyto:  
  
- Generování malou sadu hodnot pro účely testování  
  
- Vytváří se krátký seznam popisků
  
- Psaní seznam názvů sloupců vkládat do datové sady

## <a name="enter-data-manually"></a>Ruční zadání dat 
  
1.  Přidat [ručně zadat Data](./enter-data-manually.md) modulu do experimentu. V tomto modulu můžete najít **datový vstup a výstup** kategorie ve službě Azure Machine Learning. 
  
2.  Pro **DataFormat**, vyberte jednu z následujících možností. Tyto možnosti určují, jak by měl analyzovat data, která zadáte. Požadavky pro každý formát značně liší, takže nezapomeňte si přečíst související témata.  
  
    -   **ARFF**. Atribut vztah formát souboru, používá Weka.   
  
    -   **CSV**. Formát hodnot oddělených čárkami. Další informace najdete v tématu [převést na sdílený svazek clusteru](./convert-to-csv.md).  
  
    -   **SVMLight**. Formát používaný Vowpal Wabbit a další architektury strojového učení.  
  
    -   **TSV**. Formát hodnoty oddělené tabulátorem.

     Pokud volba formátu a neposkytuje data, která splňuje specifikace formátu, dojde k chybě za běhu.
  
3.  Klikněte do **Data** textové pole pro začněte zadávat data. Následující formáty vyžadují zvláštní pozornost:  
  
    - **CSV**:  Chcete-li vytvořit více sloupců, vložte text oddělený čárkami nebo zadejte pomocí čárek mezi poli více sloupců.
  
        Pokud vyberete **HasHeader** možnost, můžete použít první řádek hodnot jako záhlaví sloupce.  
  
        Pokud výběr této možnosti, názvy sloupců, Sloupec1, Sloupec2 a tak dále, se používají. Můžete přidat nebo změnit sloupce názvy později pomocí [upravit Metadata](./edit-metadata.md).  
  
    - **TSV**: Chcete-li vytvořit více sloupců, vložte tabulátory oddělený text nebo zadejte pomocí karet mezi poli více sloupců.  
  
        Pokud vyberete **HasHeader** možnost, můžete použít první řádek hodnot jako záhlaví sloupce.  
  
        Pokud výběr této možnosti, názvy sloupců, Sloupec1, Sloupec2 a tak dále, se používají. Můžete přidat nebo změnit sloupce názvy později pomocí [upravit Metadata](./edit-metadata.md).  
  
    -   **ARFF**:  Vložte existující soubor formátu ARFF. Pokud zadáte hodnoty přímo, je potřeba přidat volitelné záhlaví a pole požadovaný atribut na začátku data. 
    
        Například následující řádky záhlaví a atribut může být přidán do jednoduchého seznamu. Záhlaví sloupce bude `SampleText`.
    
        ```text
        % Title: SampleText.ARFF  
        % Source: Enter Data module  
        @ATTRIBUTE SampleText STRING  
        @DATA  
        \<type first data row here>  
        ```

    -   **SVMLight**: Zadejte nebo zkopírujte hodnoty v SVMLight formátu.  
  
        Například následující ukázka představuje první řádky několik krve datové sady ve formátu SVMight:  
  
        ```text  
        # features are [Recency], [Frequency], [Monetary], [Time]  
        1 1:2 2:50 3:12500 4:98   
        1 1:0 2:13 3:3250 4:28   
        ```  
  
        Při spuštění [ručně zadat Data](./enter-data-manually.md) modulu, tyto řádky jsou převedeny na datovou sadu sloupců a index hodnoty následujícím způsobem:  
  
        |Col1|Col2|Col3|Col4|Popisky|  
        |-|-|-|-|-|  
        |0.00016|0.004|0.999961|0.00784|1|  
        |0|0.004|0.999955|0.008615|1|  
  
4.  Po každý řádek, přejděte na nový řádek stiskněte klávesu ENTER.  
  
     **Ujistěte se, že stisknutím klávesy ENTER na posledním řádku.** 
     
     Po stisknutí klávesy ENTER několikrát, chcete-li přidat více prázdné řádky na konci, je odebrán poslední prázdný řádek oříznutý, ale jiné prázdné řádky jsou považovány za chybějící hodnoty.  
  
     Pokud vytvoříte řádky s chybějící hodnoty, můžete vždy odfiltrovat je později.  
  
5.  Klikněte pravým tlačítkem na modul a vyberte **spustit vybrané** k parsování dat a jejich načtení do pracovního prostoru jako datová sada.  
  
     Chcete-li zobrazit datovou sadu, klikněte na výstupní port a vyberte **vizualizovat**.  
## <a name="next-steps"></a>Další postup

Zobrazit [sada modulů, které jsou k dispozici](module-reference.md) do služby Azure Machine Learning. 