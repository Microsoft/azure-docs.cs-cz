---
title: PowerShellový modul Windows Virtual Desktop – Azure
description: Jak nainstalovat a nastavit modul prostředí PowerShell pro virtuální počítač s Windows
author: Heidilohr
ms.topic: how-to
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f2f01e2b58c997db08ad4427de7eef1ee3760c4a
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91323496"
---
# <a name="set-up-the-powershell-module-for-windows-virtual-desktop"></a>Nastavení modulu PowerShell pro virtuální plochu Windows

>[!IMPORTANT]
>Tento obsah se vztahuje na virtuální plochu Windows s integrací Azure Resource Manager.

Modul PowerShellu virtuálního počítače s Windows je integrovaný do modulu Azure PowerShell. V tomto článku se dozvíte, jak nastavit modul PowerShellu, abyste mohli spouštět rutiny pro virtuální počítače s Windows.

## <a name="set-up-your-powershell-environment"></a>Nastavení prostředí PowerShell

Pokud chcete začít s používáním modulu, nejdřív nainstalujte [nejnovější verzi PowerShellu Core](/powershell/scripting/install/installing-powershell#powershell-core). Rutiny virtuálních klientů Windows v tuto chvíli fungují jenom s PowerShell Core.

V dalším kroku budete muset nainstalovat modul DesktopVirtualization, který se použije v relaci PowerShellu.

Spusťte následující rutinu PowerShellu v režimu zvýšené úrovně a nainstalujte modul:

```powershell
Install-Module -Name Az.DesktopVirtualization
```

>[!NOTE]
> Pokud tato rutina nefunguje, zkuste ji znovu spustit se zvýšenými oprávněními.

Dále spusťte následující rutinu pro připojení k Azure:

```powershell
Connect-AzAccount
```

>[!IMPORTANT]
>Pokud se připojujete k portálu US Gov, spusťte místo toho tuto rutinu:
> 
> ```powershell
> Connect-AzAccount -EnvironmentName AzureUSGovernment
> ```

Přihlášení k účtu Azure vyžaduje kód, který se vygeneruje při spuštění rutiny Connect. Pokud se chcete přihlásit, přejděte na adresu <https://microsoft.com/devicelogin> , zadejte kód a pak se přihlaste pomocí svých přihlašovacích údajů správce Azure.

```powershell
Account SubscriptionName TenantId Environment

------- ---------------- -------- -----------

Youradminupn subscriptionname AzureADTenantID AzureCloud
```

Tím se vás pošle přímo do předplatného, které je ve výchozím nastavení pro vaše přihlašovací údaje správce.

## <a name="change-the-default-subscription"></a>Změna výchozího předplatného

Pokud chcete po přihlášení změnit výchozí předplatné, spusťte tuto rutinu:

```powershell
Select-AzSubscription -Subscription <preferredsubscriptionname>
```

Můžete ho také vybrat ze seznamu pomocí rutiny out-GridView:

```powershell
Get-AzSubscription | Out-GridView -PassThru | Select-AzSubscription
```

Když vyberete nové předplatné, které se má použít, nemusíte v rutinách, které spustíte později, zadávat ID tohoto předplatného. Například následující rutina načte konkrétního hostitele relace bez nutnosti ID předplatného:

```powershell
Get-AzWvdSessionHost -HostPoolName <hostpoolname> -Name <sessionhostname> -ResourceGroupName <resourcegroupname>
```

Můžete také změnit odběry na základě rutiny tak, že přidáte požadovaný název předplatného jako parametr. Další rutina je stejná jako předchozí příklad, s výjimkou ID předplatného přidané jako parametr pro změnu předplatného, které rutina používá.

```powershell
Get-AzWvdSessionHost -HostPoolName <hostpoolname> -Name <sessionhostname> -ResourceGroupName <resourcegroupname> -SubscriptionId <subscriptionGUID>
```

## <a name="get-locations"></a>Získat umístění

Parametr Location je povinný pro všechny rutiny **New-AzWVD** , které vytvářejí nové objekty.

Spuštěním následující rutiny získáte seznam umístění, která vaše předplatné podporuje:

```powershell
Get-AzLocation
```

Výstup pro **Get-AzLocation** bude vypadat takto:

```powershell
Location : eastasia

DisplayName : East Asia

Providers : {Microsoft.RecoveryServices, Microsoft.ManagedIdentity,
Microsoft.SqlVirtualMachine, microsoft.insightsΓÇª}

Location : southeastasia

DisplayName : Southeast Asia

Providers : {Microsoft.RecoveryServices, Microsoft.ManagedIdentity,
Microsoft.SqlVirtualMachine, microsoft.insightsΓÇª}

Location : centralus

DisplayName : Central US

Providers : {Microsoft.RecoveryServices, Microsoft.DesktopVirtualization,
Microsoft.ManagedIdentity, Microsoft.SqlVirtualMachineΓÇª}

Location : eastus

DisplayName : East US

Providers : {Microsoft.RecoveryServices, Microsoft.DesktopVirtualization,
Microsoft.ManagedIdentity, Microsoft.SqlVirtualMachineΓÇª}
```

Jakmile znáte umístění svého účtu, můžete ho použít v rutině. Tady je například rutina, která vytvoří fond hostitelů v umístění "southeastasia":

```powershell
New-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -WorkspaceName <workspacename> -Location “southeastasia”
```

## <a name="next-steps"></a>Další kroky

Teď, když jste nastavili modul prostředí PowerShell, můžete spouštět rutiny, abyste mohli provádět nejrůznější akce ve virtuálním počítači s Windows. Tady jsou některá místa, kde můžete použít váš modul:

- Projděte si naše [kurzy k virtuálním plochám Windows]() a nastavte své vlastní prostředí pro virtuální počítače s Windows.
- [Vytvoření fondu hostitelů pomocí PowerShellu](create-host-pools-powershell.md)
- [Konfigurace metody vyrovnávání zatížení Windows Virtual Desktop](configure-host-pool-load-balancing.md)
- [Konfigurace typu přiřazení fondu hostitelů osobní plochy](configure-host-pool-personal-desktop-assignment-type.md)
- A mnohem víc.

Pokud narazíte na nějaké problémy, podívejte se na náš [článek věnované řešení potíží s PowerShellem](troubleshoot-powershell.md) .

