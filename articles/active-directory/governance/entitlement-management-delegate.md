---
title: Delegovat úkoly ve správě oprávnění Azure AD (Preview) – Azure Active Directory
description: Další informace o rolích, které můžete přiřadit k delegování úkolů ve službě Azure Active Directory management oprávnění.
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
ms.openlocfilehash: d4c4933847a39a56084894e5bbd40e166e6b73b6
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798638"
---
# <a name="delegate-tasks-in-azure-ad-entitlement-management-preview"></a>Delegovat úkoly ve správě služby Azure AD oprávnění (Preview)

> [!IMPORTANT]
> Správa nároků Azure Active Directory (Azure AD) je aktuálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ve výchozím nastavení můžou globální správci a Správci uživatelů a vytvořit a spravovat všechny aspekty správy oprávnění Azure AD. Uživatelé v těchto rolích však nemusí vědět, že všechny scénáře, kde se vyžadují přístup k balíčkům. Obvykle je to uživatele v rámci oddělení kdo vědět, kteří potřebují spolupracovat.

Namísto udělení neomezená oprávnění k bez oprávnění správce, můžete uživatelům udělit nejnižší oprávnění, které potřebují k provádění svých úloh a vyhněte se vytváření konfliktních nebo nevhodných přístupová práva. Tento článek popisuje role, které můžete přiřadit k delegování různých úkolů ve správě oprávnění.

## <a name="delegate-example-for-departmental-adoption"></a>Příklad delegáta pro oddělení přijetí

Abyste pochopili, jak může delegovat úkoly ve správě nároku, pomáhá vezměte v úvahu příklad. Předpokládejme, že vaše organizace má následující pět uživatelů:

| Uživatel | Oddělení | Poznámky |
| --- | --- | --- |
| Alice | IT | Globální správce |
| Bob | Výzkum | Robert je také vlastníkem skupiny Research |
| Carole | Výzkum |  |
| Dave | Marketing |  |
| Elisa | Marketing | ELISA je také vlastníkem aplikace Marketing |

Výzkum a marketingové oddělení chcete použít Správa nároků pro své uživatele. Alice ještě není připravený k použití Správa nároků jiných oddělení. Tady je jedním ze způsobů, že Alice může delegovat úkoly pro výzkum a marketingové oddělení.

1. Alice vytvoří novou zabezpečení Azure AD seskupit tvůrců katalogu a přidá Bob, Karolínu, Dave a Elisa jako členové této skupiny.

1. Alice používá nastavení pro správu oprávnění pro přidání skupiny k roli Tvůrce katalogu.

1. Vytvoří Karolínu **Research** katalogu a přidá Bob jako spoluvlastníka tohoto katalogu. Bob přidá skupinu výzkumu, který má vlastní do katalogu jako prostředek, tak, aby může sloužit, přístup k balíčku pro výzkumné spolupráci.

1. Dave vytvoří **marketingové** katalogu a přidá Elisa jako spoluvlastníka tohoto katalogu. ELISA přidá marketingové aplikaci, kterou ona je vlastníkem do katalogu jako prostředek, tak, aby ho můžete použít, přístup k balíčku pro marketingové spolupráci.

Se teď můžou využívat výzkumu a marketingové oddělení Správa nároků. Bob, Karolínu, Dave a Elisa můžete vytvořit a spravovat přístup balíčky v jejich příslušných katalogů.

![Oprávnění příklad delegáta správy](./media/entitlement-management-delegate/elm-delegate.png)

## <a name="entitlement-management-roles"></a>Role pro správu oprávnění

Správa nároků má následující role, která jsou specifická pro správu oprávnění.

| Role | Popis |
| --- | --- |
| Tvůrce katalogu | Vytvoření a Správa katalogů. Obvykle správce IT, který není globální správce nebo vlastníka prostředku pro kolekci prostředků. Osoby, která se automaticky vytvoří katalog stane katalogu první katalogu vlastníkem a můžete přidat další katalogu vlastníky. |
| Vlastník katalogu | Úprava a Správa existujících katalogů. Obvykle správce IT nebo vlastníky prostředků nebo uživatel, který určil vlastníka katalogu. |
| Správce přístupu k balíčku | Úprava a Správa všech existujících balíčků přístup v rámci katalogu. |

