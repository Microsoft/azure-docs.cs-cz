---
title: Publikování integrovaných aplikací ve virtuálním počítači s Windows (Classic) – Azure
description: Publikování integrovaných aplikací ve virtuálním počítači s Windows (Classic)
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 5f204bc6d2595e7f18e59bae15ff35d49e1801e0
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/28/2020
ms.locfileid: "87284927"
---
# <a name="publish-built-in-apps-in-windows-virtual-desktop-classic"></a>Publikování integrovaných aplikací ve virtuálním počítači s Windows (Classic)

>[!IMPORTANT]
>Tento obsah se vztahuje na virtuální plochu Windows (Classic), která nepodporuje Azure Resource Manager objektů virtuálních klientů Windows. Pokud se snažíte spravovat Azure Resource Manager objektů virtuálních klientů Windows, přečtěte si [Tento článek](../publish-apps.md).

V tomto článku se dozvíte, jak publikovat aplikace v prostředí virtuálních počítačů s Windows.

## <a name="publish-built-in-apps"></a>Publikování integrovaných aplikací

Publikování předdefinované aplikace:

1. Připojte se k jednomu z virtuálních počítačů ve fondu hostitelů.
2. Postupujte podle pokynů v [tomto článku](/powershell/module/appx/get-appxpackage?view=win10-ps/)a získejte **PackageFamilyName** aplikace, kterou chcete publikovat.
3. Nakonec spusťte následující rutinu s `<PackageFamilyName>` nahrazeným **PackageFamilyName** , které jste našli v předchozím kroku:

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -FriendlyName <remoteappname> -FilePath "shell:appsFolder\<PackageFamilyName>!App"
   ```

>[!NOTE]
> Virtuální plocha Windows podporuje jenom publikování aplikací s umístěními pro instalaci, která začínají na `C:\Program Files\Windows Apps` .

## <a name="update-app-icons"></a>Aktualizovat ikony aplikace

Po publikování aplikace bude mít výchozí ikonu aplikace pro Windows namísto obrázku běžné ikony. Chcete-li ikonu změnit na její běžnou ikonu, umístěte obrázek požadované ikony do sdílené síťové složky. Podporované formáty obrázků jsou PNG, BMP, GIF, JPG, JPEG a ICO.

## <a name="publish-microsoft-edge"></a>Publikovat Microsoft Edge

Proces, který používáte k publikování Microsoft Edge, se trochu liší od procesu publikování pro jiné aplikace. Pokud chcete publikovat Microsoft Edge s výchozí domovskou stránkou, spusťte tuto rutinu:

```powershell
New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -FriendlyName <remoteappname> -FilePath "shell:Appsfolder\Microsoft.MicrosoftEdge_8wekyb3d8bbwe!MicrosoftEdge"
```

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak nakonfigurovat informační kanály pro uspořádání, jak se zobrazují aplikace uživatelům v tématu [přizpůsobení informačního kanálu pro uživatele virtuální plochy Windows](customize-feed-virtual-desktop-users-2019.md).
- Přečtěte si o funkci připojení aplikace MSIX v [nastavení připojení aplikace MSIX](../app-attach.md).

