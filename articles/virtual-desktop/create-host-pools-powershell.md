---
title: Vytvoření fondu hostitelů virtuálních desktopů Windows PowerShell – Azure
description: Jak vytvořit fond hostitelů ve virtuální ploše Windows s rutinami prostředí PowerShell.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: b390c0beb20b7557294c18f889a0f41023513e2a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246955"
---
# <a name="create-a-host-pool-with-powershell"></a>Vytvoření fondu hostitelů pomocí PowerShellu

Fondy hostitelů jsou kolekce jednoho nebo více identických virtuálních počítačů v prostředích klienta Windows Virtual Desktop. Každý fond hostitelů může obsahovat skupinu aplikací, se kterou mohou uživatelé pracovat stejně jako na fyzické ploše.

## <a name="use-your-powershell-client-to-create-a-host-pool"></a>Vytvoření fondu hostitelů pomocí klienta PowerShellu

Nejprve [si stáhněte a importujte modul Windows Virtual Desktop PowerShell,](/powershell/windows-virtual-desktop/overview/) který se použije v relaci PowerShellu, pokud jste tak ještě neučinili.

Spuštěním následující rutiny se přihlaste do prostředí Virtuální plocha systému Windows

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Dále spusťte tuto rutinu a vytvořte nový fond hostitelů v tenantovi virtuální plochy Windows:

```powershell
New-RdsHostPool -TenantName <tenantname> -Name <hostpoolname>
```

Spusťte další rutinu a vytvořte registrační token pro autorizaci hostitele relace, aby se připojil k fondu hostitelů a uložil jej do nového souboru v místním počítači. Můžete určit, jak dlouho je platný registrační token pomocí parametru -ExpirationHours.

```powershell
New-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname> -ExpirationHours <number of hours> | Select-Object -ExpandProperty Token | Out-File -FilePath <PathToRegFile>
```

Poté spusťte tuto rutinu a přidejte uživatele služby Azure Active Directory do výchozí skupiny aplikací pro stolní počítače pro fond hostitelů.

```powershell
Add-RdsAppGroupUser -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName "Desktop Application Group" -UserPrincipalName <userupn>
```

Rutina **Add-RdsAppGroupUser** nepodporuje přidávání skupin zabezpečení a do skupiny aplikací přidává pouze jednoho uživatele najednou. Pokud chcete do skupiny aplikací přidat více uživatelů, znovu spusťte rutinu s příslušnými hlavními názvy uživatelů.

