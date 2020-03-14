---
title: Rozhraní příkazového řádku – Knowledge Exploration Service API
titlesuffix: Azure Cognitive Services
description: Použijte rozhraní příkazového řádku k sestavení indexů a gramatických souborů ze strukturovaných dat a pak je nasaďte jako webové služby.
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/24/2016
ms.author: paulhsu
ms.openlocfilehash: 018552982a8ece3bbbaea2d60e2a6e64f681f822
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79220102"
---
# <a name="command-line-interface"></a>Rozhraní příkazového řádku

Rozhraní příkazového řádku Knowledge Exploration Service (KES) poskytuje možnost vytvářet index a gramatické soubory ze strukturovaných dat a nasazovat je jako webové služby.  Používá obecnou syntaxi: `kes.exe <command> <required_args> [<optional_args>]`.  Můžete spustit `kes.exe` bez argumentů pro zobrazení seznamu příkazů, nebo `kes.exe <command>` k zobrazení seznamu argumentů dostupných pro zadaný příkaz.  Níže je uveden seznam dostupných příkazů:

* build_index
* build_grammar
* host_service
* deploy_service
* describe_index
* describe_grammar

<a name="build_index-command"></a>

## <a name="build_index-command"></a>build_index – příkaz

Příkaz **build_index** vytvoří binární indexový soubor ze souboru definice schématu a datového souboru objektů, které mají být indexovány.  Výsledný indexový soubor lze použít k vyhodnocení strukturovaných výrazů dotazu nebo k vygenerování interpretace dotazů v přirozeném jazyce ve spojení s zkompilovaným gramatickým souborem.

`kes.exe build_index <schemaFile> <dataFile> <indexFile> [options]`

| Parametr      | Popis               |
|----------------|---------------------------|
| `<schemaFile>` | Cesta ke vstupnímu schématu |
| `<dataFile>`   | Cesta k vstupním datům   |
| `<indexFile>`  | Cesta k výstupnímu indexu |
| `--description <description>` | Řetězec popisu |
| `--remote <vmSize>`           | Velikost virtuálního počítače pro vzdálené sestavení |

Tyto soubory můžou být zadané pomocí místních cest souborů nebo cest URL k objektům blob Azure.  Soubor schématu popisuje strukturu indexovaných objektů a operace, které mají být podporovány (viz [Formát schématu](SchemaFormat.md)).  Datový soubor vytvoří výčet objektů a hodnot atributů, které mají být indexovány (viz [Formát dat](DataFormat.md)).  Po úspěšném sestavení obsahuje výstupní soubor indexu komprimovaná reprezentace vstupních dat, která podporují požadované operace.  

Řetězec popisu může být volitelně zadán k následné identifikaci binárního indexu pomocí příkazu **describe_index** .  

Ve výchozím nastavení je index založený na místním počítači.  Mimo prostředí Azure jsou místní buildy omezené na datové soubory, které obsahují až 10 000 objektů.  Když je zadaný příznak--Remote, index se vytvoří na dočasně vytvořeném virtuálním počítači Azure zadané velikosti.  Díky tomu můžou být velké indexy vybudovány efektivně pomocí virtuálních počítačů Azure s větší pamětí.  Aby se zabránilo stránkování, které zpomaluje proces sestavení, doporučujeme použít virtuální počítač se 3 časy velikosti paměti RAM jako velikost vstupního datového souboru.  Seznam dostupných velikostí virtuálních počítačů najdete v článku [Velikosti virtuálních počítačů](../../../articles/virtual-machines/virtual-machines-windows-sizes.md).

> [!TIP] 
> Pro rychlejší sestavení předřaďte objekty v datovém souboru zmenšením pravděpodobnosti.

<a name="build_grammar-command"></a>

## <a name="build_grammar-command"></a>build_grammar – příkaz

Příkaz **build_grammar** zkompiluje gramatiku ZADANOU v XML do binárního gramatického souboru.  Výsledný gramatický soubor lze použít ve spojení s indexovým souborem pro generování interpretace dotazů v přirozeném jazyce.

`kes.exe build_grammar <xmlFile> <grammarFile>`

| Parametr       | Popis               |
|-----------------|---------------------------|
| `<xmlFile>`     | Vstupní cesta specifikace gramatiky XML |
| `<grammarFile>` | Neznámá gramatická cesta k výstupu         |

