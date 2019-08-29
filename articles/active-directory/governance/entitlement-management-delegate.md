---
title: Delegovat úlohy ve správě nároků Azure AD (Preview) – Azure Active Directory
description: Přečtěte si o rolích, které můžete přiřadit k úlohám delegování v Azure Active Directory správě nároků.
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
ms.date: 07/10/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 064724b3c6a5faa485850ecdfa3d3759d3631be0
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70124915"
---
# <a name="delegate-tasks-in-azure-ad-entitlement-management-preview"></a>Delegovat úlohy ve správě nároků Azure AD (Preview)

> [!IMPORTANT]
> Správa opravňujících k Azure Active Directory (Azure AD) je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ve výchozím nastavení můžou globální správci a správci uživatelů vytvářet a spravovat všechny aspekty správy nároků služby Azure AD. Uživatelé v těchto rolích ale nemusí znát všechny scénáře, kdy jsou balíčky pro přístup požadovány. Obvykle se jedná o uživatele v rámci oddělení, kteří vědí, kdo potřebují spolupracovat. 

Místo udělení neomezených oprávnění nesprávcům můžete uživatelům udělit nejnižší oprávnění, která potřebují k provedení své úlohy, a vyhnout se tak vytváření konfliktních nebo nevhodných přístupových práv. Tento článek popisuje role, které můžete přiřadit k delegování různých úloh v rámci správy oprávnění. 

## <a name="delegate-example-for-departmental-adoption"></a>Příklad delegáta pro přijetí oddělení

Pokud chcete pochopit, jak byste mohli delegovat úlohy ve správě nároků, je třeba vzít v úvahu příklad. 

Předpokládejme, že vaše organizace má následující pět uživatelů:

| Uživatel | Oddělení | Poznámky |
| --- | --- | --- |
| Alice | it | Globální správce |
| Bob | Výzkum | Bob je také vlastníkem výzkumné skupiny. |
| Carole | Výzkum |  |
| Dave | Marketing |  |
| Elisa | Marketing | Test ELISA je také vlastníkem marketingové aplikace |

Oddělení výzkumu i marketingu chtějí pro své uživatele používat oprávnění ke správě. Alice ještě není připravená na jiné oddělení, aby používala správu nároků. Tady je jeden ze způsobů, jak může Alice delegovat úkoly na oddělení výzkumu a marketingu.

1. Alice vytvoří novou skupinu zabezpečení Azure AD pro tvůrci katalogu a přidá Bob, Karolínu, Dave a ELISA jako členy této skupiny.

1. Alice pomocí nastavení správy oprávnění přidá tuto skupinu do role tvůrci katalogu.

1. Karolínu vytvoří katalog **výzkumu** a přidá Bob jako spoluvlastníka tohoto katalogu. Bob přidá skupinu Research, kterou vlastní do katalogu, jako prostředek, aby ji bylo možné použít v přístupovém balíčku pro výzkumovou spolupráci.

1. Dave vytvoří **marketingový** katalog a přidá ELISA jako spoluvlastník tohoto katalogu. Test ELISA přidá marketingovou aplikaci, kterou vlastní do katalogu, jako prostředek, takže se dá použít v přístupovém balíčku pro marketingovou spolupráci.

Oddělení pro výzkum a marketing teď můžou využívat nárok na správu. Bob, Karolínu, Dave a ELISA můžou vytvořit a spravovat balíčky přístupu v příslušných katalozích.

![Příklad delegáta správy oprávnění](./media/entitlement-management-delegate/elm-delegate.png)


## <a name="entitlement-management-roles"></a>Role správy oprávnění

Správa nároků má následující role, které jsou specifické pro správu nároků.

