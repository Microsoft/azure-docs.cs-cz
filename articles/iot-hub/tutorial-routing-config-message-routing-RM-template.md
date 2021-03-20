---
title: Kurz – konfigurace směrování zpráv pro Azure IoT Hub pomocí šablony Azure Resource Manager
description: Kurz – konfigurace směrování zpráv pro Azure IoT Hub pomocí šablony Azure Resource Manager
author: robinsh
manager: philmeagit st
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 53217340b0d91f3de77e5e0d8c0a82e30599d6ed
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98621424"
---
# <a name="tutorial-use-an-azure-resource-manager-template-to-configure-iot-hub-message-routing"></a>Kurz: použití šablony Azure Resource Manager ke konfiguraci směrování zpráv IoT Hub

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="message-routing"></a>Směrování zpráv

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

## <a name="download-the-template-and-parameters-file"></a>Stažení šablony a souboru parametrů

Druhá část tohoto kurzu vám umožní stáhnout a spustit aplikaci Visual Studio pro posílání zpráv do IoT Hub. V tomto souboru se nachází složka, která obsahuje Azure Resource Manager šablona a soubor parametrů, a také skripty Azure CLI a PowerShell.

Pokračujte a Stáhněte si [ukázky pro Azure IoT C#](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) hned teď. Rozbalte soubor master.zip. Šablona Správce prostředků a soubor parametrů jsou v/iot-hub/Tutorials/Routing/SimulatedDevice/resources/jako **template_iothub.jszapnuté** a **template_iothub_parameters.js**.

## <a name="create-your-resources"></a>Vytvoření prostředků

Chystáte se použít šablonu Azure Resource Manager (RM) k vytvoření všech vašich prostředků. Skripty Azure CLI a PowerShell můžou běžet postupně po několika řádcích. Šablona služby RM se nasazuje v jednom kroku. Tento článek popisuje samostatné oddíly, které vám pomůžou porozumět každé z nich. Potom vám ukáže, jak nasadit šablonu a vytvořit virtuální zařízení pro testování. Po nasazení šablony můžete zobrazit konfiguraci směrování zpráv na portálu.

Existuje několik názvů prostředků, které musí být globálně jedinečné, například IoT Hub název a název účtu úložiště. Aby bylo snazší pojmenování prostředků, jsou tyto názvy prostředků nastaveny tak, aby připojily náhodnou alfanumerický hodnotu vygenerovanou z aktuálního data a času. 

Pokud se podíváte na šablonu, uvidíte, kde jsou pro tyto prostředky nastaveny proměnné, které přebírají parametr předaný, a zřetězí *randomValue* k parametru. 

V následující části jsou vysvětleny použité parametry.

### <a name="parameters"></a>Parametry

Většina těchto parametrů má výchozí hodnoty. Ty, které končí **_in** jsou zřetězené s *randomValue* , aby byly globálně jedinečné. 

**randomValue**: Tato hodnota je vygenerována z aktuálního data a času při nasazení šablony. Toto pole není v souboru parametrů, protože je vygenerováno v samotné šabloně.

**SubscriptionId**: Toto pole je nastaveno pro vás na předplatné, do kterého nasazujete šablonu. Toto pole není v souboru parametrů, protože je nastaveno za vás.

**IoTHubName_in**: Toto pole je základní IoT Hub název, který je zřetězený s randomValue, aby byl globálně jedinečný.

**umístění**: Toto pole je oblast Azure, do které nasazujete, například "westus".

**consumer_group**: Toto pole je nastavená skupina uživatelů pro zprávy přicházející prostřednictvím koncového bodu směrování. Slouží k filtrování výsledků v Azure Stream Analytics. Například je k dispozici celý datový proud, ve kterém získáte vše, nebo pokud máte data přicházející pomocí consumer_group nastavenou na **Contoso**, můžete nastavit datový proud Azure Stream Analytics (a Power BI sestavu), aby se zobrazily jenom tyto položky. Toto pole se používá v části 2 tohoto kurzu.

**sku_name**: Toto pole je měřítko pro IoT Hub. Tato hodnota musí být S1 nebo vyšší. Bezplatná úroveň pro tento kurz nefunguje, protože nepovoluje více koncových bodů.

**sku_units**: Toto pole se používá **sku_name** a je počet IoT Hub jednotek, které se dají použít.

**d2c_partitions**: Toto pole je počet oddílů používaných pro datový proud událostí.

**storageAccountName_in**: Toto pole je název účtu úložiště, který se má vytvořit. Zprávy jsou směrovány do kontejneru v účtu úložiště. Toto pole je zřetězené s randomValue, aby se globálně jedinečné.

**storageContainerName**: Toto pole je název kontejneru, ve kterém jsou uložené zprávy směrované do účtu úložiště.

**storage_endpoint**: Toto pole je název koncového bodu účtu úložiště používaného směrováním zpráv.

**service_bus_namespace_in**: Toto pole je název oboru názvů Service Bus, který se má vytvořit. Tato hodnota je zřetězena s randomValue, aby byla globálně jedinečná.

**service_bus_queue_in**: Toto pole je název Service Bus fronty používané pro směrování zpráv. Tato hodnota je zřetězena s randomValue, aby byla globálně jedinečná.

**AuthRules_sb_queue**: Toto pole je autorizační pravidla pro frontu Service Bus, která slouží k načtení připojovacího řetězce pro frontu.

### <a name="variables"></a>Proměnné

Tyto hodnoty se používají v šabloně a jsou převážně odvozené z parametrů.

**queueAuthorizationRuleResourceId**: Toto pole je ResourceID pro autorizační pravidlo pro frontu Service Bus. ResourceId se pak používá k načtení připojovacího řetězce pro frontu.

**iotHubName**: Toto pole je název IoT Hub po tom, co se randomValue zřetězí. 

**storageAccountName**: Toto pole je název účtu úložiště po jeho zřetězení randomValue. 

**service_bus_namespace**: Toto pole je obor názvů po randomValue zřetězení.

**service_bus_queue**: Toto pole je název fronty Service Bus po randomValue zřetězení.

**sbVersion**: verze rozhraní API pro Service Bus, která se má použít. V tomto případě je to "2017-04-01".

### <a name="resources-storage-account-and-container"></a>Prostředky: účet úložiště a kontejner

Prvním vytvořeným prostředkem je účet úložiště společně s kontejnerem, do kterého se mají zprávy směrovat. Kontejner je prostředkem v účtu úložiště. Obsahuje `dependsOn` klauzuli pro účet úložiště, která vyžaduje vytvoření účtu úložiště před kontejnerem.

Tato část vypadá takto:

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2018-07-01",
    "location": "[parameters('location')]",
    "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
    },
    "kind": "Storage",
    "properties": {},
    "resources": [
        {
        "type": "blobServices/containers",
        "apiVersion": "2018-07-01",
        "name": "[concat('default/', parameters('storageContainerName'))]",
        "properties": {
            "publicAccess": "None"
            } ,
        "dependsOn": [
            "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
            ]
        }
    ]
}
```

### <a name="resources-service-bus-namespace-and-queue"></a>Prostředky: Service Bus obor názvů a fronta

Druhým vytvořeným prostředkem je Service Bus obor názvů společně s frontou Service Bus, do které jsou zprávy směrovány. SKU je nastavené na standardní. Verze rozhraní API se načte z proměnných. Také je nastavena k aktivaci Service Bus obor názvů při nasazení této části (stav: aktivní). 

```json
{
    "type": "Microsoft.ServiceBus/namespaces",
    "comments": "The Sku should be 'Standard' for this tutorial.",
    "sku": {
        "name": "Standard",
        "tier": "Standard"
    },
    "name": "[variables('service_bus_namespace')]",
    "apiVersion": "[variables('sbVersion')]",
    "location": "[parameters('location')]",
    "properties": {
        "provisioningState": "Succeeded",
        "metricId": "[concat('a4295411-5eff-4f81-b77e-276ab1ccda12:', variables('service_bus_namespace'))]",
        "serviceBusEndpoint": "[concat('https://', variables('service_bus_namespace'),'.servicebus.windows.net:443/')]",
        "status": "Active"
    },
    "dependsOn": []
}
```

V této části se vytvoří fronta Service Bus. Tato část skriptu obsahuje `dependsOn` klauzuli, která zajišťuje, že se obor názvů vytvoří před frontou.

```json
{
    "type": "Microsoft.ServiceBus/namespaces/queues",
    "name": "[concat(variables('service_bus_namespace'), '/', variables('service_bus_queue'))]",
    "apiVersion": "[variables('sbVersion')]",
    "location": "[parameters('location')]",
    "scale": null,
    "properties": {},
    "dependsOn": [
        "[resourceId('Microsoft.ServiceBus/namespaces', variables('service_bus_namespace'))]"
    ]
}
```

### <a name="resources-iot-hub-and-message-routing"></a>Prostředky: Centrum IoT a směrování zpráv

Teď, když jste vytvořili účet úložiště a frontu Service Bus, vytvoříte IoT Hub, která do nich směruje zprávy. Šablona RM používá `dependsOn` klauzule, takže se nepokouší vytvořit centrum před Service Bus prostředky a účet úložiště se vytvořil. 

Tady je první část IoT Hub části. Tato část šablony nastavuje závislosti a začíná vlastnostmi.

```json
{
    "apiVersion": "2018-04-01",
    "type": "Microsoft.Devices/IotHubs",
    "name": "[variables('IoTHubName')]",
    "location": "[parameters('location')]",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "[resourceId('Microsoft.ServiceBus/namespaces', variables('service_bus_namespace'))]",
        "[resourceId('Microsoft.ServiceBus/namespaces/queues', variables('service_bus_namespace'), variables('service_bus_queue'))]"
    ],
    "properties": {
        "eventHubEndpoints": {}
            "events": {
                "retentionTimeInDays": 1,
                "partitionCount": "[parameters('d2c_partitions')]"
                }
            },
