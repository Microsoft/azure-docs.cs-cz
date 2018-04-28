---
title: Vytvoření služby Azure IoT Hub pomocí šablony (PowerShell) | Microsoft Docs
description: Postup vytvoření služby IoT Hub pomocí prostředí PowerShell pomocí šablony Azure Resource Manager.
services: iot-hub
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 7eade855-c289-4ffb-b5ef-02be8c5f670f
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: 0e5f95d98f772b226e162f601939bc94bf8fb78b
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="create-an-iot-hub-using-azure-resource-manager-template-powershell"></a>Vytvoření služby IoT hub pomocí šablony Azure Resource Manageru (PowerShell)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Azure Resource Manager můžete použít k vytváření a správě Azure IoT hubs prostřednictvím kódu programu. V tomto kurzu se dozvíte, jak k vytvoření služby IoT hub pomocí prostředí PowerShell pomocí šablony Azure Resource Manager.

> [!NOTE]
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Azure Resource Manager a klasický](../azure-resource-manager/resource-manager-deployment-model.md). Tento článek se zabývá pomocí modelu nasazení Azure Resource Manager.

Pro absolvování tohoto kurzu potřebujete:

* Aktivní účet Azure. <br/>Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet][lnk-free-trial].
* [Azure PowerShell 1.0] [ lnk-powershell-install] nebo novější.

> [!TIP]
> Článek [použití Azure Powershellu s Azure Resource Manager] [ lnk-powershell-arm] poskytuje další informace o tom, jak pomocí prostředí PowerShell a Azure Resource Manager šablony vytváření prostředků Azure.

## <a name="connect-to-your-azure-subscription"></a>Připojení k předplatnému služby Azure

V příkazovém řádku prostředí PowerShell zadejte následující příkaz k přihlášení k předplatnému Azure:

```powershell
Connect-AzureRmAccount
```

Pokud máte víc předplatných Azure, přihlášení do Azure uděluje přístup do všech předplatná Azure přidružená přihlašovacích údajů. Pomocí následujícího příkazu zobrazíte seznam předplatných Azure, které je k dispozici pro použití:

```powershell
Get-AzureRMSubscription
```

Pomocí následujícího příkazu vyberte předplatné, které chcete použít ke spuštění příkazů pro vytvoření centra IoT. Můžete použít název nebo ID předplatného z výstupu předchozího příkazu:

```powershell
Select-AzureRMSubscription `
    -SubscriptionName "{your subscription name}"
```

Chcete-li zjistit, kde můžete nasadit služby IoT hub a aktuálně podporované verze rozhraní API můžete použít následující příkazy:

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Devices).ResourceTypes | Where-Object ResourceTypeName -eq IoTHubs).Locations
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Devices).ResourceTypes | Where-Object ResourceTypeName -eq IoTHubs).ApiVersions
```

Vytvořte skupinu prostředků tak, aby obsahovala služby IoT hub pomocí následujícího příkazu v jednom z podporovaných umístění pro IoT Hub. Tento příklad vytvoří skupinu prostředků s názvem **MyIoTRG1**:

```powershell
New-AzureRmResourceGroup -Name MyIoTRG1 -Location "East US"
```

## <a name="submit-a-template-to-create-an-iot-hub"></a>Odeslat šablonu pro vytvoření služby IoT hub

Použijte šablonu JSON pro vytvoření služby IoT hub ve vaší skupině prostředků. Šablonu Azure Resource Manager můžete také provést změny do stávající služby IoT hub.

1. Pomocí textového editoru vytvořit šablonu Azure Resource Manager názvem **template.json** s následující definice prostředků k vytvoření nového centra IoT standardní. Přidá IoT Hub v tomto příkladu **východní USA** oblast, vytvoří dvě skupiny uživatelů (**cg1** a **cg2**) na koncový bod kompatibilní s centrem událostí a používá  **2016-02-03** verze rozhraní API. Tato šablona také očekává, že budete předávat název centra IoT jako parametr názvem **hubName**. Aktuální seznam umístění, které podporují služby IoT Hub naleznete v části [stavu Azure][lnk-status].

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": {
          "type": "string"
        }
      },
      "resources": [
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs",
        "name": "[parameters('hubName')]",
        "location": "East US",
        "sku": {
          "name": "S1",
          "tier": "Standard",
          "capacity": 1
        },
        "properties": {
          "location": "East US"
        }
      },
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs/eventhubEndpoints/ConsumerGroups",
        "name": "[concat(parameters('hubName'), '/events/cg1')]",
        "dependsOn": [
          "[concat('Microsoft.Devices/Iothubs/', parameters('hubName'))]"
        ]
      },
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs/eventhubEndpoints/ConsumerGroups",
        "name": "[concat(parameters('hubName'), '/events/cg2')]",
        "dependsOn": [
          "[concat('Microsoft.Devices/Iothubs/', parameters('hubName'))]"
        ]
      }
      ],
      "outputs": {
        "hubKeys": {
          "value": "[listKeys(resourceId('Microsoft.Devices/IotHubs', parameters('hubName')), '2016-02-03')]",
          "type": "object"
        }
      }
    }
    ```

2. Uložte soubor šablony Azure Resource Manager na místním počítači. Tento příklad předpokládá můžete uložit ve složce s názvem **c:\templates**.

3. Spusťte následující příkaz k nasazení nového centra IoT předávání název služby IoT hub jako parametr. V tomto příkladu je název služby IoT hub `abcmyiothub`. Musí být globálně jedinečný název služby IoT hub:

    ```powershell
    New-AzureRmResourceGroupDeployment -ResourceGroupName MyIoTRG1 -TemplateFile C:\templates\template.json -hubName abcmyiothub
    ```
  [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

4. Výstup zobrazuje klíče pro službu IoT hub, kterou jste vytvořili.

5. Chcete-li ověřit, vaše aplikace přidat nového centra IoT, navštivte [portál Azure] [ lnk-azure-portal] a zobrazení seznamu prostředků. Můžete taky použít **Get-AzureRmResource** rutiny prostředí PowerShell.

> [!NOTE]
> Tato ukázková aplikace přidá S1 Standard IoT Hub pro kterou se účtují. Odstraněním služby IoT hub prostřednictvím [portál Azure] [ lnk-azure-portal] nebo pomocí **AzureRmResource odebrat** rutiny prostředí PowerShell po dokončení.

## <a name="next-steps"></a>Další postup

Nyní jste nasadili služby IoT hub pomocí šablony Azure Resource Manager pomocí prostředí PowerShell, můžete chtít Další:

* Přečtěte si informace o možnostech [zprostředkovatele prostředků služby IoT Hub REST API][lnk-rest-api].
* Čtení [přehled Azure Resource Manageru] [ lnk-azure-rm-overview] Další informace o funkcích nástroje Azure Resource Manager.

Další informace o vývoji pro Centrum IoT, naleznete v následujících článcích:

* [Úvod do jazyka C SDK][lnk-c-sdk]
* [Sady SDK služby Azure IoT][lnk-sdks]

Pokud chcete prozkoumat další možnosti IoT Hub, najdete v části:

* [Nasazení AI do hraničních zařízení s použitím Azure IoT Edge][lnk-iotedge]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: /powershell/azure/install-azurerm-ps
[lnk-rest-api]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-azure-rm-overview]: ../azure-resource-manager/resource-group-overview.md
[lnk-powershell-arm]: ../azure-resource-manager/powershell-azure-resource-manager.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