| Role | Popis |
| --- | --- |
| Tvůrce katalogu | Vytvářejte a spravujte katalogy. Obvykle správce IT, který není globálním správcem, nebo vlastníkem prostředku pro kolekci prostředků. Osoba, která automaticky vytvoří katalog, se bude nacházet jako s prvním vlastníkem katalogu katalogu a může přidat další vlastníky katalogu. |
| Vlastník katalogu | Umožňuje upravovat a spravovat existující katalogy. Obvykle správce IT nebo vlastníci prostředků nebo uživatel, který určil vlastníka katalogu. |
| Přístup ke Správci balíčků | Umožňuje upravovat a spravovat všechny existující balíčky přístupu v rámci katalogu. |

Kromě toho má určený schvalovatel a žadatel pro přístupovou sadu také práva, i když se nejedná o role.
 
* Uživatelem Autorizováno zásadou ke schválení nebo zamítnutí žádostí o přístup k balíčkům, i když nemůžou měnit definice přístupového balíčku.
* Žadatele Autorizován zásadou přístupového balíčku pro vyžádání přístupového balíčku.

V následující tabulce jsou uvedeny úlohy, které tyto role mohou provádět.

| Úloha | Tvůrce katalogu | Vlastník katalogu | Přístup ke Správci balíčků | Schvalovatel |
| --- | :---: | :---: | :---: | :---: |
| [Vytvořit nový katalog](entitlement-management-catalog-create.md) | :heavy_check_mark: |  |  |  |
| [Přidání prostředku do katalogu](entitlement-management-catalog-create.md#add-resources-to-a-catalog) | | :heavy_check_mark: | | |
| [Úprava katalogu](entitlement-management-catalog-create.md#edit-a-catalog) |  | :heavy_check_mark: |  |  |
| [Odstranění katalogu](entitlement-management-catalog-create.md#delete-a-catalog) |  | :heavy_check_mark: |  |  |
| [Přidání vlastníka katalogu nebo správce balíčků přístupu do katalogu](#add-a-catalog-owner-or-an-access-package-manager) |  | :heavy_check_mark: |  |  |
| [Vytvoření nového přístupového balíčku v katalogu](entitlement-management-access-package-create.md) |  | :heavy_check_mark:  | :heavy_check_mark:  |  |
| [Správa rolí prostředků v balíčku pro přístup](entitlement-management-access-package-edit.md) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Vytvoření a úprava zásad](entitlement-management-access-package-edit.md#add-a-new-policy) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Přímé přiřazení uživatele k balíčku pro přístup](entitlement-management-access-package-edit.md#directly-assign-a-user) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Zobrazit, kdo má přiřazení k balíčku pro přístup](entitlement-management-access-package-edit.md#view-who-has-an-assignment) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Zobrazit žádosti balíčku pro přístup](entitlement-management-access-package-edit.md#view-requests) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Zobrazit chyby doručení žádosti](entitlement-management-access-package-edit.md#view-a-requests-delivery-errors) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Zrušení žádosti, která čeká na vyřízení](entitlement-management-access-package-edit.md#cancel-a-pending-request) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Skrytí přístupového balíčku](entitlement-management-access-package-edit.md#change-the-hidden-setting) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Odstranění balíčku pro přístup](entitlement-management-access-package-edit.md#delete) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Schválení žádosti o přístup](entitlement-management-request-approve.md) |  |  |  | :heavy_check_mark: |

## <a name="required-roles-to-add-resources-to-a-catalog"></a>Požadované role pro přidání prostředků do katalogu

Globální správce může přidat nebo odebrat libovolnou skupinu (v cloudu vytvořené skupiny zabezpečení nebo skupiny Office 365), aplikaci nebo web SharePointu Online v katalogu. Správce uživatele může přidat nebo odebrat libovolnou skupinu nebo aplikaci v katalogu.

Pro uživatele, který není globálním správcem nebo správcem uživatelů, pokud chcete přidat skupiny, aplikace nebo weby SharePointu Online do katalogu, musí mít tento uživatel *jak* požadovanou roli adresáře služby Azure AD, tak roli správy oprávnění vlastníka katalogu. V následující tabulce jsou uvedeny kombinace rolí, které jsou nutné k přidání prostředků do katalogu. Chcete-li odebrat prostředky z katalogu, je nutné mít stejné role.

| Role adresáře Azure AD | Role správy oprávnění | Může přidat skupinu zabezpečení. | Může přidat skupinu Office 365. | Může přidat aplikaci. | Může přidat web SharePointu Online. |
| --- | :---: | :---: | :---: | :---: | :---: |
| [Globální správce](../users-groups-roles/directory-assign-admin-roles.md) | neuvedeno |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Správce uživatele](../users-groups-roles/directory-assign-admin-roles.md) | neuvedeno |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Správce Intune](../users-groups-roles/directory-assign-admin-roles.md) | Vlastník katalogu | :heavy_check_mark: | :heavy_check_mark: |  |  |
| [Správce Exchange](../users-groups-roles/directory-assign-admin-roles.md) | Vlastník katalogu |  | :heavy_check_mark: |  |  |
| [Správce služby Teams](../users-groups-roles/directory-assign-admin-roles.md) | Vlastník katalogu |  | :heavy_check_mark: |  |  |
| [Správce služby SharePoint](../users-groups-roles/directory-assign-admin-roles.md) | Vlastník katalogu |  | :heavy_check_mark: |  | :heavy_check_mark: |
| [Správce aplikace](../users-groups-roles/directory-assign-admin-roles.md) | Vlastník katalogu |  |  | :heavy_check_mark: |  |
| [Správce cloudové aplikace](../users-groups-roles/directory-assign-admin-roles.md) | Vlastník katalogu |  |  | :heavy_check_mark: |  |
| Uživatel | Vlastník katalogu | Pouze v případě, že vlastník skupiny | Pouze v případě, že vlastník skupiny | Jenom v případě, že vlastník aplikace |  |


## <a name="add-a-catalog-creator"></a>Přidat tvůrce katalogu

Pokud chcete delegovat vytváření katalogu, přidejte uživatele do role tvůrce katalogu.  Můžete přidat jednotlivé uživatele nebo pro usnadnění můžete přidat skupinu, jejíž členové pak mohou vytvářet katalogy. Pomocí těchto kroků přiřaďte uživatele k roli tvůrce katalogu.

**Požadovaná role:** Globální správce nebo Správce uživatelů

1. V Azure Portal klikněte na **Azure Active Directory** a pak klikněte na zásady **správného řízení identity**.

1. V levé nabídce v části **Správa nároků** klikněte na **Nastavení**.

1. Klikněte na **Upravit**.

1. V části **Správa nároků delegáta** klikněte na **Přidat tvůrci katalogu** a vyberte uživatele nebo skupiny, kteří budou členy této role správy oprávnění.

1. Klikněte na tlačítko **vyberte**.

1. Klikněte na **Uložit**.

## <a name="add-a-catalog-owner-or-an-access-package-manager"></a>Přidat vlastníka katalogu nebo správce balíčků přístupu

Pokud chcete delegovat správu katalogu nebo přistupovat k balíčkům v katalogu, přidejte uživatele do role vlastníka katalogu nebo přístup k rolím Správce balíčků. Ten vytvoří katalog jako prvního vlastníka katalogu. Pomocí těchto kroků přiřaďte uživatele k roli správce balíčků v katalogu.

**Požadovaná role:** Globální správce, správce uživatele nebo vlastník katalogu

1. V Azure Portal klikněte na **Azure Active Directory** a pak klikněte na zásady **správného řízení identity**.

1. V nabídce vlevo klikněte na **katalogy** a pak otevřete katalog, do kterého chcete přidat správce.

1. V nabídce vlevo klikněte na **role a správci**.

1. Klikněte na **Přidat vlastníky** nebo **přidejte správce balíčků přístupu** a vyberte členy pro tyto role.

1. Kliknutím na **Vybrat** přidejte tyto členy.

## <a name="next-steps"></a>Další postup

- [Přidat schvalovatele](entitlement-management-access-package-edit.md#policy-request)
- [Přidání prostředků do katalogu](entitlement-management-catalog-create.md#add-resources-to-a-catalog)
