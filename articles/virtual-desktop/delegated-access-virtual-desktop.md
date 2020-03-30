---
title: Delegovaný přístup ve Virtuální desktopové ploše Windows – Azure
description: Jak delegovat možnosti správy na nasazení virtuální plochy systému Windows, včetně příkladů.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 3e27550ecc5b42c2bf0d947690da09e13d88ea4f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128030"
---
# <a name="delegated-access-in-windows-virtual-desktop"></a>Delegovaný přístup ve Windows Virtual Desktop

Windows Virtual Desktop má delegovaný přístup ový model, který umožňuje definovat množství přístupu konkrétní ho uživatel může mít přiřazením role. Přiřazení role má tři součásti: objekt zabezpečení, definici role a obor. Model delegovaného přístupu virtuální plochy Windows je založený na modelu Azure RBAC. Další informace o přiřazeních konkrétních rolí choda a jejich součástech najdete [v tématu Přehled řízení přístupu na základě rolí Azure](../role-based-access-control/built-in-roles.md).

Delegovaný přístup k virtuální ploše systému Windows podporuje následující hodnoty pro každý prvek přiřazení role:

* Objekt zabezpečení
    * Uživatelé
    * Instanční objekty
* Definice role
    * Vestavěné role
* Rozsah
    * Skupiny klientů
    * Tenanti
    * Hostitelské fondy
    * Skupiny aplikací

## <a name="built-in-roles"></a>Vestavěné role

Delegovaný přístup ve virtuální ploše systému Windows má několik předdefinovaných definic rolí, které můžete přiřadit uživatelům a instančním objektům.

* Vlastník RDS může spravovat vše, včetně přístupu k prostředkům.
* Přispěvatel RDS může spravovat vše kromě přístupu k prostředkům.
* Rds Reader může zobrazit vše, ale nemůže provádět žádné změny.
* Operátor RDS může zobrazit diagnostické aktivity.

## <a name="powershell-cmdlets-for-role-assignments"></a>Rutiny prostředí PowerShell pro přiřazení rolí

Můžete spustit následující rutiny pro vytvoření, zobrazení a odebrání přiřazení rolí:

* **Get-RdsRoleAssignment** zobrazí seznam přiřazení rolí.
* **New-RdsRoleAssignment** vytvoří nové přiřazení role.
* **Remove-RdsRoleAssignment** odstraní přiřazení rolí.

### <a name="accepted-parameters"></a>Přijaté parametry

Můžete upravit základní tři rutiny s následujícími parametry:

* **AadTenantId**: určuje ID klienta služby Azure Active Directory, ze kterého je instanční objekt členem.
* **AppGroupName**: název skupiny aplikací Vzdálená plocha.
* **Diagnostika**: označuje rozsah diagnostiky. (Musí být spárován s parametry **Infrastruktura** nebo **Tenant.)**
* **HostPoolName**: název fondu hostitelů vzdálené plochy.
* **Infrastruktura**: označuje rozsah infrastruktury.
* **RoleDefinitionName**: název role řízení přístupu na základě role služby Vzdálená plocha přiřazená uživateli, skupině nebo aplikaci. (Například vlastník služby Vzdálená plocha, Čtečka služby Vzdálená plocha a tak dále.)
* **ServerPrincipleName**: název aplikace Azure Active Directory.
* **SignInName**: e-mailová adresa uživatele nebo hlavní jméno uživatele.
* **TenantName**: název klienta vzdálené plochy.

## <a name="next-steps"></a>Další kroky

Podrobnější seznam rutin prostředí PowerShell, které může každá role použít, najdete v [tématu odkaz na prostředí PowerShell](/powershell/windows-virtual-desktop/overview).

Pokyny k nastavení prostředí Virtuální plochy systému Windows naleznete v [tématu Windows Virtual Desktop environment](environment-setup.md).
