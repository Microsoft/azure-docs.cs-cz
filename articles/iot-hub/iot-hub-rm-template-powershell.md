---
title: Vytvoření centra IoT Azure pomocí šablony (PowerShell) | Dokumentace Microsoftu
description: Jak používat šablony Azure Resource Manageru k vytvoření IoT Hubu pomocí Powershellu.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: 27e946dfdbb71bec03d3e4001ec63bd2d889e1b9
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/07/2019
ms.locfileid: "54063501"
---
# <a name="create-an-iot-hub-using-azure-resource-manager-template-powershell"></a>Vytvoření IoT hubu pomocí šablony Azure Resource Manageru (PowerShell)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Azure Resource Manageru můžete použít k vytváření a správě služby Azure IoT hubs prostřednictvím kódu programu. V tomto kurzu se dozvíte, jak pomocí šablony Azure Resource Manageru k vytvoření IoT hubu pomocí Powershellu.

> [!NOTE]
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Azure Resource Manager a classic](../azure-resource-manager/resource-manager-deployment-model.md). Tento článek se věnuje modelu nasazení Azure Resource Manageru.

Pro absolvování tohoto kurzu potřebujete:

* Aktivní účet Azure. <br/>Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet][lnk-free-trial].
* [Azure PowerShell 1.0] [ lnk-powershell-install] nebo novější.

> [!TIP]
> Tento článek [pomocí Azure Powershellu s Azure Resource Managerem] [ lnk-powershell-arm] poskytuje další informace o tom, jak vytvářet prostředky Azure pomocí šablony Azure Resource Manageru a Powershellu.

## <a name="connect-to-your-azure-subscription"></a>Připojení k předplatnému služby Azure

V příkazovém řádku prostředí PowerShell zadejte následující příkaz pro přihlášení ke svému předplatnému Azure:

```powershell
Connect-AzureRmAccount
```

Pokud máte více předplatných Azure, přihlášení k Azure získáte přístup ke Všechna předplatná Azure přidružená svoje přihlašovací údaje. Pomocí následujícího příkazu zobrazíte seznam předplatných Azure k dispozici pro použití:

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

Vytvořte skupinu prostředků obsahující službu IoT hub pomocí následujícího příkazu v jednom z podporovaných umístění pro službu IoT Hub. Tento příklad vytvoří skupinu prostředků s názvem **MyIoTRG1**:

```powershell
New-AzureRmResourceGroup -Name MyIoTRG1 -Location "East US"
```

## <a name="submit-a-template-to-create-an-iot-hub"></a>Odeslání šablony k vytvoření služby IoT hub

Pomocí šablony JSON pro vytvoření služby IoT hub ve vaší skupině prostředků. Můžete také použít šablony Azure Resource Manageru provádět změny existující služby IoT hub.

1. Pomocí textového editoru vytvořte šablonu Azure Resource Manageru volá **template.json** s následující definicí prostředku k vytvoření nového standardní centra IoT. V tomto příkladu přidá Centrum IoT **USA – východ** oblast, vytvoří dvě skupiny uživatelů (**cg1** a **cg2**) na koncový bod kompatibilní s centrem událostí a používá  **2016-02-03** verze rozhraní API. Tato šablona také očekává, že budete a zajistěte tak předání názvu centra IoT jako parametr názvem **hubName**. Aktuální seznam umístění, které podporují služby IoT Hub najdete v části [stav Azure][lnk-status].

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

2. Uložte soubor šablony Azure Resource Manageru na místním počítači. Tento příklad předpokládá, uložte ho do složky s názvem **c:\templates**.

3. Spusťte následující příkaz pro vaši novou službu IoT hub, předejte název služby IoT hub jako parametr nasazení. V tomto příkladu je název služby IoT hub `abcmyiothub`. Musí být globálně jedinečný název vašeho centra IoT:

    ```powershell
    New-AzureRmResourceGroupDeployment -ResourceGroupName MyIoTRG1 -TemplateFile C:\templates\template.json -hubName abcmyiothub
    ```
  [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

4. Ve výstupu nezobrazí klíče pro službu IoT hub, kterou jste vytvořili.

5. K ověření vaší aplikace přidat novou službu IoT hub, najdete [webu Azure portal] [ lnk-azure-portal] a zobrazit seznam prostředků. Můžete taky použít **Get-AzureRmResource** rutiny Powershellu.

> [!NOTE]
> Tato ukázková aplikace přidá S1 Standard služby IoT Hub pro kterou se vám účtuje. Můžete odstranit centrum IoT prostřednictvím [webu Azure portal] [ lnk-azure-portal] nebo s použitím **Remove-AzureRmResource** rutiny Powershellu, až budete hotovi.

## <a name="next-steps"></a>Další postup

Nyní jste nasadili IoT hubu pomocí šablony Azure Resource Manageru pomocí Powershellu, můžete dále zkoumat:

* Přečtěte si o možnostech [rozhraní REST API poskytovatele prostředků služby IoT Hub][lnk-rest-api].
* Čtení [přehled Azure Resource Manageru] [ lnk-azure-rm-overview] Další informace o možnostech Azure Resource Manageru.
* Syntaxi JSON a vlastnosti, které chcete použít v šablonách najdete v tématu [typy prostředků Microsoft.Devices](/azure/templates/microsoft.devices/iothub-allversions).

Další informace o vývoji pro službu IoT Hub, najdete v následujících článcích:

* [Seznámení s C SDK][lnk-c-sdk]
* [Sady Azure IoT SDK][lnk-sdks]

Podrobněji prozkoumat možnosti služby IoT Hub, najdete v tématech:

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
