---
title: Co je Privileged Identity Management? – Azure AD | Microsoft Docs
description: Poskytuje přehled Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: pim
ms.topic: overview
ms.date: 09/29/2020
ms.author: curtand
ms.custom: pim ; azuread-video-2020
ms.collection: M365-identity-device-management
ms.openlocfilehash: a0b7bcfa6569edd93373f493cfaa7deff2976d35
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91540825"
---
# <a name="what-is-azure-ad-privileged-identity-management"></a>Co je Azure AD Privileged Identity Management?

 Privileged Identity Management (PIM) je služba v Azure Active Directory (Azure AD), která umožňuje spravovat, řídit a monitorovat přístup k důležitým prostředkům ve vaší organizaci. Tyto prostředky zahrnují prostředky v Azure AD, Azure a další online služby Microsoftu, například Microsoft 365 nebo Microsoft Intune. Následující video vás seznámí s důležitými koncepty a funkcemi PIM.
<br><br>

> [!VIDEO https://www.youtube.com/embed/f-0K7mRUPpQ]

## <a name="reasons-to-use"></a>Důvody k použití

Organizace chtějí minimalizovat počet uživatelů, kteří mají přístup k zabezpečeným informacím nebo prostředkům, protože snižuje riziko, že škodlivý objekt actor získá takový přístup, nebo oprávněný uživatel neúmyslně ovlivnit citlivý prostředek. Uživatelé ale stále potřebují provádět privilegované operace v aplikacích Azure AD, Azure, Microsoft 365 nebo SaaS. Organizace můžou uživatelům udělit privilegovaný přístup k prostředkům Azure a Azure AD za běhu. Je potřeba mít přehled o tom, co dělají uživatelé s oprávněními správce.

## <a name="what-does-it-do"></a>Co to dělá?

Privileged Identity Management poskytuje aktivaci rolí na základě času a schválení, která vám umožní zmírnit rizika nadměrných, zbytečných nebo nepoužívaných přístupových oprávnění k prostředkům, o kterých se zajímáte. Tady jsou některé klíčové funkce Privileged Identity Management:

- Poskytnutí privilegovaného přístupu **za běhu** do prostředků Azure AD a Azure
- Přiřazení **časově náročného** přístupu k prostředkům pomocí počátečního a koncového data
- Vyžadovat **schválení** pro aktivaci privilegovaných rolí
- Vyvynuťte službu **Multi-Factor Authentication** pro aktivaci libovolné role.
- Vysvětlení, proč se uživatelé aktivují, pomocí **zdůvodnění**
- Dostávat **oznámení** , když se aktivují privilegované role
- Kontrola **přístupu** k zajištění toho, aby uživatelé stále potřebovali role
- Stáhnout **historii auditu** pro interní nebo externí audit

## <a name="what-can-i-do-with-it"></a>Co s tím můžu dělat?

Jakmile nastavíte Privileged Identity Management, v levé navigační nabídce uvidíte možnosti **úlohy**, **Spravovat**a **aktivity** . Jako správce si zvolíte mezi možnostmi, jako je Správa **rolí Azure AD**, Správa rolí **prostředků Azure** nebo privilegovaných přístupových skupin. Když zvolíte, co chcete spravovat, zobrazí se příslušná sada možností pro tuto možnost.

![Snímek obrazovky Privileged Identity Management Azure Portal](./media/pim-configure/pim-quickstart.png)

## <a name="who-can-do-what"></a>Kdo to může udělat?

Pro role Azure AD v Privileged Identity Management může spravovat přiřazení jiných správců jenom uživatel, který je v roli správce privilegované role nebo globální správce. Můžete [udělit přístup ostatním správcům ke správě Privileged Identity Management](pim-how-to-give-access-to-pim.md). Globální správci, správci zabezpečení, globální čtenáři a čtenáři zabezpečení si také můžou zobrazit přiřazení rolí Azure AD v Privileged Identity Management.

Pro role prostředků Azure v Privileged Identity Management může přiřazení pro jiné správce spravovat jenom Správce předplatného, vlastník prostředku nebo správce přístupu k uživateli. Uživatelé, kteří jsou správci privilegovaných rolí, správci zabezpečení nebo čtenáři zabezpečení, nemají ve výchozím nastavení přístup k zobrazení přiřazení k rolím prostředků Azure v Privileged Identity Management.

## <a name="scenarios"></a>Scénáře

Privileged Identity Management podporuje následující scénáře:

### <a name="privileged-role-administrator-permissions"></a>Oprávnění správce privilegovaných rolí

- Povolit schvalování pro konkrétní role
- Zadejte uživatele nebo skupiny schvalovatelů ke schválení požadavků.
- Zobrazit historii žádostí a schválení pro všechny privilegované role

### <a name="approver-permissions"></a>Oprávnění schvalovatele

- Zobrazit žádosti čekající na schválení
- Schválení nebo zamítnutí žádostí o zvýšení oprávnění role (jednoduchá a Hromadná)
- Poskytnout odůvodnění pro schválení nebo zamítnutí

### <a name="eligible-role-user-permissions"></a>Oprávnění uživatele oprávněné role

- Požádat o aktivaci role, která vyžaduje schválení
- Zobrazit stav vaší žádosti o aktivaci
- Dokončit požadovanou úlohu v Azure AD, pokud se aktivace schválí

## <a name="terminology"></a>Terminologie

Chcete-li lépe pochopit Privileged Identity Management a dokumentaci, měli byste si projít následující výrazy.

| Termín nebo koncept | Kategorie přiřazení role | Popis |
| --- | --- | --- |
| způsobil | Typ | Přiřazení role, které vyžaduje, aby uživatel prováděl jednu nebo více akcí pro použití role. Pokud uživatel nastavil nárok na roli, to znamená, že může roli aktivovat, když potřebují provádět privilegované úlohy. Neexistuje žádný rozdíl v přístupu pro někoho, kdo má trvalé přiřazení oprávnění oproti oprávněné roli. Jediným rozdílem je, že někteří lidé nepotřebují přístup ke všemu času. |
| active | Typ | Přiřazení role, které nevyžaduje, aby uživatel prováděl žádnou akci pro použití role. Uživatelé přiřazení jako aktivní mají oprávnění přiřazená k roli. |
| aktivovat |  | Proces provádění jedné nebo více akcí pro použití role, pro kterou má uživatel nárok. Akce můžou zahrnovat provádění kontroly vícefaktorového ověřování (MFA), poskytování obchodního odůvodnění nebo žádosti o schválení od určených schvalovatelů. |
| přiřazení | Stav | Uživatel, který má přiřazené aktivní roli. |
| Aktivujte | Stav | Uživatel, který má oprávněné přiřazení role, provedl akce pro aktivaci role a je teď aktivní.  Po aktivaci může uživatel tuto roli použít pro předem nakonfigurovanou časovou dobu, než se bude muset znovu aktivovat. |
| trvalé oprávněné | Doba trvání | Přiřazení role, kde má uživatel vždy nárok na aktivaci role. |
| trvalé aktivní | Doba trvání | Přiřazení role, kde může uživatel vždy používat roli bez provedení jakýchkoli akcí. |
| vypršet nárok | Doba trvání | Přiřazení role, kde má uživatel nárok na aktivaci role během zadaného počátečního a koncového data. |
| vyprší aktivní | Doba trvání | Přiřazení role, kde může uživatel používat roli bez provádění všech akcí v zadaném počátečním a koncovém datu. |
| přístup JIT (just-in-time) |  | Model, ve kterém uživatelé obdrží dočasná oprávnění k provádění privilegovaných úloh, což zabrání škodlivým nebo neoprávněným uživatelům získat přístup po vypršení platnosti oprávnění. Přístup se udělí jenom v případě, že ho uživatelé potřebují. |
| Princip minimálního přístupu k oprávněním |  | Doporučený postup zabezpečení, při kterém je každý uživatel k dispozici pouze s minimálními oprávněními potřebnými k provádění úloh, které mají autorizaci provádět. Tento postup minimalizuje počet globálních správců a místo toho používá konkrétní role správce pro určité scénáře. |

## <a name="license-requirements"></a>Licenční požadavky

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

Informace o licencích pro uživatele najdete v tématu [licenční požadavky pro použití Privileged Identity Management](subscription-requirements.md).

## <a name="next-steps"></a>Další kroky

- [Licenční požadavky pro použití Privileged Identity Management](subscription-requirements.md)
- [Zabezpečení privilegovaného přístupu pro hybridní a cloudová nasazení v Azure AD](../users-groups-roles/directory-admin-roles-secure.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json)
- [Nasazení technologie Privileged Identity Management](pim-deployment-plan.md)
