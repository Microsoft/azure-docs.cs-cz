---
title: Vytvoření služby Azure IoT Hub pomocí šablony (PowerShell) | Dokumenty společnosti Microsoft
description: Jak použít šablonu Azure Resource Manager k vytvoření ioT hubu s Azure PowerShellem.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: a1f878361a96c5584f43b31135d79ff799f66efa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75976622"
---
# <a name="create-an-iot-hub-using-azure-resource-manager-template-powershell"></a>Vytvoření centra IoT pomocí šablony Azure Resource Manager (PowerShell)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Zjistěte, jak pomocí šablony Azure Resource Manager vytvořit centrum IoT Hub a skupinu spotřebitelů. Šablony Resource Manageru jsou soubory JSON, které definují, jaké prostředky je pro řešení potřeba nasadit. Další informace o vývoji šablon Správce prostředků najdete v [dokumentaci ke Správci prostředků Azure](https://docs.microsoft.com/azure/azure-resource-manager/).

Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

Šablona Správce prostředků použitá v tomto rychlém startu je ze [šablon Azure QuickStart](https://azure.microsoft.com/resources/templates/101-iothub-with-consumergroup-create/). Zde je kopie šablony:

[!code-json[iothub-creation](~/quickstart-templates/101-iothub-with-consumergroup-create/azuredeploy.json)]

Šablona vytvoří azure iot hub se třemi koncovými body (eventhub, cloud-to-device a zasílání zpráv) a skupinou spotřebitelů. Další ukázky šablon najdete [v tématu Šablony Azure Quickstart](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Devices&pageNumber=1&sort=Popular). Schéma šablony aplikace Iot Hub naleznete [zde](https://docs.microsoft.com/azure/templates/microsoft.devices/iothub-allversions).

Existuje několik metod pro nasazení šablony.  V tomto kurzu se používá Azure PowerShell.

Chcete-li spustit skript PowerShellu, vyberte **Try to** to open the Azure Cloud shell. Pokud chcete skript vložit, klikněte pravým tlačítkem myši na prostředí a pak vyberte Vložit:

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

Jak můžete vidět ze skriptu PowerShellu, použitá šablona je ze šablon Azure Quickstart. Chcete-li použít vlastní, musíte nejprve nahrát soubor šablony do `-TemplateFile` prostředí Cloud a pak pomocí přepínače zadat název souboru.  Příklad například v [tématu Nasazení šablony](../azure-resource-manager/templates/quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell#deploy-the-template).

## <a name="next-steps"></a>Další kroky

Teď jste nasadili centrum IoT pomocí šablony Azure Resource Manager, možná budete chtít prozkoumat další:

* Přečtěte si o možnostech [rozhraní REST API poskytovatele prostředků služby IoT Hub][lnk-rest-api].
* Další informace o možnostech Azure Resource Manageru najdete v [přehledu][lnk-azure-rm-overview] správce prostředků Azure.
* Syntaxe json a vlastnosti pro použití v šablonách naleznete v tématu [Microsoft.Devices typy prostředků](/azure/templates/microsoft.devices/iothub-allversions).

Další informace o vývoji pro IoT Hub najdete v následujících článcích:

* [Úvod do sady C SDK][lnk-c-sdk]
* [Sady Azure IoT SDK][lnk-sdks]

Další informace o možnostech IoT Hubu najdete v následujících tématech:

* [Nasazení AI do hraničních zařízení s použitím Azure IoT Edge][lnk-iotedge]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: /powershell/azure/install-Az-ps
[lnk-rest-api]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-azure-rm-overview]: ../azure-resource-manager/management/overview.md
[lnk-powershell-arm]: ../azure-resource-manager/management/manage-resources-powershell.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
