---
title: Konfigurace privátních koncových bodů pro Azure Cosmos DB analytické úložiště
description: Naučte se, jak nastavit spravované privátní koncové body pro Azure Cosmos DB analytické úložiště pro omezení přístupu k síti.
author: AnithaAdusumilli
ms.service: cosmos-db
ms.topic: how-to
ms.date: 03/02/2021
ms.author: anithaa
ms.openlocfilehash: 2f15b397fbceb9e097d94080ba03fba50a96ed06
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102048501"
---
# <a name="configure-private-endpoints-for-azure-cosmos-db-analytical-store"></a>Konfigurace privátních koncových bodů pro Azure Cosmos DB analytické úložiště
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

V tomto článku se dozvíte, jak nastavit spravované privátní koncové body pro Azure Cosmos DB analytické úložiště. Pokud používáte transakční úložiště, přečtěte si část [soukromé koncové body článku o transakčním úložišti](how-to-configure-private-endpoints.md) . Pomocí spravovaných privátních koncových bodů můžete omezit přístup k síti Azure Cosmos DBho analytického úložiště do Azure synapse spravované virtuální sítě. Spravované soukromé koncové body vytvoří privátní odkaz na analytické úložiště.

## <a name="enable-private-endpoint-for-the-analytical-store"></a>Povolit privátní koncový bod pro analytické úložiště

### <a name="set-up-an-azure-synapse-analytics-workspace-with-a-managed-virtual-network"></a>Nastavení pracovního prostoru Azure synapse Analytics se spravovanou virtuální sítí

[Umožňuje vytvořit pracovní prostor ve službě Azure synapse Analytics se zapnutou datovou exfiltrace.](../synapse-analytics/security/how-to-create-a-workspace-with-data-exfiltration-protection.md) Pomocí [ochrany dat exfiltrace](../synapse-analytics/security/workspace-data-exfiltration-protection.md)můžete zajistit, aby uživatelé se zlými úmysly nemohli kopírovat ani přenášet data z prostředků Azure do umístění mimo rozsah vaší organizace.

Následující omezení přístupu se použijí, pokud je pro pracovní prostor Azure synapse Analytics zapnutá ochrana dat exfiltrace:

* Pokud používáte Azure Spark pro Azure synapse Analytics, přístup je povolený jenom pro schválené spravované privátní koncové body pro Azure Cosmos DB analytické úložiště.

* Pokud používáte synapse fondy SQL serverů bez serveru, můžete zadat dotaz na libovolný účet Azure Cosmos DB pomocí odkazu Azure synapse. Žádosti o zápis, které [vytvářejí externí tabulky jako Select (CETAS)](../synapse-analytics/sql/develop-tables-cetas.md) , jsou povolené jenom pro schválené veřejné koncové body pro správu v pracovním prostoru virtuální sítě.

> [!NOTE]
> Po vytvoření pracovního prostoru nemůžete změnit konfiguraci spravované virtuální sítě a dat exfiltrace.

### <a name="add-a-managed-private-endpoint-for-azure-cosmos-db-analytical-store"></a>Přidání spravovaného privátního koncového bodu pro Azure Cosmos DB analytické úložiště

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

1. V Azure Portal přejděte k pracovnímu prostoru analýzy synapse a otevřete podokno **Přehled** .

1. Spusťte synapse Studio tak, že přejdete do podokna **Začínáme** a v **otevřeném synapse studiu** vyberete **otevřít** .

1. V nástroji synapse Studio otevřete kartu **Spravovat** .

1. Přejděte do **spravovaných privátních koncových bodů** a vyberte **Nový** .

   :::image type="content" source="./media/analytical-store-private-endpoints/create-new-private-endpoint.png" alt-text="Vytvořte nový privátní koncový bod pro analytické úložiště." border="true":::

1. Vyberte typ účtu **Azure Cosmos dB (SQL API)** > **pokračovat**.

   :::image type="content" source="./media/analytical-store-private-endpoints/select-private-endpoint.png" alt-text="Vyberte Azure Cosmos DB SQL API k vytvoření privátního koncového bodu." border="true":::

1. Do nového formuláře **spravovaného privátního koncového bodu** zadejte následující podrobnosti:

   * **Název** – název spravovaného privátního koncového bodu. Po vytvoření se tento název nedá aktualizovat.
   * **Popis** – zadejte popisný popis k identifikaci privátního koncového bodu.
   * **Předplatné Azure** – vyberte účet Azure Cosmos DB ze seznamu dostupných účtů ve vašich předplatných Azure.
   * **Azure Cosmos DB název účtu** – vyberte existující účet Azure Cosmos DB typu SQL nebo MongoDB.
   * **Cíl dílčího zdroje** – vyberte jednu z následujících možností: **Analytická**: Pokud chcete přidat privátní koncový bod pro Azure Cosmos DB analytické úložiště.
     **SQL** (nebo **MongoDB**): Pokud chcete přidat koncový bod OLTP nebo transakčního účtu.

   > [!NOTE]
   > Do stejného Azure Cosmos DB účtu v pracovním prostoru Azure synapse Analytics můžete přidat jak transakční, tak i i analytické úložiště. Pokud chcete pouze spustit analytické dotazy, budete pravděpodobně chtít mapovat pouze privátní koncový bod analýzy.

   :::image type="content" source="./media/analytical-store-private-endpoints/choose-analytical-private-endpoint.png" alt-text="Pro cílový subzdroj vyberte analytická." border="true":::

