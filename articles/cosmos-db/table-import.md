---
title: Migrace existujících dat na účet rozhraní API pro tabulky v Azure Cosmos DB
description: Naučte se migrovat nebo importovat místní nebo cloudová data do účtu Azure rozhraní API pro tabulky v Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 12/07/2017
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 77f4c928db695bd4193ad46c93e0efbd16decf29
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106443334"
---
# <a name="migrate-your-data-to-an-azure-cosmos-db-table-api-account"></a>Migrace dat na účet Azure Cosmos DB rozhraní API pro tabulky
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

V tomto kurzu najdete pokyny k importu dat pro použití s Azure Cosmos DB [rozhraní API pro tabulky](table-introduction.md). Pokud máte data uložená v Azure Table Storage, můžete k importu dat do Azure Cosmos DB rozhraní API pro tabulky použít buď nástroj pro migraci dat, nebo AzCopy. 

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Import dat pomocí nástroje pro migraci dat
> * Import dat pomocí nástroje AzCopy

## <a name="prerequisites"></a>Požadavky

* **Zvýšit propustnost:** Doba trvání migrace dat závisí na množství propustnosti, kterou jste nastavili pro jednotlivé kontejnery nebo na sadu kontejnerů. V případě rozsáhlejších migrací dat nezapomeňte propustnost zvýšit. Po dokončení migrace propustnost snižte, abyste dosáhli nižších nákladů.

* **Vytvořit prostředky Azure Cosmos DB:** Než začnete s migrací dat, vytvořte z Azure Portal všechny tabulky. Pokud provádíte migraci na účet Azure Cosmos DB, který má propustnost na úrovni databáze, nezapomeňte při vytváření tabulek Azure Cosmos DB zadat klíč oddílu.

## <a name="data-migration-tool"></a>Nástroj pro migraci dat

Nástroj pro migraci dat z příkazového řádku (dt.exe) v Azure Cosmos DB můžete použít k importu stávajících dat služby Azure Table Storage do účtu rozhraní API pro tabulky. 

Migrace dat tabulky:

