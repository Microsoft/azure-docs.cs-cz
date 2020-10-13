---
title: Vytvoření základní skupiny a přidání členů – Azure Active Directory | Microsoft Docs
description: Pokyny, jak vytvořit základní skupinu pomocí Azure Active Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 06/05/2020
ms.author: ajburnle
ms.reviewer: krbain
ms.custom: it-pro, seodec18, contperfq4
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3dcbd8618dc0f2bae2eacc9ced67869d8209286a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89565520"
---
# <a name="create-a-basic-group-and-add-members-using-azure-active-directory"></a>Vytvoření základní skupiny a přidání členů pomocí Azure Active Directory
Základní skupinu můžete vytvořit na portálu Azure Active Directory (Azure AD). Pro účely tohoto článku přidá základní skupinu do jednoho prostředku vlastník prostředku (správce) a skupina zahrnuje konkrétní členy (zaměstnance), kteří k danému prostředku potřebují přístup. Složitější scénáře, které zahrnují dynamická členství a vytváření pravidel, najdete v [dokumentaci ke správě uživatelů ve službě Azure Active Directory](../users-groups-roles/index.yml).

## <a name="group-and-membership-types"></a>Typy skupin a členství
Existuje několik typů skupin a členství. Následující informace popisují jednotlivé typy skupin a členství a jejich použití, které vám pomohou rozhodnout, jaké možnosti použít při vytváření skupiny.

