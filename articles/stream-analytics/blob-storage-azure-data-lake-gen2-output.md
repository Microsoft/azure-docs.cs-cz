---
title: Gen2 BLOB Storage a Azure Data Lake výstup z Azure Stream Analytics
description: Tento článek popisuje úložiště objektů BLOB a Azure Data Lake Gen 2 jako výstup pro Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: d9805c45b7c0af0cfe6410defaab7ea7725691d4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90907208"
---
# <a name="blob-storage-and-azure-data-lake-gen2-output-from-azure-stream-analytics"></a>Gen2 BLOB Storage a Azure Data Lake výstup z Azure Stream Analytics

Data Lake Storage Gen2 vytváří Azure Storage základ pro vytváření laků podnikových dat v Azure. Tato možnost je navržena od začátku až po obsluhu více petabajty informací a současně udržuje stovky gigabitových přenosů, Data Lake Storage Gen2 umožňuje snadno spravovat obrovské objemy dat. Základní část Data Lake Storage Gen2 je přidání hierarchického oboru názvů do úložiště objektů BLOB.

Azure Blob Storage nabízí nákladově efektivní a škálovatelné řešení pro ukládání velkých objemů nestrukturovaných dat v cloudu. Úvod do úložiště objektů BLOB a jeho použití najdete v tématu [nahrání, stažení a výpis objektů BLOB pomocí Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md).

## <a name="output-configuration"></a>Konfigurace výstupu

V následující tabulce jsou uvedeny názvy vlastností a jejich popisy pro vytvoření objektu BLOB nebo ADLS Gen2 výstup.

| Název vlastnosti       | Description                                                                      |
| ------------------- | ---------------------------------------------------------------------------------|
| Alias pro výstup        | Popisný název, který se používá v dotazech k směrování výstupu dotazu do tohoto úložiště objektů BLOB. |
| Účet úložiště     | Název účtu úložiště, do kterého posíláte výstup.               |
| Klíč účtu úložiště | Tajný klíč přidružený k účtu úložiště                              |
| Kontejner úložiště   | Logické seskupení pro objekty blob uložené v Azure Blob service. Po nahrání objektu blob do Blob service musíte zadat kontejner pro tento objekt BLOB. |
| Vzor cesty | Nepovinný parametr. Vzor cesty k souboru, který se používá k zápisu objektů BLOB v zadaném kontejneru. <br /><br /> Ve vzoru cesty se můžete rozhodnout použít jednu nebo více instancí proměnných data a času k určení četnosti zápisu objektů BLOB: <br /> {Date}, {Time} <br /><br />Vlastní dělení objektů blob můžete použít k určení jednoho vlastního názvu {Field} z dat události pro vytvoření oddílů objektů BLOB. Název pole je alfanumerický a může obsahovat mezery, spojovníky a podtržítka. Mezi vlastní pole patří tato omezení: <ul><li>V názvech polí se nerozlišují malá a velká písmena. Například služba nemůže odlišit sloupce "ID" a "ID".</li><li>Vnořená pole nejsou povolena. Místo toho použijte alias v dotazu úlohy na možnost "sloučit" pole.</li><li>Výrazy nelze použít jako název pole.</li></ul> <br />Tato funkce povoluje použití vlastních konfigurací specifikátoru formátu data a času v cestě. Vlastní formáty data a času musí být zadány po jednom a uzavřeny \<specifier> klíčovým slovem {DateTime:}. Povolené vstupy pro \<specifier> jsou rrrr, mm, m, DD, d, hh, H, mm, M, SS nebo s. Klíčové slovo {DateTime: \<specifier> } lze v cestě použít několikrát k vytvoření vlastních konfigurací data a času. <br /><br />Příklady: <ul><li>Příklad 1: Cluster1/logs/{Date}/{Time}</li><li>Příklad 2: Cluster1/logs/{Date}</li><li>Příklad 3: Cluster1/{client_id}/{Date}/{Time}</li><li>Příklad 4: Cluster1/{DateTime: SS}/{myField}, kde je dotaz: Vyberte data. myField jako myField ze vstupu;</li><li>Příklad 5: Cluster1/Year = {DateTime: rrrr}/month = {DateTime: MM}/Day = {DateTime: DD}</ul><br />Časové razítko vytvořené struktury složek se řídí časem UTC a ne místním časem.<br /><br />Pojmenovávání souborů používá následující konvenci: <br /><br />{Path – vzor předpony}/schemaHashcode_Guid_Number. Extension<br /><br />Příklady výstupních souborů:<ul><li>MyOutput/20170901/00/45434_gguid_1.csv</li>  <li>MyOutput/20170901/01/45434_gguid_1.csv</li></ul> <br />Další informace o této funkci najdete v tématu [Azure Stream Analytics vlastního dělení výstupu objektů BLOB](stream-analytics-custom-path-patterns-blob-storage-output.md). |
| Formát data | Nepovinný parametr. Pokud je token data použit v cestě předpony, můžete vybrat formát data, ve kterém jsou soubory uspořádány. Příklad: RRRR/MM/DD |
| Formát času | Nepovinný parametr. Pokud je časový token použit v cestě předpony, zadejte formát času, ve kterém jsou soubory uspořádány. V současné době je jedinou podporovanou hodnotou HH. |
| Formát serializace události | Formát serializace pro výstupní data. Podporují se JSON, CSV, Avro a Parquet. |
|Minimální řádky |Počet minimálních řádků na dávku V případě Parquet bude každá dávka vytvářet nový soubor. Aktuální výchozí hodnota je 2 000 řádků a povolené maximum je 10 000 řádků.|
|Maximální doba |Maximální doba čekání na dávku. Po uplynutí této doby bude dávka zapsána do výstupu i v případě, že požadavek na minimální řádky není splněn. Aktuální výchozí hodnota je 1 minuta a povolené maximum je 2 hodiny. Pokud váš výstup objektu BLOB má četnost vzorů cesty, doba čekání nesmí být vyšší než časový rozsah oddílu.|
| Encoding    | Pokud používáte formát CSV nebo JSON, je nutné zadat kódování. Formát UTF-8 v tuto chvíli podporuje pouze kódování UTF-8. |
| Oddělovač   | Platí pouze pro serializaci CSV. Stream Analytics podporuje řadu běžných oddělovačů pro serializaci dat CSV. Podporované hodnoty jsou čárka, středník, mezera, tabulátor a svislá čára. |
| Formát      | Platí pouze pro serializaci JSON. **Oddělený řádek** určuje, že výstup je formátován tak, že má každý objekt JSON oddělený novým řádkem. Pokud vyberete možnost **oddělený řádek**, bude JSON číst jeden objekt současně. Celý obsah samotný by nepředstavoval platný formát JSON. **Pole** určuje, že výstup je formátován jako pole objektů JSON. Toto pole je uzavřeno pouze v případě, že se úloha zastaví nebo Stream Analytics přesunula k následujícímu časovému intervalu. Obecně je vhodnější použít JSON oddělený řádkem, protože nevyžaduje žádné speciální zpracování, pokud je výstupní soubor stále zapisován do zápisu. |

