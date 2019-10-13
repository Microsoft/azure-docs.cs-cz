---
title: Spravovat IoT Central z Azure PowerShell | Microsoft Docs
description: Umožňuje spravovat IoT Central z Azure PowerShell.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 07/11/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: b31470b329c0f63d186e5babfac3c0d8e6767538
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2019
ms.locfileid: "72286229"
---
# <a name="manage-iot-central-from-azure-powershell"></a>Správa řešení IoT Central z Azure PowerShellu

[!INCLUDE [iot-central-selector-manage](../../includes/iot-central-selector-manage.md)]

Místo vytváření a správy aplikací IoT Central na webu [Azure IoT Central Správce aplikací](https://aka.ms/iotcentral) můžete ke správě aplikací použít [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) .

## <a name="prerequisites"></a>Předpoklady

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud dáváte přednost spuštění Azure PowerShell na místním počítači, přečtěte si téma [Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps). Když spustíte Azure PowerShell místně, přihlaste se k Azure pomocí rutiny **Connect-AzAccount** , než se pokusíte rutiny v tomto článku vyzkoušet.

## <a name="install-the-iot-central-module"></a>Instalace modulu IoT Central

Spuštěním následujícího příkazu ověřte, že je v prostředí PowerShellu nainstalovaný [modul IoT Central](https://docs.microsoft.com/powershell/module/az.iotcentral/) :

```powershell
Get-InstalledModule -name Az.I*
```

Pokud seznam nainstalovaných modulů neobsahuje **AZ. IotCentral**, spusťte následující příkaz:

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
  -Sku "S1" -Template "iotc-demo@1.0.0" `
  -DisplayName "My Custom Display Name"
```

Skript nejprve vytvoří skupinu prostředků v oblasti východní USA pro aplikaci. Následující tabulka popisuje parametry, které se používají v příkazu **New-AzIotCentralApp** :

|Parametr         |Popis |
|------------------|------------|
|ResourceGroupName |Skupina prostředků, která obsahuje aplikaci. Tato skupina prostředků už musí existovat ve vašem předplatném. |
|Umístění |Ve výchozím nastavení používá tato rutina umístění ze skupiny prostředků. V současné době můžete vytvořit aplikaci IoT Central v oblastech **východní USA**, **západní USA**, **Severní Evropa**nebo **západní Evropa** nebo v zeměpisných oblastech **Austrálie** a **Asie a Tichomoří** .  |
|Name (Název)              |Název aplikace v Azure Portal. |
|Subdomény         |Subdoména v adrese URL aplikace V tomto příkladu je adresa URL aplikace https://mysubdomain.azureiotcentral.com. |
|SKU               |V současné době jediná hodnota je **S1** (úroveň Standard). Viz [ceny za Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/). |
|Šablona          | Šablona aplikace, která se má použít Další informace najdete v následující tabulce: |
|DisplayName       |Název aplikace, jak se zobrazuje v uživatelském rozhraní. |

**Šablony aplikací**

|Název šablony  |Popis |
|---------------|------------|
|iotc-default@1.0.0 |Vytvoří prázdnou aplikaci, kterou můžete naplnit vlastními šablonami zařízení a zařízeními. |
|iotc-demo@1.0.0    |Vytvoří aplikaci, která zahrnuje již vytvořenou šablonu zařízení pro chladící prodejní automat. Pomocí této šablony můžete začít zkoumat Azure IoT Central. |
|iotc-devkit-sample@1.0.0 |Vytvoří aplikaci s připravenými šablonami zařízení pro připojení zařízení MXChip nebo Raspberry Pi. Tuto šablonu použijte, pokud jste vývojář zařízení experimentovat s některým z těchto zařízení. |

> [!NOTE]
> Šablona **aplikace Preview** je aktuálně dostupná pouze v oblastech **Severní Evropa** a **střed USA** .

## <a name="view-your-iot-central-applications"></a>Zobrazení aplikací IoT Central

Pomocí rutiny [Get-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/Get-AzIotCentralApp) můžete zobrazit seznam aplikací IoT Central a zobrazovat metadata.

## <a name="modify-an-application"></a>Úprava aplikace

Pomocí rutiny [set-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/set-aziotcentralapp) aktualizujte metadata aplikace IoT Central. Například pro změnu zobrazovaného názvu vaší aplikace:

```powershell
Set-AzIotCentralApp -Name "myiotcentralapp" `
  -ResourceGroupName "MyIoTCentralResourceGroup" `
  -DisplayName "My new display name"
```

## <a name="remove-an-application"></a>Odebrání aplikace

K odstranění aplikace IoT Central použijte rutinu [Remove-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/Remove-AzIotCentralApp) . Například:

```powershell
Remove-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
 -Name "myiotcentralapp"
```

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak spravovat aplikace Azure IoT Central z Azure PowerShell, je tady doporučený další krok:

> [!div class="nextstepaction"]
> [Správa aplikace](howto-administer.md)
