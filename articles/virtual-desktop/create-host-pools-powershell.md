---
title: Vytvoření PowerShellového fondu virtuálních počítačů s Windows – Azure
description: Postup vytvoření fondu hostitelů na virtuálním počítači s Windows pomocí rutin prostředí PowerShell.
author: Heidilohr
ms.topic: how-to
ms.date: 10/02/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 9ec900f0537030d3ed0d1c875e8125806159bd51
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98251450"
---
# <a name="create-a-windows-virtual-desktop-host-pool-with-powershell"></a>Vytvoření fondu hostitelů virtuálních počítačů s Windows pomocí PowerShellu

>[!IMPORTANT]
>Tento obsah se vztahuje na virtuální plochu Windows s Azure Resource Manager objekty virtuálních klientů Windows. Pokud používáte virtuální plochu Windows (Classic) bez Azure Resource Manager objektů, přečtěte si [Tento článek](./virtual-desktop-fall-2019/create-host-pools-powershell-2019.md).

Fondy hostitelů jsou kolekce jednoho nebo více identických virtuálních počítačů v prostředích klienta virtuálních počítačů s Windows. Každý fond hostitelů je možné přidružit k několika skupinám RemoteApp, jedné skupině aplikací klasické pracovní plochy a několika hostitelům relací.

## <a name="prerequisites"></a>Předpoklady

V tomto článku se předpokládá, že už jste postupovali podle pokynů v tématu [nastavení modulu PowerShellu](powershell-module.md).

## <a name="use-your-powershell-client-to-create-a-host-pool"></a>Vytvoření fondu hostitelů pomocí klienta PowerShellu

Spuštěním následující rutiny se přihlaste do prostředí virtuálních počítačů s Windows:

```powershell
New-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -WorkspaceName <workspacename> -HostPoolType <Pooled|Personal> -LoadBalancerType <BreadthFirst|DepthFirst|Persistent> -Location <region> -DesktopAppGroupName <appgroupname>
```

Tato rutina vytvoří fond hostitelů, pracovní prostor a skupinu desktopových aplikací. Kromě toho zaregistruje skupinu desktopových aplikací do pracovního prostoru. Můžete buď vytvořit pracovní prostor pomocí této rutiny, nebo použít existující pracovní prostor.

Spusťte další rutinu pro vytvoření registračního tokenu pro autorizaci hostitele relace pro připojení k fondu hostitelů a jeho uložení do nového souboru na místním počítači. Pomocí parametru-ExpirationHours můžete určit, jak dlouho je registrační token platný.

>[!NOTE]
>Datum vypršení platnosti tokenu nemůže být menší než hodina a ne více než jeden měsíc. Pokud nastavíte *-ExpirationTime* mimo tento limit, rutina nevytvoří token.

```powershell
New-AzWvdRegistrationInfo -ResourceGroupName <resourcegroupname> -HostPoolName <hostpoolname> -ExpirationTime $((get-date).ToUniversalTime().AddDays(1).ToString('yyyy-MM-ddTHH:mm:ss.fffffffZ'))
```

Pokud například chcete vytvořit token, jehož platnost vyprší během dvou hodin, spusťte tuto rutinu:

```powershell
New-AzWvdRegistrationInfo -ResourceGroupName <resourcegroupname> -HostPoolName <hostpoolname> -ExpirationTime $((get-date).ToUniversalTime().AddHours(2).ToString('yyyy-MM-ddTHH:mm:ss.fffffffZ'))
```

Potom spuštěním této rutiny přidejte Azure Active Directory uživatele do výchozí skupiny desktopových aplikací pro fond hostitelů.

```powershell
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <hostpoolname+"-DAG"> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

Spuštěním této další rutiny přidáte Azure Active Directory skupiny uživatelů do výchozí skupiny desktopových aplikací pro fond hostitelů:

```powershell
New-AzRoleAssignment -ObjectId <usergroupobjectid> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <hostpoolname+"-DAG"> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

