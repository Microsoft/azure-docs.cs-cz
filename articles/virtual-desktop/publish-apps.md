---
title: Publikování integrovaných aplikací ve Windows Virtual Desktop – Azure
description: Jak publikovat integrované aplikace ve Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: a697c9a62e52e82a550969e1852abd1489ed59b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127738"
---
# <a name="publish-built-in-apps-in-windows-virtual-desktop"></a>Publikování integrovaných aplikací ve Windows Virtual Desktop

V tomto článku se budete chtít publikovat aplikace v prostředí Virtuální plochy Windows.

## <a name="publish-built-in-apps"></a>Publikování integrovaných aplikací

Publikování předdefinované aplikace:

1. Připojte se k jednomu z virtuálních počítačů ve vašem hostitelském fondu.
2. Získejte **Název PackageFamilyName** aplikace, kterou chcete publikovat podle pokynů v [tomto článku](/powershell/module/appx/get-appxpackage?view=win10-ps/).
3. Nakonec spusťte následující rutinu nahrazenou `<PackageFamilyName>` **packagefamilyname,** kterou jste našli v předchozím kroku:
   
   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -FriendlyName <remoteappname> -FilePath "shell:appsFolder\<PackageFamilyName>!App"
   ```

>[!NOTE]
> Virtuální plocha Systému Windows podporuje publikování `C:\Program Files\Windows Apps`pouze aplikací s umístěními instalace začínajícími na .

## <a name="update-app-icons"></a>Aktualizace ikon aplikací

Po publikování aplikace bude mít místo běžného obrázku ikony výchozí ikonu aplikace pro Windows. Chcete-li změnit ikonu na její běžnou ikonu, vložte obrázek ikony, kterou chcete, do sdílené síťové složky. Podporované formáty obrázků jsou PNG, BMP, GIF, JPG, JPEG a ICO.

## <a name="publish-microsoft-edge"></a>Publikování microsoft edge

Proces, který používáte k publikování Microsoft Edge, se trochu liší od procesu publikování pro jiné aplikace. Chcete-li publikovat aplikaci Microsoft Edge s výchozí domovskou stránkou, spusťte tuto rutinu:

```powershell
New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -FriendlyName <remoteappname> -FilePath "shell:Appsfolder\Microsoft.MicrosoftEdge_8wekyb3d8bbwe!MicrosoftEdge" 
```

## <a name="next-steps"></a>Další kroky

- Informace o konfiguraci informačních kanálů pro uspořádání způsobu zobrazení aplikací pro uživatele najdete v [na jdete na webu Přizpůsobit informační kanál pro uživatele virtuální chod Windows](customize-feed-for-virtual-desktop-users.md).
- Další informace o funkci připojení aplikace MSIX na [jdete nastavit aplikaci MSIX připojit](app-attach.md).

