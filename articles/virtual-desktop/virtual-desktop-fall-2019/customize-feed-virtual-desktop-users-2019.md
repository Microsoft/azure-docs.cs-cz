---
title: Přizpůsobení informačního kanálu pro uživatele virtuálních ploch Windows – Azure
description: Postup přizpůsobení kanálu pro uživatele virtuálních počítačů s Windows pomocí rutin PowerShellu
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f6b956981269bca83140e4d1d08067b2d0db0d1d
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2020
ms.locfileid: "85204774"
---
# <a name="customize-feed-for-windows-virtual-desktop-users"></a>Přizpůsobení kanálu pro uživatele Windows Virtual Desktopu

>[!IMPORTANT]
>Tento obsah se vztahuje na verzi 2019, která nepodporuje Azure Resource Manager objektů virtuálních klientů Windows. Pokud se snažíte spravovat Azure Resource Manager objekty virtuálních klientů Windows, které byly zavedeny v aktualizaci jarní 2020, přečtěte si [Tento článek](../customize-feed-for-virtual-desktop-users.md).

Informační kanál můžete přizpůsobit tak, aby se prostředky RemoteApp a Vzdálená plocha zobrazovaly rozpoznatelným způsobem pro vaše uživatele.

Nejdřív [Stáhněte a importujte modul PowerShellu virtuálního počítače s Windows](/powershell/windows-virtual-desktop/overview/) , který chcete použít v relaci PowerShellu, pokud jste to ještě neudělali. Potom spuštěním následující rutiny se přihlaste ke svému účtu:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="customize-the-display-name-for-a-remoteapp"></a>Přizpůsobení zobrazovaného názvu pro aplikaci RemoteApp

Zobrazované jméno publikované aplikace RemoteApp můžete změnit nastavením popisného názvu. Ve výchozím nastavení je popisný název stejný jako název aplikace RemoteApp.

Pokud chcete načíst seznam publikovaných vzdálených aplikací RemoteApp pro skupinu aplikací, spusťte následující rutinu PowerShellu:

```powershell
Get-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```
![Snímek obrazovky rutiny PowerShellu Get-RDSRemoteApp se zvýrazněným názvem a FriendlyName.](../media/get-rdsremoteapp.png)

Chcete-li přiřadit popisný název k vzdálené aplikaci RemoteApp, spusťte následující rutinu prostředí PowerShell:

```powershell
Set-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -Name <existingappname> -FriendlyName <newfriendlyname>
```
![Snímek obrazovky rutiny PowerShellu set-RDSRemoteApp s názvem a novým výrazem FriendlyName](../media/set-rdsremoteapp.png)

## <a name="customize-the-display-name-for-a-remote-desktop"></a>Přizpůsobení zobrazovaného názvu pro vzdálenou plochu

Zobrazované jméno publikované vzdálené plochy můžete změnit nastavením popisného názvu. Pokud jste ručně vytvořili fond hostitelů a skupinu aplikací klasické pracovní plochy prostřednictvím PowerShellu, výchozí popisný název je "session Desktop". Pokud jste vytvořili fond hostitelů a skupinu aplikací klasické pracovní plochy pomocí šablony Azure Resource Manager GitHubu nebo nabídky Azure Marketplace, výchozí popisný název je stejný jako název fondu hostitelů.

Pokud chcete načíst prostředek vzdálené plochy, spusťte následující rutinu PowerShellu:

```powershell
Get-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```
![Snímek obrazovky rutiny PowerShellu Get-RDSRemoteApp se zvýrazněným názvem a FriendlyName.](../media/get-rdsremotedesktop.png)

Chcete-li přiřadit popisný název k prostředku vzdálené plochy, spusťte následující rutinu prostředí PowerShell:

```powershell
Set-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -FriendlyName <newfriendlyname>
```
![Snímek obrazovky rutiny PowerShellu set-RDSRemoteApp s názvem a novým výrazem FriendlyName](../media/set-rdsremotedesktop.png)

## <a name="next-steps"></a>Další kroky

Teď, když jste přizpůsobili informační kanál pro uživatele, se můžete přihlásit k klientovi virtuální plochy Windows a otestovat ho. Provedete to tak, že přejdete do části s postupy pro připojení k virtuálnímu počítači s Windows:

 * [Připojení z Windows 10 nebo Windows 7](../connect-windows-7-and-10.md)
 * [Připojení z webového prohlížeče](connect-web-2019.md)
