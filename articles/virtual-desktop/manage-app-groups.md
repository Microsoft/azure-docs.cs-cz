---
title: Správa skupin aplikací pro Virtuální plochu Windows – Azure
description: Popisuje, jak nastavit klienty virtuální plochy Windows ve službě Azure Active Directory.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 08/29/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 9a9d92ea525c6b5a64fdf7cc74babdce6a97f923
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79127807"
---
# <a name="tutorial-manage-app-groups-for-windows-virtual-desktop"></a>Kurz: Správa skupin aplikací pro Virtuální plochu Windows

Výchozí skupina aplikací vytvořená pro nový fond hostitelů virtuální plochy Windows také publikuje celou plochu. Kromě toho můžete vytvořit jednu nebo více skupin aplikací RemoteApp pro fond hostitelů. Podle tohoto kurzu vytvořte skupinu aplikací RemoteApp a publikujte jednotlivé aplikace nabídky **Start.**

V tomto kurzu získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvořte skupinu RemoteApp.
> * Udělte přístup k programům RemoteApp.

Než začnete, [stáhněte a importujte modul Windows Virtual Desktop PowerShell,](/powershell/windows-virtual-desktop/overview/) který se použije v relaci PowerShellu, pokud jste tak ještě neučinili. Poté spusťte následující rutinu a přihlaste se ke svému účtu:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="create-a-remoteapp-group"></a>Vytvoření skupiny RemoteApp

1. Spusťte následující rutinu prostředí PowerShell a vytvořte novou prázdnou skupinu aplikací RemoteApp.

   ```powershell
   New-RdsAppGroup <tenantname> <hostpoolname> <appgroupname> -ResourceType "RemoteApp"
   ```

2. (Nepovinné) Chcete-li ověřit, že skupina aplikací byla vytvořena, můžete spustit následující rutinu a zobrazit seznam všech skupin aplikací pro fond hostitelů.

   ```powershell
   Get-RdsAppGroup <tenantname> <hostpoolname>
   ```

3. Spusťte následující rutinu a získejte seznam aplikací nabídky **Start** na bitové kopii virtuálního počítače fondu hostitelů. Poznamenejte si hodnoty pro **FilePath**, **IconPath**, **IconIndex**a další důležité informace pro aplikaci, kterou chcete publikovat.

   ```powershell
   Get-RdsStartMenuApp <tenantname> <hostpoolname> <appgroupname>
   ```
   
4. Spusťte následující rutinu a nainstalujte aplikaci založenou na aplikaci `AppAlias`. `AppAlias`se zobrazí při spuštění výstupu z kroku 3.

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -AppAlias <appalias>
   ```

5. (Nepovinné) Spusťte následující rutinu a publikujte nový program RemoteApp do skupiny aplikací vytvořené v kroku 1.

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -Filepath <filepath>  -IconPath <iconpath> -IconIndex <iconindex>
   ```

6. Chcete-li ověřit, zda byla aplikace publikována, spusťte následující rutinu.

   ```powershell
   Get-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname>
   ```

7. Opakujte kroky 1–5 pro každou aplikaci, kterou chcete publikovat pro tuto skupinu aplikací.
8. Spusťte následující rutinu, která uživatelům udělí přístup k programům RemoteApp ve skupině aplikací.

   ```powershell
   Add-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname> -UserPrincipalName <userupn>
   ```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili, jak vytvořit skupinu aplikací, naplnit ji programy RemoteApp a přiřadit uživatele ke skupině aplikací. Informace o tom, jak vytvořit fond hostitelů ověření, naleznete v následujícím kurzu. Fond hostitelů ověření můžete použít ke sledování aktualizací služeb před jejich zavedením do produkčního prostředí.

> [!div class="nextstepaction"]
> [Vytvoření fondu hostitelů pro ověření aktualizací služeb](./create-validation-host-pool.md)
