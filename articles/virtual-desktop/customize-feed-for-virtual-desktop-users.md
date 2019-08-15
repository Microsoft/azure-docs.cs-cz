---
title: Přizpůsobení informačního kanálu pro uživatele virtuálních ploch Windows – Azure
description: Postup přizpůsobení kanálu pro uživatele virtuálních počítačů s Windows pomocí rutin PowerShellu
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: helohr
ms.openlocfilehash: 40fd863d9ad92b91b8e691fbb7be233e16b82c17
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/12/2019
ms.locfileid: "68950754"
---
# <a name="customize-feed-for-windows-virtual-desktop-users"></a>Přizpůsobení kanálu pro uživatele Windows Virtual Desktopu

Informační kanál můžete přizpůsobit tak, aby se prostředky RemoteApp a Vzdálená plocha zobrazovaly rozpoznatelným způsobem pro vaše uživatele.

Nejdřív [Stáhněte a importujte modul PowerShellu virtuálního počítače s Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) , který chcete použít v relaci PowerShellu, pokud jste to ještě neudělali.

## <a name="customize-the-display-name-for-a-remoteapp"></a>Přizpůsobení zobrazovaného názvu pro aplikaci RemoteApp

Zobrazované jméno publikované aplikace RemoteApp můžete změnit nastavením popisného názvu. Ve výchozím nastavení je popisný název stejný jako název aplikace RemoteApp.

Pokud chcete načíst seznam publikovaných vzdálených aplikací RemoteApp pro skupinu aplikací, spusťte následující rutinu PowerShellu:

```powershell
Get-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```
![Snímek obrazovky rutiny PowerShellu Get-RDSRemoteApp se zvýrazněným názvem a FriendlyName.](media/get-rdsremoteapp.png)

Chcete-li přiřadit popisný název k vzdálené aplikaci RemoteApp, spusťte následující rutinu prostředí PowerShell:

```powershell
Set-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -Name <existingappname> -FriendlyName <newfriendlyname>
```
![Snímek obrazovky rutiny PowerShellu set-RDSRemoteApp s názvem a novým výrazem FriendlyName](media/set-rdsremoteapp.png)

## <a name="customize-the-display-name-for-a-remote-desktop"></a>Přizpůsobení zobrazovaného názvu pro vzdálenou plochu

Zobrazované jméno publikované vzdálené plochy můžete změnit nastavením popisného názvu. Pokud jste ručně vytvořili fond hostitelů a skupinu aplikací klasické pracovní plochy prostřednictvím PowerShellu, výchozí popisný název je "session Desktop". Pokud jste vytvořili fond hostitelů a skupinu aplikací klasické pracovní plochy pomocí šablony Azure Resource Manager GitHubu nebo nabídky Azure Marketplace, výchozí popisný název je stejný jako název fondu hostitelů.

Pokud chcete načíst prostředek vzdálené plochy, spusťte následující rutinu PowerShellu:

```powershell
Get-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```
![Snímek obrazovky rutiny PowerShellu Get-RDSRemoteApp se zvýrazněným názvem a FriendlyName.](media/get-rdsremotedesktop.png)

Chcete-li přiřadit popisný název k prostředku vzdálené plochy, spusťte následující rutinu prostředí PowerShell:

```powershell
Set-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -FriendlyName <newfriendlyname>
```
![Snímek obrazovky rutiny PowerShellu set-RDSRemoteApp s názvem a novým výrazem FriendlyName](media/set-rdsremotedesktop.png)

## <a name="next-steps"></a>Další postup

Teď, když jste přizpůsobili informační kanál pro uživatele, se můžete přihlásit k klientovi virtuální plochy Windows a otestovat ho. Provedete to tak, že přejdete do části s postupy pro připojení k virtuálnímu počítači s Windows:
    
 * [Připojení ze systému Windows 10 nebo Windows 7](connect-windows-7-and-10.md)
 * [Připojení z webového prohlížeče](connect-web.md) 
