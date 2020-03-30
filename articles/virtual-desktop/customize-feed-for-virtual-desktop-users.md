---
title: Přizpůsobení informačního kanálu pro uživatele Virtuální chod Windows – Azure
description: Jak přizpůsobit informační kanál pro uživatele virtuální chodnících prostředí Windows pomocí rutin prostředí PowerShell.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 24a295d220cfaa7efe2fdc0d4eee53bb5c409708
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128078"
---
# <a name="customize-feed-for-windows-virtual-desktop-users"></a>Přizpůsobení kanálu pro uživatele Windows Virtual Desktopu

Zdroj můžete přizpůsobit tak, aby se prostředky RemoteApp a vzdálené plochy zobrazovaly uživatelům rozpoznatelným způsobem.

Nejprve [si stáhněte a importujte modul Windows Virtual Desktop PowerShell,](/powershell/windows-virtual-desktop/overview/) který se použije v relaci PowerShellu, pokud jste tak ještě neučinili. Poté spusťte následující rutinu a přihlaste se ke svému účtu:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="customize-the-display-name-for-a-remoteapp"></a>Přizpůsobení zobrazované ho lsti

Zobrazovaný název publikované aplikace RemoteApp můžete změnit nastavením popisného názvu. Ve výchozím nastavení je popisný název stejný jako název programu RemoteApp.

Chcete-li načíst seznam publikovaných aplikací RemoteApps pro skupinu aplikací, spusťte následující rutinu prostředí PowerShell:

```powershell
Get-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```
![Snímek obrazovky rutiny prostředí PowerShell Get-RDSRemoteApp se zvýrazněným názvem a popisným názvem.](media/get-rdsremoteapp.png)

Chcete-li aplikaci RemoteApp přiřadit popisný název, spusťte následující rutinu prostředí PowerShell:

```powershell
Set-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -Name <existingappname> -FriendlyName <newfriendlyname>
```
![Snímek obrazovky rutiny PowerShell Set-RDSRemoteApp se zvýrazněným názvem a novým popisným názvem.](media/set-rdsremoteapp.png)

## <a name="customize-the-display-name-for-a-remote-desktop"></a>Přizpůsobení zobrazované ho diody vzdálené plochy

Zobrazovaný název publikované vzdálené plochy můžete změnit nastavením popisného názvu. Pokud jste ručně vytvořili fond hostitelů a skupinu aplikací klasické pracovní plochy prostřednictvím prostředí PowerShell, výchozí popisný název je Plocha relace. Pokud jste vytvořili fond hostitelů a skupinu aplikací pro stolní počítače prostřednictvím šablony GitHub Azure Resource Manager nebo nabídky Azure Marketplace, výchozí popisný název je stejný jako název fondu hostitele.

Chcete-li načíst prostředek vzdálené plochy, spusťte následující rutinu prostředí PowerShell:

```powershell
Get-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```
![Snímek obrazovky rutiny prostředí PowerShell Get-RDSRemoteApp se zvýrazněným názvem a popisným názvem.](media/get-rdsremotedesktop.png)

Chcete-li prostředku vzdálené plochy přiřadit popisný název, spusťte následující rutinu prostředí PowerShell:

```powershell
Set-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -FriendlyName <newfriendlyname>
```
![Snímek obrazovky rutiny PowerShell Set-RDSRemoteApp se zvýrazněným názvem a novým popisným názvem.](media/set-rdsremotedesktop.png)

## <a name="next-steps"></a>Další kroky

Nyní, když jste přizpůsobili informační kanál pro uživatele, můžete se přihlásit ke klientovi virtuální plochy windows a vyzkoušet si ho. Chcete-li tak učinit, pokračujte v návodech pro připojení k virtuální ploše systému Windows:
    
 * [Připojení z Windows 10 nebo Windows 7](connect-windows-7-and-10.md)
 * [Připojení z webového prohlížeče](connect-web.md) 
