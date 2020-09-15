---
title: Přizpůsobení informačního kanálu pro uživatele virtuálních ploch Windows – Azure
description: Postup přizpůsobení kanálu pro uživatele virtuálních počítačů s Windows pomocí rutin PowerShellu
author: Heidilohr
ms.topic: how-to
ms.date: 09/14/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: e47486f29537cb948aaae7cf17e97bae14b60700
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90084290"
---
# <a name="customize-the-feed-for-windows-virtual-desktop-users"></a>Přizpůsobení informačního kanálu pro uživatele virtuálních ploch Windows

>[!IMPORTANT]
>Tento obsah se vztahuje na virtuální plochu Windows s Azure Resource Manager objekty virtuálních klientů Windows. Pokud používáte virtuální plochu Windows (Classic) bez Azure Resource Manager objektů, přečtěte si [Tento článek](./virtual-desktop-fall-2019/customize-feed-virtual-desktop-users-2019.md).

Informační kanál můžete přizpůsobit tak, aby se prostředky RemoteApp a Vzdálená plocha zobrazovaly rozpoznatelným způsobem pro vaše uživatele.

## <a name="prerequisites"></a>Požadavky

V tomto článku se předpokládá, že jste už stáhli a nainstalovali modul PowerShellu pro virtuální počítače s Windows. Pokud jste to neudělali, postupujte podle pokynů v části [nastavení modulu PowerShell](powershell-module.md).

## <a name="customize-the-display-name-for-a-remoteapp"></a>Přizpůsobení zobrazovaného názvu pro aplikaci RemoteApp

Zobrazované jméno publikované aplikace RemoteApp můžete změnit nastavením popisného názvu. Ve výchozím nastavení je popisný název stejný jako název aplikace RemoteApp.

Pokud chcete načíst seznam publikovaných vzdálených aplikací RemoteApp pro skupinu aplikací, spusťte následující rutinu PowerShellu:

```powershell
Get-AzWvdApplication -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname>
```

Chcete-li přiřadit popisný název k vzdálené aplikaci RemoteApp, spusťte následující rutinu s požadovanými parametry:

```powershell
Update-AzWvdApplication -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname> -Name <applicationname> -FriendlyName <newfriendlyname>
```

Řekněme například, že jste načetli aktuální aplikace pomocí následující ukázkové rutiny:

```powershell
Get-AzWvdApplication -ResourceGroupName 0301RG -ApplicationGroupName 0301RAG | format-list
```

Výstup by vypadal takto:

```powershell
CommandLineArgument :
CommandLineSetting  : DoNotAllow
Description         :
FilePath            : C:\Program Files\Windows NT\Accessories\wordpad.exe
FriendlyName        : Microsoft Word
IconContent         : {0, 0, 1, 0…}
IconHash            : --iom0PS6XLu-EMMlHWVW3F7LLsNt63Zz2K10RE0_64
IconIndex           : 0
IconPath            : C:\Program Files\Windows NT\Accessories\wordpad.exe
Id                  : /subscriptions/<subid>/resourcegroups/0301RG/providers/Microsoft.DesktopVirtualization/applicationgroups/0301RAG/applications/Microsoft Word
Name                : 0301RAG/Microsoft Word
ShowInPortal        : False
Type                : Microsoft.DesktopVirtualization/applicationgroups/applications
```
Chcete-li aktualizovat popisný název, spusťte tuto rutinu:

```powershell
Update-AzWvdApplication -GroupName 0301RAG -Name "Microsoft Word" -FriendlyName "WordUpdate" -ResourceGroupName 0301RG -IconIndex 0 -IconPath "C:\Program Files\Windows NT\Accessories\wordpad.exe" -ShowInPortal:$true -CommandLineSetting DoNotallow -FilePath "C:\Program Files\Windows NT\Accessories\wordpad.exe"
```

Pokud chcete potvrdit, že jste úspěšně aktualizovali popisný název, spusťte tuto rutinu:

```powershell
Get-AzWvdApplication -ResourceGroupName 0301RG -ApplicationGroupName 0301RAG | format-list FriendlyName
```

Rutina by vám měla poskytnout následující výstup:

```powershell
FriendlyName        : WordUpdate
```

## <a name="customize-the-display-name-for-a-remote-desktop"></a>Přizpůsobení zobrazovaného názvu pro vzdálenou plochu

Zobrazované jméno publikované vzdálené plochy můžete změnit nastavením popisného názvu. Pokud jste ručně vytvořili fond hostitelů a skupinu aplikací klasické pracovní plochy prostřednictvím PowerShellu, výchozí popisný název je "session Desktop". Pokud jste vytvořili fond hostitelů a skupinu aplikací klasické pracovní plochy pomocí šablony Azure Resource Manager GitHubu nebo nabídky Azure Marketplace, výchozí popisný název je stejný jako název fondu hostitelů.

Pokud chcete načíst prostředek vzdálené plochy, spusťte následující rutinu PowerShellu:

```powershell
Get-AzWvdDesktop -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname> -Name <applicationname>
```

Chcete-li přiřadit popisný název k prostředku vzdálené plochy, spusťte následující rutinu prostředí PowerShell:

```powershell
Update-AzWvdDesktop -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname> -Name <applicationname> -FriendlyName <newfriendlyname>
```

## <a name="customize-a-display-name-in-azure-portal"></a>Přizpůsobení zobrazovaného názvu v Azure Portal

Zobrazované jméno publikované vzdálené plochy můžete změnit nastavením popisného názvu pomocí Azure Portal.

1. Přihlaste se k webu Azure Portal na adrese <https://portal.azure.com>.

2. Vyhledejte **virtuální počítač s Windows**.

3. V části služby vyberte **virtuální klient Windows**.

4. Na stránce virtuální počítač s Windows na levé straně obrazovky vyberte **skupiny aplikací** a potom vyberte název skupiny aplikací, kterou chcete upravit. (Pokud chcete například upravit zobrazovaný název skupiny aplikací klasické pracovní plochy, vyberte skupinu aplikací s názvem **Desktop**.)

5. V nabídce na levé straně obrazovky vyberte **aplikace** .

6. Vyberte aplikaci, kterou chcete aktualizovat, a pak zadejte nový **Zobrazovaný název**.

7. Vyberte **Uložit**. Aplikace, kterou jste upravili, by nyní měla zobrazit aktualizovaný název.

## <a name="next-steps"></a>Další kroky

Teď, když jste přizpůsobili informační kanál pro uživatele, se můžete přihlásit k klientovi virtuální plochy Windows a otestovat ho. Provedete to tak, že přejdete do části s postupy pro připojení k virtuálnímu počítači s Windows:

 * [Připojení se systémem Windows 10 nebo Windows 7](connect-windows-7-10.md)
 * [Připojení k webovému klientovi](connect-web.md)
 * [Práce s klientem Android](connect-android.md)
 * [Připojení s klientem iOS](connect-ios.md)
 * [Připojení s klientem macOS](connect-macos.md)
