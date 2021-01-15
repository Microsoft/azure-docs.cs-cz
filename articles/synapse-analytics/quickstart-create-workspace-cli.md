---
title: 'Rychlý Start: vytvoření pracovního prostoru synapse pomocí Azure CLI'
description: Pomocí rozhraní příkazového řádku Azure vytvořte pracovní prostor Azure synapse podle kroků uvedených v této příručce.
services: synapse-analytics
author: alehall
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: workspace
ms.date: 08/25/2020
ms.author: alehall
ms.reviewer: jrasnick
ms.openlocfilehash: 2658240e670e617f7296881f733ff369b9bf8f87
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2021
ms.locfileid: "98219007"
---
# <a name="quickstart-create-an-azure-synapse-workspace-with-azure-cli"></a>Rychlý Start: vytvoření pracovního prostoru Azure synapse pomocí Azure CLI

Azure CLI je prostředí příkazového řádku Azure pro správu prostředků Azure. Můžete ho používat ve svém prohlížeči prostřednictvím služby Azure Cloud Shell. Můžete ho také nainstalovat v systému macOS, Linux nebo Windows a spouštět z příkazového řádku.

V tomto rychlém startu se naučíte vytvořit pracovní prostor synapse pomocí Azure CLI.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

- Stažení a instalace [JQ](https://stedolan.github.io/jq/download/)a jednoduchého a flexibilního procesoru JSON příkazového řádku
- [Účet úložiště Azure Data Lake Storage Gen2](../storage/common/storage-account-create.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

    > [!IMPORTANT]
    > Pracovní prostor Azure synapse musí být schopný číst a zapisovat na vybraný ADLS Gen2 účet. Pro libovolný účet úložiště, který propojíte jako primární účet úložiště, je navíc nutné povolit **hierarchický obor názvů**  při vytváření účtu úložiště, jak je popsáno na stránce [Vytvoření Accout úložiště](../storage/common/storage-account-create.md?tabs=azure-portal#create-a-storage-account) . 

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="create-an-azure-synapse-workspace-using-the-azure-cli"></a>Vytvoření pracovního prostoru Azure synapse pomocí Azure CLI

1. Definujte potřebné proměnné prostředí pro vytváření prostředků pro pracovní prostor Azure synapse.

    | Název proměnné prostředí | Popis |
    |---|---|---|
    |název_účtu_úložiště| Název existujícího účtu úložiště ADLS Gen2.|
    |StorageAccountResourceGroup| Název existující skupiny prostředků účtu úložiště ADLS Gen2 |
    |FileShareName| Název existujícího systému souborů úložiště.|
    |SynapseResourceGroup| Vyberte nový název skupiny prostředků Azure synapse. |
    |Oblast| Vyberte jednu z [oblastí Azure](https://azure.microsoft.com/global-infrastructure/geographies/#overview). |
    |SynapseWorkspaceName| Vyberte jedinečný název pro nový pracovní prostor Azure synapse. |
    |SqlUser| Vyberte hodnotu pro nové uživatelské jméno.|
    |SqlPassword| Vyberte zabezpečené heslo.|
    |||

2. Vytvořte skupinu prostředků jako kontejner pro váš pracovní prostor Azure synapse:
    ```azurecli
    az group create --name $SynapseResourceGroup --location $Region
    ```
3. Načtěte klíč účtu úložiště ADLS Gen 2:
    ```azurecli
    StorageAccountKey=$(az storage account keys list \
      --account-name $StorageAccountName \
      | jq -r '.[0] | .value')
    ```
4. Načíst adresu URL koncového bodu úložiště ADLS Gen 2:
    ```azurecli
    StorageEndpointUrl=$(az storage account show \
      --name $StorageAccountName \
      --resource-group $StorageAccountResourceGroup \
      | jq -r '.primaryEndpoints | .dfs')
    ```

5. Volitelné Vždycky můžete kontrolovat, co ADLS Gen2 klíč účtu úložiště a koncový bod:
    ```azurecli
    echo "Storage Account Key: $StorageAccountKey"
    echo "Storage Endpoint URL: $StorageEndpointUrl"
    ```

6. Vytvořte pracovní prostor Azure synapse:
    ```azurecli
    az synapse workspace create \
      --name $SynapseWorkspaceName \
      --resource-group $SynapseResourceGroup \
      --storage-account $StorageAccountName \
      --file-system $FileShareName \
      --sql-admin-login-user $SqlUser \
      --sql-admin-login-password $SqlPassword \
      --location $Region
    ```

7. Získání webové a vývojářské adresy URL pro pracovní prostor Azure synapse:
    ```azurecli
    WorkspaceWeb=$(az synapse workspace show --name $SynapseWorkspaceName --resource-group $SynapseResourceGroup | jq -r '.connectivityEndpoints | .web')

    WorkspaceDev=$(az synapse workspace show --name $SynapseWorkspaceName --resource-group $SynapseResourceGroup | jq -r '.connectivityEndpoints | .dev')
    ```

8. Vytvořte pravidlo brány firewall, které umožní přístup k pracovnímu prostoru Azure synapse z počítače:

    ```azurecli
    ClientIP=$(curl -sb -H "Accept: application/json" "$WorkspaceDev" | jq -r '.message')
    ClientIP=${ClientIP##'Client Ip address : '}
    echo "Creating a firewall rule to enable access for IP address: $ClientIP"

    az synapse workspace firewall-rule create --end-ip-address $ClientIP --start-ip-address $ClientIP --name "Allow Client IP" --resource-group $SynapseResourceGroup --workspace-name $SynapseWorkspaceName
    ```

9. Otevřete adresu URL webu v pracovním prostoru Azure synapse, která je uložená v proměnné prostředí `WorkspaceWeb` pro přístup k vašemu pracovnímu prostoru:

    ```azurecli
    echo "Open your Azure Synapse Workspace Web URL in the browser: $WorkspaceWeb"
    ```
    
    [![Web ](media/quickstart-create-synapse-workspace-cli/create-workspace-cli-1.png) Azure synapse Workspace](media/quickstart-create-synapse-workspace-cli/create-workspace-cli-1.png#lightbox)


## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete odstranit pracovní prostor Azure synapse, postupujte podle následujících kroků.
> [!WARNING]
> Při odstranění pracovního prostoru Azure synapse se odstraní analytické moduly a data uložená v databázi obsažených fondů SQL a metadat pracovního prostoru. Již nebude možné se připojit k koncovým bodům SQL nebo Apache Spark. Všechny artefakty kódu se odstraní (dotazy, poznámkové bloky, definice úloh a kanály).
>
> Odstranění pracovního prostoru nebude **mít vliv** na data v Data Lake Store Gen2, která jsou propojená s pracovním prostorem.

Pokud chcete odstranit pracovní prostor Azure synapse, proveďte následující příkaz:

```azurecli
az synapse workspace delete --name $SynapseWorkspaceName --resource-group $SynapseResourceGroup
```

## <a name="next-steps"></a>Další kroky

V dalším kroku můžete [vytvořit fondy SQL](quickstart-create-sql-pool-studio.md) nebo [vytvořit fondy Apache Spark](quickstart-create-apache-spark-pool-studio.md) , abyste mohli začít analyzovat a prozkoumat data.