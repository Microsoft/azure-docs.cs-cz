---
title: Běžné scénáře ve správě nároků – Azure AD
description: Seznamte se s kroky na vysoké úrovni, které byste měli provést při běžných scénářích ve správě nároků služby Azure Active Directory.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/28/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: d9d259c6e2a6ac9ced5f9a1c29d4aec08010f4dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78190547"
---
# <a name="common-scenarios-in-azure-ad-entitlement-management"></a>Běžné scénáře ve správě oprávnění Azure AD

Existuje několik způsobů, jak můžete nakonfigurovat správu nároků pro vaši organizaci. Pokud však teprve začínáte, je užitečné porozumět běžným scénářům pro správce, vlastníky katalogů, správce balíčků, schvalovatele a žadatele.

## <a name="delegate"></a>Delegát

### <a name="administrator-delegate-management-of-resources"></a>Správce: Delegování správy prostředků

1. [Podívejte se na video: Delegace z IT na vedoucíoddělení](https://www.microsoft.com/videoplayer/embed/RE3Lq00)
1. [Delegování uživatelů na roli tvůrce katalogu](entitlement-management-delegate-catalog.md)

### <a name="catalog-creator-delegate-management-of-resources"></a>Tvůrce katalogu: Delegování správy prostředků

- [Vytvoření nového katalogu](entitlement-management-catalog-create.md#create-a-catalog)

### <a name="catalog-owner-delegate-management-of-resources"></a>Vlastník katalogu: Delegování správy prostředků

1. [Přidání spoluvlastníků do katalogu](entitlement-management-catalog-create.md#add-additional-catalog-owners)
1. [Přidání prostředků do katalogu](entitlement-management-catalog-create.md#add-resources-to-a-catalog)

### <a name="catalog-owner-delegate-management-of-access-packages"></a>Vlastník katalogu: Správa delegátů přístupových balíčků

1. [Podívejte se na video: Delegování od vlastníka katalogu pro přístup ke správci balíčků](https://www.microsoft.com/videoplayer/embed/RE3Lq08)
1. [Delegování uživatelů pro přístup k roli správce balíčků](entitlement-management-delegate-managers.md)

## <a name="govern-access-for-users-in-your-organization"></a>Řízení přístupu uživatelů ve vaší organizaci

### <a name="access-package-manager-allow-employees-in-your-organization-to-request-access-to-resources"></a>Správce balíčků aplikace Access: Povolte zaměstnancům ve vaší organizaci požádat o přístup k prostředkům.

1. [Vytvoření nového přístupového balíčku](entitlement-management-access-package-create.md#start-new-access-package)
1. [Přidání skupin, teams, aplikací nebo sharepointových webů pro přístup k balíčku](entitlement-management-access-package-create.md#resource-roles)
1. [Přidání zásad žádosti, která uživatelům ve vašem adresáři umožní požádat o přístup](entitlement-management-access-package-create.md#for-users-in-your-directory)
1. [Určení nastavení vypršení platnosti](entitlement-management-access-package-create.md#lifecycle)

### <a name="requestor-request-access-to-resources"></a>Žadatel: Požádat o přístup k prostředkům

1. [Přihlášení k portálu Můj přístup](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. Najít přístupový balíček
1. [Vyžádání přístup](entitlement-management-request-access.md#request-an-access-package)

### <a name="approver-approve-requests-to-resources"></a>Schvalovatel: Schválení žádostí o zdroje

1. [Otevřít požadavek na portálu Můj Přístup](entitlement-management-request-approve.md#open-request)
1. [Schválení nebo zamítnutí žádosti o přístup](entitlement-management-request-approve.md#approve-or-deny-request)

### <a name="requestor-view-the-resources-you-already-have-access-to"></a>Žadatel: Zobrazení prostředků, ke kterých již máte přístup.

1. [Přihlášení k portálu Můj přístup](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. Zobrazit balíčky aktivního přístupu

## <a name="govern-access-for-users-outside-your-organization"></a>Řízení přístupu pro uživatele mimo vaši organizaci

### <a name="administrator-collaborate-with-an-external-partner-organization"></a>Správce: Spolupráce s externí partnerskou organizací

1. [Přečtěte si, jak funguje přístup pro externí uživatele](entitlement-management-external-users.md#how-access-works-for-external-users)
1. [Kontrola nastavení pro externí uživatele](entitlement-management-external-users.md#settings-for-external-users)
1. [Přidání připojení k externí organizaci](entitlement-management-organization.md)

### <a name="access-package-manager-collaborate-with-an-external-partner-organization"></a>Správce balíčků accessu: Spolupráce s externí partnerskou organizací

1. [Vytvoření nového přístupového balíčku](entitlement-management-access-package-create.md#start-new-access-package)
1. [Přidání skupin, teams, aplikací nebo sharepointových webů pro přístup k balíčku](entitlement-management-access-package-resources.md#add-resource-roles)
1. [Přidání zásad žádosti, která uživatelům, kteří nejsou ve vašem adresáři, umožní požádat o přístup](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
1. [Určení nastavení vypršení platnosti](entitlement-management-access-package-create.md#lifecycle)
1. [Zkopírujte odkaz a požádejte o přístupový balíček](entitlement-management-access-package-settings.md)
1. Odeslání odkazu externímu partnerovi kontaktu pro sdílení s uživateli

### <a name="requestor-request-access-to-resources-as-an-external-user"></a>Žadatel: Požádat o přístup k prostředkům jako externí uživatel

1. Vyhledání odkazu na přístupový balíček, který jste obdrželi od kontaktu
1. [Přihlášení k portálu Můj přístup](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. [Vyžádání přístup](entitlement-management-request-access.md#request-an-access-package)

### <a name="approver-approve-requests-to-resources"></a>Schvalovatel: Schválení žádostí o zdroje

1. [Otevřít požadavek na portálu Můj Přístup](entitlement-management-request-approve.md#open-request)
1. [Schválení nebo zamítnutí žádosti o přístup](entitlement-management-request-approve.md#approve-or-deny-request)

### <a name="requestor-view-the-resources-your-already-have-access-to"></a>Žadatel: Zobrazení prostředků, ke kterým již máte přístup

1. [Přihlášení k portálu Můj přístup](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. Zobrazit balíčky aktivního přístupu

## <a name="day-to-day-management"></a>Každodenní řízení

### <a name="access-package-manager-update-the-resources-for-a-project"></a>Správce balíčků aplikace Access: Aktualizace zdrojů pro projekt

1. [Podívejte se na video: Každodenní řízení: Věci se změnily](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. Otevření přístupového balíčku
1. [Přidání nebo odebrání skupin, teams, aplikací nebo sharepointových webů](entitlement-management-access-package-resources.md#add-resource-roles)

### <a name="access-package-manager-update-the-duration-for-a-project"></a>Správce balíčků aplikace Access: Aktualizace doby trvání projektu

1. [Podívejte se na video: Každodenní řízení: Věci se změnily](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. Otevření přístupového balíčku
1. [Otevření nastavení životního cyklu](entitlement-management-access-package-lifecycle-policy.md#open-lifecycle-settings)
1. [Aktualizace nastavení vypršení platnosti](entitlement-management-access-package-lifecycle-policy.md#lifecycle)

### <a name="access-package-manager-update-how-access-is-approved-for-a-project"></a>Správce balíčků aplikace Access: Aktualizace způsobu schválení přístupu pro projekt

1. [Podívejte se na video: Každodenní řízení: Věci se změnily](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. [Otevření existující zásady nastavení požadavku a schválení](entitlement-management-access-package-request-policy.md#open-an-existing-policy-of-request-and-approval-settings)
1. [Aktualizace nastavení schválení](entitlement-management-access-package-request-policy.md#approval)

### <a name="access-package-manager-update-the-people-for-a-project"></a>Správce balíčků aplikace Access: Aktualizace osob pro projekt

1. [Podívejte se na video: Každodenní řízení: Věci se změnily](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. [Odebrání uživatelů, kteří již nepotřebují přístup](entitlement-management-access-package-assignments.md)
1. [Otevření existující zásady nastavení požadavku a schválení](entitlement-management-access-package-request-policy.md#open-an-existing-policy-of-request-and-approval-settings)
1. [Přidání uživatelů, kteří potřebují přístup](entitlement-management-access-package-request-policy.md#for-users-in-your-directory)

### <a name="access-package-manager-directly-assign-specific-users-to-an-access-package"></a>Správce balíčků aplikace Access: Přímo přiřaďte konkrétní uživatele k přístupu

1. [Pokud uživatelé potřebují různá nastavení životního cyklu, přidejte do přístupového balíčku novou zásadu](entitlement-management-access-package-request-policy.md#add-a-new-policy-of-request-and-approval-settings)
1. [Přímé přiřazení konkrétních uživatelů k přístupovému balíčku](entitlement-management-access-package-assignments.md#directly-assign-a-user)

## <a name="assignments-and-reports"></a>Přiřazení a sestavy

### <a name="administrator-view-who-has-assignments-to-an-access-package"></a>Správce: Zobrazení, kdo má přiřazení k přístupovému balíčku

1. Otevření přístupového balíčku
1. [Zobrazit přiřazení](entitlement-management-access-package-assignments.md#view-who-has-an-assignment)
1. [Archivovat sestavy a protokoly](entitlement-management-logs-and-reporting.md)

### <a name="administrator-view-resources-assigned-to-users"></a>Správce: Zobrazení prostředků přiřazených uživatelům

1. [Zobrazení přístupových balíčků pro uživatele](entitlement-management-reports.md#view-access-packages-for-a-user)
1. [Zobrazení přiřazení zdrojů pro uživatele](entitlement-management-reports.md#view-resource-assignments-for-a-user)

## <a name="programmatic-administration"></a>Programová správa

Přístupové balíčky, katalogy, zásady, požadavky a přiřazení můžete také spravovat pomocí aplikace Microsoft Graph.  Uživatel v příslušné roli s aplikací, která `EntitlementManagement.ReadWrite.All` má delegované oprávnění, může volat [rozhraní API pro správu oprávnění](https://docs.microsoft.com/graph/api/resources/entitlementmanagement-root?view=graph-rest-beta).

## <a name="next-steps"></a>Další kroky

- [Delegování a role](entitlement-management-delegate.md)
- [Žádost o proces a e-mailová oznámení](entitlement-management-process.md)
