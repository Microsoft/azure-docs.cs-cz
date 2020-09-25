---
title: Správa skupin aplikací pro virtuální počítače s Windows (Classic) – Azure
description: Přečtěte si, jak nastavit klienty virtuálních počítačů s Windows (Classic) v Azure Active Directory (AD).
author: Heidilohr
ms.topic: tutorial
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: e2a1f38918b2ea6af8a334b6648a463753f5c7b0
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91295205"
---
# <a name="tutorial-manage-app-groups-for-windows-virtual-desktop-classic"></a>Kurz: Správa skupin aplikací pro virtuální počítače s Windows (Classic)

>[!IMPORTANT]
>Tento obsah se vztahuje na virtuální plochu Windows (Classic), která nepodporuje Azure Resource Manager objektů virtuálních klientů Windows. Pokud se snažíte spravovat Azure Resource Manager objektů virtuálních klientů Windows, přečtěte si [Tento článek](../manage-app-groups.md).

Výchozí skupina aplikací vytvořená pro nový fond hostitelů virtuálních počítačů s Windows taky publikuje celou plochu. Kromě toho můžete vytvořit jednu nebo více skupin aplikací RemoteApp pro fond hostitelů. Podle tohoto kurzu vytvořte skupinu aplikací RemoteApp a publikujte jednotlivé aplikace v nabídce **Start** .

V tomto kurzu získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvořte skupinu RemoteApp.
> * Udělení přístupu k aplikacím vzdálené aplikace RemoteApp.

Než začnete, [Stáhněte a importujte modul PowerShellu virtuálního počítače s Windows](/powershell/windows-virtual-desktop/overview/) , který chcete použít v relaci PowerShellu, pokud jste to ještě neudělali. Potom spuštěním následující rutiny se přihlaste ke svému účtu:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="create-a-remoteapp-group"></a>Vytvoření skupiny vzdálených aplikací RemoteApp

1. Spuštěním následující rutiny prostředí PowerShell vytvořte novou prázdnou skupinu aplikace RemoteApp.

   ```powershell
   New-RdsAppGroup <tenantname> <hostpoolname> <appgroupname> -ResourceType "RemoteApp"
   ```

2. Volitelné Pokud chcete ověřit, jestli se vytvořila skupina aplikací, můžete spuštěním následující rutiny zobrazit seznam všech skupin aplikací pro fond hostitelů.

   ```powershell
   Get-RdsAppGroup <tenantname> <hostpoolname>
   ```

3. Spuštěním následující rutiny Získejte seznam aplikací nabídky **Start** v imagi virtuálního počítače fondu hostitelů. Zapište hodnoty pro **FilePath**, **IconPath**, **IconIndex**a další důležité informace o aplikaci, kterou chcete publikovat.

   ```powershell
   Get-RdsStartMenuApp <tenantname> <hostpoolname> <appgroupname>
   ```

4. Spuštěním následující rutiny nainstalujte aplikaci na základě `AppAlias` . `AppAlias` se zobrazí, když spustíte výstup z kroku 3.

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -AppAlias <appalias>
   ```

5. Volitelné Spusťte následující rutinu, která publikuje nový program RemoteApp pro skupinu aplikací vytvořenou v kroku 1.

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -Filepath <filepath>  -IconPath <iconpath> -IconIndex <iconindex>
   ```

6. Chcete-li ověřit, zda byla aplikace publikována, spusťte následující rutinu.

   ```powershell
   Get-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname>
   ```

7. Opakujte kroky 1 – 5 pro každou aplikaci, kterou chcete publikovat pro tuto skupinu aplikací.
8. Spuštěním následující rutiny udělte uživatelům přístup k aplikacím RemoteApp ve skupině aplikací.

   ```powershell
   Add-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname> -UserPrincipalName <userupn>
   ```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak vytvořit skupinu aplikací, naplnit ji v aplikacích RemoteApp a přiřazovat uživatele do skupiny aplikací. Další informace o tom, jak vytvořit fond ověřovacích hostitelů, najdete v následujícím kurzu. Pomocí fondu hostitelů ověření můžete monitorovat aktualizace služby před jejich vrácením do produkčního prostředí.

> [!div class="nextstepaction"]
> [Vytvoření fondu hostitelů pro ověření aktualizací služeb](create-validation-host-pool-2019.md)
