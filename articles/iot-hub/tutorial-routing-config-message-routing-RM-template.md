---
title: Konfigurace směrování zpráv pro Azure IoT Hub pomocí šablony Azure Resource Manager
description: Konfigurace směrování zpráv pro Azure IoT Hub pomocí šablony Azure Resource Manager
author: robinsh
manager: philmeagit st
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 8f245653a8b84944e1e8a3f48a49992f0065be58
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "74084394"
---
# <a name="tutorial-use-an-azure-resource-manager-template-to-configure-iot-hub-message-routing"></a>Kurz: Konfigurace směrování zpráv služby IoT Hub pomocí šablony Správce prostředků Azure

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="message-routing"></a>Směrování zpráv

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

## <a name="download-the-template-and-parameters-file"></a>Stažení souboru šablony a parametrů

Pro druhou část tohoto kurzu stáhnete a spustíte aplikaci Visual Studio pro odesílání zpráv do služby IoT Hub. V tomto stažení je složka, která obsahuje šablonu Azure Resource Manager a soubor parametrů, stejně jako skripty Azure CLI a PowerShell.

Stáhněte si [ukázky Azure IoT C#](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) teď. Rozbalte soubor master.zip. Šablona Resource Manager a soubor parametrů jsou v /iot-hub/Tutorials/Routing/SimulatedDevice/resources/ jako **template_iothub.json** a **template_iothub_parameters.json**.

## <a name="create-your-resources"></a>Vytvořte si zdroje

K vytvoření všech prostředků použijete šablonu Azure Resource Manager (RM). Skripty Azure CLI a PowerShell lze spustit několik řádků najednou. Šablona RM je nasazena v jednom kroku. Tento článek zobrazuje oddíly samostatně, které vám pomohou pochopit každý z nich. Pak vám ukáže, jak nasadit šablonu a vytvořit virtuální zařízení pro testování. Po nasazení šablony můžete zobrazit konfiguraci směrování zpráv na portálu.

Existuje několik názvů prostředků, které musí být globálně jedinečné, jako je například název služby IoT Hub a název účtu úložiště. Chcete-li usnadnit pojmenování prostředků, jsou tyto názvy prostředků nastaveny tak, aby připojovaly náhodnou alfanumerickou hodnotu generovanou z aktuálního data a času. 

Pokud se podíváte na šablonu, uvidíte, kde jsou nastaveny proměnné pro tyto prostředky, které berou parametr předán a zřetězit *randomValue* na parametr. 

V následující části jsou vysvětleny použité parametry.

### <a name="parameters"></a>Parametry

Většina těchto parametrů má výchozí hodnoty. Ty končící **_in** jsou zřetězené s *randomValue,* aby byly globálně jedinečné. 

**randomValue**: Tato hodnota je generována z aktuálního data a času při nasazení šablony. Toto pole není v souboru parametrů, protože je generováno v samotné šabloně.

**subscriptionID**: Toto pole je nastaveno pro odběr, do kterého nasazujíte šablonu. Toto pole není v souboru parametrů, protože je nastaveno pro vás.

**IoTHubName_in**: Toto pole je základní název služby IoT Hub, který je zřetězen s randomValue globálně jedinečný.

**umístění**: Toto pole je oblast Azure, do které nasazujete, například "westus".

**consumer_group**: Toto pole je nastavena skupina příjemce pro zprávy přicházející prostřednictvím koncového bodu směrování. Používá se k filtrování výsledků v Azure Stream Analytics. Například je celý datový proud, kde získáte všechno, nebo pokud máte data přicházející s consumer_group nastavena na **Contoso**, pak můžete nastavit stream Azure Stream Analytics (a sestavu Power BI) tak, aby zobrazoval jenom tyto položky. Toto pole se používá v části 2 tohoto kurzu.

**sku_name**: Toto pole je měřítko pro ioT hub. Tato hodnota musí být S1 nebo vyšší; bezplatná úroveň nefunguje pro tento kurz, protože neumožňuje více koncových bodů.

**sku_units**: Toto pole je součástí **sku_name**a je počet jednotek ioT hubu, které lze použít.

**d2c_partitions**: Toto pole je počet oddílů použitých pro datový proud událostí.

**storageAccountName_in**: Toto pole je název účtu úložiště, který má být vytvořen. Zprávy jsou směrovány do kontejneru v účtu úložiště. Toto pole je zřetězeno s randomValue, aby bylo globálně jedinečné.

**storageContainerName**: Toto pole je název kontejneru, ve kterém jsou uloženy zprávy směrované do účtu úložiště.

**storage_endpoint**: Toto pole je název koncového bodu účtu úložiště, který používá směrování zpráv.

**service_bus_namespace_in**: Toto pole je název oboru názvů Service Bus, který má být vytvořen. Tato hodnota je zřetězena s randomValue, aby byla globálně jedinečná.

**service_bus_queue_in**: Toto pole je název fronty služby Service Bus používané pro směrování zpráv. Tato hodnota je zřetězena s randomValue, aby byla globálně jedinečná.

**AuthRules_sb_queue**: Toto pole je autorizační pravidla pro frontu sběrnice, která se používají k načtení připojovacího řetězce fronty.

### <a name="variables"></a>Proměnné

Tyto hodnoty se používají v šabloně a jsou většinou odvozeny z parametrů.

**queueAuthorizationRuleResourceId**: Toto pole je id prostředku pro autorizační pravidlo fronty služby Service Bus. ResourceId se zase používá k načtení připojovacího řetězce pro frontu.

**iotHubName**: Toto pole je název služby IoT Hub po náhodném zřetězení hodnoty. 

**storageAccountName**: Toto pole je název účtu úložiště po randomValue concatenated. 

**service_bus_namespace**: Toto pole je obor názvů po zřetězení náhodné hodnoty.

**service_bus_queue**: Toto pole je název fronty služby Service Bus po zřetězení náhodné hodnoty.

**sbVersion**: THe verze rozhraní API sběrnice, která má být používána. V tomto případě je to "2017-04-01".

### <a name="resources-storage-account-and-container"></a>Prostředky: Účet úložiště a kontejner

První vytvořený prostředek je účet úložiště, spolu s kontejnerem, do kterého jsou směrovány zprávy. Kontejner je prostředek v rámci účtu úložiště. Má `dependsOn` klauzuli pro účet úložiště, vyžadující účet úložiště být vytvořen před kontejneru.

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

### <a name="resources-service-bus-namespace-and-queue"></a>Prostředky: Obor názvů a fronta služby Service Bus

Druhý vytvořený prostředek je obor názvů Service Bus spolu s frontou Service Bus, do které jsou zprávy směrovány. Skladová položka je nastavena na standardní. Verze rozhraní API je načtena z proměnných. Je také nastavena na aktivaci oboru názvů Service Bus při nasazení této části (stav:Active). 

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

Tato část vytvoří frontu služby Service Bus. Tato část skriptu `dependsOn` má klauzuli, která zajišťuje, že obor názvů je vytvořen před frontou.

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

### <a name="resources-iot-hub-and-message-routing"></a>Prostředky: Služba Iot Hub a směrování zpráv

Teď, když byl vytvořen účet úložiště a fronta Service Bus, vytvoříte službu IoT Hub, která jim směruje zprávy. Šablona RM `dependsOn` používá klauzule, takže se nepokouší vytvořit rozbočovač před vytvořením prostředků service bus a účtu úložiště. 

Tady je první část sekce IoT Hub. Tato část šablony nastaví závislosti a začíná vlastnostmi.

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

Další část je část pro konfiguraci směrování zpráv pro službu Iot Hub. První je část pro koncové body. Tato část šablony nastaví koncové body směrování pro frontu Service Bus a účet úložiště, včetně připojovacích řetězců.

Chcete-li vytvořit připojovací řetězec pro frontu, potřebujete queueAuthorizationRulesResourcedId, který je načten v řádku. Chcete-li vytvořit připojovací řetězec pro účet úložiště, načtěte primární klíč úložiště a pak jej použijte ve formátu pro připojovací řetězec.

Konfigurace koncového bodu je také místo, `AVRO` kde `JSON`nastavíte formát objektu blob nebo .

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

Tato další část je pro zprávy trasy ke koncovým bodům. Je jedno nastaveno pro každý koncový bod, takže je jeden pro frontu service bus a jeden pro kontejner účtu úložiště.

Nezapomeňte, že podmínka dotazu pro zprávy `level="storage"`směrované do úložiště je a podmínka dotazu pro `level="critical"`zprávy směrované do fronty služby Service Bus je .

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

Tento json zobrazuje zbytek oddílu IoT Hub, který obsahuje výchozí informace a skladovou položku pro rozbočovač.

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

### <a name="resources-service-bus-queue-authorization-rules"></a>Prostředky: Pravidla autorizace fronty služby Service Bus

Pravidlo autorizace fronty služby Service Bus se používá k načtení připojovacího řetězce pro frontu služby Service Bus. Používá `dependsOn` klauzuli k zajištění, že není vytvořen před oborem názvů service bus a fronty Service Bus.

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

### <a name="resources-consumer-group"></a>Zdroje: Skupina spotřebitelů

V této části vytvoříte skupinu spotřebitelů pro data služby IoT Hub, která mají být použita službou Azure Stream Analytics v druhé části tohoto kurzu.

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

### <a name="resources-outputs"></a>Zdroje: Výstupy

Pokud chcete odeslat hodnotu zpět do skriptu nasazení, který má být zobrazen, použijte výstupní oddíl. Tato část šablony vrátí připojovací řetězec pro frontu služby Service Bus. Vrácení hodnoty není povinné, je zahrnuta jako příklad, jak vrátit výsledky do volajícího skriptu.

```json
"outputs": {
    "sbq_connectionString": {
      "type": "string",
      "value": "[Concat('Endpoint=sb://',variables('service_bus_namespace'),'.servicebus.windows.net/;SharedAccessKeyName=',parameters('AuthRules_sb_queue'),';SharedAccessKey=',listkeys(variables('queueAuthorizationRuleResourceId'),variables('sbVersion')).primaryKey,';EntityPath=',variables('service_bus_queue'))]"
    }
  }
```

## <a name="deploy-the-rm-template"></a>Nasazení šablony RM

Chcete-li nasadit šablonu do Azure, nahrajte šablonu a soubor parametrů do Azure Cloud Shella a pak spusťte skript pro nasazení šablony. Otevřete Azure Cloud Shell a přihlaste se. Tento příklad používá prostředí PowerShell.

Chcete-li soubory nahrát, vyberte v řádku nabídek ikonu **Nahrát nebo stáhnout soubory** a pak zvolte Nahrát.

![Panel nabídek Prostředí Cloud se zvýrazněným seznamem Nahrát nebo stáhnout soubory](media/tutorial-routing-config-message-routing-RM-template/CloudShell_upload_files.png)

Pomocí Průzkumníka souborů, který se objeví, vyhledejte soubory na místním disku a vyberte je a pak zvolte **Otevřít**.

Po nahrání souborů se v dialogovém okně výsledků zobrazí něco jako na následujícím obrázku.

![Panel nabídek Prostředí Cloud se zvýrazněným seznamem Nahrát nebo stáhnout soubory](media/tutorial-routing-config-message-routing-RM-template/CloudShell_upload_results.png)

Soubory se nahrají do sdílené složky používané instancí prostředí Cloud Shell. 

Spusťte skript k provedení nasazení. Poslední řádek tohoto skriptu načte proměnnou, která byla nastavena na vrácení -- připojovací řetězec fronty service bus.

Skript nastavuje a používá tyto proměnné:

**$RGName** je název skupiny prostředků, do kterého chcete šablonu nasadit. Toto pole je vytvořeno před nasazením šablony.

**$location** je umístění Azure, které se má použít pro šablonu, například "westus".

**deploymentname** je název, který přiřadíte nasazení k načtení vrácené hodnoty proměnné.

Tady je skript Prostředí PowerShell. Zkopírujte tento skript prostředí PowerShell a vložte ho do okna Prostředí cloudu a pak ho spusťte klepnutím na Enter.

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

Pokud máte chyby skriptu, můžete skript upravit místně, znovu jej nahrát do prostředí Cloud Shell a skript znovu spustit. Po úspěšném spuštění skriptu pokračujte dalším krokem.

## <a name="create-simulated-device"></a>Vytvoření simulovaného zařízení

[!INCLUDE [iot-hub-include-create-simulated-device-portal](../../includes/iot-hub-include-create-simulated-device-portal.md)]

## <a name="view-message-routing-in-the-portal"></a>Zobrazení směrování zpráv na portálu

[!INCLUDE [iot-hub-include-view-routing-in-portal](../../includes/iot-hub-include-view-routing-in-portal.md)]

## <a name="next-steps"></a>Další kroky

Nyní, když máte všechny prostředky nastaveny a trasy zpráv jsou nakonfigurovány, přejde k dalšímu kurzu se dozvíte, jak zpracovat a zobrazit informace o směrovaných zpráv.

> [!div class="nextstepaction"]
> [Část 2 – Zobrazení výsledků směrování zpráv](tutorial-routing-view-message-routing-results.md)
