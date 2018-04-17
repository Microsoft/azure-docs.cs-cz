---
title: Umožňuje importovat data pro použití s rozhraním API pro Azure Cosmos DB tabulky | Microsoft Docs
description: Zjistěte, jak importovat data pomocí rozhraní API služby Azure DB Cosmos tabulky.
services: cosmos-db
author: SnehaGunda
manager: kfile
documentationcenter: ''
ms.assetid: b60743e2-0227-43ab-965a-0ae3ebacd917
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: sngun
ms.openlocfilehash: 7a8eaaaaa8bbfa8cf9397ce52dfa93d71b0df30c
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="import-data-for-use-with-the-azure-cosmos-db-table-api"></a>Umožňuje importovat data pro použití s rozhraní API služby Azure DB Cosmos tabulky

Tento kurz obsahuje pokyny týkající se importu dat pro použití s Azure Cosmos DB [tabulky API](table-introduction.md). Pokud máte data uložená ve službě Azure Table storage, můžete použít buď nástroj pro migraci dat nebo AzCopy k importu vaše data. Pokud máte data uložená v účtu Azure Cosmos DB tabulky API (preview), musíte použít nástroj Migrace dat pro migraci dat. Po importu dat je budete moct využívat nabídky Azure Cosmos DB jako připraveného globální distribuční, vyhrazené propustnosti, latenci jednociferné milisekund na 99th percentilu, zaručit vysoká dostupnost, pomocí možnosti premium a automatické sekundární indexování.

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Import dat pomocí nástroje migrace dat
> * Import dat pomocí nástroje AzCopy
> * Migrace z tabulky rozhraní API (preview) na rozhraní API tabulky 

## <a name="data-migration-tool"></a>Nástroj pro migraci dat

Nástroj příkazového řádku Azure Cosmos DB Data migrace (dt.exe) lze použít k importu existujících dat úložiště Azure Table API tabulky GA účtu nebo migrovat data z účtu tabulky rozhraní API (preview) do tabulky API GA účtu. Další zdroje nejsou aktuálně podporovány. Uživatelské rozhraní na základě migrace dat nástroj (dtui.exe) se aktuálně nepodporuje pro tabulku API účty. 

Chcete-li provést migraci dat v tabulce, proveďte následující úlohy:

