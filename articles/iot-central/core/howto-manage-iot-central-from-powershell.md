---
title: Správa IoT Central z Azure PowerShellu | Dokumenty společnosti Microsoft
description: Tento článek popisuje, jak vytvořit a spravovat aplikace IoT Central z Azure PowerShellu.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 03/27/2020
ms.topic: how-to
manager: philmea
ms.openlocfilehash: 42d853c9cf53c1c6921fbd1816ec2298c9c3583e
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365533"
---
# <a name="manage-iot-central-from-azure-powershell"></a>Správa řešení IoT Central z Azure PowerShellu

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

Místo vytváření a správy aplikací IoT Central na webu [správce aplikací Azure IoT Central](https://aka.ms/iotcentral) můžete ke správě aplikací použít Azure [PowerShell.](https://docs.microsoft.com/powershell/azure/overview)

## <a name="prerequisites"></a>Požadavky

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud dáváte přednost spuštění Azure PowerShellu v místním počítači, přečtěte si informace [o instalaci modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps). Když spustíte Azure PowerShell místně, použijte **rutinu Connect-AzAccount** k přihlášení do Azure před vyzkoušení rutiny v tomto článku.

> [!TIP]
> Pokud potřebujete spustit příkazy PowerShellu v jiném předplatném Azure, [přečtěte si tématu Změna aktivního předplatného](/powershell/azure/manage-subscriptions-azureps?view=azps-3.4.0#change-the-active-subscription).

## <a name="install-the-iot-central-module"></a>Instalace modulu IoT Central

Spuštěním následujícího příkazu zkontrolujte, zda je [modul IoT Central](https://docs.microsoft.com/powershell/module/az.iotcentral/) nainstalovaný v prostředí PowerShellu:

```powershell
Get-InstalledModule -name Az.I*
```

Pokud seznam nainstalovaných modulů neobsahuje **Az.IotCentral**, spusťte následující příkaz:

```powershell
Install-Module Az.IotCentral
```

## <a name="create-an-application"></a>Vytvoření aplikace

Pomocí rutiny [New-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/New-AzIotCentralApp) vytvořte aplikaci IoT Central ve vašem předplatném Azure. Například:

```powershell
# Create a resource group for the IoT Central application
New-AzResourceGroup -ResourceGroupName "MyIoTCentralResourceGroup" `
  -Location "East US"
```

```powershell
# Create an IoT Central application
New-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
  -Name "myiotcentralapp" -Subdomain "mysubdomain" `
  -Sku "ST1" -Template "iotc-pnp-preview@1.0.0" `
  -DisplayName "My Custom Display Name"
```

Skript nejprve vytvoří skupinu prostředků v oblasti východní USA pro aplikaci. Následující tabulka popisuje parametry použité s příkazem **New-AzIotCentralApp:**

|Parametr         |Popis |
|------------------|------------|
|ResourceGroupName |Skupina prostředků, která obsahuje aplikaci. Tato skupina prostředků musí již existovat ve vašem předplatném. |
|Umístění |Ve výchozím nastavení tato rutina používá umístění ze skupiny prostředků. V současné době můžete vytvořit aplikaci IoT Central v **oblastech Zeměpisné**oblasti Austrálie , Asie **a Tichomoří**, **Evropa**, **Spojené státy**, **Velká Británie**a **Japonsko.** |
|Name (Název)              |Název aplikace na webu Azure Portal. |
|Subdoména         |Subdoména v adrese URL aplikace. V příkladu je `https://mysubdomain.azureiotcentral.com`adresa URL aplikace . |
|Skladová jednotka (SKU)               |V současné době můžete použít **buď ST1** nebo **ST2**. Viz [Azure IoT Central pricing](https://azure.microsoft.com/pricing/details/iot-central/). |
|Šablona          | Šablona aplikace, která má být používána. Další informace najdete v následující tabulce. |
|DisplayName       |Název aplikace zobrazený v uživatelském uživatelském nastavení. |

[!INCLUDE [iot-central-template-list](../../../includes/iot-central-template-list.md)]

## <a name="view-your-iot-central-applications"></a>Zobrazení aplikací IoT Central

Pomocí rutiny [Get-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/Get-AzIotCentralApp) můžete vypsat aplikace IoT Central a zobrazit metadata.

## <a name="modify-an-application"></a>Úprava aplikace

Pomocí rutiny [Set-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/set-aziotcentralapp) aktualizujte metadata aplikace IoT Central. Chcete-li například změnit zobrazovaný název aplikace:

```powershell
Set-AzIotCentralApp -Name "myiotcentralapp" `
  -ResourceGroupName "MyIoTCentralResourceGroup" `
  -DisplayName "My new display name"
```

## <a name="remove-an-application"></a>Odebrání aplikace

Pomocí rutiny [Remove-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/Remove-AzIotCentralApp) odstraňte aplikaci IoT Central. Například:

```powershell
Remove-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
 -Name "myiotcentralapp"
```

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak spravovat aplikace Azure IoT Central z Azure PowerShellu, tady je další doporučený krok:

> [!div class="nextstepaction"]
> [Správa vaší aplikace](howto-administer.md)
