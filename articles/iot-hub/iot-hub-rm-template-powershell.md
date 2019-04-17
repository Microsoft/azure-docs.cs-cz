---
title: Vytvoření centra IoT Azure pomocí šablony (PowerShell) | Dokumentace Microsoftu
description: Jak použít šablonu Azure Resource Manageru k vytvoření služby IoT Hub pomocí Azure Powershellu.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: d23d3824c477d3bba4e4900bee355376f1317f92
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2019
ms.locfileid: "59609165"
---
# <a name="create-an-iot-hub-using-azure-resource-manager-template-powershell"></a>Vytvoření IoT hubu pomocí šablony Azure Resource Manageru (PowerShell)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Zjistěte, jak použít šablonu Azure Resource Manageru k vytvoření služby IoT Hub a skupiny příjemců. Šablony Resource Manageru jsou soubory JSON, které definují, jaké prostředky je pro řešení potřeba nasadit. Další informace o vývoji šablon Resource Manageru najdete v tématu [dokumentace ke službě Azure Resource Manageru](https://docs.microsoft.com/azure/azure-resource-manager/).

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

Šablona Resource Manageru použitá v tomto rychlém startu je z [šablon rychlého startu Azure](https://azure.microsoft.com/resources/templates/101-iothub-with-consumergroup-create/). Tady je kopie šablony:

[!code-json[iothub-creation](~/quickstart-templates/101-iothub-with-consumergroup-create/azuredeploy.json)]

Šablona vytvoří službu Azure Iot hub pomocí tří koncových bodů (centra událostí, typu cloud zařízení a zasílání zpráv) a skupiny příjemců. Další ukázkové šablony, najdete v části [šablon rychlého startu Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Devices&pageNumber=1&sort=Popular). Schéma šablony služby Iot Hub najdete [tady](https://docs.microsoft.com/azure/templates/microsoft.devices/iothub-allversions).

Existuje několik metod pro nasazení šablony.  V tomto kurzu použijete Azure PowerShell.

Chcete-li spustit skript prostředí PowerShell, vyberte **vyzkoušet** a otevřete Azure Cloud shell. Vložte skript, klikněte pravým tlačítkem na prostředí a potom vyberte Vložit:

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

Jak je vidět z uvedeného skriptu Powershellu je šablona použitá z šablon rychlého startu Azure. Pokud chcete použít vlastní, budete muset nejprve do služby Cloud shell nahrát soubor šablony a potom pomocí `-TemplateFile` přepínač k zadání názvu souboru.  Příklad najdete v tématu [nasazení šablony](../azure-resource-manager/resource-manager-quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell#deploy-the-template).

## <a name="next-steps"></a>Další postup

Nyní jste nasadili Centrum IoT s použitím šablony Azure Resource Manageru, můžete dále zkoumat:

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
[lnk-powershell-install]: /powershell/azure/install-Az-ps
[lnk-rest-api]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-azure-rm-overview]: ../azure-resource-manager/resource-group-overview.md
[lnk-powershell-arm]: ../azure-resource-manager/manage-resources-powershell.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
