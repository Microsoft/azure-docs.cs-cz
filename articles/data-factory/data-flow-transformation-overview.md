---
title: Přehled transformace toku dat
description: Přehled různých transformací dostupných v toku mapování dat
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 94f7277c1b741fb5ef6d2807fabbb266c0109415
ms.sourcegitcommit: 20429bc76342f9d365b1ad9fb8acc390a671d61e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2020
ms.locfileid: "79086928"
---
# <a name="mapping-data-flow-transformation-overview"></a>Přehled transformace toku dat

Níže je uveden seznam transformací aktuálně podporovaných v mapování toku dat. Kliknutím na jednotlivé transformace se dozvíte podrobnosti o konfiguraci.

| Název | Kategorie | Popis |
| ---- | -------- | ----------- |
| [Souhrnné](data-flow-aggregate.md) | Modifikátor schématu | Definujte různé typy agregací, jako například SUM, MIN, MAX a COUNT seskupené podle existujících nebo počítaných sloupců. | 
| [Změnit řádek](data-flow-alter-row.md) | Modifikátor řádku | Nastavení zásad vložení, odstranění, aktualizace a Upsert na řádcích |
| [Podmíněné rozdělení](data-flow-conditional-split.md) | Několik vstupů/výstupů | Směrovat řádky dat do různých datových proudů na základě vyhovujících podmínek. |
| [Odvozený sloupec](data-flow-derived-column.md) | Modifikátor schématu | Vygenerujte nové sloupce nebo upravte existující pole pomocí jazyka výrazu data Flow. | 
| [Neexistuje](data-flow-exists.md) | Několik vstupů/výstupů | Ověřte, jestli vaše data existují v jiném zdroji nebo streamu. | 
| [Filtrovací](data-flow-filter.md) | Modifikátor řádku | Filtruje řádek na základě podmínky. |
| [Flatten](data-flow-flatten.md) | Modifikátor schématu |  Hodnoty polí v hierarchických strukturách, jako je JSON, a jejich rozveďte do jednotlivých řádků. |
| [Spojení](data-flow-join.md) | Několik vstupů/výstupů |  Kombinovat data ze dvou zdrojů nebo datových proudů. |
| [Prohledávání](data-flow-lookup.md) | Několik vstupů/výstupů | Referenční data z jiného zdroje. |
| [Nová větev](data-flow-new-branch.md) | Několik vstupů/výstupů | Použijte několik sad operací a transformací proti stejnému datovému proudu. |
| [Otočný](data-flow-pivot.md) | Modifikátor schématu | Agregace, ve které má jeden nebo více sloupců seskupení jedinečné hodnoty řádků transformované na jednotlivé sloupce. |
| [Výběr](data-flow-select.md) | Modifikátor schématu | Sloupce aliasů a názvy datových proudů a přetáhnout nebo změnit uspořádání sloupců |
| [Jímkou](data-flow-sink.md) | - | Konečný cíl pro vaše data |
| [Druhu](data-flow-sort.md) | Modifikátor řádku | Seřadit příchozí řádky v aktuálním datovém proudu |
| [Zdroj](data-flow-source.md) | - | Zdroj dat pro tok dat |
| [Náhradní klíč](data-flow-surrogate-key.md) | Modifikátor schématu | Přidání přírůstku hodnoty libovolného klíče, který nepatří do zaměstnání |
| [Sjednocovací](data-flow-union.md) | Několik vstupů/výstupů | Kombinování více datových proudů svisle |
| [Unpivot](data-flow-unpivot.md) | Modifikátor schématu | Kontingenční sloupce do hodnot řádků |
| [Okno](data-flow-window.md) | Modifikátor schématu |  Definujte agregace sloupců na základě okna v datových proudech. |
