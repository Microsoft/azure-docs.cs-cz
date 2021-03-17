---
title: Správa skupin aplikací pro prostředí Windows Virtual Desktop PowerShell – Azure
description: Správa skupin aplikací virtuálních počítačů s Windows pomocí PowerShellu
author: Heidilohr
ms.topic: how-to
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: a6f24dea00a174aa0276a9b30add0854c3694056
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88008640"
---
# <a name="manage-app-groups-using-powershell"></a>Správa skupin aplikací pomocí PowerShellu

>[!IMPORTANT]
>Tento obsah se vztahuje na virtuální plochu Windows s Azure Resource Manager objekty virtuálních klientů Windows. Pokud používáte virtuální plochu Windows (Classic) bez Azure Resource Manager objektů, přečtěte si [Tento článek](./virtual-desktop-fall-2019/manage-app-groups-2019.md).

Výchozí skupina aplikací vytvořená pro nový fond hostitelů virtuálních počítačů s Windows taky publikuje celou plochu. Kromě toho můžete vytvořit jednu nebo více skupin aplikací RemoteApp pro fond hostitelů. Podle tohoto kurzu vytvořte skupinu aplikací RemoteApp a publikujte jednotlivé aplikace v nabídce **Start** .

V tomto kurzu získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvořte skupinu RemoteApp.
> * Udělení přístupu k aplikacím vzdálené aplikace RemoteApp.

## <a name="prerequisites"></a>Požadavky

V tomto článku se předpokládá, že jste postupovali podle pokynů v tématu [nastavení modulu PowerShellu](powershell-module.md) pro nastavení modulu PowerShell a přihlásíte se ke svému účtu Azure.

## <a name="create-a-remoteapp-group"></a>Vytvoření skupiny vzdálených aplikací RemoteApp

Vytvoření skupiny vzdálených aplikací RemoteApp pomocí prostředí PowerShell:

1. Spuštěním následující rutiny prostředí PowerShell vytvořte novou prázdnou skupinu aplikace RemoteApp.

   ```powershell
   New-AzWvdApplicationGroup -Name <appgroupname> -ResourceGroupName <resourcegroupname> -ApplicationGroupType "RemoteApp" -HostPoolArmPath '/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName/providers/Microsoft.DesktopVirtualization/hostPools/HostPoolName'-Location <azureregion>
   ```

2. Volitelné Pokud chcete ověřit, jestli se vytvořila skupina aplikací, můžete spuštěním následující rutiny zobrazit seznam všech skupin aplikací pro fond hostitelů.

   ```powershell
   Get-AzWvdApplicationGroup -Name <appgroupname> -ResourceGroupName <resourcegroupname>
   ```

3. Spuštěním následující rutiny Získejte seznam aplikací nabídky **Start** v imagi virtuálního počítače fondu hostitelů. Zapište hodnoty pro **FilePath**, **IconPath**, **IconIndex**a další důležité informace o aplikaci, kterou chcete publikovat.

   ```powershell
   Get-AzWvdStartMenuItem -ApplicationGroupName <appgroupname> -ResourceGroupName <resourcegroupname> | Format-List | more
   ```

   Výstup by měl zobrazovat všechny položky nabídky Start v následujícím formátu:

   ```powershell
   AppAlias            : access
   CommandLineArgument :
   FilePath            : C:\Program Files\Microsoft Office\root\Office16\MSACCESS.EXE
   FriendlyName        :
   IconIndex           : 0
   IconPath            : C:\Program Files\Microsoft Office\Root\VFS\Windows\Installer\{90160000-000F-0000-1000-0000000FF1CE}\accicons.exe
   Id                  : /subscriptions/resourcegroups/providers/Microsoft.DesktopVirtualization/applicationgroups/startmenuitems/Access
   Name                : 0301RAG/Access
   Type                : Microsoft.DesktopVirtualization/applicationgroups/startmenuitems

   AppAlias            : charactermap
   CommandLineArgument :
   FilePath            : C:\windows\system32\charmap.exe
   FriendlyName        :
   IconIndex           : 0
   IconPath            : C:\windows\system32\charmap.exe
   Id                  : /subscriptions/resourcegroups/providers/Microsoft.DesktopVirtualization/applicationgroups/startmenuitems/Character Map
   Name                : 0301RAG/Character Map
   Type                : Microsoft.DesktopVirtualization/applicationgroups/startmenuitems
   ```

4. Spuštěním následující rutiny nainstalujte aplikaci na základě `AppAlias` . `AppAlias` se zobrazí, když spustíte výstup z kroku 3.

   ```powershell
   New-AzWvdApplication -AppAlias <appalias> -GroupName <appgroupname> -Name <remoteappname> -ResourceGroupName <resourcegroupname> -CommandLineSetting <DoNotAllow|Allow|Require>
   ```

5. Volitelné Spusťte následující rutinu, která publikuje nový program RemoteApp pro skupinu aplikací vytvořenou v kroku 1.

   ```powershell
   New-AzWvdApplication -GroupName <appgroupname> -Name <remoteappname> -ResourceGroupName <resourcegroupname> -Filepath <filepath> -IconPath <iconpath> -IconIndex <iconindex> -CommandLineSetting <DoNotAllow|Allow|Require>
   ```

6. Chcete-li ověřit, zda byla aplikace publikována, spusťte následující rutinu.

   ```powershell
   Get-AzWvdApplication -GroupName <appgroupname> -ResourceGroupName <resourcegroupname>
   ```

7. Opakujte kroky 1 – 5 pro každou aplikaci, kterou chcete publikovat pro tuto skupinu aplikací.
8. Spuštěním následující rutiny udělte uživatelům přístup k aplikacím RemoteApp ve skupině aplikací.

   ```powershell
   New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
   ```

## <a name="next-steps"></a>Další kroky

Pokud jste se dostali k této příručce z našich kurzů, Projděte si článek [Vytvoření fondu hostitelů, ve kterém ověříte aktualizace služby](create-validation-host-pool.md). Pomocí fondu hostitelů ověření můžete monitorovat aktualizace služby před jejich vrácením do produkčního prostředí.