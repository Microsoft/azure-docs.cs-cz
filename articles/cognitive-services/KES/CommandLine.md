---
title: Rozhraní příkazového řádku – Knowledge Exploration Service API
titlesuffix: Azure Cognitive Services
description: Sestavení indexu a gramatiky souborů z strukturovaných dat pomocí rozhraní příkazového řádku a potom je můžete nasadit jako webové služby.
services: cognitive-services
author: bojunehsu
manager: cgronlun
ms.service: cognitive-services
ms.component: knowledge-exploration
ms.topic: conceptual
ms.date: 03/24/2016
ms.author: paulhsu
ms.openlocfilehash: 7ec2282317019275b15a8e506753408c75a68561
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "46127133"
---
# <a name="command-line-interface"></a>Command Line Interface

Rozhraní příkazového řádku Knowledge Exploration Service (KES) poskytuje možnost vytvářet index a gramatiky soubory ze strukturovaných dat a jejich nasazení jako webové služby.  Používá Obecná syntaxe: `kes.exe <command> <required_args> [<optional_args>]`.  Můžete spustit `kes.exe` bez argumentů, chcete-li zobrazit seznam příkazů, nebo `kes.exe <command>` zobrazíte seznam argumentů, která je k dispozici pro zadaný příkaz.  Níže je seznam dostupných příkazů:

* build_index
* build_grammar
* host_service
* deploy_service
* describe_index
* describe_grammar

<a name="build_index-command"></a>

## <a name="buildindex-command"></a>build_index příkazu

**Build_index** příkaz sestaví binárního indexového souboru z definičního souboru schématu a soubor dat objektů, které chcete-li indexovat.  Výsledný soubor indexu je možné vyhodnotit výrazy strukturovaných dotazů nebo ke generování interpretace dotazy v přirozeném jazyce ve spojení se souborem kompilované gramatiky.

`kes.exe build_index <schemaFile> <dataFile> <indexFile> [options]`

| Parametr      | Popis               |
|----------------|---------------------------|
| `<schemaFile>` | Cesta vstupního schématu |
| `<dataFile>`   | Cesta vstupních dat   |
| `<indexFile>`  | Výstupní cesta indexu |
| `--description <description>` | Řetězec s popisem |
| `--remote <vmSize>`           | Velikost virtuálního počítače pro vzdálené sestavení |

Tyto soubory mohou být určeno místního souboru cesty nebo cesty adresy URL pro objekty BLOB Azure.  Soubor schématu popisuje strukturu objektů indexování a také operací, které mají být podporovány (viz [formát schématu](SchemaFormat.md)).  Objekty a hodnoty atributů indexovaných vytvoří výčet datový soubor (viz [formát dat](DataFormat.md)).  Po úspěšném sestavení obsahuje výstupní soubor indexu komprimované reprezentaci vstupní data, která podporuje požadované operace.  

Řetězec s popisem je případně možné zadat následně identifikovat binárního indexového pomocí **describe_index** příkazu.  

Ve výchozím nastavení je index založený na místním počítači.  Mimo prostředí Azure místní sestavení jsou omezené na datové soubory, které obsahují až 10 000 objektů.  Když--vzdálené označen příznakem, index bude založená na dočasně vytvořeného virtuálního počítače Azure o zadané velikosti.  Díky tomu velké indexy, které se efektivně použití virtuálních počítačů Azure s více paměti.  Aby se zabránilo stránkování, což může zpomalit proces sestavení, doporučujeme používat virtuální počítač s 3krát velikost paměti RAM jako velikost souboru vstupní data.  Seznam dostupných velikostí virtuálních počítačů najdete v tématu [velikosti virtuálních počítačů](../../../articles/virtual-machines/virtual-machines-windows-sizes.md).

> [!TIP] 
> Rychlejší sestavování presort snížením pravděpodobnost objekty v datovém souboru služby.

<a name="build_grammar-command"></a>

## <a name="buildgrammar-command"></a>build_grammar příkazu

**Build_grammar** příkaz zkompiluje gramatiky zadanému v souboru XML do souboru binární gramatika.  Výsledný soubor gramatiky umožňuje ve spojení s indexový soubor generovat interpretace dotazy v přirozeném jazyce.

`kes.exe build_grammar <xmlFile> <grammarFile>`

| Parametr       | Popis               |
|-----------------|---------------------------|
| `<xmlFile>`     | Vstupní cesta specifikace gramatika XML |
| `<grammarFile>` | Výstupní cesta kompilované gramatiky         |