```

Další část je oddíl konfigurace směrování zpráv pro službu IoT Hub. První je oddíl pro koncové body. Tato část šablony nastavuje koncové body směrování pro frontu Service Bus a účet úložiště, včetně připojovacích řetězců.

Pro vytvoření připojovacího řetězce pro frontu potřebujete queueAuthorizationRulesResourcedId, který se načte jako vložený. Pokud chcete vytvořit připojovací řetězec pro účet úložiště, načtěte primární klíč úložiště a pak ho použijte ve formátu připojovacího řetězce.

Konfigurace koncového bodu taky nastavuje formát objektu BLOB na `AVRO` nebo `JSON` .

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

 ```json
"routing": {
    "endpoints": {
        "serviceBusQueues": [
        {
            "connectionString": "[Concat('Endpoint=sb://',variables('service_bus_namespace'),'.servicebus.windows.net/;SharedAccessKeyName=',parameters('AuthRules_sb_queue'),';SharedAccessKey=',listkeys(variables('queueAuthorizationRuleResourceId'),variables('sbVersion')).primaryKey,';EntityPath=',variables('service_bus_queue'))]",
            "name": "[parameters('service_bus_queue_endpoint')]",
            "subscriptionId": "[parameters('subscriptionId')]", 
            "resourceGroup": "[resourceGroup().Name]"
        }
        ],
        "serviceBusTopics": [],
        "eventHubs": [],
        "storageContainers": [
            {
                "connectionString": 
                "[Concat('DefaultEndpointsProtocol=https;AccountName=',variables('storageAccountName'),';AccountKey=',listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), providers('Microsoft.Storage', 'storageAccounts').apiVersions[0]).keys[0].value)]",
                "containerName": "[parameters('storageContainerName')]",
                "fileNameFormat": "{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}",
                "batchFrequencyInSeconds": 100,
                "maxChunkSizeInBytes": 104857600,
                "encoding": "avro",
                "name": "[parameters('storage_endpoint')]",
                "subscriptionId": "[parameters('subscriptionId')]",
                "resourceGroup": "[resourceGroup().Name]"
            }
        ]
    },
```

Tato další část je určena pro směrování zpráv do koncových bodů. U každého koncového bodu je nastavená jedna sada, takže je k dispozici pro frontu Service Bus a jednu pro kontejner účtu úložiště.

Pamatujte, že podmínka dotazu pro zprávy směrované do úložiště je `level="storage"` a podmínka dotazu pro zprávy směrované do fronty Service Bus je `level="critical"` .

```json
"routes": [
    {
        "name": "contosoStorageRoute",
        "source": "DeviceMessages",
        "condition": "level=\"storage\"",
        "endpointNames": [
            "[parameters('storage_endpoint')]"
            ],
        "isEnabled": true
    },
    {
        "name": "contosoSBQueueRoute",
        "source": "DeviceMessages",
        "condition": "level=\"critical\"",
        "endpointNames": [
            "[parameters('service_bus_queue_endpoint')]"
            ],
        "isEnabled": true
    }
],
```

Tento JSON zobrazuje zbývající část IoT Hub, která obsahuje výchozí informace a SKU pro centrum.

```json
            "fallbackRoute": {
                "name": "$fallback",
                "source": "DeviceMessages",
                "condition": "true",
                "endpointNames": [
                    "events"
                ],
                "isEnabled": true
            }
        },
        "storageEndpoints": {
            "$default": {
                "sasTtlAsIso8601": "PT1H",
                "connectionString": "",
                "containerName": ""
            }
        },
        "messagingEndpoints": {
            "fileNotifications": {
                "lockDurationAsIso8601": "PT1M",
                "ttlAsIso8601": "PT1H",
                "maxDeliveryCount": 10
            }
        },
        "enableFileUploadNotifications": false,
        "cloudToDevice": {
            "maxDeliveryCount": 10,
            "defaultTtlAsIso8601": "PT1H",
            "feedback": {
                "lockDurationAsIso8601": "PT1M",
                "ttlAsIso8601": "PT1H",
                "maxDeliveryCount": 10
            }
        }
    },
    "sku": {
        "name": "[parameters('sku_name')]",
        "capacity": "[parameters('sku_units')]"
    }
}
```

### <a name="resources-service-bus-queue-authorization-rules"></a>Prostředky: autorizační pravidla fronty Service Bus

Autorizační pravidlo fronty Service Bus slouží k načtení připojovacího řetězce pro frontu Service Bus. Používá `dependsOn` klauzuli pro zajištění, že není vytvořen před oborem názvů Service Bus a frontou Service Bus.

```json
{
    "type": "Microsoft.ServiceBus/namespaces/queues/authorizationRules",
    "name": "[concat(variables('service_bus_namespace'), '/', variables('service_bus_queue'), '/', parameters('AuthRules_sb_queue'))]",
    "apiVersion": "[variables('sbVersion')]",
    "location": "[parameters('location')]",
    "scale": null,
    "properties": {
        "rights": [
            "Send"
        ]
    },
    "dependsOn": [
        "[resourceId('Microsoft.ServiceBus/namespaces', variables('service_bus_namespace'))]",
        "[resourceId('Microsoft.ServiceBus/namespaces/queues', variables('service_bus_namespace'), variables('service_bus_queue'))]"
    ]
},
```

### <a name="resources-consumer-group"></a>Prostředky: Skupina uživatelů

V této části vytvoříte skupinu příjemců pro IoT Hubá data, která budou použita Azure Stream Analytics v druhé části tohoto kurzu.

```json
{
    "type": "Microsoft.Devices/IotHubs/eventHubEndpoints/ConsumerGroups",
    "name": "[concat(variables('iotHubName'), '/events/',parameters('consumer_group'))]",
    "apiVersion": "2018-04-01",
    "dependsOn": [
        "[concat('Microsoft.Devices/IotHubs/', variables('iotHubName'))]"
    ]
}
```

### <a name="resources-outputs"></a>Prostředky: výstupy

Pokud chcete odeslat hodnotu zpět do skriptu nasazení, který se má zobrazit, použijte výstupní část. Tato část šablony vrátí připojovací řetězec pro frontu Service Bus. Vrácení hodnoty není vyžadováno, je obsaženo jako příklad, jak vracet výsledky do volajícího skriptu.

```json
"outputs": {
    "sbq_connectionString": {
      "type": "string",
      "value": "[Concat('Endpoint=sb://',variables('service_bus_namespace'),'.servicebus.windows.net/;SharedAccessKeyName=',parameters('AuthRules_sb_queue'),';SharedAccessKey=',listkeys(variables('queueAuthorizationRuleResourceId'),variables('sbVersion')).primaryKey,';EntityPath=',variables('service_bus_queue'))]"
    }
  }
