---
title: Azure Cosmos DB výstup z Azure Stream Analytics
description: Tento článek popisuje, jak výstupní data z Azure Stream Analytics do Azure Cosmos DB.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 9382ac8dc71d1ccb3a85dc0a7a027c8e99296cc6
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2021
ms.locfileid: "98016573"
---
# <a name="azure-cosmos-db-output-from-azure-stream-analytics"></a>Azure Cosmos DB výstup z Azure Stream Analytics

[Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) je globálně distribuovaná databázová služba, která nabízí neomezené Elastické škálování po celém světě, bohatě vydaný dotaz a automatické indexování prostřednictvím datových modelů nezávislá schématu. Další informace o možnostech kontejneru Azure Cosmos DB pro Stream Analytics naleznete v článku [Stream Analytics s Azure Cosmos DB jako výstup](stream-analytics-documentdb-output.md) .

Azure Cosmos DB výstup z Stream Analytics v současnosti není k dispozici v oblastech Azure Čína 21Vianet a Azure Německo (mezinárodní).

> [!Note]
> Azure Stream Analytics podporuje připojení pouze k Azure Cosmos DB pomocí rozhraní SQL API.
> Jiná rozhraní API Azure Cosmos DB ještě nejsou podporovaná. Pokud přejdete Azure Stream Analytics na účty Azure Cosmos DB vytvořené pomocí jiných rozhraní API, data nemusí být správně uložená.

Následující tabulka popisuje vlastnosti pro vytvoření výstupu Azure Cosmos DB.

| Název vlastnosti | Popis |
| --- | --- |
| Alias pro výstup | Alias pro odkazování na tento výstup v dotazu Stream Analytics. |
| Jímka | Azure Cosmos DB. |
| Možnost importu | Zvolte buď **možnost vybrat Cosmos DB z předplatného** , nebo **Zadejte Cosmos DB nastavení ručně**.
| Account ID | Název nebo identifikátor URI koncového bodu účtu Azure Cosmos DB. |
| Klíč účtu | Sdílený přístupový klíč pro účet Azure Cosmos DB. |
| Databáze | Azure Cosmos DB název databáze. |
| Název kontejneru | Název kontejneru, který se má použít, který musí existovat v Cosmos DB. Příklad:  <br /><ul><li> _MyContainer_: musí existovat kontejner s názvem "MyContainer".</li>|
| ID dokumentu |Nepovinný parametr. Název pole ve výstupních událostech, které slouží k určení primárního klíče, na kterém jsou založeny operace INSERT nebo Update.

## <a name="partitioning"></a>Dělení na oddíly

Klíč oddílu je založen na klauzuli PARTITION BY v dotazu. Počet zapisovačů výstupu následuje za vstupními oddíly pro [plně paralelní dotazy](stream-analytics-scale-jobs.md). Stream Analytics převede Cosmos DB výstupní klíč oddílu na řetězec. Například pokud máte klíč oddílu s hodnotou 1 typu bigint, je převedena na "1" typu řetězce. Tento převod vždy probíhá bez ohledu na to, zda je do Cosmos DB zapisována vlastnost partition.

## <a name="output-batch-size"></a>Velikost výstupní dávky

Maximální velikost zprávy najdete v části [omezení Azure Cosmos DB](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-cosmos-db-limits). Velikost dávky a frekvence zápisu se dynamicky upravují na základě Azure Cosmos DBch odpovědí. Neexistují předem vymezená omezení Stream Analytics.

## <a name="next-steps"></a>Další kroky

* [Rychlý start: Vytvoření úlohy Stream Analytics pomocí webu Azure Portal](stream-analytics-quick-create-portal.md)
* [Rychlý Start: vytvoření úlohy Azure Stream Analytics pomocí Azure CLI](quick-create-azure-cli.md)
* [Rychlý Start: vytvoření úlohy Azure Stream Analytics pomocí šablony ARM](quick-create-azure-resource-manager.md)
* [Rychlý Start: vytvoření úlohy Stream Analytics pomocí Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Rychlý Start: vytvoření úlohy Azure Stream Analytics pomocí sady Visual Studio](stream-analytics-quick-create-vs.md)
* [Rychlý Start: vytvoření úlohy Azure Stream Analytics v Visual Studio Code](quick-create-visual-studio-code.md)
