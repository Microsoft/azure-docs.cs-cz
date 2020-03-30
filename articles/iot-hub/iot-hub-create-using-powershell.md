---
title: Vytvoření služby Azure IoT Hub pomocí rutiny Prostředí PowerShell | Dokumenty společnosti Microsoft
description: Zjistěte, jak pomocí rutin prostředí PowerShell vytvořit skupinu prostředků a potom vytvořit centrum IoT hub ve skupině prostředků. Přečtěte si také, jak centrum odebrat.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: robinsh
ms.openlocfilehash: 9c49f7ac744ee516aefc1571d50264132035ba8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73890601"
---
# <a name="create-an-iot-hub-using-the-new-aziothub-cmdlet"></a>Vytvoření centra IoT pomocí rutiny New-AzIotHub

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Úvod

K vytváření a správě center Azure IoT huby můžete použít rutiny Azure PowerShellu. Tento kurz ukazuje, jak vytvořit centrum IoT s PowerShellem.

K dokončení tohoto návodu potřebujete předplatné Azure. Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="connect-to-your-azure-subscription"></a>Připojení k předplatnému služby Azure

Pokud používáte cloudové prostředí, jste již přihlášeni k předplatnému. Pokud místo toho používáte PowerShell místně, zadejte následující příkaz pro přihlášení k předplatnému Azure:

```powershell
# Log into Azure account.
Login-AzAccount
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

K nasazení centra IoT potřebujete skupinu prostředků. Můžete použít existující skupinu prostředků nebo vytvořit novou.

Chcete-li vytvořit skupinu prostředků pro centrum IoT, použijte příkaz [New-AzResourceGroup.](https://docs.microsoft.com/powershell/module/az.Resources/New-azResourceGroup) Tento příklad vytvoří skupinu prostředků s názvem **MyIoTRG1** v oblasti **VÝCHODNÍ USA:**

```azurepowershell-interactive
New-AzResourceGroup -Name MyIoTRG1 -Location "East US"
```

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

Chcete-li vytvořit centrum IoT ve skupině prostředků, kterou jste vytvořili v předchozím kroku, použijte příkaz [New-AzIotHub.](https://docs.microsoft.com/powershell/module/az.IotHub/New-azIotHub) Tento příklad vytvoří centrum **S1** s názvem **MyTestIoTHub** v oblasti **USA – východ:**

```azurepowershell-interactive
New-AzIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub `
    -SkuName S1 -Units 1 `
    -Location "East US"
```

Název centra IoT musí být globálně jedinečný.

[!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

Můžete seznam všech center IoT ve vašem předplatném pomocí [příkazu Get-AzIotHub:](https://docs.microsoft.com/powershell/module/az.IotHub/Get-azIotHub)

```azurepowershell-interactive
Get-AzIotHub
```

Tento příklad ukazuje S1 Standard IoT Hub, který jste vytvořili v předchozím kroku.

Centrum IoT můžete odstranit pomocí příkazu [Remove-AzIotHub:](https://docs.microsoft.com/powershell/module/az.iothub/remove-aziothub)

```azurepowershell-interactive
Remove-AzIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub
```

Případně můžete odebrat skupinu prostředků a všechny prostředky, které obsahuje pomocí příkazu [Remove-AzResourceGroup:](https://docs.microsoft.com/powershell/module/az.Resources/Remove-azResourceGroup)

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyIoTRG1
```

## <a name="next-steps"></a>Další kroky

Teď jste nasadili centrum IoT pomocí rutiny prostředí PowerShell, pokud chcete dále prozkoumat, podívejte se na následující články:

* [Rutiny prostředí PowerShell pro práci s centrem IoT hub](https://docs.microsoft.com/powershell/module/az.iothub/).

* [Rozhraní REST API poskytovatele prostředků služby IoT Hub](https://docs.microsoft.com/rest/api/iothub/iothubresource).

Další informace o vývoji pro IoT Hub najdete v následujících článcích:

* [Úvod do sady C SDK](iot-hub-device-sdk-c-intro.md)

* [Sady Azure IoT SDK](iot-hub-devguide-sdks.md)

Další informace o možnostech IoT Hubu najdete v následujících tématech:

* [Nasazení AI do hraničních zařízení s použitím Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