```

## <a name="deploy-the-rm-template"></a>Nasazení šablony RM

Pokud chcete nasadit šablonu do Azure, nahrajte šablonu a soubor parametrů, abyste Azure Cloud Shell, a pak spuštěním skriptu nasaďte šablonu. Otevřete Azure Cloud Shell a přihlaste se. V tomto příkladu se používá PowerShell.

Chcete-li nahrát soubory, vyberte ikonu **nahrát/stáhnout soubory** v řádku nabídek a pak zvolte Odeslat.

![Snímek obrazovky, který zvýrazní ikonu pro nahrávání nebo stahování souborů.](media/tutorial-routing-config-message-routing-RM-template/CloudShell_upload_files.png)

Pomocí Průzkumníka souborů, které se zobrazí, Najděte soubory na místním disku a vyberte je a pak zvolte **otevřít**.

Po nahrání souborů se v dialogovém okně výsledků zobrazí něco jako na následujícím obrázku.

![Cloud Shell řádku nabídek s zvýrazněnými výsledky nahrávání nebo stahování](media/tutorial-routing-config-message-routing-RM-template/CloudShell_upload_results.png)

Soubory se nahrají do sdílené složky používané instancí Cloud Shell. 

Spusťte skript pro provedení nasazení. Poslední řádek tohoto skriptu načte proměnnou, která je nastavená tak, aby se vrátila – připojovací řetězec fronty Service Bus.

Skript nastaví a použije tyto proměnné:

**$RGName** je název skupiny prostředků, do které chcete šablonu nasadit. Toto pole je vytvořeno před nasazením šablony.

**$Location** je umístění Azure, které se má použít pro šablonu, například "westus".

název **nasazení** je název, který přiřadíte nasazení, aby se načetla návratová hodnota proměnné.

Tady je skript PowerShellu. Zkopírujte tento skript PowerShellu a vložte ho do okna Cloud Shell a pak stiskněte ENTER a spusťte ho.

```powershell
$RGName="ContosoResources"
$location = "westus"
$deploymentname="contoso-routing"

