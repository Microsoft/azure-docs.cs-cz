---
title: Co je Privileged Identity Management? – Azure Active Directory | Dokumentace Microsoftu
description: Poskytuje přehled Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: pim
ms.topic: overview
ms.date: 04/09/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5009310dd4ea4e99e1da2871085ee2d2d99a6eab
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804511"
---
# <a name="what-is-azure-ad-privileged-identity-management"></a>Co je Azure AD Privileged Identity Management?

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) je služba, která umožňuje spravovat, řídit a monitorovat přístup k důležitým prostředkům ve vaší organizaci. To zahrnuje přístup k prostředkům v Azure AD, prostředkům Azure a dalším online službám Microsoftu jako Office 365 nebo Microsoft Intune.

## <a name="why-should-i-use-pim"></a>Proč mám použít PIM?

Organizace chtějí minimalizovat počet uživatelů, kteří mají přístup k zabezpečeným informacím nebo prostředkům, protože snižuje riziko, že škodlivý objekt actor získá takový přístup, nebo oprávněný uživatel neúmyslně ovlivnit citlivý prostředek. Uživatelé však i nadále potřebují provádět privilegované operace v Azure AD, Azure, Office 365 nebo aplikacích SaaS. Organizace můžou uživatelům, kteří mají privilegovaný přístup k prostředkům Azure a službě Azure AD, poskytovat privilegovaný přístup za běhu (just-in-time). Je potřeba mít přehled o tom, co dělají uživatelé s oprávněními správce. PIM pomáhá zmírnit riziko nadměrných, zbytečných nebo nepoužívaných přístupových práv.

## <a name="what-can-i-do-with-pim"></a>Co můžu s PIM dělat?

PIM v podstatě pomáhá spravovat uživatele, co, kdy, kde a proč pro prostředky, které vás zajímají. Tady jsou některé z klíčových funkcí PIM:

- Poskytnutí privilegovaného přístupu **za běhu** do prostředků Azure AD a Azure
- Přiřazení **časově náročného** přístupu k prostředkům pomocí počátečního a koncového data
- Vyžadovat **schválení** pro aktivaci privilegovaných rolí
- Vyvynuťte službu **Multi-Factor Authentication** pro aktivaci libovolné role.
- Vysvětlení, proč se uživatelé aktivují, pomocí **zdůvodnění**
- Dostávat **oznámení** , když se aktivují privilegované role
- Kontrola **přístupu** k zajištění toho, aby uživatelé stále potřebovali role
- Stáhnout **historii auditu** pro interní nebo externí audit

## <a name="terminology"></a>Terminologie

Chcete-li lépe porozumět PIM a dokumentaci, měli byste si projít následující výrazy.

| Ukončení nebo koncept | Kategorie přiřazení role | Popis |
| --- | --- | --- |
| způsobil | type | Přiřazení role, které vyžaduje, aby uživatel prováděl jednu nebo více akcí pro použití role. Pokud uživatel nastavil nárok na roli, to znamená, že může roli aktivovat, když potřebují provádět privilegované úlohy. Neexistuje žádný rozdíl v přístupu pro někoho, kdo má trvalé přiřazení oprávnění oproti oprávněné roli. Jediným rozdílem je, že někteří lidé nepotřebují přístup ke všemu času. |
| aktivní | type | Přiřazení role, které nevyžaduje, aby uživatel prováděl žádnou akci pro použití role. Uživatelé přiřazení jako aktivní mají oprávnění přiřazená k roli. |
| aktivovat |  | Proces provádění jedné nebo více akcí pro použití role, pro kterou má uživatel nárok. Akce můžou zahrnovat provádění kontroly vícefaktorového ověřování (MFA), poskytování obchodního odůvodnění nebo žádosti o schválení od určených schvalovatelů. |
| přiřazení | State | Uživatel, který má přiřazené aktivní roli. |
| Aktivujte | State | Uživatel, který má oprávněné přiřazení role, provedl akce pro aktivaci role a je teď aktivní.  Po aktivaci může uživatel tuto roli použít pro předem nakonfigurovanou časovou dobu, než se bude muset znovu aktivovat. |
| trvalé oprávněné | Trvání | Přiřazení role, kde má uživatel vždy nárok na aktivaci role. |
| trvalé aktivní | Trvání | Přiřazení role, kde může uživatel vždy používat roli bez provedení jakýchkoli akcí. |
| vypršet nárok | Trvání | Přiřazení role, kde má uživatel nárok na aktivaci role během zadaného počátečního a koncového data. |
| vyprší aktivní | Trvání | Přiřazení role, kde může uživatel používat roli bez provádění všech akcí v zadaném počátečním a koncovém datu. |
| přístup JIT (just-in-time) |  | Model, ve kterém uživatelé obdrží dočasná oprávnění k provádění privilegovaných úloh, což zabrání škodlivým nebo neoprávněným uživatelům získat přístup po vypršení platnosti oprávnění. Přístup se udělí jenom v případě, že ho uživatelé potřebují. |
| Princip minimálního přístupu k oprávněním |  | Doporučený postup zabezpečení, při kterém je každý uživatel k dispozici pouze s minimálními oprávněními potřebnými k provádění úloh, které mají autorizaci provádět. Tento postup minimalizuje počet globálních správců a místo toho používá konkrétní role správce pro určité scénáře. |