Tyto soubory mohou být určeno místního souboru cesty nebo cesty adresy URL pro objekty BLOB Azure.  Specifikace gramatiky popisuje sadu výrazů vážený přirozeného jazyka a jejich sémantická interpretace (viz [formát gramatiky](GrammarFormat.md)).  Po úspěšném sestavení obsahuje výstupní soubor gramatiky binární vyjádření této specifikaci gramatiky umožňující rychlé dekódování.

<a name="host_service-command"/>

## <a name="hostservice-command"></a>host_service příkazu

**Host_service** příkaz je hostitelem instance služby KES v místním počítači.

`kes.exe host_service <grammarFile> <indexFile> [options]`

| Parametr       | Popis                |
|-----------------|----------------------------|
| `<grammarFile>` | Binární gramatika je vstupní cesta         |
| `<indexFile>`   | Vstupní binárního indexového cesta           |
| `--port <port>` | Číslo místního portu.  Výchozí: 8000 |

Tyto soubory mohou být určeno místního souboru cesty nebo cesty adresy URL pro objekty BLOB Azure.  Webová služba bude dostupný http://localhost:&lt; port&gt;/.  Zobrazit [webová rozhraní API](WebAPI.md) pro seznam podporovaných operací.

Prostředí, místně hostované služby, které jsou omezené na index mimo Azure soubory velikost 10 požadavků za sekundu a 1 000 volání celkem až 1 MB.  Chcete-li omezenému, spusťte **host_service** uvnitř virtuálního počítače Azure, nebo nasadit do cloudu Azure pomocí služby **deploy_service**.

<a name="deploy_service-command"/>

## <a name="deployservice-command"></a>deploy_service příkazu

**Deploy_service** příkaz nasadí instance KES služby do cloudové služby Azure.

`kes.exe deploy_service <grammarFile> <indexFile> <serviceName> <vmSize>[options]`

| Parametr       | Popis                  |
|-----------------|------------------------------|
| `<grammarFile>` | Binární gramatika je vstupní cesta           |
| `<indexFile>`   | Vstupní binárního indexového cesta             |
| `<serviceName>` | Název cílové cloudové služby |
| `<vmSize>`      | Velikost virtuálního počítače cloudové služby     |
| `--slot <slot>` | Slot cloudové služby: "pracovní" (výchozí), "produkční" |

Tyto soubory mohou být určeno místního souboru cesty nebo cesty adresy URL pro objekty BLOB Azure.  Určuje název služby předkonfigurované Azure cloud service (viz [jak vytvořit a nasadit Cloudovou službu](../../../articles/cloud-services/cloud-services-how-to-create-deploy-portal.md)).  Příkaz bude služba KES automaticky nasadit do Azure zadaná Cloudová služba, pomocí virtuálních počítačů zadané velikosti.  Aby se zabránilo stránkování, což významně snižuje výkon, doporučujeme pomocí virtuálního počítače s 1 GB víc paměti RAM než velikost souboru vstupní indexu.  Seznam dostupných velikostí virtuálních počítačů najdete v tématu [velikosti pro Cloud Services](../../../articles/cloud-services/cloud-services-sizes-specs.md).

Ve výchozím nasazení služby do přípravného prostředí, volitelně přepsané pomocí parametru--slot.  Zobrazit [webová rozhraní API](WebAPI.md) pro seznam podporovaných operací.

<a name="describe_index-command"/>

## <a name="describeindex-command"></a>příkaz describe_index

**Describe_index** příkaz vypíše informace o soubor indexu, včetně schéma a popis.

`kes.exe describe_index <indexFile>`

| Parametr     | Popis      |
|---------------|------------------|
| `<indexFile>` | Index vstupní cesta |

Tento soubor může být určeno místní cesta k souboru nebo adresu URL do objektu blob Azure.  Výstupní řetězec popis lze zadat pomocí parametru--popis **build_index** příkazu.

<a name="describe_grammar-command"/>

## <a name="describegrammar-command"></a>příkaz describe_grammar

**Describe_grammar** příkaz vypíše původní gramatiky specifikace sloužící k sestavení binární gramatika.

`kes.exe describe_grammar <grammarFile>`

| Parametr       | Popis      |
|-----------------|------------------|
| `<grammarFile>` | Gramatika vstupní cesta |

Tento soubor může být určeno místní cesta k souboru nebo adresu URL do objektu blob Azure.

