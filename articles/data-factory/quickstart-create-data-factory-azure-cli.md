---
title: 'Rychlý Start: vytvoření Azure Data Factory pomocí Azure CLI'
description: V tomto rychlém startu se vytvoří Azure Data Factory, včetně propojené služby, datových sad a kanálu. Můžete spustit kanál a provést akci kopírování souborů.
author: linda33wj
ms.author: jingwang
ms.service: azure-cli
ms.topic: quickstart
ms.date: 03/24/2021
ms.custom:
- template-quickstart
- devx-track-azurecli
ms.openlocfilehash: 9af5f276e49e9eb2756dc544db353c75c99bc5a9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105938058"
---
# <a name="quickstart-create-an-azure-data-factory-using-azure-cli"></a>Rychlý Start: vytvoření Azure Data Factory pomocí Azure CLI

V tomto rychlém startu se dozvíte, jak pomocí Azure CLI vytvořit Azure Data Factory. Kanál, který vytvoříte v této datové továrně, kopíruje data z jedné složky do jiné složky v Azure Blob Storage. Informace o tom, jak transformovat data pomocí Azure Data Factory, najdete [v tématu transformace dat v Azure Data Factory](transform-data.md).

Úvod do služby Azure Data Factory najdete v tématu [Úvod do Azure Data Factory](introduction.md).

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/).

[!INCLUDE [azure-cli-prepare-your-environment](../../includes/azure-cli-prepare-your-environment.md)]