# Remove the resource group if it already exists. 
#Remove-AzResourceGroup -name $RGName 
# Create the resource group.
New-AzResourceGroup -name $RGName -Location $location 

# Set a path to the parameter file. 
$parameterFile = "$HOME/template_iothub_parameters.json"
$templateFile = "$HOME/template_iothub.json"

# Deploy the template.
New-AzResourceGroupDeployment `
    -Name $deploymentname `
    -ResourceGroupName $RGName `
    -TemplateParameterFile $parameterFile `
    -TemplateFile $templateFile `
    -verbose

# Get the returning value of the connection string.
(Get-AzResourceGroupDeployment -ResourceGroupName $RGName -Name $deploymentname).Outputs.sbq_connectionString.value
```

Pokud máte chyby skriptu, můžete skript místně upravit a znovu ho nahrát do Cloud Shell a skript spustit znovu. Po úspěšném spuštění skriptu přejděte k dalšímu kroku.

## <a name="create-simulated-device"></a>Vytvoření simulovaného zařízení

[!INCLUDE [iot-hub-include-create-simulated-device-portal](../../includes/iot-hub-include-create-simulated-device-portal.md)]

## <a name="view-message-routing-in-the-portal"></a>Zobrazení směrování zpráv na portálu

[!INCLUDE [iot-hub-include-view-routing-in-portal](../../includes/iot-hub-include-view-routing-in-portal.md)]

## <a name="next-steps"></a>Další kroky

Teď, když máte nastavené všechny prostředky a nakonfigurují se trasy zpráv, přejděte k dalšímu kurzu, kde se dozvíte, jak zpracovávat a zobrazovat informace o směrovaných zprávách.

> [!div class="nextstepaction"]
> [Část 2 – zobrazení výsledků směrování zpráv](tutorial-routing-view-message-routing-results.md)
