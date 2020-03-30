---
title: Vytvoření základní skupiny a přidání členů – Azure Active Directory | Dokumenty společnosti Microsoft
description: Pokyny k vytvoření základní skupiny pomocí služby Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: quickstart
ms.date: 03/01/2019
ms.author: ajburnle
ms.reviewer: krbain
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c551a81788df8d68664abaa03f467a4521222d0
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "73473443"
---
# <a name="create-a-basic-group-and-add-members-using-azure-active-directory"></a>Vytvoření základní skupiny a přidání členů pomocí služby Azure Active Directory
Základní skupinu můžete vytvořit na portálu Azure Active Directory (Azure AD). Pro účely tohoto článku přidá základní skupinu do jednoho prostředku vlastník prostředku (správce) a skupina zahrnuje konkrétní členy (zaměstnance), kteří k danému prostředku potřebují přístup. Složitější scénáře, které zahrnují dynamická členství a vytváření pravidel, najdete v [dokumentaci ke správě uživatelů ve službě Azure Active Directory](../users-groups-roles/index.yml).

## <a name="create-a-basic-group-and-add-members"></a>Vytvoření základní skupiny a přidání členů
Vytvořit základní skupinu a přidat do ní členy můžete současně.

### <a name="to-create-a-basic-group-and-add-members"></a>Vytvoření základní skupiny a přidání členů
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí účtu globálního správce daného adresáře.

1. Vyhledejte a vyberte **Azure Active Directory**.

    ![Stránka Azure AD se zobrazenými skupinami](media/active-directory-groups-create-azure-portal/search-active-directory.png)

1. Na stránce **Služba Active Directory** vyberte **Skupiny** a potom vyberte **Nová skupina**.

    ![Stránka Azure AD se zobrazenými skupinami](media/active-directory-groups-create-azure-portal/group-full-screen.png)

