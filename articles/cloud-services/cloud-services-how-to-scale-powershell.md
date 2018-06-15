---
title: Škálování cloudové služby Azure v prostředí Windows PowerShell | Microsoft Docs
description: (klasické) Zjistěte, jak pomocí prostředí PowerShell škálování webovou roli nebo role pracovního procesu příchozí nebo odchozí v Azure.
services: cloud-services
documentationcenter: ''
author: mmccrory
manager: timlt
editor: ''
ms.assetid: ee37dd8c-6714-4c61-adb8-03d6bbf76c9a
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2016
ms.author: mmccrory
ms.openlocfilehash: a7ae8ff202d403dff19b8c9a6a09492235db27ac
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23842948"
---
# <a name="how-to-scale-a-cloud-service-in-powershell"></a>Postup škálování cloudové služby v prostředí PowerShell

Prostředí Windows PowerShell můžete škálovat webovou roli nebo role pracovního procesu příchozí nebo odchozí přidáním nebo odebráním instancí.  

## <a name="log-in-to-azure"></a>Přihlaste se k Azure.

Před provedením jakékoli operace na vaše předplatné pomocí prostředí PowerShell, musíte být přihlášení:

```powershell
Add-AzureAccount
```

Pokud máte více předplatných, které jsou spojené s vaším účtem, musíte změnit aktuální předplatné v závislosti na tom, kde se nachází cloudové služby. Pokud chcete zkontrolovat aktuální předplatné, spusťte příkaz:

```powershell
Get-AzureSubscription -Current
```

Pokud potřebujete změnit aktuální předplatné, spusťte:

```powershell
Set-AzureSubscription -SubscriptionId <subscription_id>
```

## <a name="check-the-current-instance-count-for-your-role"></a>Zkontrolujte aktuální počet instancí pro vaši roli

Pokud chcete zkontrolovat aktuální stav role, spusťte příkaz:

```powershell
Get-AzureRole -ServiceName '<your_service_name>' -RoleName '<your_role_name>'
```

Informace o roli, včetně jeho aktuální počet verze a instance operačního systému by měla vrátit zpět. V takovém případě má roli jediné instance.

![Informace o roli](./media/cloud-services-how-to-scale-powershell/get-azure-role.png)

## <a name="scale-out-the-role-by-adding-more-instances"></a>Škálování role přidáním více instancí

Chcete-li škálovat vaše role, předejte požadovaný počet instancí jako **počet** parametru **Set-AzureRole** rutiny:

```powershell
Set-AzureRole -ServiceName '<your_service_name>' -RoleName '<your_role_name>' -Slot <target_slot> -Count <desired_instances>
```

Rutiny bloky na okamžik při nové instance jsou zřízený a spuštěna. Během této doby, otevřete nové okno prostředí PowerShell a volání **Get-AzureRole** jako je uvedené výše, zobrazí se nové cílový počet instancí. A je-li si prohlédnout stav rolí na portálu, měli byste vidět novou instanci spuštění:

![Instance virtuálního počítače od portálu](./media/cloud-services-how-to-scale-powershell/role-instance-starting.png)

Jakmile nové instance spustili, rutina vrátí úspěšně:

![Úspěch zvýšení instance role](./media/cloud-services-how-to-scale-powershell/set-azure-role-success.png)

## <a name="scale-in-the-role-by-removing-instances"></a>Škálování v roli odebráním instancí

Je možné škálovat v roli odebráním instancí stejným způsobem. Nastavte **počet** parametr na **Set-AzureRole** na počet instancí, které chcete mít po dokončení měřítka v operaci.

## <a name="next-steps"></a>Další kroky

Není možné nakonfigurovat automatické škálování pro cloudové služby z prostředí PowerShell. To lze provést, najdete v části [postup automatické škálování cloudové služby](cloud-services-how-to-scale-portal.md).
