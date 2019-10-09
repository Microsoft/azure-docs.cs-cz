---
title: Delegování a role ve správě nároků ve službě Azure AD (Preview) – Azure Active Directory
description: Naučte se delegovat řízení přístupu od správců IT na manažery oddělení a vedoucí projektu, aby mohli spravovat přístup sami.
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
ms.openlocfilehash: 89cdab09e3ae03ddea6259eda657908f900f982e
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2019
ms.locfileid: "72169850"
---
# <a name="delegation-and-roles-in-azure-ad-entitlement-management-preview"></a>Delegování a role ve správě nároků ve službě Azure AD (Preview)

> [!IMPORTANT]
> Správa opravňujících k Azure Active Directory (Azure AD) je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ve výchozím nastavení můžou globální správci a správci uživatelů vytvářet a spravovat všechny aspekty správy nároků služby Azure AD. Uživatelé v těchto rolích ale nemusí znát všechny situace, kdy je potřeba získat přístup k balíčkům. Obvykle se jedná o uživatele v rámci příslušných oddělení, týmů nebo projektů, kteří vědí, kdo s nimi spolupracuje, pomocí jakých prostředků a jak dlouho. Místo udělení neomezených oprávnění nesprávcům můžete uživatelům udělit nejnižší oprávnění, která potřebují k provedení své úlohy, a vyhnout se tak vytváření konfliktních nebo nevhodných přístupových práv.

Toto video poskytuje přehled o tom, jak delegovat řízení přístupu od správce IT na uživatele, kteří nejsou správci.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3Lq00]

## <a name="delegate-example"></a>Příklad delegáta

Pokud chcete pochopit, jak byste mohli delegovat řízení přístupu ve správě nároků, je třeba vzít v úvahu příklad. Předpokládejme, že vaše organizace má následující správce a manažery.

![Delegovat od správce IT k manažerům](./media/entitlement-management-delegate/delegate-admin-dept-managers.png)

Jako správce IT má Hana kontakty v každém oddělení – Mamta marketing, označovat finance a Jana, kteří zodpovídají za prostředky svého oddělení a jejich důležitý obsah.

Správa nároků vám umožní delegovat řízení přístupu pro tyto správce bez oprávnění, protože se jedná o ty, kteří znají, kteří uživatelé potřebují mít přístup, jak dlouho a na jaké prostředky. Tím se zajistí, že uživatelé budou mít ke svým oddělením přístup i oprávnění.

Tady je jeden ze způsobů, jak může Hana delegovat řízení přístupu na marketing, finance a právní oddělení.

1. Hana vytvoří novou skupinu zabezpečení Azure AD a přidá jako členy skupiny Mamta, Mark a Jan.

1. Hana přidá tuto skupinu do role tvůrci katalogu.

    Mamta, Mark a Jana teď můžou vytvářet katalogy pro svoje oddělení, přidávat prostředky, které jejich oddělení potřebují, a provádět další delegování v rámci katalogu.

    Všimněte si, že Mamta, Mark a Jana se nemohou podívat na ostatní katalogy.

1. Mamta vytvoří **marketingový** katalog, který je kontejnerem prostředků.

1. Mamta přidá prostředky, které má marketingový oddělení vlastní k tomuto katalogu.

1. Mamta může přidat další lidi z jejího oddělení jako vlastníky katalogu pro tento katalog. To pomáhá sdílet zodpovědnost za správu katalogu.

1. Mamta může dále delegovat vytváření a správu balíčků přístupu v marketingovém katalogu do vedoucích projektů v marketingovém oddělení. To může provést přiřazením do role správce balíčků přístupu. Správce balíčků přístupu může vytvářet a spravovat balíčky přístupu. 

Následující diagram znázorňuje katalogy s prostředky pro marketingové, finanční a právní oddělení. Pomocí těchto katalogů mohou manažeři projektů vytvářet balíčky přístupu pro své týmy nebo projekty.

![Příklad delegáta správy oprávnění](./media/entitlement-management-delegate/elm-delegate.png)

Po delegování mohou mít marketingové oddělení stejné role jako v následující tabulce.

| Uživatel | Pracovní role | Role Azure AD | Role správy oprávnění |
| --- | --- | --- | --- |
| Hana | Správce IT | Globální správce nebo Správce uživatelů |  |
| Mamta | Marketingový manažer | Uživatel | Tvůrce katalogu a vlastník katalogu |
| Bobem | Marketingový zájemce | Uživatel | Vlastník katalogu |
| Jessica | Manažer marketingového projektu | Uživatel | Přístup ke Správci balíčků |

## <a name="entitlement-management-roles"></a>Role správy oprávnění

Správa nároků má následující role, které jsou specifické pro správu nároků.

| Role správy oprávnění | Popis |
| --- | --- |
| Tvůrce katalogu | Vytvářejte a spravujte katalogy. Obvykle správce IT, který není globálním správcem, nebo vlastníkem prostředku pro kolekci prostředků. Osoba, která automaticky vytvoří katalog, se bude nacházet jako s prvním vlastníkem katalogu katalogu a může přidat další vlastníky katalogu. Tvůrce katalogu nemůže spravovat ani zobrazovat katalogy, které nevlastní, a nemůže přidat prostředky, které nevlastní katalog. Pokud tvůrce katalogu potřebuje spravovat další katalog nebo přidat prostředky, které nevlastní, můžou požádat o spoluvlastníky tohoto katalogu nebo prostředku. |
| Vlastník katalogu | Umožňuje upravovat a spravovat existující katalogy. Obvykle správce IT nebo vlastníci prostředků nebo uživatel, který určil vlastníka katalogu. |
| Přístup ke Správci balíčků | Umožňuje upravovat a spravovat všechny existující balíčky přístupu v rámci katalogu. |

