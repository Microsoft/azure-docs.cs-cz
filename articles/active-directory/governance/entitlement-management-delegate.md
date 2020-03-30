---
title: Delegování a role ve správě nároků – Azure AD
description: Zjistěte, jak delegovat zásadsprávné řízení přístupu od správců IT na manažery oddělení a projektové manažery, aby mohli spravovat přístup sami.
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
ms.date: 10/07/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 86d924860e97b15a0a4af46c5bc35b0e0050292b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261837"
---
# <a name="delegation-and-roles-in-azure-ad-entitlement-management"></a>Delegování a role ve správě oprávnění Azure AD

Ve výchozím nastavení globální správci a správci uživatelů můžete vytvořit a spravovat všechny aspekty správy oprávnění Azure AD. Uživatelé v těchto rolích však nemusí znát všechny situace, kde jsou vyžadovány balíčky přístupu. Obvykle se jedná o uživatele v rámci příslušných oddělení, týmů nebo projektů, kteří vědí, s kým spolupracují, používají jaké prostředky a jak dlouho. Namísto udělování neomezených oprávnění osobám, které nejsou správci, můžete uživatelům udělit nejméně oprávnění, která potřebují k provedení své úlohy, a vyhnout se vytváření konfliktních nebo nevhodných přístupových práv.

Toto video poskytuje přehled o tom, jak delegovat zásadsprávné řízení od správce IT uživatelům, kteří nejsou správci.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3Lq00]

## <a name="delegate-example"></a>Příklad delegáta

Chcete-li pochopit, jak můžete delegovat zásadsprávné řízení přístupu ve správě nároků, pomáhá zvážit příklad. Předpokládejme, že vaše organizace má následujícísprávce a manažery.

![Delegování ze správce IT na manažery](./media/entitlement-management-delegate/delegate-admin-dept-managers.png)

Jako správce IT má Hana kontakty v každém oddělení – Mamta v marketingu, Mark in Finance a Joe in Legal, kteří jsou zodpovědní za zdroje svého oddělení a důležitý obchodní obsah.

Pomocí správy oprávnění můžete delegovat zásadsprávné řízení přístupu těmto nesprávcům, protože jsou to ti, kteří vědí, kteří uživatelé potřebují přístup, jak dlouho a ke kterým prostředkům. Tím je zajištěno, že ti správní lidé spravují přístup ke svým oddělením.

Zde je jeden způsob, jak hana mohla delegovat řízení přístupu na marketing, finance a právní oddělení.

1. Hana vytvoří novou skupinu zabezpečení Azure AD a přidá Mamta, Mark a Joe jako členy skupiny.

1. Hana přidá tuto skupinu do role tvůrců katalogu.

    Mamta, Mark a Joe teď můžou vytvářet katalogy pro svá oddělení, přidávat prostředky, které jejich oddělení potřebují, a provádět další delegování v rámci katalogu.

    Všimněte si, že Mamta, Mark a Joe nemohou vidět navzájem katalogy.

1. Mamta vytvoří **marketingový** katalog, což je kontejner prostředků.

1. Mamta přidává zdroje, které její marketingové oddělení vlastní, do tohoto katalogu.

1. Mamta může přidat další osoby ze svého oddělení jako vlastníky katalogu pro tento katalog. To pomáhá sdílet odpovědnosti za správu katalogu.

1. Mamta může dále delegovat vytváření a správu přístupových balíčků v katalogu marketingu na projektové manažery v oddělení marketingu. Může to provést tak, že je přiřadí k roli správce balíčků přístupu. Správce přístupových balíčků může vytvářet a spravovat přístupové balíčky. 

Následující diagram znázorňuje katalogy se zdroji pro marketingové, finanční a právní oddělení. Pomocí těchto katalogů mohou vedoucí projektů vytvářet přístupové balíčky pro své týmy nebo projekty.

