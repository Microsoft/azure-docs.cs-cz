---
title: Správa skupiny aplikací pro Windows Virtual Desktop Preview – Azure
description: Popisuje, jak nastavit službu Windows Virtual Desktop Preview tenantů ve službě Azure Active Directory.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: f0cdd28be8c6e7390aa26fdc2dfbf32ec5542c2d
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2019
ms.locfileid: "65233900"
---
# <a name="tutorial-manage-app-groups-for-windows-virtual-desktop-preview"></a>Kurz: Správa skupiny aplikací pro Windows Virtual Desktop Preview

Výchozí skupina aplikace vytvořené pro nový fond hostitele Windows virtuální plochy, ve verzi Preview, publikuje také úplný desktop. Kromě toho můžete vytvořit jeden nebo více skupin aplikací RemoteApp pro fond hostitele. V tomto kurzu vytvořte skupinu aplikací RemoteApp a publikování aplikací pro jednotlivé nabídky Start.

V tomto kurzu se dozvíte, jak:

> [!div class="checklist"]
> * Vytvoření skupiny Vzdálená aplikace RemoteApp.
> * Udělení přístupu k aplikace RemoteApp.

Než začnete, [stáhněte a naimportujte modul Powershellu virtuální plochy Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) modulu virtuálního klienta Windows v relaci Powershellu použijte v případě, že jste to ještě neudělali.

## <a name="create-a-remoteapp-group"></a>Vytvoření skupiny Vzdálená aplikace RemoteApp

1. Spusťte následující rutinu prostředí PowerShell k vytvoření nové prázdné skupiny aplikací RemoteApp.

   ```powershell
   New-RdsAppGroup <tenantname> <hostpoolname> <appgroupname> -ResourceType "RemoteApp"
   ```

2. (Volitelné) Chcete-li ověřit, zda že byla vytvořena skupina aplikací, spuštěním následující rutiny můžete zobrazit seznam všech skupin aplikací pro fond hostitele.

   ```powershell
   Get-RdsAppGroup <tenantname> <hostpoolname>
   ```

3. Spuštěním následující rutiny můžete získat seznam spuštění nabídky aplikace v hostiteli fondu image virtuálního počítače. Poznamenejte si hodnoty pro **FilePath**, **IconPath**, **IconIndex**a další důležité informace pro aplikaci, kterou chcete publikovat.

   ```powershell
   Get-RdsStartMenuApp <tenantname> <hostpoolname> <appgroupname>
   ```
   
4. Spusťte následující rutinu k instalaci aplikace založené na jeho appalias. appalias viditelná, když spustíte výstup z kroku 3.

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -AppAlias <appalias>
   ```

5. (Volitelné) Spusťte následující rutinu k publikování nové vzdálené aplikace RemoteApp na skupinu aplikací vytvořili v kroku 1.

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -Filepath <filepath>  -IconPath <iconpath> -IconIndex <iconindex>
   ```

6. Pokud chcete ověřit, že aplikace byla publikována, spusťte následující rutinu.

   ```powershell
   Get-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname>
   ```

7. Opakujte kroky 1 až 5 pro každou aplikaci, kterou chcete publikovat pro tuto skupinu aplikací.
8. Spuštěním následující rutiny můžete uživatelům udělit přístup k aplikace RemoteApp ve skupině aplikací.

   ```powershell
   Add-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname> -UserPrincipalName <userupn>
   ```

## <a name="next-steps"></a>Další postup

V tomto kurzu jste zjistili, jak vytvořit skupiny aplikací, přidejte do ní aplikace RemoteApp a přiřadit uživatele do skupiny aplikací. Další informace o tom, jak se přihlásit k virtuálnímu klientovi Windows, i nadále připojit k virtuální ploše postupy Windows.

- [Připojte se ke klientovi vzdálené plochy na Windows 7 a Windows 10](connect-windows-7-and-10.md)
- [Připojte se k Windows Virtual Desktop Preview webového klienta](connect-web.md)
