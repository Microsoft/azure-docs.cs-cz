---
title: Publikování integrovaných aplikací ve virtuálním počítači s Windows – Azure
description: Publikování integrovaných aplikací ve virtuálním počítači s Windows
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: a697c9a62e52e82a550969e1852abd1489ed59b9
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2020
ms.locfileid: "79127738"
---
# <a name="publish-built-in-apps-in-windows-virtual-desktop"></a>Publikování integrovaných aplikací ve virtuálním počítači s Windows

V tomto článku se dozvíte, jak publikovat aplikace v prostředí virtuálních počítačů s Windows.

## <a name="publish-built-in-apps"></a>Publikování integrovaných aplikací

Publikování předdefinované aplikace:

1. Připojte se k jednomu z virtuálních počítačů ve fondu hostitelů.
2. Postupujte podle pokynů v [tomto článku](/powershell/module/appx/get-appxpackage?view=win10-ps/)a získejte **PackageFamilyName** aplikace, kterou chcete publikovat.
3. Nakonec spusťte následující rutinu s `<PackageFamilyName>` nahrazenou **PackageFamilyName** , kterou jste našli v předchozím kroku:
   
   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -FriendlyName <remoteappname> -FilePath "shell:appsFolder\<PackageFamilyName>!App"
   ```

>[!NOTE]
> Virtuální desktop Windows podporuje publikování jenom aplikací s umístěními pro instalaci, která začínají na `C:\Program Files\Windows Apps`.

## <a name="update-app-icons"></a>Aktualizovat ikony aplikace

Po publikování aplikace bude mít výchozí ikonu aplikace pro Windows namísto obrázku běžné ikony. Chcete-li ikonu změnit na její běžnou ikonu, umístěte obrázek požadované ikony do sdílené síťové složky. Podporované formáty obrázků jsou PNG, BMP, GIF, JPG, JPEG a ICO.

## <a name="publish-microsoft-edge"></a>Publikovat Microsoft Edge

Proces, který používáte k publikování Microsoft Edge, se trochu liší od procesu publikování pro jiné aplikace. Pokud chcete publikovat Microsoft Edge s výchozí domovskou stránkou, spusťte tuto rutinu:

```powershell
New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -FriendlyName <remoteappname> -FilePath "shell:Appsfolder\Microsoft.MicrosoftEdge_8wekyb3d8bbwe!MicrosoftEdge" 
```

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak nakonfigurovat informační kanály pro uspořádání, jak se zobrazují aplikace uživatelům v tématu [přizpůsobení informačního kanálu pro uživatele virtuální plochy Windows](customize-feed-for-virtual-desktop-users.md).
- Přečtěte si o funkci připojení aplikace MSIX v [nastavení připojení aplikace MSIX](app-attach.md).

