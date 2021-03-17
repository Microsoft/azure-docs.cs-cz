---
title: Jednotky pro správu v Azure Active Directory | Microsoft Docs
description: Pro podrobnější delegování oprávnění v Azure Active Directory používejte jednotky pro správu.
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.topic: overview
ms.subservice: roles
ms.workload: identity
ms.date: 11/04/2020
ms.author: rolyon
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 49f2c290c69fcadd594d6cbd5879e7d9f5304a42
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102558011"
---
# <a name="administrative-units-in-azure-active-directory"></a>Jednotky pro správu v Azure Active Directory

Tento článek popisuje jednotky pro správu v Azure Active Directory (Azure AD). Jednotka pro správu je prostředek služby Azure AD, který může být kontejnerem pro jiné prostředky Azure AD. Jednotka pro správu může obsahovat pouze uživatele a skupiny.

Jednotky pro správu omezují oprávnění v roli na jakoukoli část vaší organizace, kterou definujete. Mohli byste například použít jednotky pro správu k delegování role [správce helpdesku](permissions-reference.md#helpdesk-administrator) na regionální specialisty podpory, aby mohli uživatelé spravovat jenom v oblasti, kterou podporují.

## <a name="deployment-scenario"></a>Scénář nasazení

Může být užitečné omezit rozsah správy pomocí jednotek pro správu v organizacích, které jsou tvořeny nezávislými divizemi jakéhokoli druhu. Vezměte v úvahu příklad velké školy, který se skládá z mnoha autonomních škol (školy firmy, školy a tak dále). Každá škola má tým správců IT, kteří řídí přístup, spravují uživatele a nastavují zásady pro své školy.

Centrální správce může:

- Vytvořte roli s oprávněními správce jenom pro uživatele Azure AD v jednotce pro správu Business School.
- Vytvořte jednotku pro správu školy firmy.
- Naplňte jednotku pro správu jenom na studenty a pracovníky společnosti Business School.
- Přidejte do role IT tým obchodní školy společně s jeho oborem.

## <a name="license-requirements"></a>Licenční požadavky

Chcete-li použít jednotky pro správu, potřebujete licenci Azure Active Directory Premium pro každého správce jednotky správy a Azure Active Directory Free licence pro členy jednotky pro správu. Další informace najdete v tématu [Začínáme s Azure AD Premium](../fundamentals/active-directory-get-started-premium.md).

## <a name="manage-administrative-units"></a>Spravovat jednotky pro správu

Jednotky pro správu můžete spravovat pomocí Azure Portal, rutin prostředí PowerShell a skriptů nebo Microsoft Graph. Další informace naleznete v tématu:

- [Vytváření, odebírání, naplnění a přidávání rolí do jednotek pro správu](admin-units-manage.md): obsahuje kompletní postupy postupů.
- [Práce s jednotkami pro správu](/powershell/azure/active-directory/working-with-administrative-units): popisuje, jak pracovat s jednotkami pro správu pomocí PowerShellu.
- [Podpora grafu jednotek pro správu](/graph/api/resources/administrativeunit): poskytuje podrobnou dokumentaci týkající se Microsoft Graph pro jednotky pro správu.

### <a name="plan-your-administrative-units"></a>Plánování jednotek pro správu

Jednotky pro správu můžete použít k logickému seskupení prostředků Azure AD. Organizace, jejíž oddělení IT je rozptýlené globálně, může vytvořit administrativní jednotky, které definují relevantní geografické hranice. V jiném scénáři, kde má globální organizace suborganizace, které jsou částečně nezávislé na jejich provozu, můžou jednotky pro správu představovat suborganizace.

Kritéria, na kterých se vytvářejí jednotky pro správu, se řídí jedinečnými požadavky organizace. Jednotky pro správu představují běžný způsob, jak definovat strukturu napříč Microsoft 365 službami. Doporučujeme, abyste připravili jednotky pro správu s využitím v rámci Microsoft 365 služby. Pokud k přidružení běžných prostředků mezi Microsoft 365 v rámci jednotky pro správu, můžete získat maximální hodnotu mimo jednotky pro správu.

Můžete očekávat, že vytváření jednotek pro správu v organizaci projdete v následujících fázích:

1. **Počáteční přijetí**: vaše organizace začne vytvářet jednotky pro správu na základě počátečních kritérií a počet jednotek pro správu se zvýší, protože jsou tato kritéria Upřesněná.
1. **Vyřazení**: po definování kritérií se odstraní jednotky pro správu, které už nejsou potřeba.
1. **Stabilizace**: vaše organizační struktura je definována a počet jednotek pro správu se v krátkém období nemění významně.

## <a name="currently-supported-scenarios"></a>Aktuálně podporované scénáře

Jako globální správce nebo správce privilegovaných rolí můžete použít portál Azure AD k těmto akcím:

- Vytvořit jednotky pro správu
- Přidání uživatelů a skupin členů jednotek pro správu
- Přiřaďte IT oddělení k rolím správců vymezeným jednotkám správy.

Správci s rozsahem administrativních jednotek můžou použít Centrum pro správu Microsoft 365 pro základní správu uživatelů v jejich administrativních jednotkách. Správce skupiny s rozsahem jednotky pro správu může spravovat skupiny pomocí PowerShellu, Microsoft Graph a Center pro správu Microsoft 365.

>[!Note]
>V centru pro správu Microsoft 365 jsou k dispozici pouze funkce popsané v této části. Pro roli Azure AD s oborem jednotky pro správu nejsou k dispozici žádné funkce na úrovni organizace.

Následující části popisují aktuální podporu scénářů správy jednotek.

### <a name="administrative-unit-management"></a>Správa jednotek pro správu

| Oprávnění |   Graf/PowerShell   | Portál Azure AD | Centrum pro správu služby Microsoft 365 |
| --- | --- | --- | --- |
| Vytváření a odstraňování jednotek pro správu   |    Podporováno    |   Podporováno   |    Nepodporováno |
| Přidávání a odebírání členů administrativní jednotky jednotlivě    |   Podporováno    |   Podporováno   |    Nepodporováno |
| Hromadné přidávání a odebírání členů administrativní jednotky pomocí souborů CSV   |    Nepodporováno     |  Podporováno   |    Žádný plán k podpoře |
| Přiřazování správců v rámci jednotky pro správu  |     Podporováno    |   Podporováno    |   Nepodporováno |
| Dynamické přidávání a odebírání členů jednotek pro správu na základě atributů | Nepodporováno | Nepodporováno | Nepodporováno

### <a name="user-management"></a>Správa uživatelů

| Oprávnění |   Graf/PowerShell   | Portál Azure AD | Centrum pro správu služby Microsoft 365 |
| --- | --- | --- | --- |
| Správa vlastností uživatele, hesel a licencí s rozsahem jednotky pro správu   |    Podporováno     |  Podporováno   |   Podporováno |
| Blokující a odblokování přihlášení uživatelů v oboru jednotky pro správu    |   Podporováno   |    Podporováno   |    Podporováno |
| Správa uživatelsky vymezených přihlašovacích údajů pro vícefaktorové ověřování uživatele v jednotkách správy   |    Podporováno   |   Podporováno   |   Nepodporováno |

### <a name="group-management"></a>Správa skupin

| Oprávnění |   Graf/PowerShell   | Portál Azure AD | Centrum pro správu služby Microsoft 365 |
| --- | --- | --- | --- |
| Správa vlastností skupin a členů s rozsahem jednotky pro správu     |  Podporováno   |    Podporováno    |  Nepodporováno |
| Správa licencování skupin pro správu s rozsahem jednotky   |    Podporováno  |    Podporováno   |   Nepodporováno |

Jednotky pro správu se vztahují pouze na oprávnění pro správu. Nebrání členům ani správcům používat [výchozí uživatelská oprávnění](../fundamentals/users-default-permissions.md) k procházení jiných uživatelů, skupin nebo prostředků mimo jednotky pro správu. V centru pro správu Microsoft 365 se odfiltrují uživatelé mimo vymezené jednotky pro správu Správce s oborem. Můžete ale Procházet Další uživatele na portálu Azure AD, PowerShellu a dalších službách Microsoftu.

## <a name="next-steps"></a>Další kroky

- [Spravovat jednotky pro správu](admin-units-manage.md)
- [Správa uživatelů v jednotkách pro správu](admin-units-add-manage-users.md)
- [Správa skupin v jednotkách pro správu](admin-units-add-manage-groups.md)
- [Přiřazení vymezených rolí k jednotce pro správu](admin-units-assign-roles.md)