> [!NOTE]
> Pro vytvoření instancí služby Data Factory musí být uživatelský účet, který použijete pro přihlášení k Azure, členem role přispěvatel nebo vlastník nebo správcem předplatného Azure. Další informace najdete v tématu [role Azure](quickstart-create-data-factory-powershell.md#azure-roles).

## <a name="prepare-a-container-and-test-file"></a>Příprava kontejneru a testovacího souboru

V tomto rychlém startu se používá účet Azure Storage, který zahrnuje kontejner se souborem.

1. Chcete-li vytvořit skupinu prostředků s názvem `ADFQuickStartRG` , použijte příkaz [AZ Group Create](/cli/azure/group#az_group_create) :

   ```azurecli
   az group create --name ADFQuickStartRG --location eastus
   ```

1. Vytvořte účet úložiště pomocí příkazu [AZ Storage Account Create](/cli/azure/storage/container#az_storage_container_create) :

   ```azurecli
   az storage account create --resource-group ADFQuickStartRG \
       --name adfquickstartstorage --location eastus
   ```

1. `adftutorial`Pomocí příkazu [AZ Storage Container Create](/cli/azure/storage/container#az_storage_container_create) vytvořte kontejner s názvem.

   ```azurecli
   az storage container create --resource-group ADFQuickStartRG --name adftutorial \
       --account-name adfquickstartstorage --auth-mode key
   ```

1. V místním adresáři vytvořte soubor s názvem `emp.txt` pro nahrání. Pokud pracujete v Azure Cloud Shell, můžete najít aktuální pracovní adresář pomocí `echo $PWD` příkazu bash. K vytvoření souboru můžete použít standardní příkazy bash, třeba `cat` .

   ```console
   cat > emp.txt
   This is text.
   ```

   Nový soubor uložíte **stisknutím kombinace kláves CTRL + D** .

1. Pokud chcete nahrát nový soubor do kontejneru úložiště Azure, použijte příkaz [AZ Storage BLOB upload](/cli/azure/storage/blob#az_storage_blob_upload) :

   ```azurecli
   az storage blob upload --account-name adfquickstartstorage --name input/emp.txt \
       --container-name adftutorial --file emp.txt --auth-mode key
   ```

   Tento příkaz nahraje do nové složky s názvem `input` .

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

Pokud chcete vytvořit datovou továrnu Azure, spusťte příkaz [AZ DataFactory-Factory Create](/cli/azure/ext/datafactory/datafactory/factory#ext_datafactory_az_datafactory_factory_create) :

```azurecli
az datafactory factory create --resource-group ADFQuickStartRG \
    --factory-name ADFTutorialFactory
```

> [!IMPORTANT]
> Nahraďte `ADFTutorialFactory` globálně jedinečným názvem datové továrny, například ADFTutorialFactorySP1127.

Datovou továrnu, kterou jste vytvořili, můžete zobrazit pomocí příkazu [AZ DataFactory-Factory show](/cli/azure/ext/datafactory/datafactory/factory#ext_datafactory_az_datafactory_factory_show) :

```azurecli
az datafactory factory show --resource-group ADFQuickStartRG \
    --factory-name ADFTutorialFactory
```

## <a name="create-a-linked-service-and-datasets"></a>Vytvoření propojené služby a datových sad

Dále vytvořte propojenou službu a dvě datové sady.

1. Pomocí příkazu [AZ Storage Account show-Connection-String](/cli/azure/ext/datafactory/datafactory/factory#ext_datafactory_az_datafactory_factory_show) Získejte připojovací řetězec pro váš účet úložiště:

   ```azurecli
   az storage account show-connection-string --resource-group ADFQuickStartRG \
       --name adfquickstartstorage --key primary
   ```

1. V pracovním adresáři vytvořte soubor JSON s tímto obsahem, který obsahuje vlastní připojovací řetězec z předchozího kroku. Název souboru `AzureStorageLinkedService.json` :

   ```json
   {
       "type":"AzureStorage",
           "typeProperties":{
           "connectionString":{
           "type": "SecureString",
           "value":"DefaultEndpointsProtocol=https;AccountName=adfquickstartstorage;AccountKey=K9F4Xk/EhYrMBIR98rtgJ0HRSIDU4eWQILLh2iXo05Xnr145+syIKNczQfORkQ3QIOZAd/eSDsvED19dAwW/tw==;EndpointSuffix=core.windows.net"
           }
       }
   }
   ```

1. Vytvořte propojenou službu s názvem `AzureStorageLinkedService` pomocí příkazu [AZ DataFactory-Link-Service Create](/cli/azure/ext/datafactory/datafactory/linked-service#ext_datafactory_az_datafactory_linked_service_create) :

   ```azurecli
   az datafactory linked-service create --resource-group ADFQuickStartRG \
       --factory-name ADFTutorialFactory --linked-service-name AzureStorageLinkedService \
       --properties @AzureStorageLinkedService.json
   ```

1. V pracovním adresáři vytvořte soubor JSON s tímto obsahem s názvem `InputDataset.json` :

   ```json
   {
       "type": 
           "AzureBlob",
           "linkedServiceName": {
               "type":"LinkedServiceReference",
               "referenceName":"AzureStorageLinkedService"
               },
           "annotations": [],
           "type": "Binary",
           "typeProperties": {
               "location": {
                   "type": "AzureBlobStorageLocation",
                   "fileName": "emp.txt",
                   "folderPath": "input",
                   "container": "adftutorial"
           }
       }
   }
   ```

1. `InputDataset`Pomocí příkazu [AZ DataFactory-DataSet Create](/cli/azure/ext/datafactory/datafactory/dataset#ext_datafactory_az_datafactory_dataset_create) vytvořte vstupní datovou sadu s názvem:

   ```azurecli
   az datafactory dataset create --resource-group ADFQuickStartRG \
       --dataset-name InputDataset --factory-name ADFQuickStartFactory \
       --properties @InputDataset.json
   ```

1. V pracovním adresáři vytvořte soubor JSON s tímto obsahem s názvem `OutputDataset.json` :

   ```json
   {
       "type": 
           "AzureBlob",
           "linkedServiceName": {
               "type":"LinkedServiceReference",
               "referenceName":"AzureStorageLinkedService"
               },
           "annotations": [],
           "type": "Binary",
           "typeProperties": {
               "location": {
                   "type": "AzureBlobStorageLocation",
                   "fileName": "emp.txt",
                   "folderPath": "output",
                   "container": "adftutorial"
           }
       }
   }
   ```

1. Vytvořte výstupní datovou sadu s názvem `OutputDataset` pomocí příkazu [AZ DataFactory DataSet Create](/cli/azure/ext/datafactory/datafactory/dataset#ext_datafactory_az_datafactory_dataset_create) :

   ```azurecli
   az datafactory dataset create --resource-group ADFQuickStartRG \
       --dataset-name OutputDataset --factory-name ADFQuickStartFactory \
       --properties @OutputDataset.json
   ```

## <a name="create-and-run-the-pipeline"></a>Vytvoření a spuštění kanálu

Nakonec vytvořte a spusťte kanál.

1. V pracovním adresáři vytvořte pomocí tohoto obsahu soubor JSON s názvem `Adfv2QuickStartPipeline.json` :

   ```json
   {
       "name": "Adfv2QuickStartPipeline",
       "properties": {
           "activities": [
               {
                   "name": "CopyFromBlobToBlob",
                   "type": "Copy",
                   "dependsOn": [],
                   "policy": {
                       "timeout": "7.00:00:00",
                       "retry": 0,
                       "retryIntervalInSeconds": 30,
                       "secureOutput": false,
                       "secureInput": false
                   },
                   "userProperties": [],
                   "typeProperties": {
                          "source": {
                           "type": "BinarySource",
                           "storeSettings": {
                               "type": "AzureBlobStorageReadSettings",
                               "recursive": true
                           }
                       },
                       "sink": {
                           "type": "BinarySink",
                           "storeSettings": {
                               "type": "AzureBlobStorageWriteSettings"
                           }
                       },
                       "enableStaging": false
                   },
                   "inputs": [
                       {
                           "referenceName": "InputDataset",
                           "type": "DatasetReference"
                       }
                   ],
                   "outputs": [
                       {
                           "referenceName": "OutputDataset",
                           "type": "DatasetReference"
                       }
                   ]
               }
           ],
           "annotations": []
       }
   }
   ```

1. Vytvořte kanál s názvem `Adfv2QuickStartPipeline` pomocí příkazu [AZ DataFactory Pipeline Create](/cli/azure/ext/datafactory/datafactory/pipeline#ext_datafactory_az_datafactory_pipeline_create) :

   ```azurecli
   az datafactory pipeline create --resource-group ADFQuickStartRG \
       --factory-name ADFTutorialFactory --name Adfv2QuickStartPipeline \
       --pipeline @Adfv2QuickStartPipeline.json
   ```

1. Spusťte kanál pomocí příkazu [AZ DataFactory Pipeline Create-Run](/cli/azure/ext/datafactory/datafactory/pipeline#ext_datafactory_az_datafactory_pipeline_create_run) :

   ```azurecli
   az datafactory pipeline create-run --resource-group ADFQuickStartRG \
       --name Adfv2QuickStartPipeline --factory-name ADFTutorialFactory
   ```

   Tento příkaz vrátí ID běhu. Zkopírujte ho pro použití v dalším příkazu.

1. Ověřte, že se kanál úspěšně spustil, pomocí příkazu [AZ DataFactory Pipeline-Run show](/cli/azure/ext/datafactory/datafactory/pipeline-run#ext_datafactory_az_datafactory_pipeline_run_show) :

   ```azurecli
   az datafactory pipeline-run show --resource-group ADFQuickStartRG \
       --factory-name ADFTutorialFactory --run-id 00000000-0000-0000-0000-000000000000
   ```

Pomocí [Azure Portal](https://portal.azure.com/)můžete také ověřit, že váš kanál běžel podle očekávání. Další informace najdete v tématu [Kontrola nasazených prostředků](quickstart-create-data-factory-powershell.md#review-deployed-resources).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Všechny prostředky v tomto rychlém startu jsou součástí stejné skupiny prostředků. Pokud je chcete odebrat, použijte příkaz [AZ Group Delete](/cli/azure/group#az_group_delete) :

```azurecli
az group delete --name ADFQuickStartRG
```

Pokud tuto skupinu prostředků používáte pro cokoliv jiného, místo toho odstraňte jednotlivé prostředky. Chcete-li například odebrat propojenou službu, použijte příkaz [AZ DataFactory-Link-Service Delete](/cli/azure/ext/datafactory/datafactory/linked-service#ext_datafactory_az_datafactory_linked_service_delete) .

V tomto rychlém startu jste vytvořili následující soubory JSON:

- AzureStorageLinkedService.jsna
- InputDataset.jsna
- OutputDataset.jsna
- Adfv2QuickStartPipeline.jsna

Odstraňte je pomocí standardních příkazů bash.

## <a name="next-steps"></a>Další kroky

- [Kanály a aktivity v Azure Data Factory](concepts-pipelines-activities.md)
- [Propojené služby v Azure Data Factory](concepts-linked-services.md)
- [Datové sady v Azure Data Factory](concepts-datasets-linked-services.md)
- [Transformace dat v Azure Data Factory](transform-data.md)
