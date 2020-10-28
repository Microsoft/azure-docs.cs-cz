---
title: Přehled transformace toku dat
description: Přehled různých transformací dostupných v toku mapování dat
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/27/2020
ms.openlocfilehash: 6fe85db6612a16099f61cb52fe9c5864882f35b3
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92892923"
---
# <a name="mapping-data-flow-transformation-overview"></a>Přehled transformace toku dat

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)] 

Níže je uveden seznam transformací aktuálně podporovaných v mapování toku dat. Kliknutím na jednotlivé transformace se dozvíte podrobnosti o konfiguraci.

| Název | Kategorie | Popis |
| ---- | -------- | ----------- |
| [Agregace](data-flow-aggregate.md) | Modifikátor schématu | Definujte různé typy agregací, jako například SUM, MIN, MAX a COUNT seskupené podle existujících nebo počítaných sloupců. | 
| [Změna řádku](data-flow-alter-row.md) | Modifikátor řádku | Nastavení zásad vložení, odstranění, aktualizace a Upsert na řádcích |
| [Podmíněné rozdělení](data-flow-conditional-split.md) | Několik vstupů/výstupů | Směrovat řádky dat do různých datových proudů na základě vyhovujících podmínek. |
| [Odvozený sloupec](data-flow-derived-column.md) | Modifikátor schématu | Vygenerujte nové sloupce nebo upravte existující pole pomocí jazyka výrazu data Flow. | 
| [Existuje](data-flow-exists.md) | Několik vstupů/výstupů | Ověřte, jestli vaše data existují v jiném zdroji nebo streamu. | 
| [Filtr](data-flow-filter.md) | Modifikátor řádku | Filtruje řádek na základě podmínky. |
| [Zploštění](data-flow-flatten.md) | Modifikátor schématu |  Hodnoty polí v hierarchických strukturách, jako je JSON, a jejich rozveďte do jednotlivých řádků. |
| [Join](data-flow-join.md) (Spojení) | Několik vstupů/výstupů |  Kombinovat data ze dvou zdrojů nebo datových proudů. |
| [Vyhledávání](data-flow-lookup.md) | Několik vstupů/výstupů | Referenční data z jiného zdroje. |
| [Nová větev](data-flow-new-branch.md) | Několik vstupů/výstupů | Použijte několik sad operací a transformací proti stejnému datovému proudu. |
| [Převést na sloupce](data-flow-pivot.md) | Modifikátor schématu | Agregace, ve které má jeden nebo více sloupců seskupení jedinečné hodnoty řádků transformované na jednotlivé sloupce. |
| [Rank](data-flow-rank.md) | Modifikátor schématu | Vygenerovat seřazené hodnocení na základě podmínek řazení |
| [Výběr](data-flow-select.md) | Modifikátor schématu | Sloupce aliasů a názvy datových proudů a přetáhnout nebo změnit uspořádání sloupců |
| [Jímka](data-flow-sink.md) | - | Konečný cíl pro vaše data |
| [Seřadit](data-flow-sort.md) | Modifikátor řádku | Seřadit příchozí řádky v aktuálním datovém proudu |
| [Zdroj](data-flow-source.md) | - | Zdroj dat pro tok dat |
| [Náhradní klíč](data-flow-surrogate-key.md) | Modifikátor schématu | Přidání přírůstku hodnoty libovolného klíče, který nepatří do zaměstnání |
| [Sjednocení](data-flow-union.md) | Několik vstupů/výstupů | Kombinování více datových proudů svisle |
| [Převést na řádky](data-flow-unpivot.md) | Modifikátor schématu | Kontingenční sloupce do hodnot řádků |
| [Okno](data-flow-window.md) | Modifikátor schématu |  Definujte agregace sloupců na základě okna v datových proudech. |