Spusťte následující rutinu pro export registračního tokenu do proměnné, kterou budete později používat v [části Registrovat virtuální počítače do hostitelského fondu virtuální plochy windows](#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool).

```powershell
$token = (Export-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname>).Token
```

## <a name="create-virtual-machines-for-the-host-pool"></a>Vytvoření virtuálních počítačů pro fond hostitelů

Teď můžete vytvořit virtuální počítač Azure, který se dá připojit k hostitelskému fondu virtuální chodů Windows.

Virtuální počítač můžete vytvořit několika způsoby:

- [Vytvoření virtuálního počítače z bitové kopie Galerie Azure](../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Vytvoření virtuálního počítače ze spravované bitové kopie](../virtual-machines/windows/create-vm-generalized-managed.md)
- [Vytvoření virtuálního počítače z nespravované bitové kopie](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-user-image-data-disks)

>[!NOTE]
>Pokud nasazujete virtuální počítač pomocí systému Windows 7 jako hostitelského operačního systému, proces vytváření a nasazování bude trochu jiný. Další podrobnosti naleznete [v tématu Nasazení virtuálního počítače se systémem Windows 7 na virtuální ploše Windows .](deploy-windows-7-virtual-machine.md)

Po vytvoření virtuálních počítačů hostitele relací [použijte licenci Systému Windows na virtuální počítač hostitele relací](./apply-windows-license.md#apply-a-windows-license-to-a-session-host-vm) a spusťte virtuální počítače se systémem Windows nebo Windows Server bez placení za jinou licenci. 

## <a name="prepare-the-virtual-machines-for-windows-virtual-desktop-agent-installations"></a>Příprava virtuálních počítačů pro instalace agenta Virtuální plochy windows

Před instalací agentů Virtuální plochy windows a registrací virtuálních počítačů do hostitelského fondu Virtuální plochy systému Windows je třeba provést následující kroky:

- Je nutné doméně připojit k počítači. To umožňuje příchozím uživatelům virtuálních desktopů Windows mapovat z účtu Azure Active Directory na jejich účet služby Active Directory a úspěšně povolit přístup k virtuálnímu počítači.
- Pokud je ve virtuálním počítači spuštěn operační systém Windows Server, je nutné nainstalovat roli Hostitele relací vzdálené plochy (RDSH). Role RDSH umožňuje agentům virtuální plochy systému Windows správně nainstalovat.

Chcete-li úspěšně připojit k doméně, proveďte na každém virtuálním počítači následující akce:

1. [Připojte se k virtuálnímu počítači](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) s přihlašovacími údaji, které jste zadali při vytváření virtuálního počítače.
2. Na virtuálním stroji spusťte **Ovládací panely** a vyberte **Systém**.
3. Vyberte **Název počítače**, vyberte **Změnit nastavení**a pak **vyberte Změnit...**
4. Vyberte **Doména** a zadejte doménu služby Active Directory ve virtuální síti.
5. Ověřte pomocí účtu domény, který má oprávnění k počítačům s připojením k doméně.

    >[!NOTE]
    > Pokud připojujete virtuální počítače k prostředí Služby Azure Active Directory Domain Services (Azure AD DS), ujistěte se, že uživatel připojení k doméně je také členem [skupiny Administrators řadiče domény AAD](../active-directory-domain-services/tutorial-create-instance-advanced.md#configure-an-administrative-group).

## <a name="register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool"></a>Registrace virtuálních počítačů do fondu hostitelů Virtuální plochy Windows

Registrace virtuálních počítačů do hostitelského fondu virtuální plochy systému Windows je stejně jednoduchá jako instalace agentů virtuální plochy systému Windows.

Chcete-li zaregistrovat agenty virtuální plochy windows, postupujte na každém virtuálním počítači takto:

1. [Připojte se k virtuálnímu počítači](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) s přihlašovacími údaji, které jste zadali při vytváření virtuálního počítače.
2. Stáhněte a nainstalujte agenta Virtuální plochy systému Windows.
   - Stáhněte si [agenta virtuální plochy systému Windows](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrmXv).
   - Klepněte pravým tlačítkem myši na staženou instalační službu, vyberte **možnost Vlastnosti**, vyberte **odblokovat**a pak vyberte **OK**. To umožní systému důvěřovat instalačnímu programu.
   - Spusťte instalační program. Když vás instalační program požádá o registrační token, zadejte hodnotu, kterou jste získali z **rutiny Export-RdsRegistrationInfo.**
3. Stáhněte a nainstalujte zavaděč Windows Virtual Desktop Agent.
   - Stáhněte si [zavaděč Windows Virtual Desktop Agent](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrxrH).
   - Klepněte pravým tlačítkem myši na staženou instalační službu, vyberte **možnost Vlastnosti**, vyberte **odblokovat**a pak vyberte **OK**. To umožní systému důvěřovat instalačnímu programu.
   - Spusťte instalační program.

>[!IMPORTANT]
>Chcete-li zabezpečit prostředí Virtuální desktopwindows v Azure, doporučujeme neotevírat příchozí port 3389 na virtuálních počítačích. Virtuální plocha Windows nevyžaduje otevřený vstupní port 3389 pro přístup uživatelů k virtuálním počítačům hostitelského fondu. Pokud musíte otevřít port 3389 pro účely řešení potíží, doporučujeme použít [přístup k virtuálním ms just-in-time](../security-center/security-center-just-in-time.md).

## <a name="next-steps"></a>Další kroky

Teď, když jste vytvořili fond hostitelů, můžete ho naplnit remoteapps. Další informace o správě aplikací ve Windows Virtual Desktop najdete v kurzu Správa skupin aplikací.

> [!div class="nextstepaction"]
> [Kurz Správa skupin aplikací](./manage-app-groups.md)