## <a name="blob-output-files"></a>Výstupní soubory objektů BLOB

Pokud jako výstup používáte úložiště objektů blob, vytvoří se v objektu BLOB nový soubor v následujících případech:

* Pokud soubor překračuje maximální povolený počet bloků (aktuálně 50 000). Můžete dosáhnout maximálního povoleného počtu bloků, aniž byste dosáhli maximální povolené velikosti objektu BLOB. Například pokud je výstupní frekvence vysoká, můžete zobrazit více bajtů na blok a velikost souboru je větší. Pokud je výstupní frekvence nízká, každý blok má méně dat a velikost souboru je menší.
* Pokud se ve výstupu změní schéma a výstupní formát vyžaduje pevné schéma (CSV a Avro).
* Pokud se úloha restartuje, buď ji uživatel zastavuje, nebo ji zahájí, nebo je interně údržba systému nebo zotavení po chybě.
* Pokud je dotaz plně rozdělený a vytvoří se nový soubor pro každý výstupní oddíl.
* Pokud uživatel odstraní soubor nebo kontejner účtu úložiště.
* Pokud je výstupem čas rozdělení na oddíly pomocí vzoru předpony cesty a nový objekt BLOB se použije, když se dotaz přesune na další hodinu.
* Pokud je výstup rozdělený vlastním polem, vytvoří se nový objekt BLOB pro každý klíč oddílu, pokud neexistuje.
* Pokud je výstup rozdělený vlastním polem, u kterého klíč oddílu překračuje 8 000, a pro každý klíč oddílu se vytvoří nový objekt BLOB.

## <a name="partitioning"></a>Dělení

Pro klíč oddílu použijte {Date} a {Time} tokeny z polí události ve vzoru cesty. Vyberte formát data, například rrrr/MM/DD, DD/MM/RRRR nebo MM-DD-RRRR. HH se používá pro formát času. Výstup objektu BLOB se dá rozdělit na oddíly jedním vlastním atributem události {FieldName} nebo {DateTime: \<specifier> }. Počet zapisovačů výstupu následuje za vstupními oddíly pro [plně paralelizovat dotazy](stream-analytics-scale-jobs.md).

## <a name="output-batch-size"></a>Velikost výstupní dávky

Maximální velikost zprávy najdete v části [omezení Azure Storage](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits). Maximální velikost bloku objektu BLOB je 4 MB a maximální počet Bock objektů BLOB je 50 000. |

## <a name="next-steps"></a>Další kroky

* [Rychlý start: Vytvoření úlohy Stream Analytics pomocí webu Azure Portal](stream-analytics-quick-create-portal.md)
* [Rychlý Start: vytvoření úlohy Azure Stream Analytics pomocí Azure CLI](quick-create-azure-cli.md)
* [Rychlý Start: vytvoření úlohy Azure Stream Analytics pomocí šablony ARM](quick-create-azure-resource-manager.md)
* [Rychlý Start: vytvoření úlohy Stream Analytics pomocí Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Rychlý Start: vytvoření úlohy Azure Stream Analytics pomocí sady Visual Studio](stream-analytics-quick-create-vs.md)
* [Rychlý Start: vytvoření úlohy Azure Stream Analytics v Visual Studio Code](quick-create-visual-studio-code.md)
