---
title: Typ přiřazení na osobním počítači s Windows (Classic) – Azure
description: Postup konfigurace typu přiřazení pro fond hostitelů osobních desktopů v systému Windows (Classic)
author: Heidilohr
ms.topic: how-to
ms.date: 05/22/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 5710fc8be66f2126f6bf665ad1808aa11e4db2c4
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106445308"
---
# <a name="configure-the-personal-desktop-host-pool-assignment-type-for-windows-virtual-desktop-classic"></a>Konfigurace typu přiřazení fondu hostitelů osobní plochy pro virtuální plochu Windows (Classic)

>[!IMPORTANT]
>Tento obsah se vztahuje na virtuální plochu Windows (Classic), která nepodporuje Azure Resource Manager objektů virtuálních klientů Windows. Pokud se snažíte spravovat Azure Resource Manager objektů virtuálních klientů Windows, přečtěte si [Tento článek](../configure-host-pool-personal-desktop-assignment-type.md).

Typ přiřazení fondu hostitelů osobní plochy můžete nakonfigurovat tak, aby se upravilo prostředí virtuálních počítačů s Windows, aby lépe vyhovovalo vašim potřebám. V tomto tématu vám ukážeme, jak nakonfigurovat automatické nebo přímé přiřazení pro vaše uživatele.

>[!NOTE]
> Pokyny v tomto článku se vztahují pouze na fondy hostitelů osobních ploch, nikoli pro fondy hostitelů ve fondu, protože uživatelé ve fondech hostitelů fondu nejsou přiřazeni ke konkrétním hostitelům relace.

## <a name="configure-automatic-assignment"></a>Konfigurace automatického přiřazení

Automatické přiřazování je výchozí typ přiřazení pro nové fondy hostitelů osobní plochy vytvořené ve vašem prostředí virtuálních počítačů s Windows. Automatické přiřazování uživatelů nevyžaduje konkrétního hostitele relace.

Pokud chcete automaticky přiřazovat uživatele, nejdřív je přiřaďte do fondu hostitelů osobní plochy, aby viděli plochu v informačním kanálu. Když přiřazený uživatel spustí plochu v informačním kanálu, vyžádá dostupného hostitele relace, pokud ještě nejsou připojeni ke fondu hostitelů, který dokončí proces přiřazení.

Než začnete, [Stáhněte a importujte modul PowerShell virtuálního klienta Windows](/powershell/windows-virtual-desktop/overview/) , pokud jste to ještě neudělali.

> [!NOTE]
> Než budete postupovat podle těchto pokynů, ujistěte se, že jste nainstalovali modul PowerShellu pro virtuální počítače s Windows verze 1.0.1534.2001 nebo novější.

Potom spuštěním následující rutiny se přihlaste ke svému účtu:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Pokud chcete nakonfigurovat fond hostitelů pro automatické přiřazování uživatelů k virtuálním počítačům, spusťte následující rutinu PowerShellu:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -AssignmentType Automatic
```

Pokud chcete přiřadit uživatele k fondu hostitelů osobní plochy, spusťte následující rutinu PowerShellu:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

## <a name="configure-direct-assignment"></a>Konfigurace přímého přiřazení

Na rozdíl od automatického přiřazení, pokud použijete přímé přiřazení, musíte uživatele přiřadit ke fondu hostitelů osobní plochy i konkrétnímu hostiteli relace, aby se mohli připojit k osobní ploše. Pokud je uživatel přiřazen pouze do fondu hostitelů bez přiřazení hostitele relace, nebude mít přístup k prostředkům.

Pokud chcete nakonfigurovat fond hostitelů tak, aby vyžadoval přímé přiřazování uživatelů na hostitele relací, spusťte následující rutinu prostředí PowerShell:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -AssignmentType Direct
```

Pokud chcete přiřadit uživatele k fondu hostitelů osobní plochy, spusťte následující rutinu PowerShellu:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

Pokud chcete přiřadit uživatele ke konkrétnímu hostiteli relace, spusťte následující rutinu PowerShellu:

```powershell
Set-RdsSessionHost <tenantname> <hostpoolname> -Name <sessionhostname> -AssignedUser <userupn>
```

## <a name="remove-a-user-assignment"></a>Odebrání přiřazení uživatele

Je možné odebrat přiřazení uživatele, protože uživatel již nepotřebuje osobní plochu, uživatel opustil společnost nebo chcete použít pracovní plochu pro někoho jiného.

V současné době je jediným způsobem, jak můžete odebrat přiřazení uživatelů pro osobní plochu, úplné odebrání hostitele relace. Chcete-li odebrat hostitele relace, spusťte tuto rutinu:

```powershell
Remove-RdsSessionHost
```

Pokud potřebujete přidat hostitele relace zpátky do fondu hostitelů osobní plochy, odinstalujte na tomto počítači virtuální plochu Windows a pak postupujte podle kroků v části [Vytvoření fondu hostitelů pomocí prostředí PowerShell](create-host-pools-powershell-2019.md) pro opětovné zaregistrování hostitele relace.

## <a name="next-steps"></a>Další kroky

Teď, když jste nakonfigurovali typ přiřazení osobní plocha, se můžete přihlásit k klientovi virtuální plochy Windows a otestovat ho v rámci uživatelské relace. Tyto další dva postupy se dozvíte, jak se připojit k relaci pomocí klienta podle vašeho výběru:

- [Připojení s desktopovým klientem Windows](connect-windows-7-10-2019.md)
- [Připojení k webovému klientovi](connect-web-2019.md)
