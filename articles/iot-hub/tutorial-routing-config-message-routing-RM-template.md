---
title: Konfigurace směrování zpráv pro službu Azure IoT Hub pomocí šablony Azure Resource Manageru | Dokumentace Microsoftu
description: Konfigurace směrování zpráv pro službu Azure IoT Hub pomocí šablony Azure Resource Manageru
author: robinsh
manager: philmeagit st
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 58b0039980f448c9bd953e9d59289c511ce95517
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2019
ms.locfileid: "58663022"
---
# <a name="tutorial-use-an-azure-resource-manager-template-to-configure-iot-hub-message-routing"></a>Kurz: Konfigurace směrování zpráv služby IoT Hub pomocí šablony Azure Resource Manageru

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="message-routing"></a>Směrování zpráv

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

## <a name="download-the-template-and-parameters-file"></a>Stáhněte si šablonu a parametry

Druhé části tohoto kurzu stáhněte a spusťte aplikaci Visual Studio pro odesílání zpráv do služby IoT Hub. Tento soubor ke stažení, který obsahuje šablony Azure Resource Manageru a soubor parametrů, jakož i skripty rozhraní příkazového řádku Azure a Powershellu složka existuje.

Pokračujte a stáhněte si [Azure IoT C# ukázky](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) nyní. Rozbalte soubor master.zip. Šablony Resource Manageru a do souboru parametrů jsou v /iot-hub/Tutorials/Routing/SimulatedDevice/resources/jako **template_iothub.json** a **template_iothub_parameters.json**.

## <a name="create-your-resources"></a>Vytvoření prostředků

Chystáte se vytvořit všech vašich prostředků pomocí šablony Azure Resource Manager (SV). Skripty rozhraní příkazového řádku Azure a Powershellu můžete současně spustit několik řádků. Šablony správce prostředků se nasadí v jednom kroku. Tento článek popisuje části samostatně, které vám pomůžou porozumět každé z nich. Pak ji bude ukazují, jak nasadit šablonu a vytvořte virtuální zařízení pro účely testování. Po nasazení šablony můžete zobrazit konfigurace směrování zpráva na portálu.

Existuje několik názvy prostředků, které musí být globálně jedinečný, jako je název služby IoT Hub a název účtu úložiště. Chcete-li snadněji prostředky pojmenování, tyto názvy prostředků nastavení připojit náhodných alfanumerických hodnota generovaná z aktuální datum a čas. 

Když se podíváte na šablonu, zobrazí se vám kde proměnné jsou nastavit pro tyto prostředky, které může parametr předaný a zřetězit *randomValue* parametru. 

Následující část vysvětluje použité parametry.

### <a name="parameters"></a>Parametry

Většina tyto parametry mají výchozí hodnoty. Ty končí **_Ne** jsou spojeny s *randomValue* , aby byly globálně jedinečné. 

**randomValue**: Tato hodnota se generuje z aktuální datum a čas při nasazování šablony. Toto pole není v souboru parametrů, jak často jsou generovaná v samotné šablony.

**subscriptionId**: Toto pole je nastavení za vás na předplatné, do kterého nasazujete šablony. Toto pole není v souboru parametrů, protože je nastaven pro vás.

**IoTHubName_in**: Toto pole je základní název služby IoT Hub, který je zřetězený s randomValue být globálně jedinečný.

**Umístění**: Toto pole je oblast Azure, do které nasazujete, jako je například "westus".

**consumer_group**: Toto pole je skupina uživatelů, nastavit pro zprávy přicházející směrování koncový bod. Používá se k filtrování výsledků v Azure Stream Analytics. Například neexistuje celého datového proudu tam, kde získáte všechno, co, nebo pokud máte data přicházející s consumer_group nastavena na **Contoso**, pak můžete nastavit Azure Stream Analytics stream (a sestavy Power BI) Chcete-li zobrazit pouze ty položky. Toto pole se používá v části 2 tohoto kurzu.

**sku_name**: Toto pole je škálování pro službu IoT Hub. Tato hodnota musí být S1 nebo novější; úroveň free pro účely tohoto kurzu nefunguje, protože neumožňuje víc koncových bodů.

**sku_units**: Toto pole doprovází **sku_name**, a počet jednotek služby IoT Hub, které lze použít.

**d2c_partitions**: Toto pole je počet oddílů pro datového proudu událostí.

**storageAccountName_in**: Toto pole je název účtu úložiště, který se má vytvořit. Zprávy jsou směrovány do kontejneru v účtu úložiště. Toto pole je zřetězená s randomValue, aby byl globálně jedinečný.

**storageContainerName**: Toto pole je název kontejneru, ve kterém jsou uložené zprávy směruje do účtu úložiště.

**storage_endpoint**: Toto pole je název pro koncový bod účtu úložiště používá směrování zpráv.

**service_bus_namespace_in**: Toto pole je název oboru názvů Service Bus, který se má vytvořit. Tato hodnota je zřetězená s randomValue, aby byl globálně jedinečný.

**service_bus_queue_in**: Toto pole je název fronty služby Service Bus používá pro směrování zpráv. Tato hodnota je zřetězená s randomValue, aby byl globálně jedinečný.

**AuthRules_sb_queue**: Toto pole je autorizačních pravidel pro fronty service bus se používá k načtení připojovacího řetězce pro frontu.

### <a name="variables"></a>Proměnné

Tyto hodnoty se používají v šabloně a jsou většinou odvozen z parametrů.

**queueAuthorizationRuleResourceId**: Toto pole je ID prostředku pro autorizační pravidlo pro frontu služby Service Bus. ResourceId zase slouží k načtení připojovacího řetězce pro frontu.

**iotHubName**: Toto pole je název služby IoT Hub po s randomValue zřetězeny. 

**storageAccountName**: Toto pole je název účtu úložiště po s randomValue zřetězeny. 

**service_bus_namespace**: Toto pole je obor názvů po s randomValue zřetězeny.

**service_bus_queue**: Toto pole je název fronty služby Service Bus po s randomValue zřetězeny.

**sbVersion**: Verze rozhraní API služby Service Bus pro použití. V takovém případě je "2017-04-01".

### <a name="resources-storage-account-and-container"></a>Prostředky: Účet úložiště a kontejner

Na první prostředek vytvořen je spolu s kontejneru, do které zprávy jsou směrovány na účet úložiště. Kontejner je prostředek v rámci účtu úložiště. Má `dependsOn` klauzuli pro účet úložiště, které vyžadují účet úložiště vytvořit před kontejneru.

Zde je, jak vypadá v této části:

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

### <a name="resources-service-bus-namespace-and-queue"></a>Prostředky: Obor názvů služby Service Bus a fronty

Druhý zdroj vytvoří se obor názvů služby Service Bus, spolu s frontu služby Service Bus, ke kterému jsou směrovány zprávy. SKU se nastaví na standardní. Verze rozhraní API je načten z proměnné. Je také nastavena na aktivovat obor názvů služby Service Bus, při nasazení v této části (stav: aktivní). 

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

Tato část vytvoří frontu služby Service Bus. Tato část souboru, který má `dependsOn` klauzuli, která zajišťuje obor názvů je vytvořen před fronty.

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

### <a name="resources-iot-hub-and-message-routing"></a>Prostředky: IOT Hub a směrování zpráv

Teď, když jste vytvořili účet úložiště a fronty služby Service Bus, vytvoření služby IoT Hub, který provádí směrování zpráv do nich. Šablona správce prostředků používá `dependsOn` klauzule, takže ho nebude pokusu o vytvoření centra předtím, než byly vytvořeny prostředky služby Service Bus a účet úložiště. 

Zde je první část oddíl služby IoT Hub. Tato část šablony nastaví závislosti a spustí s vlastnostmi.

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

V další části se v části Konfigurace směrování zpráv pro službu Iot Hub. Nejprve je oddíl pro koncové body. Tato část šablony nastaví směrování koncových bodů pro frontu služby Service Bus a účet úložiště, včetně připojovacích řetězců.

Vytvoření připojovacího řetězce pro fronty, je třeba queueAuthorizationRulesResourcedId, což je načtený vložené. Chcete-li vytvořit připojovací řetězec pro účet úložiště, načíst klíč primárního úložiště a používat ho ve formátu připojovacího řetězce.

Konfigurace koncového bodu je také, kde nastavíte formát objektu blob na `AVRO` nebo `JSON`.

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

Tato další část se týká směrování zpráv do koncových bodů. Existuje je jedna sada pro každý koncový bod, takže jeden pro frontu služby Service Bus a jeden pro kontejner účtu úložiště.

Mějte na paměti, že je podmínka dotazu pro zprávy směruje do úložiště `level="storage"`, a podmínky dotazu pro zprávy směruje do fronty služby Service Bus je `level="critical"`.

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

Tento dokument json ukazuje zbývající části služby IoT Hub, která obsahuje výchozí informace a SKU pro rozbočovač.

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

### <a name="resources-service-bus-queue-authorization-rules"></a>Prostředky: Autorizační pravidla fronty služby Service Bus

Autorizační pravidlo pro frontu služby Service Bus se používá k načtení připojovacího řetězce pro frontu služby Service Bus. Použije `dependsOn` není klauzule tak, aby byl vytvořen před obor názvů služby Service Bus a fronty služby Service Bus.

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

V této části vytvoříte skupinu příjemců pro data služby IoT Hub použije Azure Stream Analytics v druhé části tohoto kurzu.

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

### <a name="resources-outputs"></a>Prostředky: Výstupy

Pokud chcete odesílat hodnotu zpět do skriptu nasazení, který se má zobrazit, použijete výstupní sekce. Tato část šablony vrátí připojovací řetězec pro frontu služby Service Bus. Vrátí že hodnotu není vyžadována, je zahrnutý jako příklad toho, jak vracet výsledky volání skriptu.

```json
"outputs": {
    "sbq_connectionString": {
      "type": "string",
      "value": "[Concat('Endpoint=sb://',variables('service_bus_namespace'),'.servicebus.windows.net/;SharedAccessKeyName=',parameters('AuthRules_sb_queue'),';SharedAccessKey=',listkeys(variables('queueAuthorizationRuleResourceId'),variables('sbVersion')).primaryKey,';EntityPath=',variables('service_bus_queue'))]"
    }
  }
```

## <a name="deploy-the-rm-template"></a>Nasazení šablony správce prostředků

K nasazení šablony do Azure, nahrát šablonu a parametry souboru do služby Azure Cloud Shell a potom spusťte skript k nasazení šablony. Otevřete Azure Cloud Shell a přihlaste se. Tento příklad používá PowerShell.

Pokud chcete nahrát soubory, vyberte **nahrávání a stahování souborů** ikony v panelu nabídek klikněte na tlačítko Odeslat.

![Nahrávání a stahování souborů zvýrazněný řádek nabídek prostředí cloud](media/tutorial-routing-config-message-routing-RM-template/CloudShell_upload_files.png)

Pomocí Průzkumníka souborů, která se otevře na hledání souborů na místním disku a vyberte je a pak zvolte **otevřít**.

Po nahrání souborů bude dialogové okno výsledků zobrazuje něco jako na následujícím obrázku.

![Nahrávání a stahování souborů zvýrazněný řádek nabídek prostředí cloud](media/tutorial-routing-config-message-routing-RM-template/CloudShell_upload_results.png)

Soubory jsou odeslány do sdílené složky používané vaší instance Cloud Shell. 

Spusťte skript, který chcete provést nasazení. Poslední řádek tento skript načte proměnnou, kterou jste nastavili, který se má vrátit – připojovací řetězec pro frontu služby Service Bus.

Tyto proměnné se nastavují v tomto skriptu.

**$RGName** je název skupiny prostředků, do které chcete nasadit šablonu. Toto pole je vytvořen před nasazením šablony.

**$location** je umístění v Azure má být použit pro šablony, jako je například "westus".

**deploymentname** je název nasazení je přiřadit k načtení vracející hodnotu proměnné.

Tady je skript prostředí PowerShell. Zkopírujte tento skript Powershellu a vložte ho do okna služby Cloud Shell, stačí stisknout Enter a spustíme ji.

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

Pokud máte chyby skriptu, upravte skript místně, znovu nahrát do služby Cloud Shell a spusťte znovu skript pro. Po dokončení skriptu, spouští úspěšně, pokračujte k dalšímu kroku.

## <a name="create-simulated-device"></a>Vytvoření simulovaného zařízení

[!INCLUDE [iot-hub-include-create-simulated-device-portal](../../includes/iot-hub-include-create-simulated-device-portal.md)]

## <a name="view-message-routing-in-the-portal"></a>Zobrazit na portálu pro směrování zpráv

[!INCLUDE [iot-hub-include-view-routing-in-portal](../../includes/iot-hub-include-view-routing-in-portal.md)]

## <a name="next-steps"></a>Další postup

Teď, když máte všechny prostředky, nastavení a jsou nakonfigurované trasy zpráv, přejděte k dalšímu kurzu, přečtěte si, jak zpracovávat a zobrazovat informace o směrování zpráv.

> [!div class="nextstepaction"]
> [Část 2 – zobrazení výsledků směrování zpráv](tutorial-routing-view-message-routing-results.md)