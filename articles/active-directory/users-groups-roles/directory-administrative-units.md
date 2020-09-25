---
title: Jednotky pro správu v Azure Active Directory | Microsoft Docs
description: Použití jednotek pro správu k podrobnějšímu delegování oprávnění v Azure Active Directory
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
ms.openlocfilehash: a31c4a5c9d20b5b248f7d722ed3d9289bbb4dded
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91335812"
---
# <a name="administrative-units-in-azure-active-directory"></a>Jednotky pro správu v Azure Active Directory

Tento článek popisuje jednotky pro správu v Azure Active Directory (Azure AD). Jednotka pro správu je prostředek služby Azure AD, který může být kontejnerem pro jiné prostředky Azure AD. Jednotka pro správu může obsahovat pouze uživatele a skupiny.

Jednotky pro správu umožňují udělit oprávnění správce, která jsou omezená na oddělení, oblast nebo jiný segment vaší organizace, kterou definujete. Jednotky pro správu můžete použít k delegování oprávnění pro místní správce nebo k nastavení zásad na podrobné úrovni. Správce uživatelských účtů může například aktualizovat informace o profilu, resetovat hesla a přiřazovat licence uživatelům pouze ve své jednotce pro správu.

Například delegování na oblastní specialisty podpory: role [správce helpdesku](directory-assign-admin-roles.md#helpdesk-administrator) omezená na správu pouze uživatelů v oblasti, které podporují.

## <a name="deployment-scenario"></a>Scénář nasazení

Omezení rozsahu správy pomocí jednotek pro správu může být užitečné v organizacích, které jsou tvořeny nezávislými divizemi jakéhokoli druhu. Vezměte v úvahu příklad velké školy, který se skládá z mnoha autonomních škol (školy firmy, školy a tak dále), že každý má tým správců IT, kteří řídí přístup, spravují uživatele a nastavují zásady pro své školy. Centrální správce může:

- Vytvoření role s oprávněními správce jenom pro uživatele Azure AD v jednotce pro správu Business School
- Vytvoření jednotky pro správu pro školu firmy
- Naplnit jednotku pro správu jenom s studenty a zaměstnanci společnosti Business School
- Přidejte do role IT tým obchodní školy s jejich oborem.

## <a name="license-requirements"></a>Licenční požadavky

Použití jednotek pro správu vyžaduje licenci Azure Active Directory Premium pro každého správce jednotky správy a Azure Active Directory Free licence pro členy jednotek pro správu. Další informace najdete v tématu [Začínáme s Azure AD Premium](../fundamentals/active-directory-get-started-premium.md).

## <a name="manage-administrative-units"></a>Spravovat jednotky pro správu

Jednotky pro správu můžete spravovat pomocí Azure Portal, rutin prostředí PowerShell a skriptů nebo Microsoft Graph. Podrobnosti najdete v naší dokumentaci:

- [Vytváření, odebírání, naplnění a přidávání rolí do jednotek pro správu](roles-admin-units-manage.md): kompletní postupy postupů
- [Práce s jednotkami](/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0&preserve-view=true)pro správu: jak pracovat s jednotkami pro správu pomocí PowerShellu
- [Podpora grafu jednotek pro správu](/graph/api/resources/administrativeunit?view=graph-rest-beta&preserve-view=true): podrobná dokumentace k Microsoft Graph pro jednotky pro správu.

### <a name="planning-your-administrative-units"></a>Plánování jednotek pro správu

Jednotky pro správu lze použít k logickému seskupení prostředků služby Azure AD. Například pro organizaci, jejíž oddělení IT je celkově rozptýlené, může být vhodné vytvořit administrativní jednotky, které definují tyto geografické hranice. V jiném scénáři, kde má více národních organizací odlišnou "podkategorie", které jsou částečně nezávislé na operacích, může být každá dílčí organizace reprezentována jednotkou správy.

Kritéria, na kterých se vytvářejí jednotky pro správu, budou provádět jedinečné požadavky organizace. Jednotky pro správu představují běžný způsob, jak definovat strukturu napříč Microsoft 365 službami. Doporučujeme, abyste připravili jednotky pro správu s využitím v rámci Microsoft 365 služby. Pokud k přidružení běžných prostředků mezi Microsoft 365 v rámci jednotky pro správu, můžete získat maximální hodnotu mimo jednotky pro správu.

Můžete očekávat, že vytváření jednotek pro správu v organizaci projdete v následujících fázích:

1. Počáteční přijetí: vaše organizace začne vytvářet jednotky pro správu na základě počátečních kritérií a počet jednotek pro správu se zvýší, protože jsou tato kritéria upřesněna.
1. Vyřazení: Jakmile jsou kritéria dobře definovaná, odeberou se jednotky pro správu, které už nejsou potřeba.
1. Stabilizace: vaše organizační struktura je dobře definovaná a počet administrativních jednotek se nemění významně po krátkou dobu.

## <a name="currently-supported-scenarios"></a>Aktuálně podporované scénáře

Globální správci nebo správci privilegovaných rolí můžou pomocí portálu Azure AD vytvořit jednotky pro správu, přidat uživatele jako členy jednotek pro správu a pak přiřadit IT oddělení k rolím správců vymezeným jednotce správy. Správci s rozsahem administrativní jednotky mohou potom použít Centrum pro správu Microsoft 365 pro základní správu uživatelů v jejich administrativních jednotkách.

Kromě toho je možné přidat skupiny jako členy jednotky pro správu a správce skupin s oborem správy, který je může spravovat pomocí PowerShellu, Microsoft Graph a portálu Azure AD.

Následující tabulka popisuje aktuální podporu scénářů pro správu jednotek.

### <a name="administrative-unit-management"></a>Správa jednotek pro správu

Oprávnění |   MS Graph/PowerShell   | Portál Azure AD | Centrum pro správu služby Microsoft 365
----------- | ----------------------- | --------------- | -----------------
Vytváření a odstraňování jednotek pro správu   |    Podporováno    |   Podporováno   |    Nepodporováno
Přidávání a odebírání členů administrativní jednotky jednotlivě    |   Podporováno    |   Podporováno   |    Nepodporováno
Hromadné přidávání a odebírání členů jednotek pro správu pomocí souboru. csv   |    Nepodporováno     |  Podporováno   |    Žádný plán k podpoře
Přiřazování správců v rámci jednotky pro správu  |     Podporováno    |   Podporováno    |   Nepodporováno
Dynamické přidávání a odebírání členů AU na základě atributů | Nepodporováno | Nepodporováno | Nepodporováno

### <a name="user-management"></a>Správa uživatelů

Oprávnění |   MS Graph/PowerShell   | Portál Azure AD | Centrum pro správu služby Microsoft 365
----------- | ----------------------- | --------------- | -----------------
Správa vlastností uživatele, hesel a licencí s rozsahem administrativní jednotky   |    Podporováno     |  Podporováno   |   Podporováno
blokující a odblokování přihlášení uživatelů v oboru jednotky pro správu    |   Podporováno   |    Podporováno   |    Podporováno
Správa přihlašovacích údajů uživatelského ověřování s rozsahem jednotky pro správu   |    Podporováno   |   Podporováno   |   Nepodporováno

### <a name="group-management"></a>Správa skupin

Oprávnění |   MS Graph/PowerShell   | Portál Azure AD | Centrum pro správu služby Microsoft 365
----------- | ----------------------- | --------------- | -----------------
Správa vlastností skupin a členů s rozsahem jednotky pro správu     |  Podporováno   |    Podporováno    |  Nepodporováno
Správa licencování skupin pro správu s rozsahem jednotky   |    Podporováno  |    Podporováno   |   Nepodporováno

> [!NOTE]
>
> Správci s oborem jednotky pro správu nemůžou spravovat pravidla členství v dynamické skupině.

Jednotky pro správu se vztahují pouze na oprávnění pro správu. Nebrání členům ani správcům používat [výchozí uživatelská oprávnění](../fundamentals/users-default-permissions.md) k procházení jiných uživatelů, skupin nebo prostředků mimo jednotky pro správu. V centru pro správu Microsoft 365 se odfiltrují uživatelé mimo jednotky pro správu s vymezeným oborem správy, ale můžete procházet jiné uživatele na portálu Azure AD, PowerShellu a dalších službách Microsoftu.

## <a name="next-steps"></a>Další kroky

- [Správa jednotek Austrálie](roles-admin-units-manage.md)
- [Správa uživatelů v nástroji Austrálie](roles-admin-units-add-manage-users.md)
- [Správa skupin v nástroji Austrálie](roles-admin-units-add-manage-groups.md)
- [Přiřazení vymezených rolí k jednotce AU](roles-admin-units-assign-roles.md)