1. Na stránce **Nová skupina** vyplňte požadované informace.

    ![Stránka nové skupiny s vyplněnými ukázkovými informacemi](media/active-directory-groups-create-azure-portal/new-group-blade.png)

   - **Typ skupiny (vyžadováno)**: Vyberte předdefinovaný typ skupiny. To zahrnuje: - **Zabezpečení**. Slouží ke správě členů a přístupu počítače ke sdíleným prostředkům u skupiny uživatelů. Můžete například vytvořit skupinu zabezpečení pro konkrétní zásady zabezpečení. Tímto způsobem můžete udělit sadu oprávnění všem členům najednou a nemusíte přidávat oprávnění členům jednotlivě. Další informace o správě přístupu k prostředkům najdete v článku o [správě přístupu k prostředkům pomocí skupin služby Azure Active Directory](active-directory-manage-groups.md).
               - **Office 365**. Poskytuje možnosti spolupráce tím, že umožňuje členům přístup ke sdílené poštovní schránce, kalendáři, souborům, sharepointovému webu a dalším prostředkům. Tato možnost vám také umožňuje udělit přístup ke skupině lidem mimo vaši organizaci. Další informace o skupinách Office 365 najdete v článku o [skupinách Office 365](https://support.office.com/article/learn-about-office-365-groups-b565caa1-5c40-40ef-9915-60fdb2d97fa2).

   - **Název skupiny (vyžadováno)**: Přidejte název skupiny. Zvolte název, který si zapamatujete a který bude dávat smysl. Bude provedena kontrola, která určí, zda je název již použit pro jinou skupinu. Pokud je název již používán, abyste se vyhnuli duplicitnímu pojmenování, budete vyzváni k úpravě názvu skupiny.

   - **Skupinová e-mailová adresa (povinné).** Přidejte e-mailovou adresu skupiny nebo ponechte e-mailovou adresu, která je vyplněna automaticky.

   - **Popis skupiny**: Volitelně můžete přidat také popis skupiny.

   - **Typ členství (vyžadováno)**: Vyberte předdefinovaný typ členství. To zahrnuje: - **Přiřazeno.** Umožňuje přidat konkrétní uživatele jako členy této skupiny s jedinečnými oprávněními. Pro účely tohoto článku používáme tuto možnost.
          - **Dynamický uživatel.** Umožňuje používat dynamická pravidla členství k automatickému přidávání a odebírá ní členy. Pokud se změní atributy člena, systém se podívá do pravidel dynamických skupin u adresáře a zjistí, zda člen splňuje požadavky pravidla (je přidán) nebo zda již požadavky pravidel nesplňuje (je odebrán).
          - **Dynamické zařízení.** Umožňuje použít dynamická skupinová pravidla a automaticky tak přidávat a odebírat zařízení. Pokud se změní atributy zařízení, systém se podívá do pravidel dynamických skupin u adresáře a zjistí, zda zařízení splňuje požadavky pravidla (je přidáno) nebo zda již požadavky pravidel nesplňuje (je odebráno).

        >[!Important]
        >Dynamickou skupinu můžete vytvořit buď pro zařízení, nebo uživatele, ale nikoli pro obojí. Stejně tak nemůžete vytvořit skupinu zařízení na základě atributů vlastníků zařízení. Pravidla členství zařízení mohou odkazovat pouze na atributy zařízení. Další informace o vytvoření dynamické skupiny pro uživatele a zařízení najdete [v tématu Vytvoření dynamické skupiny a kontrola stavu](../users-groups-roles/groups-create-rule.md).

1. Vyberte **Vytvořit**.

    Vaše skupina je vytvořená a připravená na přidání členů.

1. Na stránce **Skupina** vyberte oblast **Členové** a potom na stránce **Vybrat členy** začněte vyhledávat členy, které chcete přidat do skupiny.

    ![Výběr členů pro skupinu během procesu vytváření skupiny](media/active-directory-groups-create-azure-portal/select-members-create-group.png)

1. Po přidání členů zvolte **Vybrat**.

    Stránka **Přehled skupiny** se aktualizuje a zobrazí počet členů, kteří jsou nyní přidaní do skupiny.

    ![Stránka Přehled skupiny se zvýrazněným počtem členů](media/active-directory-groups-create-azure-portal/group-overview-blade-number-highlight.png)

## <a name="turn-on-or-off-welcome-email"></a>Zapnutí nebo vypnutí uvítacího e-mailu

Když je vytvořena nová skupina Office 365, ať už s dynamickým nebo statickým členstvím, je všem uživatelům, kteří jsou přidáni do skupiny, odesláno uvítací oznámení. Při změně některých atributů uživatele nebo zařízení jsou zpracována všechna pravidla dynamické skupiny v organizaci pro potenciální změny členství. Uživatelé, kteří jsou přidáni pak také obdrží uvítací oznámení. Toto chování můžete vypnout v [prostředí Exchange PowerShell](https://docs.microsoft.com/powershell/module/exchange/users-and-groups/Set-UnifiedGroup?view=exchange-ps). 

## <a name="next-steps"></a>Další kroky
Teď, když máte přidanou skupinu a alespoň jednoho uživatele, můžete provést tyto akce:

- [Zobrazení skupin a členů](active-directory-groups-view-azure-portal.md)

- [Správa členství ve skupinách](active-directory-groups-membership-azure-portal.md)

- [Správa dynamických pravidel pro uživatele ve skupině](../users-groups-roles/groups-create-rule.md)

- [Úprava nastavení skupiny](active-directory-groups-settings-azure-portal.md)

- [Správa přístupu k prostředkům pomocí skupin](active-directory-manage-groups.md)

- [Správa přístupu k aplikacím SaaS pomocí skupin](../users-groups-roles/groups-saasapps.md)

- [Správa skupin pomocí powershellových příkazů](../users-groups-roles/groups-settings-v2-cmdlets.md)

- [Přidružení nebo přidání předplatného Azure do Služby Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)
