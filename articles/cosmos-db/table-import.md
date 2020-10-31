---
title: Migrace existujících dat na účet rozhraní API pro tabulky v Azure Cosmos DB
description: Přečtěte si, jak migrovat nebo importovat místní nebo cloudová data do Azure rozhraní API pro tabulky účtu v Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 12/07/2017
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: e876ca028532bb3721146e90a91d68c4c12bf79f
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93096072"
---
# <a name="migrate-your-data-to-azure-cosmos-db-table-api-account"></a>Migrace dat do účtu rozhraní Table API služby Azure Cosmos DB
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

V tomto kurzu najdete pokyny k importu dat pro použití s Azure Cosmos DB [rozhraní API pro tabulky](table-introduction.md). Pokud máte data uložená ve službě Azure Table Storage, můžete k importu dat do rozhraní Table API služby Azure Cosmos DB použít nástroj pro migraci dat nebo AzCopy. Pokud máte data uložená v účtu rozhraní Table API služby Azure Cosmos DB (Preview), musíte k migraci dat použít nástroj pro migraci dat. 

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Import dat pomocí nástroje pro migraci dat
> * Import dat pomocí nástroje AzCopy
> * Migrace z rozhraní Table API (Preview) na rozhraní Table API 

## <a name="prerequisites"></a>Předpoklady

* **Zvýšit propustnost:** Doba trvání migrace dat závisí na množství propustnosti, kterou jste nastavili pro jednotlivé kontejnery nebo na sadu kontejnerů. V případě rozsáhlejších migrací dat nezapomeňte propustnost zvýšit. Po dokončení migrace propustnost snižte, abyste dosáhli nižších nákladů. Další informace o zvýšení propustnosti na webu Azure Portal najdete v tématu Úrovně výkonu a cenové úrovně ve službě Azure Cosmos DB.

* **Vytvoření prostředků služby Azure Cosmos DB:** Ještě před zahájením migrace dat vytvořte všechny tabulky na webu Azure Portal. Pokud provádíte migraci na účet služby Azure Cosmos DB, který má propustnost na úrovni databáze, nezapomeňte při vytváření tabulek Azure Cosmos DB zadat klíč oddílu.

## <a name="data-migration-tool"></a>Nástroj pro migraci dat

Nástroj příkazového řádku pro migraci dat Azure Cosmos DB (dt.exe) je možné použít k importu stávajících dat ve službě Azure Table Storage do účtu obecně dostupného rozhraní Table API nebo k migraci dat z účtu rozhraní Table API (Preview) do účtu obecně dostupného rozhraní Table API. Ostatní zdroje se v současné době nepodporují. Nástroj pro migraci dat s uživatelským rozhraním (dtui.exe) se v současné době nepodporuje pro účty rozhraní Table API. 

Pokud chcete provést migraci tabulkových dat, proveďte následující úlohy:

