---
title: Vytvoření IoT Hub Azure pomocí rutiny prostředí PowerShell | Microsoft Docs
description: Naučte se, jak pomocí rutin PowerShellu vytvořit skupinu prostředků a pak ve skupině prostředků vytvořit centrum IoT. Také se dozvíte, jak odebrat centrum.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: robinsh
ms.openlocfilehash: da021e3ba0fd93a182ea76a1ba4b7042b325aacc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92142373"
---
# <a name="create-an-iot-hub-using-the-new-aziothub-cmdlet"></a>Vytvoření centra IoT pomocí rutiny New-AzIotHub

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Úvod

Pomocí rutin Azure PowerShell můžete vytvářet a spravovat centra IoT Azure. V tomto kurzu se dozvíte, jak vytvořit centrum IoT pomocí PowerShellu.

K dokončení tohoto postupu potřebujete předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="connect-to-your-azure-subscription"></a>Připojení k předplatnému služby Azure

Pokud používáte Cloud Shell, už jste přihlášeni k vašemu předplatnému. Pokud místo toho používáte PowerShell místně, zadejte následující příkaz pro přihlášení k předplatnému Azure:

```powershell
# Log into Azure account.
Login-AzAccount
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

K nasazení služby IoT Hub potřebujete skupinu prostředků. Můžete použít stávající skupinu prostředků nebo vytvořit novou skupinu.

Pokud chcete vytvořit skupinu prostředků pro Centrum IoT, použijte příkaz [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup) . Tento příklad vytvoří skupinu prostředků s názvem **MyIoTRG1** v oblasti **východní USA** :

```azurepowershell-interactive
New-AzResourceGroup -Name MyIoTRG1 -Location "East US"
```

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

Pokud chcete vytvořit centrum IoT ve skupině prostředků, kterou jste vytvořili v předchozím kroku, použijte příkaz [New-AzIotHub](/powershell/module/az.IotHub/New-azIotHub) . Tento příklad vytvoří centrum **S1** s názvem **MyTestIoTHub** v oblasti **východní USA** :

```azurepowershell-interactive
New-AzIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub `
    -SkuName S1 -Units 1 `
    -Location "East US"
```

Název centra IoT musí být globálně jedinečný.

[!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

Pomocí příkazu [Get-AzIotHub](/powershell/module/az.IotHub/Get-azIotHub) můžete v předplatném vypsat všechna centra IoT.

```azurepowershell-interactive
Get-AzIotHub
```

Tento příklad ukazuje standardní IoT Hub S1, které jste vytvořili v předchozím kroku.

Centrum IoT můžete odstranit pomocí příkazu [Remove-AzIotHub](/powershell/module/az.iothub/remove-aziothub) :

```azurepowershell-interactive
Remove-AzIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub
```

Případně můžete odebrat skupinu prostředků a všechny prostředky, které obsahuje, pomocí příkazu [Remove-AzResourceGroup](/powershell/module/az.Resources/Remove-azResourceGroup) :

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyIoTRG1
```

## <a name="next-steps"></a>Další kroky

Nyní jste nasadili IoT Hub pomocí rutiny prostředí PowerShell, pokud chcete prozkoumat další postup, Projděte si následující články:

* [Rutiny PowerShellu pro práci se službou IoT Hub](/powershell/module/az.iothub/).

* [REST API poskytovatele prostředků IoT Hub](/rest/api/iothub/iothubresource).

Další informace o vývoji pro IoT Hub najdete v následujících článcích:

* [Seznámení se sadou C SDK](iot-hub-device-sdk-c-intro.md)

* [Sady Azure IoT SDK](iot-hub-devguide-sdks.md)

Chcete-li dále prozkoumat možnosti IoT Hub, přečtěte si:

* [Nasazení AI do hraničních zařízení s použitím Azure IoT Edge](../iot-edge/quickstart-linux.md)