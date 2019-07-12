---
title: Přizpůsobení kanálu pro uživatele virtuální plochy Windows – Azure
description: Přizpůsobení kanálu pro uživatele Windows virtuální plochy pomocí rutin prostředí PowerShell.
services: virtual-desktop
author: v-hevem
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: v-hevem
ms.openlocfilehash: 5fe2a8b8ee5870ff7986ca2d91739f82a5128882
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2019
ms.locfileid: "67618984"
---
# <a name="customize-feed-for-windows-virtual-desktop-users"></a>Přizpůsobení kanálu pro uživatele Windows Virtual Desktopu

Informační kanál můžete přizpůsobit tak, že vzdálené aplikace RemoteApp a vzdálené ploše prostředky objevit rozpoznatelných způsobem pro vaše uživatele.

Nejprve je potřeba [stáhněte a naimportujte modul Powershellu virtuální plochy Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) použít v relaci Powershellu, pokud jste tak již neučinili.

## <a name="customize-the-display-name-for-a-remoteapp"></a>Přizpůsobení zobrazovaný název vzdálené aplikace RemoteApp

Zobrazovaný název publikované aplikace RemoteApp můžete změnit nastavením popisný název. Ve výchozím nastavení popisný název je stejný jako název programu RemoteApp.

Pokud chcete načíst seznam publikované aplikace RemoteApp pro skupinu aplikací, spusťte následující rutinu Powershellu:

```powershell
Get-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```
![Snímek obrazovky rutiny Powershellu Get-RDSRemoteApp s názvem a FriendlyName zvýrazní.](media/get-rdsremoteapp.png)

Přiřadit popisný název vzdálené aplikace RemoteApp, spusťte následující rutinu Powershellu:

```powershell
Set-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -Name <existingappname> -FriendlyName <newfriendlyname>
```
![Snímek obrazovky s rutiny prostředí PowerShell Set-RDSRemoteApp s názvem a nové FriendlyName zvýrazní.](media/set-rdsremoteapp.png)

## <a name="customize-the-display-name-for-a-remote-desktop"></a>Přizpůsobení zobrazovaný název pro vzdálenou plochu

Zobrazovaný název pro publikovaná Vzdálená plocha můžete změnit nastavením popisný název. Pokud jste ručně vytvořili hostitele fondu a desktopové aplikace skupiny prostřednictvím prostředí PowerShell, je výchozí popisný název "Desktop relace." Pokud jste vytvořili hostitele fondu a skupiny desktopovou aplikaci pomocí šablony GitHub Azure Resource Manageru nebo webu Azure Marketplace nabízí, popisný název výchozí je stejný jako název hostitele fondu.

Pokud chcete načíst vzdáleného prostředku klasické pracovní plochy, spusťte následující rutiny Powershellu:

```powershell
Get-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```
![Snímek obrazovky rutiny Powershellu Get-RDSRemoteApp s názvem a FriendlyName zvýrazní.](media/get-rdsremotedesktop.png)

Přiřadit popisný název vzdáleného prostředku klasické pracovní plochy, spusťte následující rutinu Powershellu:

```powershell
Set-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -FriendlyName <newfriendlyname>
```
![Snímek obrazovky s rutiny prostředí PowerShell Set-RDSRemoteApp s názvem a nové FriendlyName zvýrazní.](media/set-rdsremotedesktop.png)

## <a name="next-steps"></a>Další postup

Teď, když jste přizpůsobili informačního kanálu pro uživatele, můžete přihlásit ke klientovi virtuální plochy Windows na otestování. Uděláte to tak, i nadále připojit k virtuální ploše postupy Windows:
    
 * [Připojení z Windows 10 nebo Windows 7](connect-windows-7-and-10.md)
 * [Připojte se z webového prohlížeče](connect-web.md) 