1. Stáhněte si nástroj pro migraci z [GitHubu](https://github.com/azure/azure-documentdb-datamigrationtool).
2. Spusťte `dt.exe` pomocí argumentů příkazového řádku pro váš scénář. `dt.exe` přijímá příkaz v následujícím formátu:

   ```bash
    dt.exe [/<option>:<value>] /s:<source-name> [/s.<source-option>:<value>] /t:<target-name> [/t.<target-option>:<value>] 
   ```

Podporované možnosti pro tento příkaz:

* **/Errorlog:** Volitelné. Název souboru CSV pro přesměrování selhání přenosu dat
* **/OverwriteErrorLog:** Volitelné. Přepsat soubor protokolu chyb.
* **/ProgressUpdateInterval:** Volitelné, výchozí nastavení je `00:00:01` . Časový interval pro obnovení průběhu přenosu dat na obrazovce.
* **/ErrorDetails:** Volitelné, výchozí nastavení je `None` . Určuje, že se mají zobrazit podrobné informace o chybách pro následující chyby: `None` , `Critical` nebo `All` .
* **/EnableCosmosTableLog:** Volitelné. Nasměrujte protokol na účet Azure Cosmos DB tabulky. Pokud je tato hodnota nastavená, použije se výchozí připojovací řetězec cílového účtu, pokud `/CosmosTableLogConnectionString` není k dispozici ani ta. To je užitečné, pokud je současně spuštěno více instancí nástroje.
* **/CosmosTableLogConnectionString:** Volitelné. Připojovací řetězec pro přesměrování protokolu na účet vzdálené Azure Cosmos DB tabulky.

### <a name="command-line-source-settings"></a>Nastavení zdroje na příkazovém řádku

Při definování Table Storage Azure jako zdroje migrace použijte následující možnosti zdroje.

* **/s: Azure:** Načte data z Table Storage.
* **/s.ConnectionString:** Připojovací řetězec pro koncový bod tabulky Můžete ho načíst z Azure Portal.
* **/s.LocationMode:** Volitelné, výchozí nastavení je `PrimaryOnly` . Určuje, který režim umístění se má použít při připojování k Table Storage: `PrimaryOnly` , `PrimaryThenSecondary` , `SecondaryOnly` , `SecondaryThenPrimary` .
* **/s.Table:** Název tabulky Azure
* **/s.InternalFields:** Nastaveno na `All` pro migraci tabulky, protože `RowKey` a `PartitionKey` jsou vyžadovány pro import.
* **/s.Filter:** Volitelné. Řetězec filtru, který se má použít
* **/s.projection:** Volitelné. Seznam sloupců, které se mají vybrat

Pokud chcete načíst zdrojový připojovací řetězec při importu z Table Storage, otevřete Azure Portal. Vyberte   >    >  **přístupové klíče** účtu úložiště a zkopírujte **připojovací řetězec**.

:::image type="content" source="./media/table-import/storage-table-access-key.png" alt-text="Snímek obrazovky, který zobrazuje účty úložiště > účtu > možnosti přístupových klíčů a zvýrazní ikonu kopírování.":::

### <a name="command-line-target-settings"></a>Nastavení cíle na příkazovém řádku

Použijte následující cílové možnosti při definování Azure Cosmos DB rozhraní API pro tabulky jako cíle migrace.

* **/t: TableAPIBulk:** Nahraje data do Azure Cosmos DB rozhraní API pro tabulky v dávkách.
* **/t.ConnectionString:** Připojovací řetězec pro koncový bod tabulky
* **/t.TableName:** Určuje název tabulky, do které se má zapisovat.
* **/t.Overwrite:** Volitelné, výchozí nastavení je `false` . Určuje, zda mají být přepsány existující hodnoty.
* **/t.MaxInputBufferSize:** Volitelné, výchozí nastavení je `1GB` . Přibližný odhad vstupních bajtů do vyrovnávací paměti před vyprázdněním dat do jímky.
* **/t.throughput:** Volitelné, výchozí nastavení služby, pokud není zadáno. Určuje propustnost, která se má nakonfigurovat pro tabulku.
* **/t.MaxBatchSize:** Volitelné, výchozí nastavení je `2MB` . Zadejte velikost dávky v bajtech.

### <a name="sample-command-source-is-table-storage"></a>Vzorový příkaz: zdroj je Table Storage

Zde je příklad příkazového řádku, který ukazuje, jak importovat z Table Storage do rozhraní API pro tabulky:

```bash
dt /s:AzureTable /s.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Table storage account name>;AccountKey=<Account Key>;EndpointSuffix=core.windows.net /s.Table:<Table name> /t:TableAPIBulk /t.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Cosmos DB account name>;AccountKey=<Azure Cosmos DB account key>;TableEndpoint=https://<Account name>.table.cosmosdb.azure.com:443 /t.TableName:<Table name> /t.Overwrite
```

## <a name="migrate-data-by-using-azcopy"></a>Migrace dat pomocí AzCopy

K migraci dat z Table Storage do rozhraní API pro tabulky Azure Cosmos DB můžete použít také nástroj příkazového řádku AzCopy. Chcete-li použít AzCopy, je třeba nejprve exportovat data, jak je popsáno v tématu [Export dat z Table Storage](/previous-versions/azure/storage/storage-use-azcopy#export-data-from-table-storage). Pak data naimportujete do Azure Cosmos DB, jak je popsáno v [Azure Cosmos DB rozhraní API pro tabulky](/previous-versions/azure/storage/storage-use-azcopy#import-data-into-table-storage).

Pokud importujete do Azure Cosmos DB, přečtěte si následující ukázku. Všimněte si, že `/Dest` hodnota používá `cosmosdb` , ne `core` .

Ukázka příkazu pro import:

```bash
AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.table.cosmosdb.windows.net/mytable1/ /DestKey:key /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:InsertOrReplace
```

## <a name="next-steps"></a>Další kroky

Naučte se, jak zadávat dotazy na data pomocí rozhraní API pro tabulky Azure Cosmos DB. 

> [!div class="nextstepaction"]
>[Jak dotazovat data?](../cosmos-db/tutorial-query-table.md)