Spuštěním následující rutiny exportujte registrační token do proměnné, kterou použijete později v části [registrace virtuálních počítačů do fondu hostitelů virtuálních počítačů s Windows](#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool).

```powershell
$token = Get-AzWvdRegistrationInfo -ResourceGroupName <resourcegroupname> -HostPoolName <hostpoolname>
```

## <a name="create-virtual-machines-for-the-host-pool"></a>Vytváření virtuálních počítačů pro fond hostitelů

Teď můžete vytvořit virtuální počítač Azure, který se dá připojit ke fondu hostitelů virtuálních počítačů s Windows.

Virtuální počítač můžete vytvořit několika způsoby:

- [Vytvoření virtuálního počítače z Image Galerie Azure](../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Vytvoření virtuálního počítače ze spravované image](../virtual-machines/windows/create-vm-generalized-managed.md)
- [Vytvoření virtuálního počítače z nespravované image](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-user-image-data-disks)

>[!NOTE]
>Pokud nasazujete virtuální počítač s operačním systémem Windows 7 jako hostitelský operační systém, proces vytvoření a nasazení bude trochu jiný. Další podrobnosti najdete v tématu [nasazení virtuálního počítače s Windows 7 na virtuálním počítači s Windows](./virtual-desktop-fall-2019/deploy-windows-7-virtual-machine.md).

Po vytvoření virtuálních počítačů hostitele relace [použijte licenci Windows pro virtuální počítač hostitele relace](./apply-windows-license.md#apply-a-windows-license-to-a-session-host-vm) , aby se spouštěly vaše virtuální počítače s Windows nebo Windows serverem bez placení na jinou licenci.

## <a name="prepare-the-virtual-machines-for-windows-virtual-desktop-agent-installations"></a>Příprava virtuálních počítačů pro instalace agenta virtuálních počítačů s Windows

Než budete moct nainstalovat agenty virtuálních počítačů s Windows a zaregistrovat virtuální počítače do fondu hostitelů virtuálních počítačů s Windows, musíte provést následující akce:

- Počítač musí být připojený k doméně. To umožňuje, aby se uživatelé virtuálních ploch Windows namapovali z jejich Azure Active Directory účtu na svůj účet Active Directory a úspěšně povolili přístup k tomuto virtuálnímu počítači.
- Pokud na virtuálním počítači běží operační systém Windows Server, musíte nainstalovat roli Hostitel relace vzdálené plochy (vzdálené relace). Role vzdálené plochy (hostitele) umožňuje, aby se agenti virtuálních počítačů s Windows správně nainstalovaly.

K úspěšnému připojení k doméně udělejte na každém virtuálním počítači tyto věci:

1. [Připojte se k virtuálnímu počítači](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) pomocí přihlašovacích údajů, které jste zadali při vytváření virtuálního počítače.
2. Na virtuálním počítači spusťte **Ovládací panely** a vyberte možnost **systém**.
3. Vyberte **název počítače**, vyberte **změnit nastavení** a pak zvolte **změnit...**
4. Vyberte **doména** a pak zadejte doménu služby Active Directory ve virtuální síti.
5. Proveďte ověření pomocí doménového účtu, který má oprávnění k počítačům připojeným k doméně.

    >[!NOTE]
    > Pokud se připojujete k virtuálním počítačům do prostředí Azure Active Directory Domain Services (Azure služba AD DS), ujistěte se, že je uživatel připojení k doméně také členem [skupiny správců AAD řadiče domény](../active-directory-domain-services/tutorial-create-instance-advanced.md#configure-an-administrative-group).

>[!IMPORTANT]
>Doporučujeme Nepovolit žádné zásady ani konfigurace zakazující Instalační služba systému Windows. Pokud Instalační služba systému Windows zakážete, služba nebude moci instalovat aktualizace agenta na hostitele vaší relace a hostitelé relace nebudou správně fungovat.

## <a name="register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool"></a>Zaregistrujte virtuální počítače do fondu hostitelů virtuálních počítačů s Windows.

Registrace virtuálních počítačů do fondu hostitelů virtuálních klientů Windows je jednoduché jako instalace agentů virtuálních počítačů s Windows.

Pokud chcete zaregistrovat agenty virtuálních počítačů s Windows, udělejte na každém virtuálním počítači následující:

1. [Připojte se k virtuálnímu počítači](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) pomocí přihlašovacích údajů, které jste zadali při vytváření virtuálního počítače.
2. Stáhněte a nainstalujte agenta virtuálního počítače s Windows.
   - Stáhněte si [agenta pro virtuální počítače s Windows](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrmXv).
   - Spusťte instalační program. Když instalační program požaduje registrační token, zadejte hodnotu, kterou jste získali z rutiny **Get-AzWvdRegistrationInfo** .
3. Stáhněte a nainstalujte si zaváděcí program pro Windows Virtual Desktop agent.
   - Stáhněte si [zaváděcí program pro Windows Virtual Desktop agent](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrxrH).
   - Spusťte instalační program.

>[!IMPORTANT]
>Pro lepší zabezpečení prostředí virtuálních počítačů s Windows v Azure doporučujeme na svých virtuálních počítačích neotevírat port 3389 pro příchozí spojení. Virtuální počítač s Windows nevyžaduje pro přístup k virtuálním počítačům fondu hostitelů otevřený příchozí port 3389. Pokud musíte pro účely řešení potíží otevřít port 3389, doporučujeme použít [přístup k virtuálnímu počítači za běhu](../security-center/security-center-just-in-time.md). Doporučujeme také nepřiřazovat virtuální počítače k veřejné IP adrese.

## <a name="update-the-agent"></a>Aktualizace agenta

Agenta budete muset aktualizovat, pokud jste v některé z následujících situací:

- Chcete migrovat dříve registrovaného hostitele relace do nového fondu hostitelů
- Po aktualizaci se hostitel relace nezobrazí ve fondu hostitelů.

Aktualizace agenta:

1. Přihlaste se k virtuálnímu počítači jako správce.
2. Vyhledejte **služby** a pak zastavte procesy **Rdagent** a **Remote Desktop agent Loader** .
3. Dále vyhledejte agenta a MSIs zaváděcího programu. Budou umístěny buď ve složce **C:\DeployAgent** , nebo podle umístění, do kterého jste ho uložili při instalaci.
4. Vyhledejte následující soubory a odinstalujte je:
     
     - Microsoft. RDInfra. RDAgent. Installer-x64-verx. x. x
     - Microsoft. RDInfra. RDAgentBootLoader. Installer-x64

   Pokud chcete tyto soubory odinstalovat, klikněte pravým tlačítkem na každý název souboru a vyberte **odinstalovat**.
5. Volitelně můžete také odebrat následující nastavení registru:
     
     - Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\RDInfraAgent
     - Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\RDAgentBootLoader

6. Po odinstalaci těchto položek by se měla odebrat všechna přidružení s původním fondem hostitelů. Pokud chcete tohoto hostitele znovu zaregistrovat ke službě, postupujte podle pokynů v části [registrace virtuálních počítačů do fondu hostitelů virtuálních počítačů s Windows](create-host-pools-powershell.md#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool).


## <a name="next-steps"></a>Další kroky

Teď, když jste vytvořili fond hostitelů, můžete ho naplnit pomocí vzdálených aplikací RemoteApp. Další informace o správě aplikací ve virtuálním počítači s Windows najdete v kurzu Správa skupin aplikací.

> [!div class="nextstepaction"]
> [Kurz správy skupin aplikací](./manage-app-groups.md)
