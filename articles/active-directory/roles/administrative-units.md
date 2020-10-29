---
title: Jednotky pro správu v Azure Active Directory | Microsoft Docs
description: Pro podrobnější delegování oprávnění v Azure Active Directory používejte jednotky pro správu.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: overview
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 09/22/2020
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0fb24d0cd2714969b5a888b1036f524c4c062d76
ms.sourcegitcommit: daab0491bbc05c43035a3693a96a451845ff193b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2020
ms.locfileid: "93027614"
---
# <a name="administrative-units-in-azure-active-directory"></a>Jednotky pro správu v Azure Active Directory

Tento článek popisuje jednotky pro správu v Azure Active Directory (Azure AD). Jednotka pro správu je prostředek služby Azure AD, který může být kontejnerem pro jiné prostředky Azure AD. Jednotka pro správu může obsahovat pouze uživatele a skupiny.

Pomocí jednotek pro správu můžete udělit oprávnění správce, která jsou omezená na oddělení, oblast nebo jiný segment vaší organizace, kterou definujete. Jednotky pro správu můžete použít k delegování oprávnění pro místní správce nebo k nastavení zásad na podrobné úrovni. Správce uživatelských účtů může například aktualizovat informace o profilu, resetovat hesla a přiřazovat licence uživatelům pouze ve své jednotce pro správu.

Můžete například delegovat místní specialisty podpory na roli [správce helpdesku](permissions-reference.md#helpdesk-administrator) , která je omezená jenom na správu uživatelů v oblasti, kterou podporují.

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

Jednotky pro správu můžete spravovat pomocí Azure Portal, rutin prostředí PowerShell a skriptů nebo Microsoft Graph. Další informace naleznete v tématech:

- [Vytváření, odebírání, naplnění a přidávání rolí do jednotek pro správu](admin-units-manage.md): obsahuje kompletní postupy postupů.
- [Práce s jednotkami pro správu](/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0&preserve-view=true): popisuje, jak pracovat s jednotkami pro správu pomocí PowerShellu.
- [Podpora grafu jednotek pro správu](/graph/api/resources/administrativeunit?view=graph-rest-1.0&preserve-view=true): poskytuje podrobnou dokumentaci týkající se Microsoft Graph pro jednotky pro správu.

### <a name="plan-your-administrative-units"></a>Plánování jednotek pro správu

Jednotky pro správu můžete použít k logickému seskupení prostředků Azure AD. Například pro organizaci, jejíž oddělení IT je celkově rozptýlené, může být vhodné vytvořit administrativní jednotky, které definují tyto geografické hranice. V jiném scénáři, kde má nadnárodní organizace různé *suborganizace* , které jsou částečně nezávislé na jejich provozu, může představovat jednotka pro správu každou dílčí organizaci.

Kritéria, na kterých se vytvářejí jednotky pro správu, se řídí jedinečnými požadavky organizace. Jednotky pro správu představují běžný způsob, jak definovat strukturu napříč Microsoft 365 službami. Doporučujeme, abyste připravili jednotky pro správu s využitím v rámci Microsoft 365 služby. Pokud k přidružení běžných prostředků mezi Microsoft 365 v rámci jednotky pro správu, můžete získat maximální hodnotu mimo jednotky pro správu.

Můžete očekávat, že vytváření jednotek pro správu v organizaci projdete v následujících fázích:

1. **Počáteční přijetí** : vaše organizace začne vytvářet jednotky pro správu na základě počátečních kritérií a počet jednotek pro správu se zvýší, protože jsou tato kritéria Upřesněná.
1. **Vyřazení** : po správném definování kritérií se odstraní jednotky pro správu, které už nejsou potřeba.
1. **Stabilizace** : vaše organizační struktura je dobře definovaná a počet jednotek pro správu se v krátkém období nemění významně.

## <a name="currently-supported-scenarios"></a>Aktuálně podporované scénáře

Jako globální správce nebo správce privilegovaných rolí můžete použít portál Azure AD k vytváření jednotek pro správu, přidávání uživatelů jako členů jednotek pro správu a přiřazení IT pracovníků k rolím správců vymezeným jednotkám. Správci s vymezenou jednotkou můžou potom použít Centrum pro správu Microsoft 365 pro základní správu uživatelů v jejich administrativních jednotkách.

Kromě toho můžete přidat skupiny jako členy jednotky pro správu. Správce skupin s vymezenými jednotkami pro správu je může spravovat pomocí PowerShellu, Microsoft Graph a portálu Azure AD.

Následující tabulky popisují aktuální podporu scénářů administrativních jednotek:

### <a name="administrative-unit-management"></a>Správa jednotek pro správu

| Oprávnění |   Graf/PowerShell   | Portál Azure AD | Centrum pro správu služby Microsoft 365 | 
| -- | -- | -- | -- |
| Vytváření a odstraňování jednotek pro správu   |    Podporováno    |   Podporováno   |    Nepodporováno | 
| Přidávání a odebírání členů administrativní jednotky jednotlivě    |   Podporováno    |   Podporováno   |    Nepodporováno | 
| Hromadné přidávání a odebírání členů administrativní jednotky pomocí souborů CSV   |    Nepodporováno     |  Podporováno   |    Žádný plán k podpoře | 
| Přiřazování správců v rámci jednotky pro správu  |     Podporováno    |   Podporováno    |   Nepodporováno | 
| Dynamické přidávání a odebírání členů jednotek pro správu na základě atributů | Nepodporováno | Nepodporováno | Nepodporováno 

### <a name="user-management"></a>Správa uživatelů

| Oprávnění |   Graf/PowerShell   | Portál Azure AD | Centrum pro správu služby Microsoft 365 |
| -- | -- | -- | -- |
| Správa vlastností uživatele, hesel a licencí s rozsahem jednotky pro správu   |    Podporováno     |  Podporováno   |   Podporováno |
| Blokující a odblokování přihlášení uživatelů v oboru jednotky pro správu    |   Podporováno   |    Podporováno   |    Podporováno |
| Správa uživatelsky vymezených přihlašovacích údajů pro vícefaktorové ověřování uživatele v jednotkách správy   |    Podporováno   |   Podporováno   |   Nepodporováno |

### <a name="group-management"></a>Správa skupin

| Oprávnění |   Graf/PowerShell   | Portál Azure AD | Centrum pro správu služby Microsoft 365 |
| -- | -- | -- | -- |
| Správa vlastností skupin a členů s rozsahem jednotky pro správu     |  Podporováno   |    Podporováno    |  Nepodporováno |
| Správa licencování skupin pro správu s rozsahem jednotky   |    Podporováno  |    Podporováno   |   Nepodporováno |


Jednotky pro správu se vztahují pouze na oprávnění pro správu. Nebrání členům ani správcům používat [výchozí uživatelská oprávnění](../fundamentals/users-default-permissions.md) k procházení jiných uživatelů, skupin nebo prostředků mimo jednotky pro správu. V centru pro správu Microsoft 365 se odfiltrují uživatelé mimo vymezené jednotky pro správu Správce s oborem. Můžete ale Procházet Další uživatele na portálu Azure AD, PowerShellu a dalších službách Microsoftu.

## <a name="next-steps"></a>Další kroky

- [Spravovat jednotky pro správu](admin-units-manage.md)
- [Správa uživatelů v jednotkách pro správu](admin-units-add-manage-users.md)
- [Správa skupin v jednotkách pro správu](admin-units-add-manage-groups.md)
- [Přiřazení vymezených rolí k jednotce pro správu](admin-units-assign-roles.md)
