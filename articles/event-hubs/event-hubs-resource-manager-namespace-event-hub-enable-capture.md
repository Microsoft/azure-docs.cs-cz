---
title: Vytvoření centra událostí pomocí povoleny – zachytávání služby Azure Event Hubs | Dokumentace Microsoftu
description: Vytvoření oboru názvů Azure Event Hubs s jedním centrem událostí a povolení funkce Capture pomocí šablony Azure Resource Manageru
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: 8bdda6a2-5ff1-45e3-b696-c553768f1090
ms.service: event-hubs
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 02/06/2019
ms.author: shvija
ms.openlocfilehash: 26e248590ab6a5aa84da36abe1f1c3fb0a3dc35f
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56238041"
---
# <a name="create-a-namespace-with-event-hub-and-enable-capture-using-a-template"></a>Vytvoření oboru názvů v centru událostí a povolení funkce Capture pomocí šablony

Tento článek ukazuje, jak použít šablonu Azure Resource Manageru, která vytvoří obor názvů [Event Hubs](event-hubs-what-is-event-hubs.md) s jednou instancí centra událostí, ve kterém také povolí [funkci Capture](event-hubs-capture-overview.md). Tento článek popisuje, jak definovat, které prostředky se nasadí, a jak definovat parametry zadávané při spuštění nasazení. Tuto šablonu můžete použít pro vlastní nasazení nebo ji upravit, aby splňovala vaše požadavky.

Tento článek také ukazuje, jak určit, že se události mají zachytávat do objektů Azure Storage Blob nebo do služby Azure Data Lake Store v závislosti na zvoleném cíli.

Další informace o vytváření šablon najdete v tématu [Tvorba šablon Azure Resource Manageru][Authoring Azure Resource Manager templates]. Syntaxi JSON a vlastnosti, které chcete použít v šabloně najdete v tématu [typy prostředků Microsoft.EventHub](/azure/templates/microsoft.eventhub/allversions).

Další informace o vzorech a postupech pro zásady vytváření názvů prostředků Azure najdete v tématu [Zásady vytváření názvů prostředků Azure][Azure Resources naming conventions].

Hotové šablony můžete získat kliknutím na následující odkazy na web GitHub:

- [Šablona centra událostí a povolení zachytávání pomocí funkce Capture do služby Storage][Event Hub and enable Capture to Storage template] 
- [Šablona centra událostí a povolení zachytávání pomocí funkce Capture do služby Azure Data Lake Store][Event Hub and enable Capture to Azure Data Lake Store template]

> [!NOTE]
> Nejnovější šablony můžete zkontrolovat tak, že přejdete do galerie [Šablony Azure pro rychlý start][Azure Quickstart Templates] a vyhledáte Event Hubs.
> 
> 

## <a name="what-will-you-deploy"></a>Co budete nasazovat?

Pomocí této šablony nasadíte obor názvů Event Hubs s centrem událostí a povolíte funkci [Event Hubs Capture](event-hubs-capture-overview.md). Funkce Event Hubs Capture umožňuje automatické doručování streamovaných dat ve službě Event Hubs do služby Azure Blob Storage nebo Azure Data Lake Store v rámci zvoleného časového nebo velikostního intervalu. Kliknutím na následující tlačítko povolíte zachytávání pomocí funkce Event Hubs Capture do služby Azure Storage:

