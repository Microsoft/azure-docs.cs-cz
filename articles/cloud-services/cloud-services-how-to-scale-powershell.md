---
title: Škálování cloudové služby Azure ve Windows Powershellu | Dokumentace Microsoftu
description: (classic) Zjistěte, jak škálovat webovou roli nebo roli pracovního procesu snížení nebo navýšení kapacity v Azure pomocí Powershellu.
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
ms.author: memccror
ms.openlocfilehash: 6c013687faaca33938d0b27303e9b1252482fcb9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65963315"
---
# <a name="how-to-scale-a-cloud-service-in-powershell"></a>Jak škálovat cloudové služby v prostředí PowerShell

Prostředí Windows PowerShell můžete škálovat přidáváním nebo odebíráním instancí webové role nebo role pracovního procesu snížení nebo navýšení kapacity.  

## <a name="log-in-to-azure"></a>Přihlášení k Azure

Před provedením jakékoli operace v rámci předplatného pomocí Powershellu, musíte být přihlášení:

```powershell
Add-AzureAccount
```

Pokud máte více předplatných, které jsou spojené s vaším účtem, budete muset změnit aktuální předplatné, v závislosti na tom, kde se nachází vaši cloudovou službu. Pokud chcete zkontrolovat aktuální předplatné, spusťte:

```powershell
Get-AzureSubscription -Current
```

Pokud potřebujete změnit aktuální předplatné, spusťte:

```powershell
Set-AzureSubscription -SubscriptionId <subscription_id>
```

## <a name="check-the-current-instance-count-for-your-role"></a>Zkontrolujte aktuální počet instancí pro vaši roli

Pokud chcete zkontrolovat aktuální stav vaší role, spusťte:

```powershell
Get-AzureRole -ServiceName '<your_service_name>' -RoleName '<your_role_name>'
```

Informace o roli, včetně jeho aktuální počet verze a instance operačního systému by měla vrátit zpět. V tomto případě roli má jednu instanci.

![Informace o roli](./media/cloud-services-how-to-scale-powershell/get-azure-role.png)

## <a name="scale-out-the-role-by-adding-more-instances"></a>Škálování role přidáním více instancí

Chcete-li horizontálně navýšit kapacitu vaší role, předejte požadovaný počet instancí **počet** parametr **Set-AzureRole** rutiny:

```powershell
Set-AzureRole -ServiceName '<your_service_name>' -RoleName '<your_role_name>' -Slot <target_slot> -Count <desired_instances>
```

Bloky rutiny okamžik při nové instance jsou zřízené a spustit. Během této doby, pokud otevřete nové okno Powershellu a volání **Get-AzureRole** jak je uvedeno výše, zobrazí se nový cílový počet instancí. A je-li si prohlédnout stav role na portálu, měli byste vidět nové instance spuštění:

![Instance virtuálního počítače od portálu](./media/cloud-services-how-to-scale-powershell/role-instance-starting.png)

Po zahájení mají nové instance, rutina vrátí úspěšně:

![Úspěch zvýšení instance role](./media/cloud-services-how-to-scale-powershell/set-azure-role-success.png)

## <a name="scale-in-the-role-by-removing-instances"></a>Škálování v roli tak, že odeberete instance

Je možné škálovat tak, že odeberete instance stejně jako v roli. Nastavte **počet** parametru u **Set-AzureRole** na počet instancí, které chcete mít po dokončení škálování v operaci.

## <a name="next-steps"></a>Další postup

Není možné nakonfigurovat automatické škálování pro cloud services z Powershellu. Chcete-li to mohli udělat, najdete v článku [jak automaticky škálovat cloudovou službu](cloud-services-how-to-scale-portal.md).
