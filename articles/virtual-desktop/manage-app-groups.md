---
title: Správa skupiny aplikací pro Windows Virtual Desktop Preview – Azure
description: Popisuje, jak nastavit službu Windows Virtual Desktop Preview tenantů ve službě Azure Active Directory.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 2baabe6837d25cce5e9f5d9e1071af8417fe5f4d
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/25/2019
ms.locfileid: "58401858"
---
# <a name="tutorial-manage-app-groups-for-windows-virtual-desktop-preview"></a>Kurz: Správa skupiny aplikací pro Windows Virtual Desktop Preview

Výchozí skupina aplikace vytvořené pro nový fond hostitele Windows virtuální plochy, ve verzi Preview, publikuje také úplný desktop. Kromě toho můžete vytvořit jeden nebo více skupin aplikací RemoteApp pro fond hostitele. V tomto kurzu vytvořte skupinu aplikací RemoteApp a publikování aplikací pro jednotlivé nabídky Start.

V tomto kurzu získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření skupiny Vzdálená aplikace RemoteApp.
> * Udělení přístupu k aplikace RemoteApp.

Než začnete, [stáhněte a naimportujte modul Powershellu virtuální plochy Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) modulu virtuálního klienta Windows v relaci Powershellu použijte v případě, že jste to ještě neudělali.

## <a name="create-a-remoteapp-group"></a>Vytvoření skupiny Vzdálená aplikace RemoteApp

1. Spusťte následující rutinu prostředí PowerShell k vytvoření nové prázdné skupiny Vzdálená aplikace RemoteApp.

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

4. Spusťte následující rutinu k publikování nové vzdálené aplikace RemoteApp na skupinu aplikací vytvořili v kroku 1.

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> <remoteappname> -Filepath <filepath>  -IconPath <iconpath> -IconIndex <iconindex>
   ```

5. (Volitelné) Spusťte následující rutinu k instalaci aplikace založená na knihovnách appalias. appalias viditelná, když spustíte výstup z kroku 3.

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> <remoteappname> -AppAlias <appalias>
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

Po vytvoření skupiny vaší aplikace, vytvoření instančních objektů a přiřazovat role uživatelům. Zjistěte, jak to provést, najdete v kurzu pro vytvoření instančních objektů a přiřazení role pomocí prostředí PowerShell.

> [!div class="nextstepaction"]
> [Vytvoření instančních objektů a přiřazení role pomocí prostředí PowerShell](create-service-principal-role-powershell.md)
