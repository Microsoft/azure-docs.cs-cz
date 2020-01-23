---
title: 'Zadat data ručně: odkaz na modul'
titleSuffix: Azure Machine Learning
description: Naučte se používat modul zadat data ručně v Azure Machine Learning k vytvoření malé datové sady zadáním hodnot. Datová sada může mít více sloupců.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: 8db1d782b6f92637ad929ba863a8d24c81280577
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2020
ms.locfileid: "76548420"
---
# <a name="enter-data-manually-module"></a>Zadat data ručně modul

Tento článek popisuje modul v Návrháři Azure Machine Learning (Preview).

Pomocí tohoto modulu můžete vytvořit malou datovou sadu zadáním hodnot. Datová sada může mít více sloupců.
  
Tento modul může být užitečný ve scénářích, jako jsou tyto:  
  
- Generování malé sady hodnot pro testování  
  
- Vytvoření krátkého seznamu popisků
  
- Zadání seznamu názvů sloupců, které se mají vložit do datové sady

## <a name="enter-data-manually"></a>Ruční zadávání dat 
  
1.  Přidejte do kanálu modul [zadat data ručně](./enter-data-manually.md) . Tento modul můžete najít v kategorii **vstup a výstup dat** v Azure Machine Learning. 
  
2.  V poli **Formát DataFormat**vyberte jednu z následujících možností. Tyto možnosti určují, jak se mají analyzovat data, která zadáte. Požadavky na jednotlivé formáty se značně liší, proto si nezapomeňte přečíst Příbuzná témata.  
  
    -   **Arff**. Formát souboru relace atributu, který používá weka.   
  
    -   **CSV**. Formát hodnot oddělených čárkami. Další informace najdete v tématu [převedení do sdíleného svazku clusteru](./convert-to-csv.md).  
  
    -   **SVMLight**. Formát používaný pro dostupné a dalšími architekturami strojového učení.  
  
    -   **TSV**. Formát hodnot oddělených tabulátorem.

     Pokud zvolíte Formát a neposkytnete data, která vyhovují specifikacím formátu, dojde k chybě modulu runtime.
  
3.  Klikněte do textového pole **data** a začněte zadávat data. Následující formáty vyžadují zvláštní pozornost:  
  
    - **CSV**: pro vytvoření více sloupců, vložení do čárkami odděleného textu nebo zadání více sloupců s čárkami mezi poli.
  
        Pokud vyberete možnost **HasHeader** , můžete použít první řádek hodnot jako záhlaví sloupce.  
  
        Pokud zrušíte výběr této možnosti, budou použity názvy sloupců, Sloupec1, col2 a tak dále. Později můžete přidat nebo změnit názvy sloupců pomocí [Upravit metadata](./edit-metadata.md).  
  
    - **TSV**: Chcete-li vytvořit více sloupců, vložte text oddělený tabulátorem nebo zadejte více sloupců pomocí tabulátorů mezi poli.  
  
        Pokud vyberete možnost **HasHeader** , můžete použít první řádek hodnot jako záhlaví sloupce.  
  
        Pokud zrušíte výběr této možnosti, budou použity názvy sloupců, Sloupec1, col2 a tak dále. Později můžete přidat nebo změnit názvy sloupců pomocí [Upravit metadata](./edit-metadata.md).  
  
    -   **Arff**: vložte do existujícího souboru formátu arff. Pokud zadáváte hodnoty přímo, nezapomeňte přidat volitelné záhlaví a požadovaná pole atributu na začátek dat. 
    
        Například následující záhlaví a řádky atributů mohou být přidány do jednoduchého seznamu. Záhlaví sloupce by bylo `SampleText`.
    
        ```text
        % Title: SampleText.ARFF  
        % Source: Enter Data module  
        @ATTRIBUTE SampleText STRING  
        @DATA  
        \<type first data row here>  
        ```

    -   **SVMLight**: zadejte nebo vložte hodnoty pomocí formátu SVMLight.  
  
        Například následující příklad představuje první pár řádků datové sady krve darování ve formátu SVMight:  
  
        ```text  
        # features are [Recency], [Frequency], [Monetary], [Time]  
        1 1:2 2:50 3:12500 4:98   
        1 1:0 2:13 3:3250 4:28   
        ```  
  
        Když spustíte modul [zadat data ručně](./enter-data-manually.md) , tyto řádky se převedou na datovou sadu sloupců a hodnot indexu následujícím způsobem:  
  
        |Sloupec 1|Sloupec 2|Col3|Col4|Popisky|  
        |-|-|-|-|-|  
        |0,00016|0,004|0,999961|0,00784|1\. místo|  
        |0|0,004|0,999955|0,008615|1\. místo|  
  
4.  Po každém řádku stiskněte klávesu ENTER a začněte nový řádek.  
  
     **Nezapomeňte stisknout ENTER po posledním řádku.** 
     
     Pokud přidáte více prázdných koncových řádků několikrát stisknutím klávesy ENTER, bude poslední prázdný řádek oříznutý, ale jiné prázdné řádky budou považovány za chybějící hodnoty.  
  
     Pokud vytvoříte řádky s chybějícími hodnotami, můžete je kdykoli filtrovat později.  
  
5.  Připojte výstupní port k ostatním modulům a spusťte kanál.  
  
     Chcete-li zobrazit datovou sadu, klikněte pravým tlačítkem na modul a vyberte **vizualizovat**.  
## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 