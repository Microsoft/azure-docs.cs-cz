---
title: Vytvoření fondu hostitele s prostředím PowerShell (preview) – Azure
description: Postup vytvoření fondu hostitele v virtuální plochy Windows pomocí rutin prostředí PowerShell.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 4b65d7614db94a9cc3fdca3f4b784c2c84ebaef8
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/21/2019
ms.locfileid: "58318536"
---
# <a name="create-a-host-pool-with-powershell-preview"></a>Vytvoření fondu hostitele s prostředím PowerShell (Preview)

Hostitel fondy jsou kolekce jednoho nebo víc stejných virtuálních počítačů v rámci tenanta (preview) prostředí virtuálního klienta Windows. Každý hostitel fond může obsahovat skupinu aplikací, které mohou uživatelé komunikovat s stejně jako ve fyzických stolním počítači.

## <a name="use-your-powershell-client-to-create-a-host-pool"></a>Použití klienta Powershellu k vytvoření fondu hostitele

Nejprve je potřeba [stáhněte a naimportujte modul Powershellu virtuální plochy Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) použít v relaci Powershellu, pokud jste tak již neučinili.

Spuštěním následující rutiny pro přihlášení k prostředí virtuálního klienta Windows

```powershell
Add-RdsAccount -DeploymentUrl https://rdbroker.wvd.microsoft.com
```

Potom spusťte následující rutinu k nastavení kontextu u vaší skupiny pro tenanta. Pokud nemáte k dispozici název skupiny pro tenanta, váš tenant je pravděpodobně v "Výchozí Tenanta skupinu,", takže přeskočíte Tato rutina.

```powershell
Set-RdsContext -TenantGroupName <tenantgroupname>
```

V dalším kroku spusťte tuto rutinu k vytvoření nového fondu hostitele ve vašem tenantovi virtuální plochy Windows:

```powershell
New-RdsHostPool -TenantName <tenantname> -Name <hostpoolname>
```

Další rutiny k vytvoření tokenu registrace k autorizaci hostitele relace připojí k fondu hostitele a uložte ho do nového souboru na místním počítači. Můžete určit, jak dlouho je registrační token platný, pomocí parametru - ExpirationHours.

```powershell
New-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname> -ExpirationHours <number of hours> | Select-Object -ExpandProperty Token > <PathToRegFile>
```

Potom spusťte tuto rutinu přidat uživatele Azure Active Directory do výchozí skupiny aplikace klasické pracovní plochy pro fond hostitele.

```powershell
Add-RdsAppGroupUser -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName "Desktop Application Group" -UserPrincipalName <userupn>
```

**Přidat RdsAppGroupUser** rutina nepodporuje přidávání skupin zabezpečení a přidá pouze jeden uživatel v čase do skupiny aplikací. Pokud chcete přidat více uživatelů na skupinu aplikací, spusťte rutinu s hlavních názvů uživatelů odpovídající znovu.

