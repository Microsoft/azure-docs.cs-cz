---
title: Škálování cloudové služby Azure (Classic) v prostředí Windows PowerShell | Microsoft Docs
description: standardním Naučte se používat PowerShell ke škálování webové role nebo role pracovního procesu v Azure.
ms.topic: article
ms.service: cloud-services
ms.subservice: autoscale
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 0b0a9cc8032cfd849f053e2d45e05f85e4216a23
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105936610"
---
# <a name="how-to-scale-an-azure-cloud-service-classic-in-powershell"></a>Jak škálovat cloudovou službu Azure (Classic) v PowerShellu

> [!IMPORTANT]
> [Azure Cloud Services (Rozšířená podpora)](../cloud-services-extended-support/overview.md) je nový model nasazení založený na Azure Resource Manager pro produkt Azure Cloud Services.V důsledku této změny se Azure Cloud Services běžící na modelu nasazení založeném na Azure Service Manager přejmenovala jako Cloud Services (Classic) a všechna nová nasazení by měla používat [Cloud Services (Rozšířená podpora)](../cloud-services-extended-support/overview.md).

K škálování webové role nebo role pracovního procesu můžete přidat nebo odebrat instance pomocí prostředí Windows PowerShell.  

## <a name="log-in-to-azure"></a>Přihlaste se k Azure.

Než budete moct v předplatném provádět jakékoli operace prostřednictvím PowerShellu, musíte se přihlásit:

```powershell
Add-AzureAccount
```

Pokud máte k vašemu účtu přidružené víc předplatných, možná budete muset změnit aktuální předplatné v závislosti na tom, kde se nachází vaše cloudová služba. Chcete-li zjistit aktuální předplatné, spusťte příkaz:

```powershell
Get-AzureSubscription -Current
```

Pokud potřebujete změnit aktuální předplatné, spusťte příkaz:

```powershell
Set-AzureSubscription -SubscriptionId <subscription_id>
```

## <a name="check-the-current-instance-count-for-your-role"></a>Zkontroluje aktuální počet instancí pro vaši roli.

Chcete-li zjistit aktuální stav vaší role, spusťte příkaz:

```powershell
Get-AzureRole -ServiceName '<your_service_name>' -RoleName '<your_role_name>'
```

Měli byste se vrátit informace o roli, včetně její aktuální verze operačního systému a počtu instancí. V takovém případě má role jednu instanci.

![Informace o roli](./media/cloud-services-how-to-scale-powershell/get-azure-role.png)

## <a name="scale-out-the-role-by-adding-more-instances"></a>Horizontální navýšení kapacity role přidáním dalších instancí

Pro horizontální navýšení kapacity role předejte požadovaný počet instancí jako parametr **Count** rutiny **set-AzureRole** :

```powershell
Set-AzureRole -ServiceName '<your_service_name>' -RoleName '<your_role_name>' -Slot <target_slot> -Count <desired_instances>
```

Rutina se zablokuje v době, kdy jsou nové instance zřízené a spuštěné. Během této doby, pokud otevřete nové okno PowerShellu a zavoláte **Get-AzureRole** , jak je uvedeno výše, zobrazí se nový počet cílových instancí. A pokud zkontrolujete stav role na portálu, měli byste vidět, že se nová instance spouští:

![Instance virtuálního počítače spouštěná na portálu](./media/cloud-services-how-to-scale-powershell/role-instance-starting.png)

Po spuštění nových instancí se rutina vrátí úspěšně:

![Úspěšnost navýšení instance role](./media/cloud-services-how-to-scale-powershell/set-azure-role-success.png)

## <a name="scale-in-the-role-by-removing-instances"></a>Škálovat v roli odebráním instancí

V roli můžete škálovat tak, že odeberete instance stejným způsobem. Nastavte parametr **Count** u **set-AzureRole** na počet instancí, které chcete mít po dokončení operace škálování.

## <a name="next-steps"></a>Další kroky

Pro cloudové služby z PowerShellu není možné konfigurovat automatické škálování. Postup najdete v tématu [Jak automaticky škálovat cloudovou službu](cloud-services-how-to-scale-portal.md).
