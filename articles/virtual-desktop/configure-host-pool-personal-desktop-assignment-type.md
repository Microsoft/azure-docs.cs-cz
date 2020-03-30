---
title: Typ přiřazení osobní plochy pro Virtuální plochu Windows – Azure
description: Jak nakonfigurovat typ přiřazení pro osobní fond hostitelů plochy Virtuální plochy windows.
services: virtual-desktop
author: HeidiLohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 41b24a94d36b21fe5d5f539e056abb535bda433a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128283"
---
# <a name="configure-the-personal-desktop-host-pool-assignment-type"></a>Konfigurace typu přiřazení osobního fondu hostitelů plochy

Můžete nakonfigurovat typ přiřazení vašeho osobního fondu hostitelů plochy tak, aby upravoval prostředí Virtuální plochy systému Windows tak, aby lépe vyhovovalo vašim potřebám. V tomto tématu vám ukážeme, jak nakonfigurovat automatické nebo přímé přiřazení pro uživatele.

>[!NOTE]
> Pokyny v tomto článku platí pouze pro osobní fondy hostitelů plochy, nikoli ve fondech hostitelů ve fondu sdružených hostitelů, protože uživatelé ve fondech hostitelů ve fondu s draženou fondy nejsou přiřazeni k konkrétním hostitelům relací.

## <a name="configure-automatic-assignment"></a>Konfigurace automatického přiřazení

Automatické přiřazení je výchozí typ přiřazení pro nové fondy osobních hostitelů plochy vytvořené v prostředí Virtuální plochy systému Windows. Automatické přiřazování uživatelů nevyžaduje konkrétního hostitele relace.

Chcete-li uživatele automaticky přiřadit, přiřaďte je nejprve do fondu osobního hostitele plochy, aby mohli zobrazit plochu ve zdroji. Když přiřazený uživatel spustí plochu ve svém zdroji, bude nárokovat hostitele relace k dispozici, pokud se ještě nepřipojil k fondu hostitelů, který dokončí proces přiřazení.

Než začnete, [stáhněte a importujte modul Windows Virtual Desktop PowerShell,](/powershell/windows-virtual-desktop/overview/) pokud jste tak ještě neučinili. 

> [!NOTE]
> Než budete podle těchto pokynů dodržovat tyto pokyny, ujistěte se, že jste nainstalovali modul Windows Virtual Desktop PowerShell verze 1.0.1534.2001 nebo novější.

Poté spusťte následující rutinu a přihlaste se ke svému účtu:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Chcete-li nakonfigurovat fond hostitelů tak, aby automaticky přiřazoval uživatele k virtuálním počítačům, spusťte následující rutinu prostředí PowerShell:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -AssignmentType Automatic
```

Chcete-li přiřadit uživatele k osobnímu fondu hostitelů plochy, spusťte následující rutinu prostředí PowerShell:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

## <a name="configure-direct-assignment"></a>Konfigurace přímého přiřazení

Na rozdíl od automatického přiřazení je při použití přímého přiřazení nutné přiřadit uživatele k osobnímu fondu hostitelů plochy i k určitému hostiteli relací, aby se mohl připojit ke své osobní ploše. Pokud je uživatel přiřazen pouze k fondu hostitelů bez přiřazení hostitele relace, nebude mít přístup k prostředkům.

Chcete-li nakonfigurovat fond hostitelů tak, aby vyžadoval přímé přiřazení uživatelů k hostitelům relací, spusťte následující rutinu prostředí PowerShell:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -AssignmentType Direct
```

Chcete-li přiřadit uživatele k osobnímu fondu hostitelů plochy, spusťte následující rutinu prostředí PowerShell:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

Chcete-li přiřadit uživatele k určitému hostiteli relace, spusťte následující rutinu prostředí PowerShell:

```powershell
Set-RdsSessionHost <tenantname> <hostpoolname> -Name <sessionhostname> -AssignedUser <userupn>
```

## <a name="next-steps"></a>Další kroky

Teď, když jste nakonfigurovali typ přiřazení osobní plochy, můžete se přihlásit ke klientovi virtuální plochy systému Windows a otestovat jej jako součást relace uživatele. Tyto další dva způsoby vám řeknou, jak se připojit k relaci pomocí klienta podle vašeho výběru:

- [Připojení s desktopovým klientem Windows](connect-windows-7-and-10.md)
- [Připojení k webovému klientovi](connect-web.md)
