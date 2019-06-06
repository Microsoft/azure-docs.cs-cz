---
title: Správa IoT Central z prostředí Azure PowerShell | Dokumentace Microsoftu
description: IoT Central spravujte z prostředí Azure PowerShell.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 01/14/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 086c7d303fd199090de3be77b2456c4ebcd053a8
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/06/2019
ms.locfileid: "66726935"
---
# <a name="manage-iot-central-from-azure-powershell"></a>Správa řešení IoT Central z Azure PowerShellu

[!INCLUDE [iot-central-selector-manage](../../includes/iot-central-selector-manage.md)]

Místo vytváření a správu aplikací IoT Central z IoT Central [Správce aplikací](https://aka.ms/iotcentral) stránku, použijte [prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) ke správě vašich aplikací.

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud chcete spustit prostředí Azure PowerShell na místním počítači, přečtěte si téma [instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps). Při spuštění prostředí Azure PowerShell místně, použijte **připojit AzAccount** rutiny pro přihlášení k Azure, než se pokusíte rutiny v tomto článku.

## <a name="install-the-iot-central-module"></a>Instalace modulu IoT Central

Spusťte následující příkaz a zkontrolujte [modulu IoT Central](https://docs.microsoft.com/powershell/module/az.iotcentral/) je nainstalovaný ve vašem prostředí PowerShell:

```powershell
Get-InstalledModule -name Az.I*
```

Pokud se seznam nainstalovaných modulů neobsahuje **Az.IotCentral**, spusťte následující příkaz:

```powershell
Install-Module Az.IotCentral
```

## <a name="create-an-application"></a>Vytvoření aplikace

Použití [New-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/New-AzIotCentralApp) rutina pro vytvoření aplikace IoT Central ve vašem předplatném Azure. Příklad:

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

Skript nejprve vytvoří skupinu prostředků na východě USA oblast pro aplikaci. Následující tabulka popisuje parametry používané s **New-AzIotCentralApp** příkaz:

|Parametr         |Popis |
|------------------|------------|
|ResourceGroupName |Skupina prostředků, která obsahuje aplikaci. Tato skupina prostředků musí existovat ve vašem předplatném. |
|Location |Tato rutina se používá ve výchozím umístění, ze skupiny prostředků. V současné době můžete vytvořit aplikace IoT Central **USA – východ**, **USA – západ**, **Severní Evropa**, nebo **západní Evropa** oblastech. |
|Name              |Název aplikace na webu Azure Portal. |
|Subdoména         |Subdoména v adrese URL aplikace. V tomto příkladu je adresa URL aplikace https://mysubdomain.azureiotcentral.com. |
|Skladová jednotka (SKU)               |V současné době je jediná hodnota **S1** (úrovně standard). Zobrazit [ceny Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/). |
|Šablona          | Šablony aplikace používat. Další informace najdete v tématu v následující tabulce: |
|displayName       |Název aplikace, jak se zobrazuje v uživatelském rozhraní. |

**Šablony aplikací**

|Název šablony  |Popis |
|---------------|------------|
|iotc-default@1.0.0 |Vytvoří prázdnou aplikaci, kterou můžete naplnit vlastními šablonami zařízení a zařízeními. |
|iotc-demo@1.0.0    |Vytvoří aplikaci, která zahrnuje již vytvořenou šablonu zařízení pro chladící prodejní automat. Pomocí této šablony můžete začít zkoumat Azure IoT Central. |
|iotc-devkit-sample@1.0.0 |Vytvoří aplikaci s připravenými šablonami zařízení pro připojení zařízení MXChip nebo Raspberry Pi. Tuto šablonu použijte, pokud jste vývojář zařízení experimentovat s některou z těchto zařízení. |

## <a name="view-your-iot-central-applications"></a>Zobrazit aplikace IoT Central

Použití [Get-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/Get-AzIotCentralApp) rutina pro zobrazení seznamu aplikací IoT Central a zobrazení metadat.

## <a name="modify-an-application"></a>Upravit aplikace

Použití [Set-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/set-aziotcentralapp) rutiny pro aktualizaci metadat aplikace IoT Central. Chcete-li například změnit zobrazovaný název aplikace:

```powershell
Set-AzIotCentralApp -Name "myiotcentralapp" `
  -ResourceGroupName "MyIoTCentralResourceGroup" `
  -DisplayName "My new display name"
```

## <a name="remove-an-application"></a>Odebrání aplikace

Použití [odebrat AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/Remove-AzIotCentralApp) rutina pro odstranění aplikace IoT Central. Příklad:

```powershell
Remove-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
 -Name "myiotcentralapp"
```

## <a name="next-steps"></a>Další postup

Teď, když jste zjistili, jak spravovat Azure IoT Central aplikace z prostředí Azure PowerShell, tady je navrhované další krok:

> [!div class="nextstepaction"]
> [Spravovat aplikaci](howto-administer.md)
