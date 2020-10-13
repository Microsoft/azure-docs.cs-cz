---
title: Azure Data Lake Storage výstup 1. generace z Azure Stream Analytics
description: Tento článek popisuje Azure Data Lake Storage Gen 1 jako možnost výstupu pro Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 59b4a04231df3b93b093750cc6c9d70982a418a9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91665340"
---
# <a name="azure-data-lake-storage-gen-1-output-from-azure-stream-analytics"></a>Azure Data Lake Storage výstup 1. generace z Azure Stream Analytics

Stream Analytics podporuje [Azure Data Lake Storage 1. generace](../data-lake-store/data-lake-store-overview.md) výstupů. Azure Data Lake Storage je škálovatelné úložiště s velkými objemy dat v celé organizaci. Data Lake Storage můžete použít k ukládání dat libovolné velikosti, typu a rychlosti příjmu pro provozní a průzkumné analýzy. Stream Analytics musí mít oprávnění pro přístup k Data Lake Storage.

Azure Data Lake Storage výstup z Stream Analytics není k dispozici v oblastech Azure Čína 21Vianet a Azure Německo (mezinárodní systémy).

## <a name="output-configuration"></a>Konfigurace výstupu

V následující tabulce jsou uvedeny názvy vlastností a jejich popisy pro konfiguraci Data Lake Storageho výstupu 1. generace.

| Název vlastnosti | Description |
| --- | --- |
| Alias pro výstup | Popisný název, který se v dotazech používá k přímému nasměrování výstupu dotazu na Data Lake Store. |
| Předplatné | Předplatné, které obsahuje váš účet Azure Data Lake Storage. |
| Název účtu | Název účtu Data Lake Store, do kterého posíláte výstup. Zobrazí se rozevírací seznam Data Lake Store účtů, které jsou k dispozici v rámci vašeho předplatného. |
| Vzor předpony cesty | Cesta k souboru, která se používá k zápisu souborů v rámci zadaného Data Lake Store účtu. Můžete zadat jednu nebo více instancí proměnných {Date} a {Time}:<br /><ul><li>Příklad 1: složku1/logs/{Date}/{Time}</li><li>Příklad 2: složku1/logs/{Date}</li></ul><br />Časové razítko vytvořené struktury složek se řídí časem UTC a ne místním časem.<br /><br />Pokud vzor cesty k souboru neobsahuje koncové lomítko (/), považuje se poslední vzor v cestě k souboru za předponu názvu souboru. <br /><br />V těchto případech se vytvoří nové soubory:<ul><li>Změna ve schématu výstupu</li><li>Externí nebo interní restartování úlohy</li></ul> |
| Formát data | Nepovinný parametr. Pokud je token data použit v cestě předpony, můžete vybrat formát data, ve kterém jsou soubory uspořádány. Příklad: RRRR/MM/DD |
|Formát času | Nepovinný parametr. Pokud je časový token použit v cestě předpony, zadejte formát času, ve kterém jsou soubory uspořádány. V současné době je jedinou podporovanou hodnotou HH. |
| Formát serializace události | Formát serializace pro výstupní data. Podporují se JSON, CSV a Avro.|
| Encoding | Pokud používáte formát CSV nebo JSON, je nutné zadat kódování. Formát UTF-8 v tuto chvíli podporuje pouze kódování UTF-8.|
| Oddělovač | Platí pouze pro serializaci CSV. Stream Analytics podporuje řadu běžných oddělovačů pro serializaci dat CSV. Podporované hodnoty jsou čárka, středník, mezera, tabulátor a svislá čára.|
| Formát | Platí pouze pro serializaci JSON. **Oddělený řádek** určuje, že výstup je formátován tak, že má každý objekt JSON oddělený novým řádkem. Pokud vyberete možnost **oddělený řádek**, bude JSON číst jeden objekt současně. Celý obsah samotný by nepředstavoval platný formát JSON.  **Pole** určuje, že výstup je formátován jako pole objektů JSON. Toto pole je uzavřeno pouze v případě, že se úloha zastaví nebo Stream Analytics přesunula k následujícímu časovému intervalu. Obecně je vhodnější použít JSON oddělený řádkem, protože nevyžaduje žádné speciální zpracování, pokud je výstupní soubor stále zapisován do zápisu.|
| Režim ověřování | Přístup k účtu Data Lake Storage můžete autorizovat pomocí [spravované identity](stream-analytics-managed-identities-adls.md) nebo tokenu uživatele. Po udělení přístupu můžete přístup odvolat změnou hesla uživatelského účtu, odstraněním Data Lake Storageho výstupu této úlohy nebo odstraněním úlohy Stream Analytics. |

## <a name="partitioning"></a>Dělení

Pro klíč oddílu použijte tokeny {Date} a {Time} ve vzoru předpony cesty. Vyberte formát data, například rrrr/MM/DD, DD/MM/RRRR nebo MM-DD-RRRR. Pro formát času použijte HH. Počet zapisovačů výstupu následuje za vstupními oddíly pro [plně paralelizovat dotazy](stream-analytics-scale-jobs.md).

## <a name="output-batch-size"></a>Velikost výstupní dávky

Maximální velikost zprávy najdete v části [omezení Data Lake Storage](../azure-resource-manager/management/azure-subscription-service-limits.md#data-lake-storage-limits). Pro optimalizaci velikosti dávky použijte až 4 MB na operaci zápisu.

## <a name="next-steps"></a>Další kroky

* [Rychlý start: Vytvoření úlohy Stream Analytics pomocí webu Azure Portal](stream-analytics-quick-create-portal.md)
* [Rychlý Start: vytvoření úlohy Azure Stream Analytics pomocí Azure CLI](quick-create-azure-cli.md)
* [Rychlý Start: vytvoření úlohy Azure Stream Analytics pomocí šablony ARM](quick-create-azure-resource-manager.md)
* [Rychlý Start: vytvoření úlohy Stream Analytics pomocí Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Rychlý Start: vytvoření úlohy Azure Stream Analytics pomocí sady Visual Studio](stream-analytics-quick-create-vs.md)
* [Rychlý Start: vytvoření úlohy Azure Stream Analytics v Visual Studio Code](quick-create-visual-studio-code.md)