![Příklad delegáta správy oprávnění](./media/entitlement-management-delegate/elm-delegate.png)

Po delegování může mít marketingové oddělení role podobné následující tabulce.

| Uživatel | Pracovní role | Role Azure AD | Role správy nároků |
| --- | --- | --- | --- |
| Hana | Správce IT | Globální správce nebo správce uživatelů |  |
| Mamta | Marketingový manažer | Uživatel | Tvůrce katalogu a vlastník katalogu |
| Bob | Vedoucí marketingu | Uživatel | Vlastník katalogu |
| Jessica | Marketingový projektový manažer | Uživatel | Správce balíčků aplikace Access |

## <a name="entitlement-management-roles"></a>Role správy nároků

Správa nároků má následující role, které jsou specifické pro správu nároků.

| Role správy nároků | Popis |
| --- | --- |
| Tvůrce katalogu | Vytvářejte a spravujte katalogy. Správce IT, který není globálním správcem, nebo vlastníkem prostředků pro kolekci prostředků. Osoba, která vytvoří katalog, se automaticky stane prvním vlastníkem katalogu a může přidat další vlastníky katalogu. Tvůrce katalogu nemůže spravovat nebo zobrazit katalogy, které nevlastní, a nemůže do katalogu přidávat prostředky, které nevlastní. Pokud tvůrce katalogu potřebuje spravovat jiný katalog nebo přidat prostředky, které nevlastní, může požádat o co-vlastník tohoto katalogu nebo prostředku. |
| Vlastník katalogu | Upravujte a spravujte existující katalogy. Obvykle správce IT nebo vlastníci prostředků nebo uživatel, kterého vlastník katalogu určil. |
| Správce balíčků aplikace Access | Upravte a spravujte všechny existující balíčky přístupu v rámci katalogu. |

Kromě toho určený schvalovatel a žadatel o přístup balíček mají také práva, i když nejsou role.

| Vpravo | Popis |
| --- | --- |
| Schvalovatel | Autorizováno zásadou ke schválení nebo zamítnutí žádostí o přístup k balíčkům, i když nemohou změnit definice balíčků přístupu. |
| Requestor | Autorizováno zásadami přístupového balíčku k vyžádání tohoto přístupového balíčku. |

V následující tabulce jsou uvedeny úkoly, které mohou role správy nároků provádět.

