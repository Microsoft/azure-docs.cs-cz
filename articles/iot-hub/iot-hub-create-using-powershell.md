---
title: Vytvořit službu Azure IoT Hub pomocí rutiny prostředí PowerShell | Dokumentace Microsoftu
description: Jak používat rutiny prostředí PowerShell k vytvoření služby IoT hub.
author: robinsh
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: robinsh
ms.openlocfilehash: 7ecd35ba33d2860ba052aa27286c69985c2f7dd9
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/29/2018
ms.locfileid: "43190226"
---
# <a name="create-an-iot-hub-using-the-new-azurermiothub-cmdlet"></a>Vytvoření IoT hubu pomocí rutiny New-AzureRmIotHub

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Úvod

Rutiny prostředí Azure PowerShell můžete použít k vytváření a správě služby Azure IoT hub. V tomto kurzu se dozvíte, jak vytvořit IoT hub s využitím Powershellu.

Pokud chcete dokončit tento návod, potřebujete předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="connect-to-your-azure-subscription"></a>Připojení k předplatnému služby Azure

Pokud používáte Cloud Shell, jste již přihlášeni ke svému předplatnému. Pokud používáte PowerShell místně místo toho, zadejte následující příkaz pro přihlášení ke svému předplatnému Azure:

```powershell
# Log into Azure account.
Login-AzureRMAccount
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Potřebujete skupinu prostředků pro nasazení služby IoT hub. Můžete použít existující skupinu prostředků nebo vytvořte novou.

Chcete-li vytvořit skupinu prostředků pro službu IoT hub, použijte [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/AzureRM.Resources/New-AzureRmResourceGroup) příkazu. Tento příklad vytvoří skupinu prostředků s názvem **MyIoTRG1** v **USA – východ** oblasti:

```azurepowershell-interactive
New-AzureRmResourceGroup -Name MyIoTRG1 -Location "East US"
```

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

K vytvoření služby IoT hub ve skupině prostředků, kterou jste vytvořili v předchozím kroku, použijte [New-AzureRmIotHub](https://docs.microsoft.com/powershell/module/AzureRM.IotHub/New-AzureRmIotHub) příkazu. Tento příklad vytvoří **S1** centrum s názvem **MyTestIoTHub** v **USA – východ** oblasti:

```azurepowershell-interactive
New-AzureRmIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub `
    -SkuName S1 -Units 1 `
    -Location "East US"
```

Název služby IoT hub, musí být globálně jedinečný.

[!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

IoT hub můžete vytvořit seznam v předplatném pomocí [Get-AzureRmIotHub](https://docs.microsoft.com/powershell/module/AzureRM.IotHub/Get-AzureRmIotHub) příkaz:

```azurepowershell-interactive
Get-AzureRmIotHub
```

Tento příklad ukazuje S1 Standard služby IoT Hub, že kterou jste vytvořili v předchozím kroku.

Můžete odstranit pomocí centra IoT [odebrat AzureRmIotHub](https://docs.microsoft.com/powershell/module/azurerm.iothub/remove-azurermiothub) příkaz:

```azurepowershell-interactive
Remove-AzureRmIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub
```

Alternativně můžete odebrat skupinu prostředků a všechny prostředky obsahuje pomocí [Remove-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/AzureRM.Resources/Remove-AzureRmResourceGroup) příkaz:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name MyIoTRG1
```

## <a name="next-steps"></a>Další postup

Nyní jste nasadili službu IoT hub pomocí rutiny Powershellu, pokud chcete podrobněji, projděte si následující články:

* [Rutiny Powershellu pro práci se službou IoT hub](https://docs.microsoft.com/powershell/module/azurerm.iothub/).

* [Rozhraní REST API poskytovatele prostředků služby IoT Hub](https://docs.microsoft.com/rest/api/iothub/iothubresource).

Další informace o vývoji pro službu IoT Hub, najdete v následujících článcích:

* [Seznámení s C SDK](iot-hub-device-sdk-c-intro.md)

* [Sady Azure IoT SDK](iot-hub-devguide-sdks.md)

Podrobněji prozkoumat možnosti služby IoT Hub, najdete v tématech:

* [Nasazení AI do hraničních zařízení pomocí služby Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)