Spuštěním následující rutiny můžete exportovat registrační token na proměnnou, kterou použijete později v [registraci virtuálních počítačů do hostitelů fondu virtuální plochy Windows](#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool).

```powershell
$token = (Export-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname>).Token
```

## <a name="create-virtual-machines-for-the-host-pool"></a>Vytvoření virtuálního počítače pro fond hostitele

Nyní můžete vytvořit virtuální počítač Azure, který může být připojen k hostiteli fondu virtuální plochy Windows.

Vytvoření virtuálního počítače v několika způsoby:

- [Vytvoření virtuálního počítače z image Galerie Azure](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#create-virtual-machine)
- [Vytvoření virtuálního počítače ze spravované image](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-generalized-managed)
- [Vytvoření virtuálního počítače z nespravované image](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

## <a name="prepare-the-virtual-machines-for-windows-virtual-desktop-agent-installations"></a>Příprava virtuálních počítačů pro virtuální plochy Windows instalace agentů

Je třeba provést následující kroky, kterými Příprava virtuálních počítačů, aby bylo možné instalovat agenty virtuální plochy Windows a registraci virtuálních počítačů do hostitelů fondu virtuální plochy Windows:

- Je nutné připojení k doméně počítače. To umožňuje příchozí uživatelé virtuální plochy Windows ze svého účtu Azure Active Directory mapovat na svůj účet služby Active Directory a mít přístup k virtuálnímu počítači úspěšně povolený.
- Pokud virtuální počítač je spuštěný operační systém Windows Server, je nutné nainstalovat roli hostitel relace vzdálené plochy (RDSH) (preview). RDSH role umožňuje agentů virtuálního klienta Windows správně nainstalovat.

Pro úspěšné připojení k doméně proveďte následující akce na každém virtuálním počítači:

1. [Připojení k virtuálnímu počítači](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine) s přihlašovacími údaji, které jste zadali při vytváření virtuálního počítače.
2. Na virtuálním počítači spusťte **ovládací panely** a vyberte **systému**.
3. Vyberte **název_počítače**vyberte **změnit nastavení**a pak vyberte **změn...**
4. Vyberte **domény** a pak zadejte doménu služby Active Directory ve virtuální síti.
5. Ověření pomocí účtu domény, který má oprávnění k připojení k doméně počítače.

## <a name="register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool"></a>Registrace virtuálních počítačů do hostitelů fondu virtuálního klienta Windows

Registrace virtuálních počítačů do hostitelů fondu virtuální plochy Windows je stejně jednoduché jako instalace agentů virtuálního klienta Windows.

K registraci agentů virtuálního klienta Windows, proveďte postup na každý virtuální počítač:

1. [Připojení k virtuálnímu počítači](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine) s přihlašovacími údaji, které jste zadali při vytváření virtuálního počítače.
2. Stáhněte a nainstalujte agenta virtuální plochy Windows.
   - Stáhněte si [virtuální plochy agenta Windows](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrmXv).
   - Klikněte pravým tlačítkem na stažený instalační program, vyberte **vlastnosti**vyberte **Odblokovat**a pak vyberte **OK**. To vám umožní váš systém důvěřovat Instalační služby.
   - Spusťte instalační program. Pokud jste instalační program vyzve k zadání registračního tokenu, zadejte hodnotu jste získali z **Export RdsRegistrationInfo** rutiny.
3. Stáhněte a nainstalujte agenta zaváděcím virtuální plochy Windows.
   - Stáhněte si [spouštěcí zavaděč virtuální plochy agenta Windows](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrxrH).
   - Klikněte pravým tlačítkem na stažený instalační program, vyberte **vlastnosti**vyberte **Odblokovat**a pak vyberte **OK**. To vám umožní váš systém důvěřovat Instalační služby.
   - Spusťte instalační program.
4. Nainstalujte nebo aktivovat zásobníku vedle sebe virtuální plochy Windows. Postup se bude lišit v závislosti na tom, jaké verze operačního systému virtuálního počítače používá.
   - Je-li operační systém virtuálního počítače Windows serveru 2016:
     - Stáhněte si [virtuální plochy Windows vedle sebe zásobníku](https://go.microsoft.com/fwlink/?linkid=2084270).
     - Klikněte pravým tlačítkem na stažený instalační program, vyberte **vlastnosti**vyberte **Odblokovat**a pak vyberte **OK**. To vám umožní váš systém důvěřovat Instalační služby.
     - Spusťte instalační program.
   - Pokud je operační systém virtuálního počítače Windows 10 1809 nebo novější a Windows Server 2019 nebo novější:
     - Stáhněte si [skript](https://go.microsoft.com/fwlink/?linkid=2084268) aktivovat zásobníku vedle sebe.
     - Klikněte pravým tlačítkem na staženého skriptu, vyberte **vlastnosti**vyberte **Odblokovat**a pak vyberte **OK**. To vám umožní váš systém důvěřovat skriptu.
     - Z **Start** nabídky, vyhledejte Windows PowerShell ISE, pravým tlačítkem myši a potom vyberte **spustit jako správce**.
     - Vyberte **souboru**, pak **otevřít...** a potom vyhledejte Powershellový skript z stažené soubory a otevřete ho.
     - Vyberte tlačítko Přehrát zelená pro spuštění skriptu.

## <a name="next-steps"></a>Další postup

Teď, když jste provedli fondu hostitele, je čas na přidání aplikace RemoteApp (preview). Další informace o tom, jak spravovat aplikace v virtuální plochy Windows, najdete v kurzu skupiny spravovat aplikace.

> [!div class="nextstepaction"]
> [Správa kurz skupiny aplikací](./manage-app-groups.md)
