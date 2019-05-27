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
ms.openlocfilehash: fd376728a1ebdf769c7f2ae76d61a60703e13711
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "66146609"
---
# <a name="create-an-iot-hub-using-the-new-aziothub-cmdlet"></a>Vytvoření IoT hubu pomocí rutiny New-AzIotHub

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Úvod

Rutiny prostředí Azure PowerShell můžete použít k vytváření a správě služby Azure IoT hub. V tomto kurzu se dozvíte, jak vytvořit IoT hub s využitím Powershellu.

Pokud chcete dokončit tento návod, potřebujete předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="connect-to-your-azure-subscription"></a>Připojení k předplatnému služby Azure

Pokud používáte Cloud Shell, jste již přihlášeni ke svému předplatnému. Pokud používáte PowerShell místně místo toho, zadejte následující příkaz pro přihlášení ke svému předplatnému Azure:

```powershell
# Log into Azure account.
Login-AzAccount
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Potřebujete skupinu prostředků pro nasazení služby IoT hub. Můžete použít existující skupinu prostředků nebo vytvořte novou.

Chcete-li vytvořit skupinu prostředků pro službu IoT hub, použijte [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.Resources/New-azResourceGroup) příkazu. Tento příklad vytvoří skupinu prostředků s názvem **MyIoTRG1** v **USA – východ** oblasti:

```azurepowershell-interactive
New-AzResourceGroup -Name MyIoTRG1 -Location "East US"
```

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

K vytvoření služby IoT hub ve skupině prostředků, kterou jste vytvořili v předchozím kroku, použijte [New-AzIotHub](https://docs.microsoft.com/powershell/module/az.IotHub/New-azIotHub) příkazu. Tento příklad vytvoří **S1** centrum s názvem **MyTestIoTHub** v **USA – východ** oblasti:

```azurepowershell-interactive
New-AzIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub `
    -SkuName S1 -Units 1 `
    -Location "East US"
```

Název služby IoT hub, musí být globálně jedinečný.

[!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

IoT hub můžete vytvořit seznam v předplatném pomocí [Get-AzIotHub](https://docs.microsoft.com/powershell/module/az.IotHub/Get-azIotHub) příkaz:

```azurepowershell-interactive
Get-AzIotHub
```

Tento příklad ukazuje S1 Standard služby IoT Hub, že kterou jste vytvořili v předchozím kroku.

Můžete odstranit pomocí centra IoT [odebrat AzIotHub](https://docs.microsoft.com/powershell/module/az.iothub/remove-aziothub) příkaz:

```azurepowershell-interactive
Remove-AzIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub
```

Alternativně můžete odebrat skupinu prostředků a všechny prostředky obsahuje pomocí [odebrat AzResourceGroup](https://docs.microsoft.com/powershell/module/az.Resources/Remove-azResourceGroup) příkaz:

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyIoTRG1
```

## <a name="next-steps"></a>Další postup

Nyní jste nasadili službu IoT hub pomocí rutiny Powershellu, pokud chcete podrobněji, projděte si následující články:

* [Rutiny Powershellu pro práci se službou IoT hub](https://docs.microsoft.com/powershell/module/az.iothub/).

* [Rozhraní REST API poskytovatele prostředků služby IoT Hub](https://docs.microsoft.com/rest/api/iothub/iothubresource).

Další informace o vývoji pro službu IoT Hub, najdete v následujících článcích:

* [Seznámení s C SDK](iot-hub-device-sdk-c-intro.md)

* [Sady Azure IoT SDK](iot-hub-devguide-sdks.md)

Podrobněji prozkoumat možnosti služby IoT Hub, najdete v tématech:

* [Nasazení AI do hraničních zařízení pomocí služby Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