1. Stáhněte si nástroj pro migraci z [Githubu](https://github.com/azure/azure-documentdb-datamigrationtool).
2. Spustit `dt.exe` pomocí argumenty příkazového řádku pro váš scénář.

DT.exe trvá příkaz v následujícím formátu:

    dt.exe [/<option>:<value>] /s:<source-name> [/s.<source-option>:<value>] /t:<target-name> [/t.<target-option>:<value>] 

Jsou možnosti pro příkaz:

    /ErrorLog: Optional. Name of the CSV file to redirect data transfer failures
    /OverwriteErrorLog: Optional. Overwrite error log file
    /ProgressUpdateInterval: Optional, default is 00:00:01. Time interval to refresh on-screen data transfer progress
    /ErrorDetails: Optional, default is None. Specifies that detailed error information should be displayed for the following errors: None, Critical, All

### <a name="command-line-source-settings"></a>Nastavení příkazového řádku zdroje

Při definování náhled úložiště tabulek Azure nebo rozhraní API tabulky jako zdroj migrace, použijte následující možnosti zdroje.

    /s:AzureTable: Reads data from Azure Table storage
    /s.ConnectionString: Connection string for the table endpoint. This can be retrieved from the Azure portal
    /s.LocationMode: Optional, default is PrimaryOnly. Specifies which location mode to use when connecting to Azure Table storage: PrimaryOnly, PrimaryThenSecondary, SecondaryOnly, SecondaryThenPrimary
    /s.Table: Name of the Azure Table
    /s.InternalFields: Set to All for table migration as RowKey and PartitionKey are required for import.
    /s.Filter: Optional. Filter string to apply
    /s.Projection: Optional. List of columns to select

Chcete-li načíst řetězec připojení zdroje při importu z úložiště tabulek Azure, otevřete portál Azure a klikněte na **účty úložiště** > **účet**  >   **Přístupové klíče**a pak použijte tlačítko Kopírovat, pokud pro kopírování **připojovací řetězec**.

![Možnosti nastavení zdroje snímek HBase](./media/table-import/storage-table-access-key.png)

Chcete-li načíst řetězec připojení zdroje při importu z účtu Azure Cosmos DB tabulky API (preview), otevřete Azure portálu klikněte na **Azure Cosmos DB** > **účet**  >  **Připojovací řetězec** a použijte tlačítko Kopírovat, pokud pro kopírování **připojovací řetězec**.

![Možnosti nastavení zdroje snímek HBase](./media/table-import/cosmos-connection-string.png)

[Příkaz Ukázka Azure Table Storage](#azure-table-storage)

[Příkaz Ukázka Azure Cosmos DB tabulky API (preview)](#table-api-preview)

### <a name="command-line-target-settings"></a>Nastavení cílového příkazového řádku

Při definování rozhraní API služby Azure Cosmos DB tabulky jako cíl migrace, použijte následující možnosti cíl.

    /t:TableAPIBulk: Uploads data into Azure CosmosDB Table in batches
    /t.ConnectionString: Connection string for the table endpoint
    /t.TableName: Specifies the name of the table to write to
    /t.Overwrite: Optional, default is false. Specifies if existing values should be overwritten
    /t.MaxInputBufferSize: Optional, default is 1GB. Approximate estimate of input bytes to buffer before flushing data to sink
    /t.Throughput: Optional, service defaults if not specified. Specifies throughput to configure for table
    /t.MaxBatchSize: Optional, default is 2MB. Specify the batch size in bytes

<a id="azure-table-storage"></a>
### <a name="sample-command-source-is-azure-table-storage"></a>Ukázkový příkaz: Azure Table storage je zdroj

Zde je ukázka příkazového řádku znázorňující postup importu z úložiště tabulek Azure Table API:

```
dt /s:AzureTable /s.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Table storage account name>;AccountKey=<Account Key>;EndpointSuffix=core.windows.net /s.Table:<Table name> /t:TableAPIBulk /t.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Cosmos DB account name>;AccountKey=<Azure Cosmos DB account key>;TableEndpoint=https://<Account name>.table.cosmosdb.azure.com:443 /t.TableName:<Table name> /t.Overwrite
```
<a id="table-api-preview"></a>
### <a name="sample-command-source-is-azure-cosmos-db-table-api-preview"></a>Ukázkový příkaz: zdroj je rozhraní API DB tabulky Azure Cosmos (preview)

Zde je ukázka příkazového řádku k importu z verze preview rozhraní API tabulky do tabulky API GA:

```
dt /s:AzureTable /s.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Table API preview account name>;AccountKey=<Table API preview account key>;TableEndpoint=https://<Account Name>.documents.azure.com; /s.Table:<Table name> /t:TableAPIBulk /t.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Cosmos DB account name>;AccountKey=<Azure Cosmos DB account key>;TableEndpoint=https://<Account name>.table.cosmosdb.azure.com:443 /t.TableName:<Table name> /t.Overwrite
```

## <a name="azcopy-command"></a>Příkaz AzCopy

Pomocí nástroje příkazového řádku azcopy je další možností pro migraci dat z Azure Table storage do rozhraní API služby Azure DB Cosmos tabulky. Použití nástroje AzCopy, je nejprve exportovat data jak je popsáno v [exportovat data z úložiště tabulek](../storage/common/storage-use-azcopy.md#export-data-from-table-storage), pak importovat data do databáze Cosmos Azure, jak je popsáno v [rozhraní API služby Azure Cosmos DB tabulky](../storage/common/storage-use-azcopy.md#import-data-into-table-storage).

Při importu do Azure Cosmos DB, naleznete v následujícím příkladu. Všimněte si, že hodnota/dest používá cosmosdb, ne jádro.

Příkaz import příklad:

```
AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.table.cosmosdb.windows.net/mytable1/ /DestKey:key /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:InsertOrReplace
```

## <a name="migrate-from-table-api-preview-to-table-api"></a>Migrace z tabulky API (preview) do tabulky rozhraní API

> [!WARNING]
> Pokud chcete okamžitě využívat výhod obecně dostupných tabulek pak proveďte prosím migraci vaší stávající verzi preview tabulky jako zadaná v této části, v opačném případě jsme bude provádět automatických migrací pro stávající zákazníky preview v následujících týdnech, Všimněte si, ale který auto migrovat preview tabulky budou mít určitá omezení k nim, které nově vytvořené tabulky budou není.
> 

Rozhraní API tabulky je nyní všeobecně dostupná (GA). Jsou rozdíly mezi preview a verze GA tabulek v kód, který běží v cloudu i v kódu, který běží na straně klienta. Proto se nedoporučuje k pokusu o kombinovat s účtem GA tabulky rozhraní API klienta SDK preview a naopak. Rozhraní API preview zákazníci, kteří chtějí dál používat své existující tabulky, ale v produkčním prostředí muset migraci z verze preview GA prostředí, nebo počkejte Automatická migrace tabulky. Pokud počkat Automatická migrace, budete upozorněni, omezení pro migrované tabulky. Po migraci bude moct vytvářet nové tabulky na existujícího účtu bez omezení (pouze migrované tabulek bude mít omezení).

Při migraci z tabulky rozhraní API (preview) do rozhraní API všeobecně dostupná tabulky:

1. Vytvořit nový účet Azure Cosmos DB a nastavte její typ rozhraní API do tabulky Azure, jak je popsáno v [vytvoření databázového účtu](create-table-dotnet.md#create-a-database-account).

2. Změnit klientům používat GA verzi [tabulky rozhraní API sady SDK](table-sdk-dotnet.md).

3. Migrace dat klienta z tabulky preview do tabulek GA pomocí nástroje migrace dat. Pokyny týkající se použití nástroj pro migraci dat pro tento účel, jsou popsané v [nástroj pro migraci dat](#data-migration-tool). 

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Umožňuje importovat data pomocí nástroje migrace dat
> * Import dat pomocí nástroje AzCopy
> * Migrace z tabulky API (preview) do tabulky rozhraní API

Teď můžete pokračovat v dalším kurzu a zjistěte, jak zadávat dotazy na data pomocí rozhraní API služby Azure DB Cosmos tabulky. 

> [!div class="nextstepaction"]
>[Postup dotazování dat?](../cosmos-db/tutorial-query-table.md)
