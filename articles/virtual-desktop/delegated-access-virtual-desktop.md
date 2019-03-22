---
title: Delegovaný přístup ve Windows virtuálního klienta (preview) – Azure
description: Jak delegovat správu funkce při nasazení virtuálního klienta Windows, včetně příkladů.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 49645b697071abb8a2f8c85ebde1e6761a3536ab
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/21/2019
ms.locfileid: "58336095"
---
# <a name="delegated-access-in-windows-virtual-desktop-preview"></a>Delegovaný přístup ve Windows virtuálního klienta (Preview)

Virtuální plochy Windows (preview) je model Delegovaný přístup, který umožňuje definovat takovou úroveň přístupu, které konkrétní uživatel může mít přiřazením role. Přiřazení role má tři komponenty: objekt zabezpečení, definice role a obor. Model Delegovaný přístup virtuálního klienta Windows je založen na modelu Azure RBAC. Další informace o přiřazení konkrétních rolí a jejich komponent, naleznete v tématu [Přehled řízení přístupu na základě rolí Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles).

Virtuální Desktop Windows Delegovaný přístup podporuje následující hodnoty pro každý prvek přiřazení role:

* Objekt zabezpečení
    * Uživatelé
    * Instanční objekty
* Definice role
    * Vestavěné role
* Rozsah
    * Skupiny tenanta
    * Tenanti
    * Fondy hostitele
    * Skupiny aplikací

## <a name="built-in-roles"></a>Vestavěné role

Delegovaný přístup v virtuální plochy Windows má několik definic předdefinovaná role, které můžete přiřadit uživatele a instančních objektů.

* Vlastník vzdálené plochy můžou spravovat všechno včetně přístupu k prostředkům.
* Přispěvatel vzdálené plochy můžou spravovat všechno kromě přístupu k prostředkům.
* Čtečku vzdálené plochy může vše zobrazit, ale nemůže provádět žádné změny.
* Operátor vzdálenou plochu můžete zobrazit diagnostické aktivity.

## <a name="powershell-cmdlets-for-role-assignments"></a>Rutiny Powershellu pro přiřazení rolí

Spuštěním následující rutiny vytvořit, zobrazit a upravovat přiřazení rolí:

* **Get-RdsRoleAssignment** zobrazí seznam přiřazení rolí.
* **Nové RdsRoleAssignment** vytvoří nové přiřazení role.
* **Set-RdsRoleAssignment** upraví přiřazení rolí.

### <a name="accepted-parameters"></a>Parametry akceptované

Můžete upravit základní tři rutiny s následujícími parametry:

* **AadTenantId**: Určuje ID tenanta Azure Active Directory, ze kterého instanční objekt se členem.
* **AppGroupName**: názvem skupiny aplikaci Vzdálená plocha.
* **Diagnostika**: Určuje obor, diagnostiky. (Musí být párována s buď **infrastruktury** nebo **Tenanta** parametry.)
* **HostPoolName**: název hostitele fondu vzdálené plochy.
* **Infrastruktura**: Určuje obor, infrastruktury.
* **RoleDefinitionName**: název role řízení přístupu na základě role služby Vzdálená plocha přiřazené uživatele, skupinu nebo aplikaci. (Například vlastník služby vzdálené plochy, čtenář služby vzdálené plochy a atd.)
* **ServerPrincipleName**: název aplikace Azure Active Directory.
* **SignInName**: e-mailovou adresu uživatele nebo hlavní název uživatele.
* **TenantName**: název klienta vzdálené plochy.

## <a name="next-steps"></a>Další postup

Úplnější seznam rutin Powershellu pro každou roli můžete použít, najdete v článku [referenční informace prostředí PowerShell](/powershell/windows-virtual-desktop/overview).

Pokyny, jak nastavit prostředí virtuálního klienta Windows, naleznete v tématu [nastavení prostředí virtuálního klienta Windows](environment-setup.md).