1. Po vytvoření přejít na název privátního koncového bodu a vybrat **Spravovat schválení v Azure Portal**.

1. Přejděte na účet Azure Cosmos DB, vyberte privátní koncový bod a vyberte **schválit**.

1. Přejděte zpátky do pracovního prostoru synapse Analytics a v podokně **spravované soukromé koncové body** klikněte na **aktualizovat** . Ověřte, jestli je privátní koncový bod ve stavu **schváleno** .

   :::image type="content" source="./media/analytical-store-private-endpoints/approved-private-endpoint.png" alt-text="Ověřte, jestli je privátní koncový bod schválený." border="true":::

## <a name="use-apache-spark-for-azure-synapse-analytics"></a>Použití Apache Spark pro Azure synapse Analytics

Pokud jste vytvořili pracovní prostor Azure synapse se zapnutou ochranou dat exfiltrace, bude ve výchozím nastavení blokovaný přístup z synapse Sparku na Azure Cosmos DB účty. Pokud už Azure Cosmos DB již má existující privátní koncový bod, bude k přístupu na synapse Spark zablokován přístup.

Povolení přístupu k Azure Cosmos DB datům:

* Pokud k dotazování Azure Cosmos DB dat používáte Azure synapse, přidejte pro účet Azure Cosmos DB spravovaného **analytického** privátního koncového bodu.

* Pokud používáte dávkové zápisy/čtení nebo zápisy do transakčního kódu nebo jejich čtení do transakčního úložiště, přidejte pro Azure Cosmos DB účet spravovaný MongoDB privátní koncový bod *SQL* nebo  . Kromě toho byste měli nastavit také *connectionMode* na *bránu* , jak je znázorněno v následujícím fragmentu kódu:

  ```python
  # Write a Spark DataFrame into an Azure Cosmos DB container
  # To select a preferred lis of regions in a multi-region account, add .option("spark.cosmos.preferredRegions", "<Region1>, <Region2>")
  
  YOURDATAFRAME.write\
    .format("cosmos.oltp")\
    .option("spark.synapse.linkedService", "<your-Cosmos-DB-linked-service-name>")\
    .option("spark.cosmos.container","<your-Cosmos-DB-container-name>")\
    .option("spark.cosmos.write.upsertEnabled", "true")\
    .option("spark.cosmos.connection.mode", "Gateway")\
    .mode('append')\
    .save()
  
  ```

## <a name="using-synapse-serverless-sql-pools"></a>Používání synapse fondů SQL bez serveru

Synapse fondy SQL bez serveru používají možnosti víceklientské architektury, které nejsou nasazené do spravované virtuální sítě. Pokud má účet Azure Cosmos DB existující soukromý koncový bod, synapse fondu SQL bez serveru se zablokuje přístup k účtu kvůli kontrolám izolace sítě na účtu Azure Cosmos DB.

Postup konfigurace izolace sítě pro tento účet z pracovního prostoru synapse:

1. Umožněte synapse pracovnímu prostoru přístup k účtu Azure Cosmos DB zadáním `NetworkAclBypassResourceId` nastavení na účtu.

   **Použití PowerShellu**

   ```powershell-interactive
   Update-AzCosmosDBAccount -Name MyCosmosDBDatabaseAccount -ResourceGroupName MyResourceGroup -NetworkAclBypass AzureServices -NetworkAclBypassResourceId "/subscriptions/subId/resourceGroups/rgName/providers/Microsoft.Synapse/workspaces/wsName"
   ```

   **Použití Azure CLI**

   ```azurecli-interactive
   az cosmosdb update --name MyCosmosDBDatabaseAccount --resource-group MyResourceGroup --network-acl-bypass AzureServices --network-acl-bypass-resource-ids "/subscriptions/subId/resourceGroups/rgName/providers/Microsoft.Synapse/workspaces/wsName"
   ```

   > [!NOTE]
   > Účet Azure Cosmos DB a pracovní prostor Azure synapse Analytics by měly být ve stejném Azure Active Directory (AD).

2. Nyní můžete získat přístup k účtu z neserverových fondů SQL pomocí dotazů T-SQL přes připojení Azure synapse. Pokud ale chcete zajistit izolaci sítě pro data v analytickém úložišti, musíte pro tento účet **Přidat spravovaný privátní** koncový bod. V opačném případě nebudou data v analytickém úložišti zablokována z veřejného přístupu.

> [!IMPORTANT]
> Pokud používáte Azure synapse Link a potřebujete izolaci sítě pro vaše data v analytickém úložišti, musíte účet Azure Cosmos DB namapovat do pracovního prostoru **synapse pomocí** spravovaného privátního koncového bodu.

## <a name="next-steps"></a>Další kroky

* Začínáme s [dotazem na analytické úložiště pomocí Azure synapse Spark](../synapse-analytics/synapse-link/how-to-query-analytical-store-spark.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json)
* Začínáme s [dotazem na analytické úložiště s synapse fondy SQL bez serveru Azure](../synapse-analytics/sql/query-cosmos-db-analytical-store.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json)