Kromě toho určeného schvalovatele a žadatele o přístup k balíčku mít rovněž určitá práva, i když se nejedná o role.
 
* Schvalovatel: Oprávnění od zásady pro schválení nebo zamítnutí žádosti o přístup k balíčkům, ale nemůžou změnit definice balíčku přístup.
* Žadatel: Oprávnění od zásady přístupu k balíčku k požádat o přístup k balíčku.

V následující tabulce jsou uvedeny úlohy, které můžete provádět tyto role.

| Úloha | Tvůrce katalogu | Vlastník katalogu | Správce přístupu k balíčku | Schvalovatele |
| --- | :---: | :---: | :---: | :---: |
| [Vytvořit nový katalog](entitlement-management-catalog-create.md) | :heavy_check_mark: |  |  |  |
| [Přidejte prostředek do katalogu](entitlement-management-catalog-create.md#add-resources-to-a-catalog) | | :heavy_check_mark: | | |
| [Upravit katalogu](entitlement-management-catalog-create.md#edit-a-catalog) |  | :heavy_check_mark: |  |  |
| [Odstranit katalog](entitlement-management-catalog-create.md#delete-a-catalog) |  | :heavy_check_mark: |  |  |
| [Přidat katalog vlastník nebo správce přístupu k balíčku do katalogu](#add-a-catalog-owner-or-an-access-package-manager) |  | :heavy_check_mark: |  |  |
| [Vytvořit nový balíček pro přístup do katalogu](entitlement-management-access-package-create.md) |  | :heavy_check_mark:  | :heavy_check_mark:  |  |
| [Spravovat role prostředků, přístup k balíčku](entitlement-management-access-package-edit.md) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Zadejte, kdo žádosti přístup k balíčku](entitlement-management-access-package-edit.md#add-a-new-policy) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Přímo přiřadit uživatele přístupu k balíčku](entitlement-management-access-package-edit.md#directly-assign-a-user) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Zobrazení, který má přiřazení přístupu k balíčku](entitlement-management-access-package-edit.md#view-who-has-an-assignment) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Zobrazení žádostí přístup k balíčku](entitlement-management-access-package-edit.md#view-requests) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Zobrazit chyby doručování požadavku](entitlement-management-access-package-edit.md#view-a-requests-delivery-errors) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Zrušení čekající žádosti o](entitlement-management-access-package-edit.md#cancel-a-pending-request) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Skrýt přístupu k balíčku](entitlement-management-access-package-edit.md#change-the-hidden-setting) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Odstranit balíček přístup](entitlement-management-access-package-edit.md#delete) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Schválení žádosti o přístup](entitlement-management-request-approve.md) |  |  |  | :heavy_check_mark: |

## <a name="required-roles-to-add-resources-to-a-catalog"></a>Požadované role, které chcete přidat prostředky do katalogu

Globální správce můžete přidat nebo odebrat všechny skupiny (skupiny zabezpečení vytvořené cloud nebo cloud vytvořené skupiny Office 365), aplikace nebo webu Sharepointu Online v katalogu. Správce uživatelů můžete přidat nebo odebrat jakékoli skupiny nebo aplikace v katalogu.

Pro uživatele, který není globální správce nebo Správce uživatelů, přidat do katalogu skupin, aplikace nebo weby SharePoint Online, musí mít *obě* vyžaduje Azure AD directory role a katalog vlastníka nárok role správy. V následující tabulce jsou uvedeny kombinace rolí, které je potřeba přidat prostředky do katalogu. Pokud chcete odebrat prostředky z katalogu, musí mít stejné role.

| Role adresáře Azure AD | Role pro správu oprávnění | Můžete přidat skupiny zabezpečení | Můžete přidat skupiny Office 365 | Můžete přidat aplikace | Můžete přidat webu Sharepointu Online |
| --- | :---: | :---: | :---: | :---: | :---: |
| [Globální správce](../users-groups-roles/directory-assign-admin-roles.md) | neuvedeno |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Správce uživatelů](../users-groups-roles/directory-assign-admin-roles.md) | neuvedeno |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Správce služby Intune](../users-groups-roles/directory-assign-admin-roles.md) | Vlastník katalogu | :heavy_check_mark: | :heavy_check_mark: |  |  |
| [Správce Exchange](../users-groups-roles/directory-assign-admin-roles.md) | Vlastník katalogu |  | :heavy_check_mark: |  |  |
| [Správce služby pro týmy](../users-groups-roles/directory-assign-admin-roles.md) | Vlastník katalogu |  | :heavy_check_mark: |  |  |
| [Správce Sharepointu](../users-groups-roles/directory-assign-admin-roles.md) | Vlastník katalogu |  | :heavy_check_mark: |  | :heavy_check_mark: |
| [Správce aplikace](../users-groups-roles/directory-assign-admin-roles.md) | Vlastník katalogu |  |  | :heavy_check_mark: |  |
| [Správce cloudové aplikace](../users-groups-roles/directory-assign-admin-roles.md) | Vlastník katalogu |  |  | :heavy_check_mark: |  |
| Uživatel | Vlastník katalogu | Vlastník skupiny pouze tehdy, pokud | Vlastník skupiny pouze tehdy, pokud | Pouze tehdy, pokud vlastník aplikace |  |

## <a name="add-a-catalog-creator"></a>Přidat katalog creator

Pokud chcete delegovat vytvoření katalogu, přidat uživatele k roli Tvůrce katalogu.  Můžete přidat jednotlivé uživatele, nebo pro usnadnění práce můžete přidat skupinu, jejíž členové počítače pak můžou k vytváření katalogů. Použijte následující postup přiřazení uživatele k roli Tvůrce katalogu.

**Požadované role:** Globální správce nebo Správce uživatelů

1. Na webu Azure Portal, klikněte na tlačítko **Azure Active Directory** a potom klikněte na tlačítko **zásady správného řízení identit**.

1. V nabídce vlevo v **Správa nároků** klikněte na tlačítko **nastavení**.

1. Klikněte na **Upravit**.

1. V **delegovat správu oprávnění** klikněte na tlačítko **přidat autory katalogu** vybrat uživatele nebo skupiny, kteří budou členy této role správy oprávnění.

1. Klikněte na tlačítko **vyberte**.

1. Klikněte na **Uložit**.

## <a name="add-a-catalog-owner-or-an-access-package-manager"></a>Přidat katalog vlastník nebo správce přístupu k balíčku

Pokud chcete delegovat správu katalogu nebo přístup balíčků v katalogu, přidat uživatele na roli vlastníka katalogu nebo role správce přístupu k balíčku. Kdo vytvoří katalog se stane první Vlastník katalogu. Použijte následující postup přiřazení uživatele k vlastníka katalogu nebo role správce přístupu k balíčku.

**Požadované role:** Uživatel správce nebo vlastníka katalogu

1. Na webu Azure Portal, klikněte na tlačítko **Azure Active Directory** a potom klikněte na tlačítko **zásady správného řízení identit**.

1. V nabídce vlevo klikněte na tlačítko **katalogy** a pak otevřete katalogu, které chcete přidat oprávnění správce.

1. V nabídce vlevo klikněte na tlačítko **role a správci**.

1. Klikněte na tlačítko **přidat vlastníky** nebo **přidat přístup správců balíčků** vyberte členy pro tyto role.

1. Klikněte na tlačítko **vyberte** pro přidání těchto členů.

## <a name="next-steps"></a>Další kroky

- [Přidání schvalovatele](entitlement-management-access-package-edit.md#policy-request)
- [Přidat prostředky do katalogu](entitlement-management-catalog-create.md#add-resources-to-a-catalog)
