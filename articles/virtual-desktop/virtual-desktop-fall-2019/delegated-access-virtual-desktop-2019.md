---
title: Delegovaný přístup ve virtuální ploše Windows (klasický) – Azure
description: Jak delegovat možnosti správy v nasazení virtuálních počítačů s Windows (Classic), včetně příkladů.
author: Heidilohr
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 0152dc5102fd3f77418448234cab1234d25b97c6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "88008147"
---
# <a name="delegated-access-in-windows-virtual-desktop-classic"></a>Delegovaný přístup ve virtuální ploše Windows (klasický)

>[!IMPORTANT]
>Tento obsah se vztahuje na virtuální plochu Windows (Classic), která nepodporuje Azure Resource Manager objektů virtuálních klientů Windows. Pokud se snažíte spravovat Azure Resource Manager objektů virtuálních klientů Windows, přečtěte si [Tento článek](../delegated-access-virtual-desktop.md).

Virtuální klient Windows má delegovaný přístupový model, který umožňuje definovat množství přístupu, které může určitý uživatel přiřadit roli. Přiřazení role má tři součásti: objekt zabezpečení, definice role a obor. Model delegovaného přístupu k virtuálním plochám Windows je založený na modelu Azure RBAC. Další informace o konkrétních přiřazeních rolí a jejich součástech najdete v tématu [Přehled řízení přístupu na základě role v Azure](../../role-based-access-control/built-in-roles.md).

Delegovaný přístup k virtuálním plochám Windows podporuje následující hodnoty pro každý prvek přiřazení role:

* Objekt zabezpečení
    * Uživatelé
    * Instanční objekty
* Definice role
    * Vestavěné role
* Obor
    * Skupiny tenantů
    * Tenanti
    * Fondy hostitelů
    * Skupiny aplikací

## <a name="built-in-roles"></a>Vestavěné role

Delegovaný přístup na virtuálním počítači s Windows má několik předdefinovaných definic rolí, které můžete přiřadit uživatelům a instančním objektům.

* Vlastník vzdálené plochy může spravovat všechno, včetně přístupu k prostředkům.
* Přispěvatel RDS může spravovat všechno, ale nemůže získat přístup k prostředkům.
* Čtenář služby Vzdálená plocha může zobrazit vše, ale nemůže provádět žádné změny.
* Operátor vzdálené plochy může zobrazit diagnostické aktivity.

## <a name="powershell-cmdlets-for-role-assignments"></a>Rutiny PowerShellu pro přiřazení rolí

Chcete-li vytvořit, zobrazit a odebrat přiřazení rolí, můžete spustit následující rutiny:

* **Get-RdsRoleAssignment** zobrazí seznam přiřazení rolí.
* **New-RdsRoleAssignment** vytvoří nové přiřazení role.
* **Remove-RdsRoleAssignment** odstraní přiřazení rolí.

### <a name="accepted-parameters"></a>Přijaté parametry

Základní tři rutiny můžete upravit následujícími parametry:

* **AadTenantId**: Určuje ID tenanta Azure Active Directory, ze kterého je objekt služby členem.
* **AppGroupName**: název skupiny aplikací vzdálené plochy.
* **Diagnostika**: Určuje obor diagnostiky. (Musí být párována s parametry **infrastruktura** nebo **tenanta** .)
* **HostPoolName**: název fondu hostitelů vzdálené plochy.
* **Infrastruktura**: označuje rozsah infrastruktury.
* **RoleDefinitionName**: název role řízení přístupu na základě role služby Vzdálená plocha přiřazená uživateli, skupině nebo aplikaci. (Například vlastník služby Vzdálená plocha, čtenář vzdálené plochy atd.)
* **ServerPrincipleName**: název aplikace Azure Active Directory.
* **SignInName**: e-mailová adresa uživatele nebo hlavní název uživatele.
* **Tenant**: název tenanta vzdálené plochy.

## <a name="next-steps"></a>Další kroky

Podrobnější seznam rutin PowerShellu, které můžou jednotlivé role použít, najdete v [referenčních informacích k PowerShellu](/powershell/windows-virtual-desktop/overview).

Pokyny, jak nastavit prostředí virtuálních počítačů s Windows, najdete v tématu [prostředí virtuálních počítačů s Windows](environment-setup-2019.md).
