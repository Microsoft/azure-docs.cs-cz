---
title: Publikování integrovaných aplikací ve virtuálním počítači s Windows – Azure
description: Publikování integrovaných aplikací ve virtuálním počítači s Windows
author: Heidilohr
ms.topic: how-to
ms.date: 04/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 68db8e9d50dff0bb4580476bae990374830a2a1f
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106445818"
---
# <a name="publish-built-in-apps-in-windows-virtual-desktop"></a>Publikování integrovaných aplikací ve virtuálním počítači s Windows

>[!IMPORTANT]
>Tento obsah se vztahuje na virtuální plochu Windows s Azure Resource Manager objekty virtuálních klientů Windows. Pokud používáte virtuální plochu Windows (Classic) bez Azure Resource Manager objektů, přečtěte si [Tento článek](./virtual-desktop-fall-2019/publish-apps-2019.md).

V tomto článku se dozvíte, jak publikovat aplikace v prostředí virtuálních počítačů s Windows.

## <a name="publish-built-in-apps"></a>Publikování integrovaných aplikací

Publikování předdefinované aplikace:

1. Připojte se k jednomu z virtuálních počítačů ve fondu hostitelů.
2. Postupujte podle pokynů v [tomto článku](/powershell/module/appx/get-appxpackage)a získejte **PackageFamilyName** aplikace, kterou chcete publikovat.
3. Nakonec spusťte následující rutinu s `<PackageFamilyName>` nahrazeným **PackageFamilyName** , které jste našli v předchozím kroku:

   ```powershell
   New-AzWvdApplication -Name <applicationname> -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname> -FilePath "shell:appsFolder\<PackageFamilyName>!App" -CommandLineSetting <Allow|Require|DoNotAllow> -IconIndex 0 -IconPath <iconpath> -ShowInPortal:$true
   ```

>[!NOTE]
> Virtuální plocha Windows podporuje jenom publikování aplikací s umístěními pro instalaci, která začínají na `C:\Program Files\WindowsApps` .

## <a name="update-app-icons"></a>Aktualizovat ikony aplikace

Po publikování aplikace bude mít výchozí ikonu aplikace pro Windows namísto obrázku běžné ikony. Chcete-li ikonu změnit na její běžnou ikonu, umístěte obrázek požadované ikony do sdílené síťové složky. Podporované formáty obrázků jsou PNG, BMP, GIF, JPG, JPEG a ICO.

## <a name="publish-microsoft-edge"></a>Publikovat Microsoft Edge

Proces, který používáte k publikování Microsoft Edge, se trochu liší od procesu publikování pro jiné aplikace. Pokud chcete publikovat Microsoft Edge s výchozí domovskou stránkou, spusťte tuto rutinu:

```powershell
New-AzWvdApplication -Name -ResourceGroupName -ApplicationGroupName -FilePath "shell:Appsfolder\Microsoft.MicrosoftEdge_8wekyb3d8bbwe!MicrosoftEdge" -CommandLineSetting <Allow|Require|DoNotAllow> -iconPath "C:\Windows\SystemApps\Microsoft.MicrosoftEdge_8wekyb3d8bbwe\microsoftedge.exe" -iconIndex 0 -ShowInPortal:$true
```

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak nakonfigurovat informační kanály pro uspořádání, jak se zobrazují aplikace uživatelům v tématu [přizpůsobení informačního kanálu pro uživatele virtuální plochy Windows](customize-feed-for-virtual-desktop-users.md).
- Přečtěte si o funkci připojení aplikace MSIX v [nastavení připojení aplikace MSIX](app-attach.md).

