---
title: Typ přiřazení na osobním počítači s Windows Virtual Desktop – Azure
description: Postup konfigurace typu přiřazení pro fond hostitelů na osobním počítači s Windows VM
services: virtual-desktop
author: HeidiLohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 41b24a94d36b21fe5d5f539e056abb535bda433a
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2020
ms.locfileid: "79128283"
---
# <a name="configure-the-personal-desktop-host-pool-assignment-type"></a>Konfigurace typu přiřazení fondu hostitelů osobní plochy

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

## <a name="next-steps"></a>Další kroky

Teď, když jste nakonfigurovali typ přiřazení osobní plocha, se můžete přihlásit k klientovi virtuální plochy Windows a otestovat ho v rámci uživatelské relace. Tyto další dva postupy se dozvíte, jak se připojit k relaci pomocí klienta podle vašeho výběru:

- [Připojení pomocí desktopového klienta Windows](connect-windows-7-and-10.md)
- [Připojení k webovému klientovi](connect-web.md)
