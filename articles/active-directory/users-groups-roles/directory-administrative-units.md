---
title: Správa administrativních jednotek (preview) – Azure AD | Dokumenty společnosti Microsoft
description: Použití jednotek pro správu pro podrobnější delegování oprávnění ve službě Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 04/16/2020
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 12101b170066bc52e75f4aa281c6f1c21283c004
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81406473"
---
# <a name="administrative-units-management-in-azure-active-directory-preview"></a>Správa administrativních jednotek ve službě Azure Active Directory (preview)

Tento článek popisuje jednotky pro správu ve službě Azure Active Directory (Azure AD). Jednotka pro správu je prostředek Azure AD, který může být kontejner pro jiné prostředky Azure AD. V této verzi preview může administrativní jednotka obsahovat pouze uživatele a skupiny.

Jednotky pro správu umožňují udělit oprávnění správce, která jsou omezena na oddělení, oblast nebo jiný segment organizace, který definujete. Pomocí jednotek pro správu můžete delegovat oprávnění na místní správce nebo nastavit zásady na podrobné úrovni. Správce uživatelského účtu může například aktualizovat informace o profilu, resetovat hesla a přiřazovat licence uživatelům pouze v jejich administrativní jednotce.

 Například delegování na regionální specialisty podpory role [správce helpdesku](directory-assign-admin-roles.md#helpdesk-administrator) omezena na správu pouze uživatelé v oblasti, kterou podporují.

## <a name="deployment-scenario"></a>Scénář nasazení

Omezení oboru správy pomocí administrativních jednotek může být užitečné v organizacích, které se sřučí z nezávislých divizí jakéhokoli druhu. Vezměme si příklad velké univerzity, která se skládá z mnoha autonomních škol (School of Business, School of Engineering, a tak dále), že každý má tým it administrátorů, kteří řídí přístup, spravovat uživatele a nastavit zásady pro jejich školy. Ústřední správce by mohl:

- Vytvoření role s oprávněními správce pouze před uživateli Služby Azure AD v jednotce pro správu podnikové školy
- Vytvoření administrativní jednotky pro obchodní školu
- Naplňte administrativní jednotku pouze studenty a zaměstnanci obchodní školy
- Přidání IT týmu obchodní školy do role s jejich rozsahem

## <a name="license-requirements"></a>Licenční požadavky

Použití jednotek pro správu vyžaduje licenci Azure Active Directory Premium pro každého správce jednotky pro správu a licence Azure Active Directory Free pro členy jednotky pro správu. Další informace najdete [v tématu Začínáme s Azure AD Premium](../fundamentals/active-directory-get-started-premium.md).

## <a name="manage-administrative-units"></a>Správa administrativních jednotek

V této verzi preview můžete spravovat jednotky pro správu pomocí portálu Azure, rutin a skriptů prostředí PowerShell nebo Microsoft Graphu. Podrobnosti naleznete v naší dokumentaci:

- [Vytvoření, odebrání, vyplnění a přidání rolí do administrativních jednotek](roles-admin-units-manage.md): Dokončení postupů s postupy
- [Práce s jednotkami pro správu:](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0)Jak pracovat s jednotkami pro správu pomocí Prostředí PowerShell
- [Podpora grafu administrativní jednotky](https://docs.microsoft.com/graph/api/resources/administrativeunit?view=graph-rest-beta): Podrobná dokumentace k aplikaci Microsoft Graph pro administrativní jednotky.

### <a name="planning-your-administrative-units"></a>Plánování administrativních jednotek

Jednotky pro správu lze logicky seskupit prostředky Azure AD. Například pro organizaci, jejíž oddělení IT je rozptýleno globálně, může mít smysl vytvořit administrativní jednotky, které definují tyto zeměpisné hranice. V jiném scénáři, kdy nadnárodní organizace má různé "podorganizace", které jsou semi-autonomní v provozu, každá podorganizace může být zastoupena správní jednotkou.

Kritéria, na kterých jsou vytvořeny administrativní jednotky, se budou řídit jedinečnými požadavky organizace. Administrativní jednotky jsou běžným způsobem definování struktury napříč službami M365. Doporučujeme připravit administrativní jednotky s ohledem na jejich využití v rámci služeb M365. Maximální hodnotu můžete získat z administrativních jednotek, pokud můžete přidružit společné prostředky v rámci m365 pod administrativní jednotkou.

Můžete očekávat, že vytvoření administrativních jednotek v organizaci projít následující fáze:

1. Počáteční přijetí: Vaše organizace začne vytvářet administrativní jednotky na základě počátečních kritérií a počet jednotek pro správu se bude zvyšovat s upřesněním kritérií.
1. Prořezávání: Jakmile jsou kritéria dobře definována, budou odstraněny administrativní jednotky, které již nejsou vyžadovány.
1. Stabilizace: Organizační struktura je dobře definována a počet administrativních jednotek se během krátké doby výrazně nezmění.

## <a name="currently-supported-scenarios"></a>Aktuálně podporované scénáře

Globální správci nebo správci privilegovaných rolí můžou pomocí portálu Azure AD vytvářet jednotky pro správu, přidávat uživatele jako členy jednotek pro správu a pak přiřazovat pracovníky IT k rolím správce s rozsahem správy. Správci s rozsahem správy pak můžou používat portál Office 365 pro základní správu uživatelů v jejich administrativních jednotkách.

Kromě toho skupiny mohou být přidány jako členové administrativní jednotky a správce skupiny správce jednotky správce je můžete spravovat pomocí PowerShell, Microsoft Graph a portál Azure AD.

Níže uvedená tabulka popisuje aktuální podporu pro scénáře správních jednotek.

### <a name="administrative-unit-management"></a>Vedení správní jednotky

Oprávnění |   MS Graf/PowerShell   | Portál Azure AD | Centrum pro správu Microsoftu 365
----------- | ----------------------- | --------------- | -----------------
Vytváření a mazání administrativních jednotek   |    Podporuje se    |   Podporuje se   |    Nepodporuje se
Individuální přidávání a odebírání členů správní jednotky    |   Podporuje se    |   Podporuje se   |    Nepodporuje se
Hromadné přidávání a odebírání členů správní jednotky pomocí souboru CSV   |    Nepodporuje se     |  Podporuje se   |    Žádný plán na podporu
Přiřazení správců masu s rozsahem správní jednotky  |     Podporuje se    |   Podporuje se    |   Nepodporuje se
Dynamické přidávání a odebírání členů AU na základě atributů | Nepodporuje se | Nepodporuje se | Nepodporuje se

### <a name="user-management"></a>Správa uživatelů

Oprávnění |   MS Graf/PowerShell   | Portál Azure AD | Centrum pro správu Microsoftu 365
----------- | ----------------------- | --------------- | -----------------
správa uživatelských vlastností, hesel, licencí s rozsahem administrativní jednotky   |    Podporuje se     |  Podporuje se   |   Podporuje se
blokování a odblokování přihlášení uživatele s rozsahem jednotky    |   Podporuje se   |    Podporuje se   |    Podporuje se
správa pověření vícefaktorové ověřování uživatelů s rozsahem správy   |    Podporuje se   |   Podporuje se   |   Nepodporuje se

### <a name="group-management"></a>Správa skupin

Oprávnění |   MS Graf/PowerShell   | Portál Azure AD | Centrum pro správu Microsoftu 365
----------- | ----------------------- | --------------- | -----------------
správa vlastností a členů s oborem správní jednotky     |  Podporuje se   |    Podporuje se    |  Nepodporuje se
správa skupinových licencí s rozsahem správní jednotky   |    Podporuje se  |    Podporuje se   |   Nepodporuje se

> [!NOTE]
>
> Správci s oborem správy nemohou spravovat pravidla dynamického členství ve skupinách.

Jednotky pro správu používají obor pouze pro oprávnění správce. Nezabrání členům nebo správcům v používání [výchozích uživatelských oprávnění](../fundamentals/users-default-permissions.md) k procházení jiných uživatelů, skupin nebo prostředků mimo jednotku pro správu. Na portálu Office 365 jsou odfiltrováni uživatelé mimo administrativní jednotky správce s rozsahem, ale ostatní uživatele můžete procházet na portálu Azure AD, PowerShellu a dalších službách Microsoftu.

## <a name="next-steps"></a>Další kroky

- [Správa AUs](roles-admin-units-manage.md)
- [Správa uživatelů v au](roles-admin-units-add-manage-users.md)
- [Správa skupin v au](roles-admin-units-add-manage-groups.md)
- [Přiřazení rolí s vymezeným oborem k AU](roles-admin-units-assign-roles.md)