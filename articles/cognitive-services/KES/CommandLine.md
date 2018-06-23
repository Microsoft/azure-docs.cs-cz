---
title: Rozhraní příkazového řádku služby zkoumání znalostní báze | Microsoft Docs
description: Pomocí rozhraní příkazového řádku KES sestavení indexu a gramatické souborů z strukturovaných dat a potom je nasadit jako webové služby v kognitivní služby společnosti Microsoft.
services: cognitive-services
author: bojunehsu
manager: stesp
ms.service: cognitive-services
ms.component: knowledge-exploration
ms.topic: article
ms.date: 03/24/2016
ms.author: paulhsu
ms.openlocfilehash: ffa42ac73b42a8271004d2d45d7a80f3307ef059
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342504"
---
# <a name="command-line-interface"></a>Command Line Interface
Rozhraní příkazového řádku KES poskytuje možnost pro sestavení indexu a gramatické soubory z strukturovaných dat a jejich nasazení jako webové služby.  Použije obecnou syntaxi: `kes.exe <command> <required_args> [<optional_args>]`.  Můžete spustit `kes.exe` bez argumentů, chcete-li zobrazit seznam příkazů, nebo `kes.exe <command>` zobrazíte seznam argumentů, která je k dispozici pro tento příkaz.  Níže uvádíme seznam dostupné příkazy:
* build_index
* build_grammar
* host_service
* deploy_service
* describe_index
* describe_grammar

<a name="build_index-command"></a>
## <a name="buildindex-command"></a>build_index příkaz
**Build_index** příkaz vytvoří soubor binární index z definičního souboru schématu a datový soubor objektů, které chcete indexovat.  Výsledný soubor indexu slouží k vyhodnocení výrazů strukturovaných dotazů nebo ke generování interpretace přirozeného jazyka dotazů ve spojení s kompilované gramatika souboru.

`kes.exe build_index <schemaFile> <dataFile> <indexFile> [options]`

| Parametr      | Popis               |
|----------------|---------------------------|
| `<schemaFile>` | Vstupní schéma cesta |
| `<dataFile>`   | Cesta ke vstupní data   |
| `<indexFile>`  | Výstupní cesta indexu |
| `--description <description>` | Popisný řetězec |
| `--remote <vmSize>`           | Velikost virtuálního počítače pro vzdálený sestavení |

Tyto soubory je možné zadat místní cesty souborů nebo cesty adresy URL do Azure BLOB.  Souboru schématu popisuje strukturu objektů probíhá indexování a také operace, které mají být podporovány (viz [formát schématu](SchemaFormat.md)).  Datový soubor zobrazí objekty a hodnoty atributů indexovaných (viz [formát dat](DataFormat.md)).  Při úspěšném sestavení indexu výstupní soubor obsahuje komprimované reprezentace vstupní data, která podporuje požadované operace.  

Popisný řetězec můžete volitelně zadat následně identifikovat binární index pomocí **describe_index** příkaz.  

Ve výchozím nastavení je index založený na místní počítač.  Mimo prostředí Azure místní sestavení jsou omezeny na datové soubory obsahující až 10 000 objektů.  Když--vzdálené zadán příznak, budou vytvořeny index na dočasně vytvořený virtuální počítač Azure zadané velikosti.  To umožňuje velké indexy, které má být sestaven efektivně virtuální počítače Azure pomocí více paměti.  Abyste se vyhnuli stránkování, což zpomalí procesu sestavení, doporučujeme používat virtuální počítač s 3krát velikost paměti RAM jako velikost souboru vstupní data.  Seznam dostupných velikostí virtuálních počítačů najdete v tématu [velikosti virtuálních počítačů](../../../articles/virtual-machines/virtual-machines-windows-sizes.md).

> [!TIP] 
> Pro rychlejší sestavení presort objekty v datovém souboru, a tím snižují pravděpodobnosti.

<a name="build_grammar-command"></a>
## <a name="buildgrammar-command"></a>build_grammar příkaz
**Build_grammar** příkaz zkompiluje zadaný v souboru XML do souboru binární gramatika gramatiku.  Výsledný soubor gramatika můžete použít ve spojení s indexový soubor ke generování interpretace dotazy v přirozeném jazyce.

`kes.exe build_grammar <xmlFile> <grammarFile>`

| Parametr       | Popis               |
|-----------------|---------------------------|
| `<xmlFile>`     | Vstupní XML gramatika specifikace cesta |
| `<grammarFile>` | Výstupní cesta kompilované – gramatika         |