Tyto soubory můžou být zadané pomocí místních cest souborů nebo cest URL k objektům blob Azure.  Specifikace gramatiky popisuje sadu vážených výrazů přirozeného jazyka a jejich sémantické výklady (viz [gramatický formát](GrammarFormat.md)).  Po úspěšném sestavení obsahuje výstupní soubor gramatiky binární reprezentaci gramatické specifikace, která umožňuje rychlé dekódování.

<a name="host_service-command"/>

## <a name="host_service-command"></a>host_service – příkaz

Příkaz **host_service** hostuje instanci služby KES na místním počítači.

`kes.exe host_service <grammarFile> <indexFile> [options]`

| Parametr       | Popis                |
|-----------------|----------------------------|
| `<grammarFile>` | Vstupní binární gramatická cesta         |
| `<indexFile>`   | Cesta ke vstupnímu binárnímu indexu           |
| `--port <port>` | Číslo místního portu  Výchozí: 8000 |

Tyto soubory můžou být zadané pomocí místních cest souborů nebo cest URL k objektům blob Azure.  Webová služba bude hostována na http://localhost:&lt;pí&gt;/.  Seznam podporovaných operací najdete v tématu [webová rozhraní API](WebAPI.md) .

Mimo prostředí Azure jsou místně hostované služby omezené na indexování souborů o velikosti až 1 MB, 10 požadavků za sekundu a všech voláních (1000).  Pokud chcete tato omezení překonat, spusťte **host_service** ve virtuálním počítači Azure nebo nasaďte do cloudové služby azure pomocí **deploy_service**.

<a name="deploy_service-command"/>

## <a name="deploy_service-command"></a>deploy_service – příkaz

Příkaz **deploy_service** nasadí instanci služby KES do cloudové služby Azure.

`kes.exe deploy_service <grammarFile> <indexFile> <serviceName> <vmSize>[options]`

| Parametr       | Popis                  |
|-----------------|------------------------------|
| `<grammarFile>` | Vstupní binární gramatická cesta           |
| `<indexFile>`   | Cesta ke vstupnímu binárnímu indexu             |
| `<serviceName>` | Název cílové cloudové služby |
| `<vmSize>`      | Velikost virtuálního počítače cloudové služby     |
| `--slot <slot>` | Slot pro cloudovou službu: "fázování" (výchozí), "produkční" |

Tyto soubory můžou být zadané pomocí místních cest souborů nebo cest URL k objektům blob Azure.  Název služby určuje předem nakonfigurovanou cloudovou službu Azure (viz [Vytvoření a nasazení cloudové služby](../../../articles/cloud-services/cloud-services-how-to-create-deploy-portal.md)).  Příkaz automaticky nasadí službu KES do zadané cloudové služby Azure pomocí virtuálních počítačů zadané velikosti.  Abyste se vyhnuli stránkování, které významně snižuje výkon, doporučujeme použít virtuální počítač s 1 GB paměti RAM, než je velikost vstupního souboru indexu.  Seznam dostupných velikostí virtuálních počítačů najdete v tématu [velikosti pro Cloud Services](../../../articles/cloud-services/cloud-services-sizes-specs.md).

Ve výchozím nastavení je služba nasazena do přípravného prostředí, volitelně přepsána prostřednictvím parametru--slot.  Seznam podporovaných operací najdete v tématu [webová rozhraní API](WebAPI.md) .

<a name="describe_index-command"/>

## <a name="describe_index-command"></a>describe_index – příkaz

Příkaz **describe_index** vypíše informace o souboru indexu, včetně schématu a popisu.

`kes.exe describe_index <indexFile>`

| Parametr     | Popis      |
|---------------|------------------|
| `<indexFile>` | Cesta ke vstupnímu indexu |

Tento soubor může být určený cestou k místnímu souboru nebo cestou k adrese URL objektu blob Azure.  Výstupní řetězec s popisem lze zadat pomocí parametru--Description příkazu **build_index** .

<a name="describe_grammar-command"/>

## <a name="describe_grammar-command"></a>describe_grammar – příkaz

Příkaz **describe_grammar** vypíše původní gramatickou specifikaci použitou k sestavení binární gramatiky.

`kes.exe describe_grammar <grammarFile>`

| Parametr       | Popis      |
|-----------------|------------------|
| `<grammarFile>` | Vstupní gramatická cesta |

Tento soubor může být určený cestou k místnímu souboru nebo cestou k adrese URL objektu blob Azure.