| Úkol | Správce | Tvůrce katalogu | Vlastník katalogu | Správce balíčků aplikace Access |
| --- | :---: | :---: | :---: | :---: |
| [Delegát na tvůrce katalogu](entitlement-management-delegate-catalog.md) | :heavy_check_mark: |  |  |  |
| [Přidání připojené organizace](entitlement-management-organization.md) | :heavy_check_mark: |  |  |  |
| [Vytvoření nového katalogu](entitlement-management-catalog-create.md) | :heavy_check_mark: | :heavy_check_mark: |  |  |
| [Přidání zdroje do katalogu](entitlement-management-catalog-create.md#add-resources-to-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [Přidání vlastníka katalogu](entitlement-management-catalog-create.md#add-additional-catalog-owners) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [Úprava katalogu](entitlement-management-catalog-create.md#edit-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [Odstranění katalogu](entitlement-management-catalog-create.md#delete-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [Delegát na správce balíčků přístupu](entitlement-management-delegate-managers.md) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [Odebrání správce přístupových balíčků](entitlement-management-delegate-managers.md#remove-an-access-package-manager) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [Vytvoření nového přístupového balíčku v katalogu](entitlement-management-access-package-create.md) | :heavy_check_mark: |  | :heavy_check_mark:  | :heavy_check_mark:  |
| [Změna rolí prostředků v balíčku přístupu](entitlement-management-access-package-resources.md) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Vytváření a úpravy zásad](entitlement-management-access-package-request-policy.md) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Přímé přiřazení uživatele k přístupovému balíčku](entitlement-management-access-package-assignments.md#directly-assign-a-user) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Zobrazení, kdo má přiřazení k přístupovému balíčku](entitlement-management-access-package-assignments.md#view-who-has-an-assignment) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Zobrazení požadavků přístupového balíčku](entitlement-management-access-package-requests.md#view-requests) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Zobrazení chyb doručení požadavku](entitlement-management-troubleshoot.md#view-a-requests-delivery-errors) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Opětovné zpracování požadavku](entitlement-management-troubleshoot.md#reprocess-a-request) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Zrušení nevyřízené žádosti](entitlement-management-troubleshoot.md#cancel-a-pending-request) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Skrytí přístupového balíčku](entitlement-management-access-package-edit.md#change-the-hidden-setting) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Odstranění přístupového balíčku](entitlement-management-access-package-edit.md#delete-an-access-package) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |

## <a name="required-roles-to-add-resources-to-a-catalog"></a>Požadované role pro přidání prostředků do katalogu

Globální správce může přidat nebo odebrat libovolnou skupinu (skupiny zabezpečení vytvořené v cloudu nebo cloudové skupiny Office 365), aplikaci nebo web SharePointu Online v katalogu. Správce uživatele může přidat nebo odebrat libovolnou skupinu nebo aplikaci v katalogu.

Chcete-li přidat skupiny, aplikace nebo weby SharePointu Online do katalogu, musí mít tento uživatel požadovanou roli adresáře Azure AD *i* roli správy oprávnění vlastníka katalogu, aby mohl přidávat skupiny, aplikace nebo weby SharePointu Online do katalogu. V následující tabulce jsou uvedeny kombinace rolí, které jsou nutné k přidání prostředků do katalogu. Chcete-li odebrat prostředky z katalogu, musíte mít stejné role.

| Role adresáře Azure AD | Role správy nároků | Může přidat skupinu zabezpečení | Přidání skupiny Office 365 | Můžete přidat aplikaci | Přidání webu SharePointu Online |
| --- | :---: | :---: | :---: | :---: | :---: |
| [Globální správce](../users-groups-roles/directory-assign-admin-roles.md) | neuvedeno |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Správce uživatele](../users-groups-roles/directory-assign-admin-roles.md) | neuvedeno |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Správce Intune](../users-groups-roles/directory-assign-admin-roles.md) | Vlastník katalogu | :heavy_check_mark: | :heavy_check_mark: |  |  |
| [Správce Exchange](../users-groups-roles/directory-assign-admin-roles.md) | Vlastník katalogu |  | :heavy_check_mark: |  |  |
| [Správce teams- služeb](../users-groups-roles/directory-assign-admin-roles.md) | Vlastník katalogu |  | :heavy_check_mark: |  |  |
| [Správce SharePointu](../users-groups-roles/directory-assign-admin-roles.md) | Vlastník katalogu |  | :heavy_check_mark: |  | :heavy_check_mark: |
| [Správce aplikace](../users-groups-roles/directory-assign-admin-roles.md) | Vlastník katalogu |  |  | :heavy_check_mark: |  |
| [Správce cloudových aplikací](../users-groups-roles/directory-assign-admin-roles.md) | Vlastník katalogu |  |  | :heavy_check_mark: |  |
| Uživatel | Vlastník katalogu | Pouze v případě, že vlastník skupiny | Pouze v případě, že vlastník skupiny | Pouze v případě, že vlastník aplikace |  |

Chcete-li určit nejméně privilegovanou roli pro úlohu, můžete také odkazovat na [role správce podle úlohy správce ve službě Azure Active Directory](../users-groups-roles/roles-delegate-by-task.md#entitlement-management).

## <a name="next-steps"></a>Další kroky

- [Delegování zásad správného řízení přístupu na tvůrce katalogů](entitlement-management-delegate-catalog.md)
- [Vytvoření a správa katalogu zdrojů](entitlement-management-catalog-create.md)
