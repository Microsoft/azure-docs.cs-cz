---
title: Správa skupiny aplikací pro Windows Virtual Desktop Preview – Azure
description: Popisuje, jak nastavit službu Windows Virtual Desktop Preview tenantů ve službě Azure Active Directory.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 73425df1f0cfedd2a681650fc2b536a652b621d5
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67206680"
---
# <a name="tutorial-manage-app-groups-for-windows-virtual-desktop-preview"></a>Kurz: Správa skupiny aplikací pro Windows Virtual Desktop Preview

Výchozí skupina aplikace vytvořené pro nový fond hostitele Windows virtuální plochy, ve verzi Preview, publikuje také úplný desktop. Kromě toho můžete vytvořit jeden nebo více skupin aplikací RemoteApp pro fond hostitele. V tomto kurzu vytvořte skupinu aplikací RemoteApp a publikování jednotlivých **Start** nabídku aplikace.

V tomto kurzu se dozvíte, jak:

> [!div class="checklist"]
> * Vytvoření skupiny Vzdálená aplikace RemoteApp.
> * Udělení přístupu k aplikacím RemoteApp.

Než začnete, [stáhněte a naimportujte modul Powershellu virtuální plochy Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) použít v relaci Powershellu, pokud jste tak již neučinili.

## <a name="create-a-remoteapp-group"></a>Vytvoření skupiny Vzdálená aplikace RemoteApp

1. Spusťte následující rutinu prostředí PowerShell k vytvoření nové prázdné skupiny aplikací RemoteApp.

   ```powershell
   New-RdsAppGroup <tenantname> <hostpoolname> <appgroupname> -ResourceType "RemoteApp"
   ```

2. (Volitelné) Pokud chcete ověřit, zda byla vytvořena skupina aplikací, spuštěním následující rutiny můžete zobrazit seznam všech skupin aplikací pro fond hostitele.

   ```powershell
   Get-RdsAppGroup <tenantname> <hostpoolname>
   ```

3. Pomocí následující rutiny Získejte seznam sad **Start** nabídky aplikace v imagi virtuálního počítače hostitele fondu. Poznamenejte si hodnoty pro **FilePath**, **IconPath**, **IconIndex**a další důležité informace pro aplikaci, kterou chcete publikovat.

   ```powershell
   Get-RdsStartMenuApp <tenantname> <hostpoolname> <appgroupname>
   ```
   
4. Spusťte následující rutinu k instalaci aplikace na základě `AppAlias`. `AppAlias` Když spustíte výstup z kroku 3 stane viditelnou.

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -AppAlias <appalias>
   ```

5. (Volitelné) Spusťte následující rutinu k publikování aplikace RemoteApp novou skupinu aplikací vytvořili v kroku 1.

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -Filepath <filepath>  -IconPath <iconpath> -IconIndex <iconindex>
   ```

6. Pokud chcete ověřit, že aplikace byla publikována, spusťte následující rutinu.

   ```powershell
   Get-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname>
   ```

7. Opakujte kroky 1 až 5 pro každou aplikaci, kterou chcete publikovat pro tuto skupinu aplikací.
8. Spuštěním následující rutiny můžete uživatelům udělit přístup k programům Vzdálená aplikace RemoteApp ve skupině aplikací.

   ```powershell
   Add-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname> -UserPrincipalName <userupn>
   ```

## <a name="next-steps"></a>Další postup

V tomto kurzu jste zjistili, jak vytvořit skupinu aplikací, přidejte do ní aplikacím RemoteApp a přiřadit uživatele do skupiny aplikací. Zjistěte, jak vytvořit fond ověření hostitele, najdete v následujícím kurzu. Ověření hostitele fondu můžete použít k monitorování služby aktualizací před jejich zavádět do produkčního prostředí.

> [!div class="nextstepaction"]
> [Vytvoření fondu hostitele pro ověření aktualizace služeb](./create-validation-host-pool.md)
