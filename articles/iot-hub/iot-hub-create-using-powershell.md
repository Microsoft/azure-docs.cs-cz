---
title: Vytvoření služby Azure IoT Hub pomocí rutiny prostředí PowerShell | Microsoft Docs
description: Jak používat rutiny prostředí PowerShell k vytvoření služby IoT hub.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: 78cf7844223b660eef3dea0a32cd7c325e88e083
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34634042"
---
# <a name="create-an-iot-hub-using-the-new-azurermiothub-cmdlet"></a>Vytvoření služby IoT hub pomocí rutiny New-AzureRmIotHub

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Úvod

Rutiny prostředí Azure PowerShell můžete použít k vytváření a správě Azure IoT hubs. V tomto kurzu se dozvíte, jak vytvoření služby IoT hub pomocí prostředí PowerShell.

> [!NOTE]
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Azure Resource Manager a klasický](../azure-resource-manager/resource-manager-deployment-model.md). Tento článek se zabývá pomocí modelu nasazení Azure Resource Manager.

Pro absolvování tohoto kurzu potřebujete:

* Aktivní účet Azure. <br/>Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet][lnk-free-trial].
* [Rutiny Azure PowerShell][lnk-powershell-install].

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

## <a name="create-resource-group"></a>Vytvoření skupiny prostředků

Potřebujete skupinu prostředků pro nasazení služby IoT hub. Můžete použít existující skupinu prostředků nebo vytvořte novou.

Tento příkaz můžete vyhledat umístění, kde můžete nasadit služby IoT hub:

```powershell
((Get-AzureRmResourceProvider `
  -ProviderNamespace Microsoft.Devices).ResourceTypes `
  | Where-Object ResourceTypeName -eq IoTHubs).Locations
```

Pokud chcete vytvořit skupinu prostředků pro službu IoT hub v jednom z podporovaných umístění pro IoT Hub, použijte následující příkaz. Tento příklad vytvoří skupinu prostředků s názvem **MyIoTRG1** v **východní USA** oblasti:

```powershell
New-AzureRmResourceGroup -Name MyIoTRG1 -Location "East US"
```

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

Chcete-li vytvoření služby IoT hub ve skupině prostředků, kterou jste vytvořili v předchozím kroku, použijte následující příkaz. Tento příklad vytvoří **S1** rozbočovače názvem **MyTestIoTHub** v **východní USA** oblasti:

```powershell
New-AzureRmIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub `
    -SkuName S1 -Units 1 `
    -Location "East US"
```

Název služby IoT hub musí být jedinečný.

[!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]


Všechny služby IoT hubs je možné uvést v rámci vašeho předplatného, pomocí následujícího příkazu:

```powershell
Get-AzureRmIotHub
```

Předchozí příklad přidá S1 Standard IoT Hub pro kterou se účtují. Odstraněním služby IoT hub pomocí následujícího příkazu:

```powershell
Remove-AzureRmIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub
```

Alternativně můžete odebrat skupinu prostředků a všechny prostředky, které obsahuje pomocí následujícího příkazu:

```powershell
Remove-AzureRmResourceGroup -Name MyIoTRG1
```

## <a name="next-steps"></a>Další postup

Nyní jste nasadili služby IoT hub pomocí rutiny prostředí PowerShell, můžete chtít Další:

* Zjišťování dalších [rutiny prostředí PowerShell pro práci se službou IoT hub][lnk-iothub-cmdlets].
* Přečtěte si informace o možnostech [zprostředkovatele prostředků služby IoT Hub REST API][lnk-rest-api].

Další informace o vývoji pro Centrum IoT, naleznete v následujících článcích:

* [Úvod do jazyka C SDK][lnk-c-sdk]
* [Sady SDK služby Azure IoT][lnk-sdks]

Pokud chcete prozkoumat další možnosti IoT Hub, najdete v části:

* [Nasazení AI do hraničních zařízení s použitím Azure IoT Edge][lnk-iotedge]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-powershell-install]: https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[lnk-iothub-cmdlets]: https://docs.microsoft.com/powershell/module/azurerm.iothub/
[lnk-rest-api]: https://docs.microsoft.com/rest/api/iothub/iothubresource

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
