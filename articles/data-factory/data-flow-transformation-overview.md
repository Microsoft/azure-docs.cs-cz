---
title: Přehled transformace toku dat mapování
description: Přehled různých transformací dostupných v mapování toku dat
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 94f7277c1b741fb5ef6d2807fabbb266c0109415
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79086928"
---
# <a name="mapping-data-flow-transformation-overview"></a>Přehled transformace toku dat mapování

Níže je uveden seznam transformací aktuálně podporovaných v mapování toku dat. Klikněte na jednotlivé transformace se dozvíte jeho podrobnosti o konfiguraci.

| Name (Název) | Kategorie | Popis |
| ---- | -------- | ----------- |
| [Agregace](data-flow-aggregate.md) | Modifikátor schématu | Definujte různé typy agregací, například SUMA, MIN, MAX a POČET seskupených podle existujících nebo vypočítaných sloupců. | 
| [Změna řádku](data-flow-alter-row.md) | Modifikátor řádků | Nastavte zásady vložení, odstranění, aktualizace a upsert na řádcích. |
| [Podmíněné rozdělení](data-flow-conditional-split.md) | Více vstupů/výstupů | Směrovat řádky dat do různých datových proudů na základě odpovídajících podmínek. |
| [Odvozený sloupec](data-flow-derived-column.md) | Modifikátor schématu | generovat nové sloupce nebo upravovat existující pole pomocí jazyka pro vyjádření toku dat. | 
| [Existuje](data-flow-exists.md) | Více vstupů/výstupů | Zkontrolujte, zda vaše data existují v jiném zdroji nebo datovém proudu. | 
| [Filtr](data-flow-filter.md) | Modifikátor řádků | Filtrujte řádek na základě podmínky. |
| [Zploštit](data-flow-flatten.md) | Modifikátor schématu |  Vezměte hodnoty pole uvnitř hierarchických struktur, jako je například JSON, a rozbalte je do jednotlivých řádků. |
| [Připojit](data-flow-join.md) | Více vstupů/výstupů |  Kombinujte data ze dvou zdrojů nebo datových proudů. |
| [Vyhledávání](data-flow-lookup.md) | Více vstupů/výstupů | Referenční data z jiného zdroje. |
| [Nová větev](data-flow-new-branch.md) | Více vstupů/výstupů | Použít více sad operací a transformací proti stejnému datovému proudu. |
| [Převést na sloupce](data-flow-pivot.md) | Modifikátor schématu | Agregace, kde jeden nebo více sloupců seskupení má své odlišné hodnoty řádků transformovány do jednotlivých sloupců. |
| [Vybrat](data-flow-select.md) | Modifikátor schématu | Alias sloupce a názvy datových proudů a přetažení nebo přepychové pořadí sloupců |
| [Jímka](data-flow-sink.md) | - | Konečný cíl pro vaše data |
| [Seřadit](data-flow-sort.md) | Modifikátor řádků | Řazení příchozích řádků v aktuálním datovém proudu |
| [Zdroj](data-flow-source.md) | - | Zdroj dat pro tok dat |
| [Náhradní klíč](data-flow-surrogate-key.md) | Modifikátor schématu | Přidání zvyšující se hodnoty libovolného klíče, která není obchodní, |
| [Sjednocení](data-flow-union.md) | Více vstupů/výstupů | Svisle kombinujte více datových proudů |
| [Převést na řádky](data-flow-unpivot.md) | Modifikátor schématu | Převést sloupce na hodnoty řádků |
| [Okno](data-flow-window.md) | Modifikátor schématu |  Definujte agregace sloupců ve vašich datových proudech na základě oken. |
