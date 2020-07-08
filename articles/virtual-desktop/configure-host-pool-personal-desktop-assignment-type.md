---
title: Typ přiřazení na osobním počítači s Windows Virtual Desktop – Azure
description: Postup konfigurace typu přiřazení pro fond hostitelů na osobním počítači s Windows VM
services: virtual-desktop
author: HeidiLohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 158ac92a930b53e02ee81570c62711ca27dc4ae8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85200388"
---
# <a name="configure-the-personal-desktop-host-pool-assignment-type"></a>Konfigurace typu přiřazení fondu hostitelů osobní plochy

>[!IMPORTANT]
>Tento obsah se vztahuje na jarní 2020 aktualizaci s Azure Resource Manager objekty virtuálních klientů Windows. Pokud používáte virtuální plochu Windows na verzi 2019 bez Azure Resource Manager objektů, přečtěte si [Tento článek](./virtual-desktop-fall-2019/configure-host-pool-personal-desktop-assignment-type-2019.md).
>
> V současnosti je ve verzi Public Preview na jaře 2020 aktualizace virtuálních počítačů s Windows. Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučujeme ji používat pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Typ přiřazení fondu hostitelů osobní plochy můžete nakonfigurovat tak, aby se upravilo prostředí virtuálních počítačů s Windows, aby lépe vyhovovalo vašim potřebám. V tomto tématu vám ukážeme, jak nakonfigurovat automatické nebo přímé přiřazení pro vaše uživatele.

>[!NOTE]
> Pokyny v tomto článku se vztahují pouze na fondy hostitelů osobních ploch, nikoli pro fondy hostitelů ve fondu, protože uživatelé ve fondech hostitelů fondu nejsou přiřazeni ke konkrétním hostitelům relace.

## <a name="prerequisites"></a>Požadavky

V tomto článku se předpokládá, že jste už stáhli a nainstalovali modul PowerShellu pro virtuální počítače s Windows. Pokud jste to neudělali, postupujte podle pokynů v části [nastavení modulu PowerShell](powershell-module.md).

## <a name="configure-automatic-assignment"></a>Konfigurace automatického přiřazení

Automatické přiřazování je výchozí typ přiřazení pro nové fondy hostitelů osobní plochy vytvořené ve vašem prostředí virtuálních počítačů s Windows. Automatické přiřazování uživatelů nevyžaduje konkrétního hostitele relace.

Pokud chcete automaticky přiřazovat uživatele, nejdřív je přiřaďte do fondu hostitelů osobní plochy, aby viděli plochu v informačním kanálu. Když přiřazený uživatel spustí plochu v informačním kanálu, vyžádá dostupného hostitele relace, pokud ještě nejsou připojeni ke fondu hostitelů, který dokončí proces přiřazení.

Pokud chcete nakonfigurovat fond hostitelů pro automatické přiřazování uživatelů k virtuálním počítačům, spusťte následující rutinu PowerShellu:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -PersonalDesktopAssignmentType Automatic
```

Pokud chcete přiřadit uživatele k fondu hostitelů osobní plochy, spusťte následující rutinu PowerShellu:

```powershell
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

## <a name="configure-direct-assignment"></a>Konfigurace přímého přiřazení

Na rozdíl od automatického přiřazení, pokud použijete přímé přiřazení, musíte uživatele přiřadit ke fondu hostitelů osobní plochy i konkrétnímu hostiteli relace, aby se mohli připojit k osobní ploše. Pokud je uživatel přiřazen pouze do fondu hostitelů bez přiřazení hostitele relace, nebude mít přístup k prostředkům.

Pokud chcete nakonfigurovat fond hostitelů tak, aby vyžadoval přímé přiřazování uživatelů na hostitele relací, spusťte následující rutinu prostředí PowerShell:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -PersonalDesktopAssignmentType Direct
```

Pokud chcete přiřadit uživatele k fondu hostitelů osobní plochy, spusťte následující rutinu PowerShellu:

```powershell
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

Pokud chcete přiřadit uživatele ke konkrétnímu hostiteli relace, spusťte následující rutinu PowerShellu:

```powershell
Update-AzWvdSessionHost -HostPoolName <hostpoolname> -Name <sessionhostname> -ResourceGroupName <resourcegroupname> -AssignedUser <userupn>
```

## <a name="next-steps"></a>Další kroky

Teď, když jste nakonfigurovali typ přiřazení osobní plocha, se můžete přihlásit k klientovi virtuální plochy Windows a otestovat ho v rámci uživatelské relace. Tyto další dva postupy se dozvíte, jak se připojit k relaci pomocí klienta podle vašeho výběru:

- [Připojení s desktopovým klientem Windows](connect-windows-7-and-10.md)
- [Připojení k webovému klientovi](connect-web.md)
- [Práce s klientem Android](connect-android.md)
- [Připojení s klientem iOS](connect-ios.md)
- [Připojení s klientem macOS](connect-macos.md)