[![Nasazení do Azure](./media/event-hubs-resource-manager-namespace-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-eventhubs-create-namespace-and-enable-capture%2Fazuredeploy.json)

Kliknutím na následující tlačítko povolíte zachytávání pomocí funkce Event Hubs Capture do služby Azure Data Lake Store:

[![Nasazení do Azure](./media/event-hubs-resource-manager-namespace-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-eventhubs-create-namespace-and-enable-capture-for-adls%2Fazuredeploy.json)

## <a name="parameters"></a>Parametry

Pomocí Azure Resource Manageru definujete parametry pro hodnoty, které chcete zadat při nasazení šablony. Šablona obsahuje část `Parameters`, která obsahuje všechny hodnoty parametrů. Parametr byste měli definovat pro hodnoty, které se mění v závislosti na nasazovaném projektu nebo prostředí, do kterého nasazujete. Nedefinujte parametry pro hodnoty, které jsou vždy stejné. Každá hodnota parametru se v šabloně použije k definování nasazovaných prostředků.

Šablona definuje následující parametry.

### <a name="eventhubnamespacename"></a>eventHubNamespaceName

Název oboru názvů Event Hubs, který se má vytvořit.

```json
"eventHubNamespaceName":{  
     "type":"string",
     "metadata":{  
         "description":"Name of the EventHub namespace"
      }
}
```

### <a name="eventhubname"></a>eventHubName

Název centra událostí vytvořeného v oboru názvů Event Hubs.

```json
"eventHubName":{  
    "type":"string",
    "metadata":{  
        "description":"Name of the event hub"
    }
}
```

### <a name="messageretentionindays"></a>messageRetentionInDays

Počet dní, po které se zprávy budou uchovávat v centru událostí. 

```json
"messageRetentionInDays":{
    "type":"int",
    "defaultValue": 1,
    "minValue":"1",
    "maxValue":"7",
    "metadata":{
       "description":"How long to retain the data in event hub"
     }
 }
```

### <a name="partitioncount"></a>partitionCount

Počet oddílů, které se mají vytvořit v centru událostí.

```json
"partitionCount":{
    "type":"int",
    "defaultValue":2,
    "minValue":2,
    "maxValue":32,
    "metadata":{
        "description":"Number of partitions chosen"
    }
 }
```

### <a name="captureenabled"></a>captureEnabled

Povolení funkce Capture v centru událostí.

```json
"captureEnabled":{
    "type":"string",
    "defaultValue":"true",
    "allowedValues": [
    "false",
    "true"],
    "metadata":{
        "description":"Enable or disable the Capture for your event hub"
    }
 }
```
### <a name="captureencodingformat"></a>captureEncodingFormat

Formát kódování, který zadáte pro serializaci dat událostí.

```json
"captureEncodingFormat":{
    "type":"string",
    "defaultValue":"Avro",
    "allowedValues":[
    "Avro"],
    "metadata":{
        "description":"The encoding format in which Capture serializes the EventData"
    }
}
```

### <a name="capturetime"></a>captureTime

Časový interval, ve kterém funkce Event Hubs Capture začne zachytávat data.

```json
"captureTime":{
    "type":"int",
    "defaultValue":300,
    "minValue":60,
    "maxValue":900,
    "metadata":{
         "description":"The time window in seconds for the capture"
    }
}
```

### <a name="capturesize"></a>captureSize
Velikostní interval, ve kterém funkce Capture začne zachytávat data.

```json
"captureSize":{
    "type":"int",
    "defaultValue":314572800,
    "minValue":10485760,
    "maxValue":524288000,
    "metadata":{
        "description":"The size window in bytes for capture"
    }
}
```

### <a name="capturenameformat"></a>captureNameFormat

Formát názvu, který má funkce Event Hubs Capture používat k zápisu souborů Avro. Nezapomeňte, že formát názvu pro funkci Capture musí obsahovat pole `{Namespace}`, `{EventHub}`, `{PartitionId}`, `{Year}`, `{Month}`, `{Day}`, `{Hour}`, `{Minute}` a `{Second}`. Tato pole můžete uspořádat v libovolném pořadí s oddělovači nebo bez.
 
```json
"captureNameFormat": {
      "type": "string",
      "defaultValue": "{Namespace}/{EventHub}/{PartitionId}/{Year}/{Month}/{Day}/{Hour}/{Minute}/{Second}",
      "metadata": {
        "description": "A Capture Name Format must contain {Namespace}, {EventHub}, {PartitionId}, {Year}, {Month}, {Day}, {Hour}, {Minute} and {Second} fields. These can be arranged in any order with or without delimeters. E.g.  Prod_{EventHub}/{Namespace}\\{PartitionId}_{Year}_{Month}/{Day}/{Hour}/{Minute}/{Second}"
      }
    }
  
```

### <a name="apiversion"></a>apiVersion

Verze rozhraní API šablony.

```json
 "apiVersion":{  
    "type":"string",
    "defaultValue":"2017-04-01",
    "metadata":{  
        "description":"ApiVersion used by the template"
    }
 }
```

Pokud jako cíl zvolíte službu Azure Storage, použijte následující parametry.

### <a name="destinationstorageaccountresourceid"></a>destinationStorageAccountResourceId

Funkce Capture pro povolení zachytávání do požadovaného účtu Storage vyžaduje ID prostředku účtu Azure Storage.

```json
 "destinationStorageAccountResourceId":{
    "type":"string",
    "metadata":{
        "description":"Your existing Storage account resource ID where you want the blobs be captured"
    }
 }
```

### <a name="blobcontainername"></a>blobContainerName

Kontejner objektů blob, do kterého se mají zachytávat data událostí.

```json
 "blobContainerName":{
    "type":"string",
    "metadata":{
        "description":"Your existing storage container in which you want the blobs captured"
    }
}
```

Pokud jako cíl zvolíte službu Azure Data Lake Store, použijte následující parametry. Je potřeba nastavit oprávnění k cestě Data Lake Store, ve které chcete událost zachytávat. Informace o nastavení oprávnění najdete v [tomto článku](event-hubs-capture-enable-through-portal.md#capture-data-to-an-azure-data-lake-store-account).

### <a name="subscriptionid"></a>subscriptionId

ID předplatného pro obor názvů Event Hubs a službu Azure Data Lake Store. Oba tyto prostředky musí patřit pod stejné ID předplatného.

```json
"subscriptionId": {
    "type": "string",
    "metadata": {
        "description": "Subscription ID of both Azure Data Lake Store and Event Hubs namespace"
     }
 }
```

### <a name="datalakeaccountname"></a>dataLakeAccountName

Název služby Azure Data Lake Store pro zachycené události.

```json
"dataLakeAccountName": {
    "type": "string",
    "metadata": {
        "description": "Azure Data Lake Store name"
    }
}
```

### <a name="datalakefolderpath"></a>dataLakeFolderPath

Cesta k cílové složce pro zachycené události. Do této složky ve službě Data Lake Store budou při operaci zachycení doručovány události. Pokud chcete nastavit oprávnění k této složce, přečtěte si článek [Použití Azure Data Lake Store k zaznamenání dat ze služby Event Hubs](../data-lake-store/data-lake-store-archive-eventhub-capture.md).

```json
"dataLakeFolderPath": {
    "type": "string",
    "metadata": {
        "description": "Destination capture folder path"
    }
}
```

## <a name="resources-to-deploy-for-azure-storage-as-destination-to-captured-events"></a>Prostředky k nasazení pro službu Azure Storage jako cíl zachycených událostí

Vytvoří se obor názvů typu **EventHub** s jedním centrem událostí a povolí zachytávání funkcí Capture do služby Azure Blob Storage.

```json
"resources":[  
      {  
         "apiVersion":"[variables('ehVersion')]",
         "name":"[parameters('eventHubNamespaceName')]",
         "type":"Microsoft.EventHub/Namespaces",
         "location":"[variables('location')]",
         "sku":{  
            "name":"Standard",
            "tier":"Standard"
         },
         "resources": [
    {
      "apiVersion": "2017-04-01",
      "name": "[parameters('eventHubNamespaceName')]",
      "type": "Microsoft.EventHub/Namespaces",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard"
      },
      "properties": {
        "isAutoInflateEnabled": "true",
        "maximumThroughputUnits": "7"
      },
      "resources": [
        {
          "apiVersion": "2017-04-01",
          "name": "[parameters('eventHubName')]",
          "type": "EventHubs",
          "dependsOn": [
            "[concat('Microsoft.EventHub/namespaces/', parameters('eventHubNamespaceName'))]"
          ],
          "properties": {
            "messageRetentionInDays": "[parameters('messageRetentionInDays')]",
            "partitionCount": "[parameters('partitionCount')]",
            "captureDescription": {
              "enabled": "true",
              "skipEmptyArchives": false,
              "encoding": "[parameters('captureEncodingFormat')]",
              "intervalInSeconds": "[parameters('captureTime')]",
              "sizeLimitInBytes": "[parameters('captureSize')]",
              "destination": {
                "name": "EventHubArchive.AzureBlockBlob",
                "properties": {
                  "storageAccountResourceId": "[parameters('destinationStorageAccountResourceId')]",
                  "blobContainer": "[parameters('blobContainerName')]",
                  "archiveNameFormat": "[parameters('captureNameFormat')]"
                }
              }
            }
          }

        }
      ]
    }
  ]
```

## <a name="resources-to-deploy-for-azure-data-lake-store-as-destination"></a>Prostředky k nasazení pro službu Azure Data Lake Store jako cíl

Vytvoří obor názvů typu **EventHub** s jedním centrem událostí a povolí funkci Capture ve službě Azure Data Lake Store.

```json
 "resources": [
        {
            "apiVersion": "2017-04-01",
            "name": "[parameters('namespaceName')]",
            "type": "Microsoft.EventHub/Namespaces",
            "location": "[variables('location')]",
            "sku": {
                "name": "Standard",
                "tier": "Standard"
            },
            "resources": [
                {
                    "apiVersion": "2017-04-01",
                    "name": "[parameters('eventHubName')]",
                    "type": "EventHubs",
                    "dependsOn": [
                        "[concat('Microsoft.EventHub/namespaces/', parameters('namespaceName'))]"
                    ],
                    "properties": {
                        "path": "[parameters('eventHubName')]",
                        "captureDescription": {
                            "enabled": "true",
                            "skipEmptyArchives": false,
                            "encoding": "[parameters('archiveEncodingFormat')]",
                            "intervalInSeconds": "[parameters('captureTime')]",
                            "sizeLimitInBytes": "[parameters('captureSize')]",
                            "destination": {
                                "name": "EventHubArchive.AzureDataLake",
                                "properties": {
                                    "DataLakeSubscriptionId": "[parameters('subscriptionId')]",
                                    "DataLakeAccountName": "[parameters('dataLakeAccountName')]",
                                    "DataLakeFolderPath": "[parameters('dataLakeFolderPath')]",
                                    "ArchiveNameFormat": "[parameters('captureNameFormat')]"
                                }
                            }
                        }
                    }
                }
            ]
        }
    ]
```

> [!NOTE]
> Můžete povolit nebo zakázat generování prázdných souborů, dojde-li žádné události během časového intervalu pro sběr dat s použitím **skipEmptyArchives** vlastnost. 

## <a name="commands-to-run-deployment"></a>Příkazy pro spuštění nasazení

[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Nasazení šablony pro povolení zachytávání pomocí funkce Event Hubs Capture do služby Azure Storage:
 
```powershell
New-AzResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-capture/azuredeploy.json
```

Nasazení šablony pro povolení zachytávání pomocí funkce Event Hubs Capture do služby Azure Data Lake Store:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-capture-for-adls/azuredeploy.json
```

## <a name="azure-cli"></a>Azure CLI

Azure Blob Storage jako cíl:

```azurecli
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri [https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-capture/azuredeploy.json][]
```

Azure Data Lake Store jako cíl:

```azurecli
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri [https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-capture-for-adls/azuredeploy.json][]
```

## <a name="next-steps"></a>Další postup

Funkci Event Hubs Capture můžete konfigurovat také prostřednictvím webu [Azure Portal](https://portal.azure.com). Další informace najdete v tématu [Povolení funkce Event Hubs Capture pomocí webu Azure Portal](event-hubs-capture-enable-through-portal.md).

Další informace o službě Event Hubs najdete na následujících odkazech:

* [Přehled služby Event Hubs](event-hubs-what-is-event-hubs.md)
* [Vytvoření centra událostí](event-hubs-create.md)
* [Nejčastější dotazy k Event Hubs](event-hubs-faq.md)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Azure Quickstart Templates]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Azure Resources naming conventions]: https://azure.microsoft.com/documentation/articles/guidance-naming-conventions/
[Event hub and enable Capture to Storage template]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-capture
[Event hub and enable Capture to Azure Data Lake Store template]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-capture-for-adls
