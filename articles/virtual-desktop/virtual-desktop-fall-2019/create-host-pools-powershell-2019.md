---
title: Vytvoření hostitelského fondu virtuálních počítačů s Windows (Classic) – Azure
description: Postup vytvoření fondu hostitelů ve virtuální ploše Windows (Classic) pomocí rutin prostředí PowerShell.
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: c035a7fbafe9b3a42fbd16e3f8377014010ddd49
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "88003540"
---
# <a name="create-a-host-pool-in-windows-virtual-desktop-classic-with-powershell"></a>Vytvoření fondu hostitelů ve virtuální ploše Windows (Classic) pomocí PowerShellu

>[!IMPORTANT]
>Tento obsah se vztahuje na virtuální plochu Windows (Classic), která nepodporuje Azure Resource Manager objektů virtuálních klientů Windows. Pokud se snažíte spravovat Azure Resource Manager objektů virtuálních klientů Windows, přečtěte si [Tento článek](../create-host-pools-powershell.md).

Fondy hostitelů jsou kolekce jednoho nebo více identických virtuálních počítačů v prostředích klienta virtuálních počítačů s Windows. Každý fond hostitelů může obsahovat skupinu aplikací, se kterou můžou uživatelé interaktivně pracovat, jako by na fyzickém počítači.

## <a name="use-your-powershell-client-to-create-a-host-pool"></a>Vytvoření fondu hostitelů pomocí klienta PowerShellu

Nejdřív [Stáhněte a importujte modul PowerShellu virtuálního počítače s Windows](/powershell/windows-virtual-desktop/overview/) , který chcete použít v relaci PowerShellu, pokud jste to ještě neudělali.

Spuštěním následující rutiny se přihlaste do prostředí virtuálních počítačů s Windows.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Potom spuštěním této rutiny vytvořte nový fond hostitelů ve vašem tenantovi virtuálních klientů s Windows:

```powershell
New-RdsHostPool -TenantName <tenantname> -Name <hostpoolname>
```

Spusťte další rutinu pro vytvoření registračního tokenu pro autorizaci hostitele relace pro připojení k fondu hostitelů a jeho uložení do nového souboru na místním počítači. Pomocí parametru-ExpirationHours můžete určit, jak dlouho je registrační token platný.

```powershell
New-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname> -ExpirationHours <number of hours> | Select-Object -ExpandProperty Token | Out-File -FilePath <PathToRegFile>
```

Potom spuštěním této rutiny přidejte Azure Active Directory uživatele do výchozí skupiny desktopových aplikací pro fond hostitelů.

```powershell
Add-RdsAppGroupUser -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName "Desktop Application Group" -UserPrincipalName <userupn>
```

Rutina **Add-RdsAppGroupUser** nepodporuje přidávání skupin zabezpečení a do skupiny aplikací přičítá pouze jednoho uživatele. Pokud chcete do skupiny aplikací přidat více uživatelů, spusťte rutinu znovu s příslušnými hlavními názvy uživatelů.