1. Stáhněte si nástroj pro migraci z [GitHubu](https://github.com/azure/azure-documentdb-datamigrationtool).
2. Spusťte `dt.exe` s použitím argumentů příkazového řádku pro váš scénář. `dt.exe` přijímá příkaz v následujícím formátu:

   ```bash
    dt.exe [/<option>:<value>] /s:<source-name> [/s.<source-option>:<value>] /t:<target-name> [/t.<target-option>:<value>] 
   ```

Podporované možnosti pro tento příkaz:

* **/Errorlog:** Volitelné. Název souboru CSV pro přesměrování selhání přenosu dat
* **/OverwriteErrorLog:** Volitelné. Přepsat soubor protokolu chyb
* **/ProgressUpdateInterval:** Volitelné, výchozí hodnota je 00:00:01. Časový interval pro obnovení průběhu přenosu dat na obrazovce
* **/ErrorDetails:** Volitelné, výchozí hodnota je None. Určuje, že se mají zobrazit podrobné informace o chybách pro následující chyby: žádné, kritické, vše.
* **/EnableCosmosTableLog:** Volitelné. Nasměrujte protokol na účet tabulky Cosmos. Pokud je tato hodnota nastavená, použije se výchozí hodnota připojovací řetězec cílového účtu, pokud není k dispozici ani/CosmosTableLogConnectionString. To je užitečné v případě, že je současně spuštěno více instancí DT.
* **/CosmosTableLogConnectionString:** Volitelné. ConnectionString pro přesměrování protokolu na vzdálený účet tabulky Cosmos.

### <a name="command-line-source-settings"></a>Nastavení zdroje na příkazovém řádku

Při definování služby Azure Table Storage nebo rozhraní Table API Preview jako zdroje migrace použijte následující možnosti zdroje.

* **/s: Azure:** Čte data z Azure Table Storage.
* **/s.ConnectionString:** Připojovací řetězec pro koncový bod tabulky Dá se načíst z Azure Portal
* **/s.LocationMode:** Volitelné, výchozí nastavení je PrimaryOnly. Určuje, který režim umístění se má použít při připojování ke službě Azure Table Storage: PrimaryOnly, PrimaryThenSecondary, SecondaryOnly, SecondaryThenPrimary.
* **/s.Table:** Název tabulky Azure
* **/s.InternalFields:** Pro import je potřeba nastavit vše pro migraci tabulky jako RowKey a PartitionKey.
* **/s.Filter:** Volitelné. Řetězec filtru, který se má použít
* **/s.projection:** Volitelné. Seznam sloupců k výběru

Pokud chcete načíst zdrojový připojovací řetězec při importu z Azure Table Storage, otevřete Azure Portal a klikněte na **Storage accounts**  >  **Account**  >  **přístupové klíče** účtu úložiště a pak pomocí tlačítka Kopírovat zkopírujte **připojovací řetězec** .

:::image type="content" source="./media/table-import/storage-table-access-key.png" alt-text="Snímek obrazovky, který zobrazuje účty úložiště > účtu > možnosti přístupových klíčů a zvýrazní tlačítko Kopírovat.":::

Pokud chcete při importu z účtu Azure Cosmos DB rozhraní API pro tabulky (Preview) načíst zdrojový připojovací řetězec, otevřete Azure Portal, klikněte na **Azure Cosmos DB**  >  **Account**  >  **připojovací řetězec** účtu a pomocí tlačítka Kopírovat zkopírujte **připojovací řetězec** .

:::image type="content" source="./media/table-import/cosmos-connection-string.png" alt-text="Snímek obrazovky, který zobrazuje účty úložiště > účtu > možnosti přístupových klíčů a zvýrazní tlačítko Kopírovat.":::

[Ukázkový příkaz pro Azure Table Storage](#azure-table-storage)

[Ukázkový příkaz pro rozhraní Table API služby Azure Cosmos DB (Preview)](#table-api-preview)

### <a name="command-line-target-settings"></a>Nastavení cíle na příkazovém řádku

Při definování rozhraní Table API služby Azure Storage jako cíle migrace použijte následující možnosti cíle.

* **/t: TableAPIBulk:** Nahrává data do tabulky Azure CosmosDB v dávkách.
* **/t.ConnectionString:** Připojovací řetězec pro koncový bod tabulky
* **/t.TableName:** Určuje název tabulky, do které se má zapisovat.
* **/t.Overwrite:** Volitelné, výchozí hodnota je false. Určuje, zda mají být přepsány existující hodnoty.
* **/t.MaxInputBufferSize:** Volitelné, výchozí hodnota je 1 GB. Přibližný odhad vstupních bajtů do vyrovnávací paměti před vyprázdněním dat do jímky
* **/t.throughput:** Volitelné, výchozí nastavení služby, pokud není zadáno. Určuje propustnost, která se má nakonfigurovat pro tabulku.
* **/t.MaxBatchSize:** Volitelné, výchozí nastavení je 2 MB. Zadejte velikost dávky v bajtech.

<a id="azure-table-storage"></a>
### <a name="sample-command-source-is-azure-table-storage"></a>Ukázkový příkaz: Zdrojem je Azure Table Storage

Tady je ukázka příkazového řádku znázorňující provedení importu ze služby Azure Table Storage do rozhraní Table API:

```bash
dt /s:AzureTable /s.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Table storage account name>;AccountKey=<Account Key>;EndpointSuffix=core.windows.net /s.Table:<Table name> /t:TableAPIBulk /t.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Cosmos DB account name>;AccountKey=<Azure Cosmos DB account key>;TableEndpoint=https://<Account name>.table.cosmosdb.azure.com:443 /t.TableName:<Table name> /t.Overwrite
```

<a id="table-api-preview"></a>
### <a name="sample-command-source-is-azure-cosmos-db-table-api-preview"></a>Ukázkový příkaz: Zdrojem je rozhraní Table API služby Azure Cosmos DB (Preview)

Tady je ukázka příkazového řádku pro import z rozhraní Table API Preview do obecně dostupného rozhraní Table API:

```bash
dt /s:AzureTable /s.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Table API preview account name>;AccountKey=<Table API preview account key>;TableEndpoint=https://<Account Name>.documents.azure.com; /s.Table:<Table name> /t:TableAPIBulk /t.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Cosmos DB account name>;AccountKey=<Azure Cosmos DB account key>;TableEndpoint=https://<Account name>.table.cosmosdb.azure.com:443 /t.TableName:<Table name> /t.Overwrite
```

## <a name="migrate-data-by-using-azcopy"></a>Migrace dat pomocí AzCopy

Druhou možností, jak migrovat data ze služby Azure Table Storage do rozhraní Table API služby Azure Cosmos DB, je použít nástroj příkazového řádku AzCopy. Pokud chcete použít AzCopy, je potřeba nejprve data exportovat, jak je popsáno v části [Export dat ze služby Table Storage](/previous-versions/azure/storage/storage-use-azcopy#export-data-from-table-storage), a pak data importovat do služby Azure Cosmos DB, jak je popsáno v části [Rozhraní Table API služby Azure Cosmos DB](/previous-versions/azure/storage/storage-use-azcopy#import-data-into-table-storage).

Při provádění importu do služby Azure Cosmos DB použijte jako referenci následující ukázku. Všimněte si, že v hodnotě /Dest se používá cosmosdb, a ne core.

Ukázka příkazu pro import:

```bash
AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.table.cosmosdb.windows.net/mytable1/ /DestKey:key /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:InsertOrReplace
```

## <a name="migrate-from-table-api-preview-to-table-api"></a>Migrace z rozhraní Table API (Preview) na rozhraní Table API

> [!WARNING]
> Pokud chcete okamžitě začít využívat výhod obecně dostupných tabulek, migrujte své stávající tabulky verze Preview, jak je popsáno v této části. Jinak pro stávající zákazníky verze Preview v nadcházejících týdnech provedeme automatickou migraci. Mějte však na paměti, že pro automaticky migrované databáze verze Preview budou platit určitá omezení, která se na nově vytvořené tabulky vztahovat nebudou.

Rozhraní Table API je teď obecně dostupné. Mezi tabulkami verze Preview a obecně dostupnými tabulkami existují rozdíly jak v kódu, který běží v cloudu, tak v kódu, který běží v klientovi. Proto se nedoporučuje pokoušet se o kombinaci klienta sady SDK verze Preview a účtu obecně dostupného rozhraní Table API a naopak. Zákazníci rozhraní Table API Preview, kteří chtějí i nadále používat své stávající tabulky, ale v produkčním prostředí, musí provést migraci z verze Preview do obecně dostupného prostředí nebo počkat na automatickou migraci. Pokud počkáte na automatickou migraci, dostanete upozornění na omezení migrovaných tabulek. Po migraci budete ve svém stávajícím účtu moct vytvářet nové tabulky bez omezení (omezení se budou vztahovat pouze na migrované tabulky).

Migrace z rozhraní Table API (Preview) na obecně dostupné rozhraní Table API:

1. Vytvořte nový účet služby Azure Cosmos DB a nastavte jeho typ rozhraní API na Azure Table, jak je popsáno v části [Vytvoření účtu databáze](create-table-dotnet.md#create-a-database-account).

2. Upravte klienty tak, aby používali obecně dostupnou verzi sad [Table API SDK](table-sdk-dotnet.md).

3. Pomocí nástroje pro migraci dat migrujte klientská data z tabulek verze Preview do obecně dostupných tabulek. Pokyny pro použití nástroje pro migraci dat k tomuto účelu jsou popsané v části [Nástroj pro migraci dat](#data-migration-tool). 

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Import dat pomocí nástroje pro migraci dat
> * Import dat pomocí nástroje AzCopy
> * Migrace z rozhraní Table API (Preview) na rozhraní Table API

Teď můžete pokračovat k dalšímu kurzu, kde zjistíte, jak dotazovat data pomocí rozhraní Table API služby Azure Cosmos DB. 

> [!div class="nextstepaction"]
>[Jak dotazovat data?](../cosmos-db/tutorial-query-table.md)