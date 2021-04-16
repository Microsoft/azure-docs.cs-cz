---
title: Běžné scénáře správy nároků – Azure AD
description: Seznamte se se základními kroky, které byste měli postupovat při běžných scénářích Azure Active Directory správě nároků.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 28c16e4d73fc2379806e1a2bce2fa5dbb3247fed
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531961"
---
# <a name="common-scenarios-in-azure-ad-entitlement-management"></a>Běžné scénáře správy nároků ve službě Azure AD

Existuje několik způsobů, jak můžete nakonfigurovat správu nároků pro vaši organizaci. Pokud však teprve začínáte, je vhodné porozumět běžným scénářům správců, vlastníkům katalogu, správcům balíčků, schvalovatelům a žadatelům.

## <a name="delegate"></a>Delegát

### <a name="administrator-delegate-management-of-resources"></a>Správce: delegování správy prostředků

1. [Přehrát video: delegování z něho do oddělení IT Manager](https://www.microsoft.com/videoplayer/embed/RE3Lq00)
1. [Delegovat uživatele na roli tvůrce katalogu](entitlement-management-delegate-catalog.md)

### <a name="catalog-creator-delegate-management-of-resources"></a>Tvůrce katalogu: delegování správy prostředků

- [Vytvořit nový katalog](entitlement-management-catalog-create.md#create-a-catalog)

### <a name="catalog-owner-delegate-management-of-resources"></a>Vlastník katalogu: delegování správy prostředků

1. [Přidat spoluvlastníky do katalogu](entitlement-management-catalog-create.md#add-additional-catalog-owners)
1. [Přidání prostředků do katalogu](entitlement-management-catalog-create.md#add-resources-to-a-catalog)

### <a name="catalog-owner-delegate-management-of-access-packages"></a>Vlastník katalogu: delegování správy balíčků přístupu

1. [Přehrát video: delegování z vlastníka katalogu na přístup správce balíčků](https://www.microsoft.com/videoplayer/embed/RE3Lq08)
1. [Delegovat uživatelům přístup k roli správce balíčků](entitlement-management-delegate-managers.md)

## <a name="govern-access-for-users-in-your-organization"></a>Řízení přístupu pro uživatele ve vaší organizaci

### <a name="access-package-manager-allow-employees-in-your-organization-to-request-access-to-resources"></a>Přístup správce balíčků: umožňuje zaměstnancům ve vaší organizaci požádat o přístup k prostředkům.

1. [Vytvoření nového přístupového balíčku](entitlement-management-access-package-create.md#start-new-access-package)
1. [Přidání skupin, týmů, aplikací nebo webů SharePointu pro přístup k balíčku](entitlement-management-access-package-create.md#resource-roles)
1. [Přidejte zásadu žádosti, která uživatelům ve vašem adresáři umožní požádat o přístup.](entitlement-management-access-package-create.md#for-users-in-your-directory)
1. [Zadat nastavení vypršení platnosti](entitlement-management-access-package-create.md#lifecycle)

### <a name="requestor-request-access-to-resources"></a>Žadatel: vyžádání přístupu k prostředkům

1. [Přihlášení k portálu pro přístup](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. Najít balíček pro přístup
1. [Vyžádání přístup](entitlement-management-request-access.md#request-an-access-package)

### <a name="approver-approve-requests-to-resources"></a>Schvalovatel: schválení žádostí na prostředky

1. [Otevřít žádost na portálu pro přístup](entitlement-management-request-approve.md#open-request)
1. [Schválit nebo zamítnout žádost o přístup](entitlement-management-request-approve.md#approve-or-deny-request)

### <a name="requestor-view-the-resources-you-already-have-access-to"></a>Žadatel: zobrazení prostředků, ke kterým už máte přístup

1. [Přihlášení k portálu pro přístup](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. Zobrazit balíčky aktivního přístupu

## <a name="govern-access-for-users-outside-your-organization"></a>Řízení přístupu pro uživatele mimo vaši organizaci

### <a name="administrator-collaborate-with-an-external-partner-organization"></a>Správce: spolupráce s externí partnerskou organizací

1. [Přečtěte si, jak funguje přístup pro externí uživatele](entitlement-management-external-users.md#how-access-works-for-external-users)
1. [Kontrola nastavení pro externí uživatele](entitlement-management-external-users.md#settings-for-external-users)
1. [Přidání připojení k externí organizaci](entitlement-management-organization.md)

### <a name="access-package-manager-collaborate-with-an-external-partner-organization"></a>Přístup ke Správci balíčků: spolupráce s externí organizací partnera

1. [Vytvoření nového přístupového balíčku](entitlement-management-access-package-create.md#start-new-access-package)
1. [Přidání skupin, týmů, aplikací nebo webů SharePointu pro přístup k balíčku](entitlement-management-access-package-resources.md#add-resource-roles)
1. [Přidejte zásadu žádosti, která uživatelům, kteří nejsou ve vašem adresáři, umožní požádat o přístup.](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
1. [Zadat nastavení vypršení platnosti](entitlement-management-access-package-create.md#lifecycle)
1. [Zkopírovat odkaz pro vyžádání přístupového balíčku](entitlement-management-access-package-settings.md)
1. Poslat odkaz na externího partnera kontaktní osoby pro sdílení s uživateli

### <a name="requestor-request-access-to-resources-as-an-external-user"></a>Žadatel: vyžádat přístup k prostředkům jako externí uživatel

1. Najít odkaz na balíček přístupu, který jste dostali od svého kontaktu
1. [Přihlášení k portálu pro přístup](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. [Vyžádání přístup](entitlement-management-request-access.md#request-an-access-package)

### <a name="approver-approve-requests-to-resources"></a>Schvalovatel: schválení žádostí na prostředky

1. [Otevřít žádost na portálu pro přístup](entitlement-management-request-approve.md#open-request)
1. [Schválit nebo zamítnout žádost o přístup](entitlement-management-request-approve.md#approve-or-deny-request)

### <a name="requestor-view-the-resources-your-already-have-access-to"></a>Žadatel: zobrazení prostředků, ke kterým už máte přístup

1. [Přihlášení k portálu pro přístup](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. Zobrazit balíčky aktivního přístupu

## <a name="day-to-day-management"></a>Každodenní správa

### <a name="access-package-manager-update-the-resources-for-a-project"></a>Přístup správce balíčků: aktualizace prostředků projektu

1. [Přehrát video: každodenní správa: věci se změnily](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. Otevření balíčku pro přístup
1. [Přidání nebo odebrání skupin, týmů, aplikací nebo webů SharePointu](entitlement-management-access-package-resources.md#add-resource-roles)

### <a name="access-package-manager-update-the-duration-for-a-project"></a>Přístup správce balíčků: aktualizace doby trvání projektu

1. [Přehrát video: každodenní správa: věci se změnily](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. Otevření balíčku pro přístup
1. [Otevřete nastavení životního cyklu.](entitlement-management-access-package-lifecycle-policy.md#open-lifecycle-settings)
1. [Aktualizace nastavení vypršení platnosti](entitlement-management-access-package-lifecycle-policy.md#lifecycle) 

### <a name="access-package-manager-update-how-access-is-approved-for-a-project"></a>Přístup správce balíčků: aktualizace způsobu schválení přístupu pro projekt

1. [Přehrát video: každodenní správa: věci se změnily](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. [Otevře existující zásadu nastavení požadavků.](entitlement-management-access-package-request-policy.md#open-an-existing-access-package-and-add-a-new-policy-of-request-settings)
1. [Aktualizace nastavení schválení](entitlement-management-access-package-approval-policy.md#change-approval-settings-of-an-existing-access-package)

### <a name="access-package-manager-update-the-people-for-a-project"></a>Přístup správce balíčků: aktualizace uživatelů pro projekt

1. [Přehrát video: každodenní správa: věci se změnily](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. [Odebrat uživatele, kteří už nepotřebují přístup](entitlement-management-access-package-assignments.md)
1. [Otevře existující zásadu nastavení požadavků.](entitlement-management-access-package-request-policy.md#open-an-existing-access-package-and-add-a-new-policy-of-request-settings)
1. [Přidat uživatele, kteří potřebují přístup](entitlement-management-access-package-request-policy.md#for-users-in-your-directory)

### <a name="access-package-manager-directly-assign-specific-users-to-an-access-package"></a>Přístup správce balíčků: přímé přiřazení konkrétních uživatelů k balíčku pro přístup

1. [Pokud uživatelé potřebují jiné nastavení životního cyklu, přidejte do balíčku pro přístup novou zásadu.](entitlement-management-access-package-request-policy.md#open-an-existing-access-package-and-add-a-new-policy-of-request-settings)
1. [Přímo přiřadit konkrétní uživatele k balíčku pro přístup](entitlement-management-access-package-assignments.md#directly-assign-a-user)

## <a name="assignments-and-reports"></a>Přiřazení a sestavy

### <a name="administrator-view-who-has-assignments-to-an-access-package"></a>Správce: zobrazení, kdo má přiřazení k balíčku přístupu

1. Otevření balíčku pro přístup
1. [Zobrazit přiřazení](entitlement-management-access-package-assignments.md#view-who-has-an-assignment)
1. [Archivace sestav a protokolů](entitlement-management-logs-and-reporting.md)

### <a name="administrator-view-resources-assigned-to-users"></a>Správce: zobrazení prostředků přiřazených uživatelům

1. [Zobrazit balíčky pro přístup pro uživatele](entitlement-management-reports.md#view-access-packages-for-a-user)
1. [Zobrazení přiřazení prostředků pro uživatele](entitlement-management-reports.md#view-resource-assignments-for-a-user)

## <a name="programmatic-administration"></a>Programová správa

Pomocí Microsoft Graph můžete také spravovat balíčky přístupu, katalogy, zásady, požadavky a přiřazení.  Uživatel v příslušné roli s aplikací, která má delegované `EntitlementManagement.ReadWrite.All` oprávnění, může volat [rozhraní API pro správu nároků](/graph/tutorial-access-package-api).

## <a name="next-steps"></a>Další kroky

- [Delegování a role](entitlement-management-delegate.md)
- [Žádost o proces a e-mailová oznámení](entitlement-management-process.md)