Spuštěním následující rutiny exportujte registrační token do proměnné, kterou použijete později v části [registrace virtuálních počítačů do fondu hostitelů virtuálních počítačů s Windows](#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool).

```powershell
$token = (Export-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname>).Token
```

## <a name="create-virtual-machines-for-the-host-pool"></a>Vytváření virtuálních počítačů pro fond hostitelů

Teď můžete vytvořit virtuální počítač Azure, který se dá připojit ke fondu hostitelů virtuálních počítačů s Windows.

Virtuální počítač můžete vytvořit několika způsoby:

- [Vytvoření virtuálního počítače z Image Galerie Azure](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Vytvoření virtuálního počítače ze spravované image](../../virtual-machines/windows/create-vm-generalized-managed.md)
- [Vytvoření virtuálního počítače z nespravované image](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

>[!NOTE]
>Pokud nasazujete virtuální počítač s operačním systémem Windows 7 jako hostitelský operační systém, proces vytvoření a nasazení bude trochu jiný. Další podrobnosti najdete v tématu [nasazení virtuálního počítače s Windows 7 na virtuálním počítači s Windows](deploy-windows-7-virtual-machine.md).

Po vytvoření virtuálních počítačů hostitele relace [použijte licenci Windows pro virtuální počítač hostitele relace](../apply-windows-license.md#apply-a-windows-license-to-a-session-host-vm) , aby se spouštěly vaše virtuální počítače s Windows nebo Windows serverem bez placení na jinou licenci.

## <a name="prepare-the-virtual-machines-for-windows-virtual-desktop-agent-installations"></a>Příprava virtuálních počítačů pro instalace agenta virtuálních počítačů s Windows

Než budete moct nainstalovat agenty virtuálních počítačů s Windows a zaregistrovat virtuální počítače do fondu hostitelů virtuálních počítačů s Windows, musíte provést následující akce:

- Počítač musí být připojený k doméně. To umožňuje, aby se uživatelé virtuálních ploch Windows namapovali z jejich Azure Active Directory účtu na svůj účet Active Directory a úspěšně povolili přístup k tomuto virtuálnímu počítači.
- Pokud na virtuálním počítači běží operační systém Windows Server, musíte nainstalovat roli Hostitel relace vzdálené plochy (vzdálené relace). Role vzdálené plochy (hostitele) umožňuje, aby se agenti virtuálních počítačů s Windows správně nainstalovaly.

K úspěšnému připojení k doméně udělejte na každém virtuálním počítači tyto věci:

1. [Připojte se k virtuálnímu počítači](../../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) pomocí přihlašovacích údajů, které jste zadali při vytváření virtuálního počítače.
2. Na virtuálním počítači spusťte **Ovládací panely** a vyberte možnost **systém**.
3. Vyberte **název počítače**, vyberte **změnit nastavení** a pak zvolte **změnit...**
4. Vyberte **doména** a pak zadejte doménu služby Active Directory ve virtuální síti.
5. Proveďte ověření pomocí doménového účtu, který má oprávnění k počítačům připojeným k doméně.

    >[!NOTE]
    > Pokud se připojujete k virtuálním počítačům do prostředí Azure Active Directory Domain Services (Azure služba AD DS), ujistěte se, že je uživatel připojení k doméně také členem [skupiny správců AAD řadiče domény](../../active-directory-domain-services/tutorial-create-instance-advanced.md#configure-an-administrative-group).

## <a name="register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool"></a>Zaregistrujte virtuální počítače do fondu hostitelů virtuálních počítačů s Windows.

Registrace virtuálních počítačů do fondu hostitelů virtuálních klientů Windows je jednoduché jako instalace agentů virtuálních počítačů s Windows.

Pokud chcete zaregistrovat agenty virtuálních počítačů s Windows, udělejte na každém virtuálním počítači následující:

1. [Připojte se k virtuálnímu počítači](../../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) pomocí přihlašovacích údajů, které jste zadali při vytváření virtuálního počítače.
2. Stáhněte a nainstalujte agenta virtuálního počítače s Windows.
   - Stáhněte si [agenta pro virtuální počítače s Windows](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrmXv).
   - Klikněte pravým tlačítkem na stažený instalační program, vyberte **vlastnosti**, vyberte **odblokování** a pak vyberte **OK**. To umožní vašemu systému důvěřovat instalačnímu programu.
   - Spusťte instalační program. Když instalační program požaduje registrační token, zadejte hodnotu, kterou jste získali z rutiny **Export-RdsRegistrationInfo** .
3. Stáhněte a nainstalujte si zaváděcí program pro Windows Virtual Desktop agent.
   - Stáhněte si [zaváděcí program pro Windows Virtual Desktop agent](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrxrH).
   - Klikněte pravým tlačítkem na stažený instalační program, vyberte **vlastnosti**, vyberte **odblokování** a pak vyberte **OK**. To umožní vašemu systému důvěřovat instalačnímu programu.
   - Spusťte instalační program.

>[!IMPORTANT]
>Pro lepší zabezpečení prostředí virtuálních počítačů s Windows v Azure doporučujeme na svých virtuálních počítačích neotevírat port 3389 pro příchozí spojení. Virtuální počítač s Windows nevyžaduje pro přístup k virtuálním počítačům fondu hostitelů otevřený příchozí port 3389. Pokud musíte pro účely řešení potíží otevřít port 3389, doporučujeme použít [přístup k virtuálnímu počítači za běhu](../../security-center/security-center-just-in-time.md).

## <a name="next-steps"></a>Další kroky

Teď, když jste vytvořili fond hostitelů, můžete ho naplnit pomocí vzdálených aplikací RemoteApp. Další informace o správě aplikací ve virtuálním počítači s Windows najdete v kurzu Správa skupin aplikací.

> [!div class="nextstepaction"]
> [Kurz správy skupin aplikací](../manage-app-groups.md)