Kromě toho má určený schvalovatel a žadatel pro přístupovou sadu také práva, i když se nejedná o role.

| Kliknutím | Popis |
| --- | --- |
| Uživatelem | Autorizováno zásadou ke schválení nebo zamítnutí žádostí o přístup k balíčkům, i když nemůžou měnit definice přístupového balíčku. |
| Žadatele | Autorizován zásadou přístupového balíčku pro vyžádání přístupového balíčku. |

V následující tabulce jsou uvedeny úlohy, které mohou provádět role správy oprávnění.

| Úkol | Správ | Tvůrce katalogu | Vlastník katalogu | Přístup ke Správci balíčků |
| --- | :---: | :---: | :---: | :---: |
| [Delegovat na tvůrce katalogu](entitlement-management-delegate-catalog.md) | :heavy_check_mark: |  |  |  |
| [Vytvořit nový katalog](entitlement-management-catalog-create.md) | :heavy_check_mark: | :heavy_check_mark: |  |  |
| [Přidání prostředku do katalogu](entitlement-management-catalog-create.md#add-resources-to-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [Přidat vlastníka katalogu](entitlement-management-catalog-create.md#add-additional-catalog-owners) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [Úprava katalogu](entitlement-management-catalog-create.md#edit-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [Odstranění katalogu](entitlement-management-catalog-create.md#delete-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [Delegování do Správce balíčků přístupu](entitlement-management-delegate-managers.md) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [Odebrání správce balíčků přístupu](entitlement-management-delegate-managers.md#remove-an-access-package-manager) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [Vytvoření nového přístupového balíčku v katalogu](entitlement-management-access-package-create.md) | :heavy_check_mark: |  | :heavy_check_mark:  | :heavy_check_mark:  |
| [Správa rolí prostředků v balíčku pro přístup](entitlement-management-access-package-edit.md) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Vytvoření a úprava zásad](entitlement-management-access-package-edit.md#add-a-new-policy) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Přímé přiřazení uživatele k balíčku pro přístup](entitlement-management-access-package-edit.md#directly-assign-a-user) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Zobrazit, kdo má přiřazení k balíčku pro přístup](entitlement-management-access-package-edit.md#view-who-has-an-assignment) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Zobrazit žádosti balíčku pro přístup](entitlement-management-access-package-edit.md#view-requests) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Zobrazit chyby doručení žádosti](entitlement-management-access-package-edit.md#view-a-requests-delivery-errors) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Zrušení žádosti, která čeká na vyřízení](entitlement-management-access-package-edit.md#cancel-a-pending-request) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Skrytí přístupového balíčku](entitlement-management-access-package-edit.md#change-the-hidden-setting) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Odstranění balíčku pro přístup](entitlement-management-access-package-edit.md#delete) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |

## <a name="required-roles-to-add-resources-to-a-catalog"></a>Požadované role pro přidání prostředků do katalogu

Globální správce může přidat nebo odebrat libovolnou skupinu (v cloudu vytvořené skupiny zabezpečení nebo skupiny Office 365), aplikaci nebo web SharePointu Online v katalogu. Správce uživatele může přidat nebo odebrat libovolnou skupinu nebo aplikaci v katalogu.

Pro uživatele, který není globálním správcem nebo správcem uživatelů, pokud chcete přidat skupiny, aplikace nebo weby SharePointu Online do katalogu, musí mít tento uživatel *jak* požadovanou roli adresáře služby Azure AD, tak roli správy oprávnění vlastníka katalogu. V následující tabulce jsou uvedeny kombinace rolí, které jsou nutné k přidání prostředků do katalogu. Chcete-li odebrat prostředky z katalogu, je nutné mít stejné role.

| Role adresáře Azure AD | Role správy oprávnění | Může přidat skupinu zabezpečení. | Může přidat skupinu Office 365. | Může přidat aplikaci. | Může přidat web SharePointu Online. |
| --- | :---: | :---: | :---: | :---: | :---: |
| [Globální správce](../users-groups-roles/directory-assign-admin-roles.md) | – |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Správce uživatele](../users-groups-roles/directory-assign-admin-roles.md) | – |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Správce Intune](../users-groups-roles/directory-assign-admin-roles.md) | Vlastník katalogu | :heavy_check_mark: | :heavy_check_mark: |  |  |
| [Správce Exchange](../users-groups-roles/directory-assign-admin-roles.md) | Vlastník katalogu |  | :heavy_check_mark: |  |  |
| [Správce služby Teams](../users-groups-roles/directory-assign-admin-roles.md) | Vlastník katalogu |  | :heavy_check_mark: |  |  |
| [Správce služby SharePoint](../users-groups-roles/directory-assign-admin-roles.md) | Vlastník katalogu |  | :heavy_check_mark: |  | :heavy_check_mark: |
| [Správce aplikace](../users-groups-roles/directory-assign-admin-roles.md) | Vlastník katalogu |  |  | :heavy_check_mark: |  |
| [Správce cloudové aplikace](../users-groups-roles/directory-assign-admin-roles.md) | Vlastník katalogu |  |  | :heavy_check_mark: |  |
| Uživatel | Vlastník katalogu | Pouze v případě, že vlastník skupiny | Pouze v případě, že vlastník skupiny | Jenom v případě, že vlastník aplikace |  |

K určení nejnižší privilegované role pro úlohu můžete také [v Azure Active Directory odkazovat na role správce podle úlohy](../users-groups-roles/roles-delegate-by-task.md#entitlement-management)správce.

## <a name="next-steps"></a>Další kroky

- [Delegování zásad správného řízení přístupu k tvůrcům katalogu](entitlement-management-delegate-catalog.md)
- [Vytvoření a správa katalogu prostředků](entitlement-management-catalog-create.md)