## <a name="what-does-pim-look-like"></a>Co vypadá PIM?

Po nastavení PIM se v levé navigační nabídce zobrazí možnosti **úlohy**, **Správa**a **aktivity** . Jako správce si zvolíte, že budete spravovat **role Azure AD** a role **prostředků Azure** . Když zvolíte typ rolí, které se mají spravovat, zobrazí se podobná sada možností pro daný typ role.

![Snímek PIM v Azure Portal](./media/pim-configure/pim-overview.png)

## <a name="who-can-do-what-in-pim"></a>Kdo může dělat co v PIM?

Pokud jste první, kdo používá PIM, přiřadíte automaticky role správce [zabezpečení](../users-groups-roles/directory-assign-admin-roles.md#security-administrator) a [správce privilegované role](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) v adresáři.

Pro role Azure AD může přiřazení pro ostatní správce v PIM spravovat jenom uživatel, který je v roli správce privilegované role. [Pro správu PIM můžete udělit přístup dalším správcům](pim-how-to-give-access-to-pim.md). Globální správci, správci zabezpečení a čtenáři zabezpečení můžou zobrazovat přiřazení rolí Azure AD v PIM.

Pro role prostředků Azure může přiřazení pro jiné správce v PIM spravovat jenom Správce předplatného, vlastník prostředku nebo správce přístupu k prostředkům. Uživatelé, kteří jsou správci privilegovaných rolí, správci zabezpečení nebo čtenáři zabezpečení, nemají ve výchozím nastavení přístup k zobrazení přiřazení k rolím prostředků Azure v PIM.

## <a name="scenarios"></a>Scénáře

PIM podporuje následující scénáře:

**Jako správce privilegovaných rolí můžete:**

- Povolit schvalování pro konkrétní role
- Určit schvalovatele (uživatele nebo skupiny) pro schvalování žádostí
- Zobrazit historii žádostí a schválení pro všechny privilegované role

**Jako schvalovatele můžete:**

- Zobrazit žádosti čekající na schválení
- Schvalovat nebo zamítat žádosti o zvýšení oprávnění role (jednotně nebo hromadně)
- Uvést odůvodnění schválení nebo zamítnutí 

**Jako uživatel oprávněné role můžete:**

- Požádat o aktivaci role, která vyžaduje schválení
- Zobrazit stav vaší žádosti o aktivaci
- Dokončit požadovanou úlohu v Azure AD, pokud se aktivace schválí

## <a name="license-requirements"></a>Licenční požadavky

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

Informace o licencích pro uživatele najdete v tématu [licenční požadavky pro použití PIM](subscription-requirements.md).

## <a name="next-steps"></a>Další postup

- [Licenční požadavky na používání PIM](subscription-requirements.md)
- [Zabezpečení privilegovaného přístupu pro hybridní a cloudová nasazení v Azure AD](../users-groups-roles/directory-admin-roles-secure.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json)
- [Nasazení PIM](pim-deployment-plan.md)
