---
title: Spravovat IoT Central z Azure PowerShell | Microsoft Docs
description: Tento článek popisuje, jak vytvořit a spravovat vaše aplikace IoT Central z Azure PowerShell.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 07/11/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: c731dae02e9013fc436d6f30d8c8b2ab384968a0
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453997"
---
# <a name="manage-iot-central-from-azure-powershell"></a>Správa řešení IoT Central z Azure PowerShellu

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

Místo vytváření a správy aplikací IoT Central na webu [Azure IoT Central Správce aplikací](https://aka.ms/iotcentral) můžete ke správě aplikací použít [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) .

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

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

Pomocí rutiny [New-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/New-AzIotCentralApp) vytvořte aplikaci IoT Central ve vašem předplatném Azure. Příklad:

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

Skript nejprve vytvoří skupinu prostředků v umístění východní USA pro danou aplikaci. Následující tabulka popisuje parametry, které se používají v příkazu **New-AzIotCentralApp** :

|Parametr         |Popis |
|------------------|------------|
|ResourceGroupName |Skupina prostředků, která obsahuje aplikaci. Tato skupina prostředků už musí existovat ve vašem předplatném. |
|Umístění |Ve výchozím nastavení používá tato rutina umístění ze skupiny prostředků. V současné době můžete vytvořit aplikaci IoT Central v **USA**, **Austrálii**, **Asie a Tichomoří**nebo v **Evropě** .  |
|Name (Název)              |Název aplikace v Azure Portal. |
|Poddoména         |Subdoména v adrese URL aplikace V příkladu je adresa URL aplikace https://mysubdomain.azureiotcentral.com. |
|SKU               |V současné době jediná hodnota je **S1** (úroveň Standard). Viz [ceny za Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/). |
|Šablona          | Šablona aplikace, která se má použít Další informace najdete v následující tabulce: |
|DisplayName       |Název aplikace, jak se zobrazuje v uživatelském rozhraní. |

**Šablona aplikace s všeobecně dostupnými funkcemi**

| Název šablony            | Popis |
| ------------------------ | ----------- |
| iotc-default@1.0.0       | Vytvoří prázdnou aplikaci, kterou můžete naplnit vlastními šablonami zařízení a zařízeními.


**Šablony aplikací s funkcemi veřejné verze Preview**

| Název šablony            | Popis |
| ------------------------ | ----------- |
| iotc-pnp-preview@1.0.0   | Vytvoří prázdnou aplikaci typu Plug and Play Preview, která vám umožní naplnit vlastní šablony zařízení a zařízení. |
| iotc-condition@1.0.0     | Vytvoří aplikaci s analytickou šablonou monitorování podmínek in-Store. Pomocí této šablony můžete připojit a monitorovat prostředí úložiště. |
| iotc-consumption@1.0.0   | Vytvoří aplikaci s šablonou monitorování spotřeby vody. Pomocí této šablony můžete monitorovat a řídit tok vody. |
| iotc-distribution@1.0.0  | Vytvoří aplikaci s šablonou digitální distribuce. Pomocí této šablony můžete zvýšit efektivitu výstupu datového skladu tím, že digitalizing klíče a akce. |
| iotc-inventory@1.0.0     | Vytvoří aplikaci se šablonou správy inteligentního inventáře. Pomocí této šablony můžete automatizovat příjem, pohyb produktů, počítání cyklů a sledování senzorů. |
| iotc-logistics@1.0.0     | Vytvoří aplikaci s připojenou logistickou šablonou. Pomocí této šablony můžete sledovat svou dodávku v reálném čase napříč vzduchem, vodou a půdou pomocí monitorování polohy a stavu. |
| iotc-meter@1.0.0         | Vytvoří aplikaci se šablonou monitorování inteligentního měření. Pomocí této šablony můžete monitorovat spotřebu energie, stav sítě a identifikovat trendy pro zlepšení zákaznické podpory a správy inteligentních měřičů.  |
| iotc-patient@1.0.0       | Vytvoří aplikaci se šablonou monitorování nepřetržitého pacienta. Tato šablona slouží k prodloužení péče o pacienty, opětovném přístupu a správě nemocí. |
| iotc-power@1.0.0         | Vytvoří aplikaci s šablonou monitorování na panelu slunečního příplatku. Pomocí této šablony můžete monitorovat stav panelu slunečního vývoje, trendy v oblasti generování energie. |
| iotc-quality@1.0.0       | Vytvoří aplikaci se šablonou monitorování kvality vody. Pomocí této šablony můžete digitálně monitorovat kvalitu vody.|
| iotc-store@1.0.0         | Vytvoří aplikaci se šablonou pro rezervaci in-Store –. Pomocí této šablony můžete monitorovat a spravovat tok rezervací v rámci svého obchodu. |
| iotc-waste@1.0.0         | Vytvoří aplikaci s propojenou šablonou správy odpadu. Tuto šablonu použijte k monitorování odpadkových přihrádek a operátorů polí dispatch. |

> [!NOTE]
> Šablony aplikace ve verzi Preview jsou v tuto chvíli dostupné jenom v oblastech **Evropa** a **USA** .

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

K odstranění aplikace IoT Central použijte rutinu [Remove-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/Remove-AzIotCentralApp) . Příklad:

```powershell
Remove-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
 -Name "myiotcentralapp"
```

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak spravovat aplikace Azure IoT Central z Azure PowerShell, je tady doporučený další krok:

> [!div class="nextstepaction"]
> [Správa aplikace](howto-administer.md)