Tyto soubory je možné zadat místní cesty souborů nebo cesty adresy URL do Azure BLOB.  Specifikace gramatika popisuje sadu vyvážené přirozeného jazyka výrazy a jejich sémantického interpretace (najdete v části [gramatika formátu](GrammarFormat.md)).  Při úspěšném sestavení gramatika výstupní soubor obsahuje binární reprezentace specifikace gramatika umožňující rychlé dekódování.

<a name="host_service-command"/>
## <a name="hostservice-command"></a>host_service příkaz
**Host_service** příkaz hostitelem instance služby KES v místním počítači.

`kes.exe host_service <grammarFile> <indexFile> [options]`

| Parametr       | Popis                |
|-----------------|----------------------------|
| `<grammarFile>` | Vstupní gramatika binární cesta         |
| `<indexFile>`   | Vstupní index binární cesta           |
| `--port <port>` | Číslem místního portu.  Výchozí: 8000 |

Tyto soubory je možné zadat místní cesty souborů nebo cesty adresy URL do Azure BLOB.  Webová služba se bude hostovat na http://localhost:&lt; port&gt;/.  V tématu [webovým rozhraním API](WebAPI.md) seznam podporované operace.

Prostředí, místně hostované služby je omezený na index mimo Azure soubory velikost 10 požadavků za sekundu a celkový počet volání 1000 až 1 MB.  Chcete-li omezenému, spusťte **host_service** uvnitř virtuálního počítače Azure, nebo nasazení do služby Azure cloud pomocí **deploy_service**.

<a name="deploy_service-command"/>
## <a name="deployservice-command"></a>deploy_service příkaz
**Deploy_service** příkaz nasadí instance KES služby do cloudové služby Azure.

`kes.exe deploy_service <grammarFile> <indexFile> <serviceName> <vmSize>[options]`

| Parametr       | Popis                  |
|-----------------|------------------------------|
| `<grammarFile>` | Vstupní gramatika binární cesta           |
| `<indexFile>`   | Vstupní index binární cesta             |
| `<serviceName>` | Název cílové cloudové služby |
| `<vmSize>`      | Velikost virtuální počítač cloudové služby     |
| `--slot <slot>` | Cloudové služby slotu: "přípravy" (výchozí), "výroba" |

Tyto soubory je možné zadat místní cesty souborů nebo cesty adresy URL do Azure BLOB.  Název služby určuje předkonfigurované Azure cloudové služby (viz [postup vytvoření a nasazení cloudové služby](../../../articles/cloud-services/cloud-services-how-to-create-deploy-portal.md)).  Příkaz automaticky nasadí službu KES ke službě zadaný Azure cloud pomocí virtuální počítače po zadanou velikost.  Abyste se vyhnuli stránkování, což významně snižuje výkon, doporučujeme použít virtuální počítač s 1 GB víc paměti RAM než velikost souboru vstupní index.  Seznam dostupných velikostí virtuálních počítačů najdete v tématu [velikosti pro cloudové služby](../../../articles/cloud-services/cloud-services-sizes-specs.md).

Ve výchozím nasazení služby do pracovní prostředí, volitelně přepsaného prostřednictvím parametr – slot.  V tématu [webovým rozhraním API](WebAPI.md) seznam podporované operace.

<a name="describe_index-command"/>
## <a name="describeindex-command"></a>příkaz describe_index
**Describe_index** příkaz výstupy informace o soubor indexu, včetně schéma a popis.

`kes.exe describe_index <indexFile>`

| Parametr     | Popis      |
|---------------|------------------|
| `<indexFile>` | Cesta vstupní indexu |

Tento soubor může být určena cesta k místnímu souboru nebo cestu adresy URL do objektu blob Azure.  Výstupní řetězec popisu je možné zadat pomocí – popis parametru **build_index** příkaz.

<a name="describe_grammar-command"/>
## <a name="describegrammar-command"></a>příkaz describe_grammar
**Describe_grammar** příkaz výstupy specifikace původní gramatika sloužící k vytvoření binární gramatika.

`kes.exe describe_grammar <grammarFile>`

| Parametr       | Popis      |
|-----------------|------------------|
| `<grammarFile>` | Vstupní gramatika cesta |

Tento soubor může být určena cesta k místnímu souboru nebo cestu adresy URL do objektu blob Azure.

