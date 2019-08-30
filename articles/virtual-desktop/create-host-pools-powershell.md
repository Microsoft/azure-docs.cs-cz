---
title: Vytvoření fondu hostitelů ve verzi Preview virtuálních počítačů s Windows pomocí PowerShellu – Azure
description: Postup vytvoření fondu hostitelů ve verzi Preview ve Windows Virtual desktopu pomocí rutin PowerShellu
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
ms.openlocfilehash: 1fb377d482277a4776214d08b879d99f4234ca40
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/29/2019
ms.locfileid: "70163683"
---
# <a name="create-a-host-pool-with-powershell"></a>Vytvoření fondu hostitelů pomocí PowerShellu

Fondy hostitelů jsou kolekce jednoho nebo více identických virtuálních počítačů v prostředích klienta ve verzi Preview virtuálních počítačů s Windows. Každý fond hostitelů může obsahovat skupinu aplikací, se kterou můžou uživatelé interaktivně pracovat, jako by na fyzickém počítači.

## <a name="use-your-powershell-client-to-create-a-host-pool"></a>Vytvoření fondu hostitelů pomocí klienta PowerShellu

Nejdřív [Stáhněte a importujte modul PowerShellu virtuálního počítače s Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) , který chcete použít v relaci PowerShellu, pokud jste to ještě neudělali.

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
New-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname> -ExpirationHours <number of hours> | Select-Object -ExpandProperty Token > <PathToRegFile>
```

Potom spuštěním této rutiny přidejte Azure Active Directory uživatele do výchozí skupiny desktopových aplikací pro fond hostitelů.

```powershell
Add-RdsAppGroupUser -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName "Desktop Application Group" -UserPrincipalName <userupn>
```

Rutina **Add-RdsAppGroupUser** nepodporuje přidávání skupin zabezpečení a do skupiny aplikací přičítá pouze jednoho uživatele. Pokud chcete do skupiny aplikací přidat více uživatelů, spusťte rutinu znovu s příslušnými hlavními názvy uživatelů.

Spuštěním následující rutiny exportujte registrační token do proměnné, kterou použijete později v části [registrace virtuálních počítačů do fondu hostitelů virtuálních počítačů s Windows](#register-the-virtual-machines-to-the-windows-virtual-desktop-preview-host-pool).

```powershell
$token = (Export-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname>).Token
```

## <a name="create-virtual-machines-for-the-host-pool"></a>Vytváření virtuálních počítačů pro fond hostitelů

Teď můžete vytvořit virtuální počítač Azure, který se dá připojit ke fondu hostitelů virtuálních počítačů s Windows.

Virtuální počítač můžete vytvořit několika způsoby:

- [Vytvoření virtuálního počítače z Image Galerie Azure](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#create-virtual-machine)
- [Vytvoření virtuálního počítače ze spravované image](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-generalized-managed)
- [Vytvoření virtuálního počítače z nespravované image](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

Po vytvoření virtuálních počítačů hostitele relace [použijte licenci Windows pro virtuální počítač hostitele relace](./apply-windows-license.md#apply-a-windows-license-to-a-session-host-vm) , aby se spouštěly vaše virtuální počítače s Windows nebo Windows serverem bez placení na jinou licenci. 

## <a name="prepare-the-virtual-machines-for-windows-virtual-desktop-preview-agent-installations"></a>Příprava virtuálních počítačů pro instalace agenta verze Preview pro virtuální počítače s Windows

Než budete moct nainstalovat agenty virtuálních počítačů s Windows a zaregistrovat virtuální počítače do fondu hostitelů virtuálních počítačů s Windows, musíte provést následující akce:

- Počítač musí být připojený k doméně. To umožňuje, aby se uživatelé virtuálních ploch Windows namapovali z jejich Azure Active Directory účtu na svůj účet Active Directory a úspěšně povolili přístup k tomuto virtuálnímu počítači.
- Pokud na virtuálním počítači běží operační systém Windows Server, musíte nainstalovat roli Hostitel relace vzdálené plochy (vzdálené relace). Role vzdálené plochy (hostitele) umožňuje, aby se agenti virtuálních počítačů s Windows správně nainstalovaly.

K úspěšnému připojení k doméně udělejte na každém virtuálním počítači tyto věci:

1. [Připojte se k virtuálnímu počítači](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine) pomocí přihlašovacích údajů, které jste zadali při vytváření virtuálního počítače.
2. Na virtuálním počítači spusťte **Ovládací panely** a vyberte možnost **systém**.
3. Vyberte **název počítače**, vyberte **změnit nastavení**a pak zvolte **změnit...**
4. Vyberte **doména** a pak zadejte doménu služby Active Directory ve virtuální síti.
5. Proveďte ověření pomocí doménového účtu, který má oprávnění k počítačům připojeným k doméně.

    >[!NOTE]
    > Pokud se připojujete k virtuálním počítačům do prostředí Azure Active Directory Domain Services (Azure služba AD DS), ujistěte se, že je uživatel připojení k doméně také členem [skupiny správců AAD řadiče domény](../active-directory-domain-services/tutorial-create-instance.md#configure-an-administrative-group).

## <a name="register-the-virtual-machines-to-the-windows-virtual-desktop-preview-host-pool"></a>Registrace virtuálních počítačů do fondu hostitelů ve službě Windows Virtual Desktop Preview

Registrace virtuálních počítačů do fondu hostitelů virtuálních klientů Windows je jednoduché jako instalace agentů virtuálních počítačů s Windows.

Pokud chcete zaregistrovat agenty virtuálních počítačů s Windows, udělejte na každém virtuálním počítači následující:

1. [Připojte se k virtuálnímu počítači](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine) pomocí přihlašovacích údajů, které jste zadali při vytváření virtuálního počítače.
2. Stáhněte a nainstalujte agenta virtuálního počítače s Windows.
   - Stáhněte si [agenta pro virtuální počítače s Windows](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrmXv).
   - Klikněte pravým tlačítkem na stažený instalační program, vyberte **vlastnosti**, vyberte odblokování a pak vyberte **OK**. To umožní vašemu systému důvěřovat instalačnímu programu.
   - Spusťte instalační program. Když instalační program požaduje registrační token, zadejte hodnotu, kterou jste získali z rutiny **Export-RdsRegistrationInfo** .
3. Stáhněte a nainstalujte si zaváděcí program pro Windows Virtual Desktop agent.
   - Stáhněte si [zaváděcí program pro Windows Virtual Desktop agent](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrxrH).
   - Klikněte pravým tlačítkem na stažený instalační program, vyberte **vlastnosti**, vyberte odblokování a pak vyberte **OK**. To umožní vašemu systému důvěřovat instalačnímu programu.
   - Spusťte instalační program.

>[!IMPORTANT]
>Pro lepší zabezpečení prostředí virtuálních počítačů s Windows v Azure doporučujeme na svých virtuálních počítačích neotevírat port 3389 pro příchozí spojení. Virtuální počítač s Windows nevyžaduje pro přístup k virtuálním počítačům fondu hostitelů otevřený příchozí port 3389. Pokud musíte pro účely řešení potíží otevřít port 3389, doporučujeme použít [přístup k virtuálnímu počítači za běhu](https://docs.microsoft.com/azure/security-center/security-center-just-in-time).

## <a name="next-steps"></a>Další postup

Teď, když jste vytvořili fond hostitelů, můžete ho naplnit pomocí vzdálených aplikací RemoteApp. Další informace o správě aplikací ve virtuálním počítači s Windows najdete v kurzu Správa skupin aplikací.

> [!div class="nextstepaction"]
> [Kurz správy skupin aplikací](./manage-app-groups.md)
