---
title: Vytvoření IoT Hub Azure pomocí šablony (PowerShell) | Microsoft Docs
description: Jak pomocí šablony Azure Resource Manager vytvořit IoT Hub pomocí Azure PowerShell.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: 1fedadfa7e5b4ec3d7de30d0ad3ef1b1bfa0e0ec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92144394"
---
# <a name="create-an-iot-hub-using-azure-resource-manager-template-powershell"></a>Vytvoření služby IoT Hub pomocí šablony Azure Resource Manager (PowerShell)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Naučte se používat šablonu Azure Resource Manager k vytvoření IoT Hub a skupiny uživatelů. Šablony Resource Manageru jsou soubory JSON, které definují, jaké prostředky je pro řešení potřeba nasadit. Další informace o vývoji šablon Správce prostředků najdete v [dokumentaci k Azure Resource Manager](../azure-resource-manager/index.yml).

Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

Šablona Správce prostředků použitá v tomto rychlém startu je ze [šablon Azure pro rychlý Start](https://azure.microsoft.com/resources/templates/101-iothub-with-consumergroup-create/). Tady je kopie šablony:

[!code-json[iothub-creation](~/quickstart-templates/101-iothub-with-consumergroup-create/azuredeploy.json)]

Šablona vytvoří službu Azure IoT Hub se třemi koncovými body (eventhub, Cloud-zařízení a zasílání zpráv) a skupinou uživatelů. Další ukázky šablon najdete v tématu [šablony rychlý Start pro Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Devices&pageNumber=1&sort=Popular). Schéma šablon IoT Hub najdete  [tady](/azure/templates/microsoft.devices/iothub-allversions).

Existuje několik metod pro nasazení šablony.  V tomto kurzu použijete Azure PowerShell.

Pokud chcete spustit skript PowerShellu, vyberte **vyzkoušet** a otevřete službu Azure Cloud Shell. Skript vložíte tak, že kliknete pravým tlačítkem na prostředí a pak vyberete vložit:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$iotHubName = Read-Host -Prompt "Enter the IoT Hub name"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-iothub-with-consumergroup-create/azuredeploy.json" `
    -iotHubName $iotHubName
```

Jak vidíte z PowerShellového skriptu, šablona se používá ze šablon Azure pro rychlý Start. Pokud chcete použít vlastní, musíte nejdřív nahrát soubor šablony do Cloud shellu a potom pomocí `-TemplateFile` přepínače zadat název souboru.  Příklad najdete v tématu [nasazení šablony](../azure-resource-manager/templates/quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell#deploy-the-template).

## <a name="next-steps"></a>Další kroky

Nyní jste nasadili IoT Hub pomocí šablony Azure Resource Manager, možná budete chtít prozkoumat další:

* Přečtěte si o možnostech [REST API IoT Hub poskytovatele prostředků][lnk-rest-api].
* Přečtěte si [Azure Resource Manager přehled][lnk-azure-rm-overview] , kde najdete další informace o možnostech Azure Resource Manager.
* Informace o syntaxi a vlastnostech JSON pro použití v šablonách najdete v tématu [typy prostředků Microsoft. Devices](/azure/templates/microsoft.devices/iothub-allversions).

Další informace o vývoji pro IoT Hub najdete v následujících článcích:

* [Seznámení se sadou C SDK][lnk-c-sdk]
* [Sady Azure IoT SDK][lnk-sdks]

Chcete-li dále prozkoumat možnosti IoT Hub, přečtěte si:

* [Nasazení AI do hraničních zařízení s použitím Azure IoT Edge][lnk-iotedge]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: /powershell/azure/install-Az-ps
[lnk-rest-api]: /rest/api/iothub/iothubresource
[lnk-azure-rm-overview]: ../azure-resource-manager/management/overview.md
[lnk-powershell-arm]: ../azure-resource-manager/management/manage-resources-powershell.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/quickstart-linux.md