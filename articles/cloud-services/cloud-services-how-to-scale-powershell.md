---
title: Škálování cloudové služby Azure v Prostředí Windows PowerShell | Dokumenty společnosti Microsoft
description: (klasická) Zjistěte, jak pomocí PowerShellu škálovat webovou roli nebo roli pracovního procesu v Azure nebo mimo ni.
services: cloud-services
author: mmccrory
ms.service: cloud-services
ms.topic: article
ms.date: 12/01/2016
ms.author: memccror
ms.openlocfilehash: a1945aad12eb34bad1b593878779e1ceb0dae686
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68359041"
---
# <a name="how-to-scale-a-cloud-service-in-powershell"></a>Jak škálovat cloudovou službu v PowerShellu

Pomocí prostředí Windows PowerShell můžete škálovat webovou roli nebo roli pracovního procesu přidáním nebo odebráním instancí.  

## <a name="log-in-to-azure"></a>Přihlaste se k Azure.

Než budete moci provádět jakékoli operace s předplatným prostřednictvím prostředí PowerShell, musíte se přihlásit:

```powershell
Add-AzureAccount
```

Pokud máte k vašemu účtu přidruženo více předplatných, možná budete muset změnit aktuální předplatné v závislosti na tom, kde se nachází vaše cloudová služba. Chcete-li zkontrolovat aktuální předplatné, spusťte:

```powershell
Get-AzureSubscription -Current
```

Pokud potřebujete změnit aktuální předplatné, spusťte:

```powershell
Set-AzureSubscription -SubscriptionId <subscription_id>
```

## <a name="check-the-current-instance-count-for-your-role"></a>Kontrola aktuálního počtu instancí pro vaši roli

Chcete-li zkontrolovat aktuální stav role, spusťte:

```powershell
Get-AzureRole -ServiceName '<your_service_name>' -RoleName '<your_role_name>'
```

Měli byste získat zpět informace o roli, včetně jeho aktuální verze operačního systému a počet instancí. V tomto případě má role jednu instanci.

![Informace o roli](./media/cloud-services-how-to-scale-powershell/get-azure-role.png)

## <a name="scale-out-the-role-by-adding-more-instances"></a>Horizontální navýšení kapacity role přidáním dalších instancí

Chcete-li horizontální navýšení kapacity vaší role, předejte požadovaný počet instancí jako **count** parametr **set-AzureRole** rutina:

```powershell
Set-AzureRole -ServiceName '<your_service_name>' -RoleName '<your_role_name>' -Slot <target_slot> -Count <desired_instances>
```

Rutina blokuje na okamžik, zatímco nové instance jsou zřízeny a spuštěny. Během této doby pokud otevřete nové okno PowerShell a volání **Get-AzureRole,** jak je uvedeno výše, uvidíte počet nových cílových instancí. A pokud zkontrolujete stav role na portálu, měli byste vidět spuštění nové instance:

![Instance virtuálního aplikace začínající na portálu](./media/cloud-services-how-to-scale-powershell/role-instance-starting.png)

Po spuštění nových instancí se rutina úspěšně vrátí:

![Instance role zvyšuje úspěšnost](./media/cloud-services-how-to-scale-powershell/set-azure-role-success.png)

## <a name="scale-in-the-role-by-removing-instances"></a>Škálování v roli odebráním instancí

Můžete škálovat v roli odebráním instance stejným způsobem. Nastavte **count** parametr na **Set-AzureRole** na počet instancí, které chcete mít po dokončení škálování v provozu.

## <a name="next-steps"></a>Další kroky

Není možné konfigurovat automatické škálování pro cloudové služby z Prostředí PowerShell. Chcete-li to provést, naleznete v tématu [Automatické škálování cloudové služby](cloud-services-how-to-scale-portal.md).
