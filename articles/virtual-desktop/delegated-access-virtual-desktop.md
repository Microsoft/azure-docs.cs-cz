---
title: Delegovaný přístup na virtuálním počítači s Windows – Azure
description: Jak delegovat možnosti správy pro nasazení virtuálních klientů s Windows, včetně příkladů.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 849a3b1eb1a4648c2eaae7dd564f14b9c511fdcf
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2020
ms.locfileid: "77367361"
---
# <a name="delegated-access-in-windows-virtual-desktop"></a>Delegovaný přístup ve Windows Virtual Desktop

Virtuální klient Windows má delegovaný přístupový model, který umožňuje definovat množství přístupu, které může určitý uživatel přiřadit roli. Přiřazení role má tři součásti: objekt zabezpečení, definice role a obor. Model delegovaného přístupu k virtuálním plochám Windows je založený na modelu Azure RBAC. Další informace o konkrétních přiřazeních rolí a jejich součástech najdete v tématu [Přehled řízení přístupu na základě role v Azure](../role-based-access-control/built-in-roles.md).

Delegovaný přístup k virtuálním plochám Windows podporuje následující hodnoty pro každý prvek přiřazení role:

* Objekt zabezpečení
    * Uživatelé
    * Instanční objekty
* Definice rolí
    * Vestavěné role
* Obor
    * Skupiny tenantů
    * Klienti
    * Fondy hostitelů
    * Skupiny aplikací

## <a name="built-in-roles"></a>Vestavěné role

Delegovaný přístup na virtuálním počítači s Windows má několik předdefinovaných definic rolí, které můžete přiřadit uživatelům a instančním objektům.

* Vlastník vzdálené plochy může spravovat všechno, včetně přístupu k prostředkům.
* Přispěvatel RDS může spravovat všechno, ale přístup k prostředkům.
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

Pokyny, jak nastavit prostředí virtuálních počítačů s Windows, najdete v tématu [prostředí virtuálních počítačů s Windows](environment-setup.md).