### <a name="group-types"></a>Typy skupin:
- **Zabezpečení**. Slouží ke správě členů a přístupu počítače ke sdíleným prostředkům u skupiny uživatelů. Můžete například vytvořit skupinu zabezpečení pro konkrétní zásady zabezpečení. Tímto způsobem můžete udělit sadu oprávnění všem členům najednou a nemusíte přidávat oprávnění členům jednotlivě. Skupina zabezpečení může mít uživatele, zařízení, skupiny a instanční objekty jako své členy a uživatele a instanční objekty jako vlastníci. Další informace o správě přístupu k prostředkům najdete v článku o [správě přístupu k prostředkům pomocí skupin služby Azure Active Directory](active-directory-manage-groups.md).
- **Microsoft 365**. Poskytuje možnosti spolupráce tím, že umožňuje členům přístup ke sdílené poštovní schránce, kalendáři, souborům, sharepointovému webu a dalším prostředkům. Tato možnost vám také umožňuje udělit přístup ke skupině lidem mimo vaši organizaci. Skupina Microsoft 365 může mít jako své členy pouze uživatele. Uživatelé i instanční objekty můžou být vlastníky skupiny Microsoft 365. Další informace o Microsoft 365 skupin najdete v tématu [informace o Microsoft 365Ch skupinách](https://support.office.com/article/learn-about-office-365-groups-b565caa1-5c40-40ef-9915-60fdb2d97fa2).

### <a name="membership-types"></a>Typy členství:
- **Přiřazení.** Umožňuje přidat konkrétní uživatele jako členy této skupiny s jedinečnými oprávněními. Pro účely tohoto článku používáme tuto možnost.
- **Dynamický uživatel.** Umožňuje používat dynamická pravidla členství k automatickému přidávání a odebírání členů. Pokud se změní atributy člena, systém se podívá do pravidel dynamických skupin u adresáře a zjistí, zda člen splňuje požadavky pravidla (je přidán) nebo zda již požadavky pravidel nesplňuje (je odebrán).
- **Dynamické zařízení**: Umožňuje použít dynamická skupinová pravidla a automaticky tak přidávat a odebírat zařízení. Pokud se změní atributy zařízení, systém se podívá do pravidel dynamických skupin u adresáře a zjistí, zda zařízení splňuje požadavky pravidla (je přidáno) nebo zda již požadavky pravidel nesplňuje (je odebráno).

    > [!IMPORTANT]
    > Dynamickou skupinu můžete vytvořit buď pro zařízení, nebo uživatele, ale nikoli pro obojí. Stejně tak nemůžete vytvořit skupinu zařízení na základě atributů vlastníků zařízení. Pravidla členství zařízení mohou odkazovat pouze na atributy zařízení. Další informace o tom, jak vytvořit dynamickou skupinu pro uživatele a zařízení, najdete v tématu [Vytvoření dynamické skupiny a kontroly stavu](../users-groups-roles/groups-create-rule.md) .

## <a name="create-a-basic-group-and-add-members"></a>Vytvoření základní skupiny a přidání členů
Vytvořit základní skupinu a přidat do ní členy můžete současně. Chcete-li vytvořit základní skupinu a přidat členy, použijte následující postup:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí účtu globálního správce daného adresáře.

1. Vyhledejte a vyberte **Azure Active Directory**.

1. Na stránce **Služba Active Directory** vyberte **skupiny** a pak vyberte **Nová skupina**.

    ![Stránka Azure AD se skupinami, které zobrazují](media/active-directory-groups-create-azure-portal/group-full-screen.png)

1. Zobrazí se podokno **Nová skupina** a musíte vyplnit požadované informace.

    ![Stránka nové skupiny s vyplněnými ukázkovými informacemi](media/active-directory-groups-create-azure-portal/new-group-blade.png)

1. Vyberte předem definovaný **typ skupiny**. Další informace o typech skupin najdete v tématu [typy skupin a členství](#group-types).

1. Vytvořte a přidejte **název skupiny.** Vyberte si název, který si pamatujete a který dává smysl pro skupinu. Provede se ověření, aby se zjistilo, jestli se tento název už používá v jiné skupině. Pokud se název už používá, abyste se vyhnuli duplicitním názvům, zobrazí se výzva, abyste změnili název vaší skupiny.

1. Přidejte **e-mailovou adresu skupiny** pro skupinu nebo nechte automaticky vyplněnou e-mailovou adresu.

1. **Popis skupiny** Volitelně můžete přidat také popis skupiny.

1. Vyberte předem definovaný **typ členství (povinné).** Další informace o typech členství naleznete v tématu [Group and Membership Types](#membership-types).

1. Vyberte **Vytvořit**. Vaše skupina je vytvořená a připravená na přidání členů.

1. Na stránce **Skupina** vyberte oblast **Členové** a potom na stránce **Vybrat členy** začněte vyhledávat členy, které chcete přidat do skupiny.

    ![Výběr členů pro skupinu během procesu vytváření skupiny](media/active-directory-groups-create-azure-portal/select-members-create-group.png)

1. Po přidání členů zvolte **Vybrat**.

    Stránka **Přehled skupiny** se aktualizuje a zobrazí počet členů, kteří jsou nyní přidaní do skupiny.

    ![Stránka Přehled skupiny se zvýrazněným počtem členů](media/active-directory-groups-create-azure-portal/group-overview-blade-number-highlight.png)

## <a name="turn-on-or-off-group-welcome-email"></a>Zapnout nebo vypnout e-mail pro uvítání skupiny

Když se vytvoří jakákoli nová skupina Microsoft 365, ať už s dynamickým nebo statickým členstvím, pošle se oznámení uvítání všem uživatelům, kteří se do této skupiny přidají. Když se změní kterýkoli atribut uživatele nebo zařízení, zpracují se všechna pravidla dynamické skupiny v organizaci, aby se projevily případné změny členství. Přidaní uživatelé pak obdrží i uvítací oznámení. Toto chování můžete v [prostředí Exchange PowerShell](/powershell/module/exchange/users-and-groups/Set-UnifiedGroup?view=exchange-ps)vypnout. 

## <a name="next-steps"></a>Další kroky

- [Správa přístupu k aplikacím SaaS pomocí skupin](../users-groups-roles/groups-saasapps.md)
- [Správa skupin pomocí powershellových příkazů](../users-groups-roles/groups-settings-v2-cmdlets.md)