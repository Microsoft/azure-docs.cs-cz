---
title: Spravovat IoT Central z Azure PowerShell | Microsoft Docs
description: Tento článek popisuje, jak vytvořit a spravovat vaše aplikace IoT Central z Azure PowerShell.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 03/27/2020
ms.topic: how-to
ms.custom: devx-track-azurepowershell
manager: philmea
ms.openlocfilehash: a870b72d1dda04ab29dbb5f056873d47f888b837
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2021
ms.locfileid: "102501358"
---
# <a name="manage-iot-central-from-azure-powershell"></a>Správa řešení IoT Central z Azure PowerShellu

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

Místo vytváření a správy aplikací IoT Central na webu [Azure IoT Central Správce aplikací](https://aka.ms/iotcentral) můžete ke správě aplikací použít [Azure PowerShell](/powershell/azure/) .

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Warning About Access Required](../../../includes/iot-central-warning-contribitorrequireaccess.md)]

Pokud dáváte přednost spuštění Azure PowerShell na místním počítači, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Když spustíte Azure PowerShell místně, přihlaste se k Azure pomocí rutiny **Connect-AzAccount** , než se pokusíte rutiny v tomto článku vyzkoušet.

> [!TIP]
> Pokud potřebujete spustit příkazy PowerShellu v jiném předplatném Azure, přečtěte si téma [Změna aktivního předplatného](/powershell/azure/manage-subscriptions-azureps#change-the-active-subscription).

## <a name="install-the-iot-central-module"></a>Instalace modulu IoT Central

Spuštěním následujícího příkazu ověřte, že je v prostředí PowerShellu nainstalovaný [modul IoT Central](/powershell/module/az.iotcentral/) :

```powershell
Get-InstalledModule -name Az.I*
```

Pokud seznam nainstalovaných modulů neobsahuje **AZ. IotCentral**, spusťte následující příkaz:

```powershell
Install-Module Az.IotCentral
```

## <a name="create-an-application"></a>Vytvoření aplikace

Pomocí rutiny [New-AzIotCentralApp](/powershell/module/az.iotcentral/New-AzIotCentralApp) vytvořte aplikaci IoT Central ve vašem předplatném Azure. Například:

```powershell
# Create a resource group for the IoT Central application
New-AzResourceGroup -ResourceGroupName "MyIoTCentralResourceGroup" `
  -Location "East US"
```

```powershell
# Create an IoT Central application
New-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
  -Name "myiotcentralapp" -Subdomain "mysubdomain" `
  -Sku "ST1" -Template "iotc-pnp-preview" `
  -DisplayName "My Custom Display Name"
```

Skript nejprve vytvoří skupinu prostředků v oblasti východní USA pro aplikaci. Následující tabulka popisuje parametry, které se používají v příkazu **New-AzIotCentralApp** :

|Parametr         |Popis |
|------------------|------------|
|ResourceGroupName |Skupina prostředků, která obsahuje aplikaci. Tato skupina prostředků už musí existovat ve vašem předplatném. |
|Umístění |Ve výchozím nastavení používá tato rutina umístění ze skupiny prostředků. V současné době můžete vytvořit aplikaci IoT Central v oblastech **Austrálie**, **Asie a Tichomoří**, **Evropa**, **USA**, **Spojené království** a **Japonsko** . |
|Name              |Název aplikace v Azure Portal. |
|Subdoména         |Subdoména v adrese URL aplikace V tomto příkladu je adresa URL aplikace `https://mysubdomain.azureiotcentral.com` . |
|Skladová jednotka (SKU)               |V současné době můžete použít buď **ST1** nebo **ST2**. Viz [ceny za Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/). |
|Template (Šablona)          | Šablona aplikace, která se má použít Další informace najdete v následující tabulce. |
|DisplayName       |Název aplikace, jak se zobrazuje v uživatelském rozhraní. |

[!INCLUDE [iot-central-template-list](../../../includes/iot-central-template-list.md)]

## <a name="view-your-iot-central-applications"></a>Zobrazení aplikací IoT Central

Pomocí rutiny [Get-AzIotCentralApp](/powershell/module/az.iotcentral/Get-AzIotCentralApp) můžete zobrazit seznam aplikací IoT Central a zobrazovat metadata.

## <a name="modify-an-application"></a>Úprava aplikace

Pomocí rutiny [set-AzIotCentralApp](/powershell/module/az.iotcentral/set-aziotcentralapp) aktualizujte metadata aplikace IoT Central. Například pro změnu zobrazovaného názvu vaší aplikace:

```powershell
Set-AzIotCentralApp -Name "myiotcentralapp" `
  -ResourceGroupName "MyIoTCentralResourceGroup" `
  -DisplayName "My new display name"
```

## <a name="remove-an-application"></a>Odebrání aplikace

K odstranění aplikace IoT Central použijte rutinu [Remove-AzIotCentralApp](/powershell/module/az.iotcentral/Remove-AzIotCentralApp) . Například:

```powershell
Remove-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
 -Name "myiotcentralapp"
```

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak spravovat aplikace Azure IoT Central z Azure PowerShell, je tady doporučený další krok:

> [!div class="nextstepaction"]
> [Správa vaší aplikace](howto-administer.md)