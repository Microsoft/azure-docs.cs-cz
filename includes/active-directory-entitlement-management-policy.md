---
title: zahrnout soubor
description: zahrnout soubor
services: active-directory
author: rolyon
ms.service: active-directory
ms.topic: include
ms.date: 04/16/2019
ms.author: rolyon
ms.custom: include file
ms.openlocfilehash: ef8155cda6c31a63204af80ca091a13bb16a687d
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2019
ms.locfileid: "64866441"
---
### <a name="policy-for-users-in-your-directory"></a>Zásady: Pro uživatele ve vašem adresáři

Pokud chcete, aby vaše zásady pro uživatele a skupiny v adresáři, který můžete požádat o tento balíček přístup, postupujte následovně.

1. V **uživatelé, kteří můžou požádat o přístup** vyberte **pro uživatele ve vašem adresáři**.

1. V **výběr uživatelů a skupin** klikněte na tlačítko **přidávat uživatele a skupiny**.

1. Vybraní uživatelé a skupiny vyberte uživatele a skupiny, které chcete přidat.

    ![Přístup k balíčku - výběr zásad uživatele a skupiny](./media/active-directory-entitlement-management-policy/policy-select-users-groups.png)

1. Klikněte na tlačítko **vyberte** přidávat uživatele a skupiny.

1. Přejděte dolů k [zásad: Požádat o](#policy-request) oddílu.

### <a name="policy-for-users-not-in-your-directory"></a>Zásady: Pro uživatele není ve vašem adresáři

Pokud chcete, aby vaše zásady pro uživatele není ve vašem adresáři, který můžete požádat o tento balíček přístup, postupujte následovně. Adresáře musí být nakonfigurovaný na povolí **omezení spolupráce organizační vztahy** nastavení.

1. V **uživatelé, kteří můžou požádat o přístup** vyberte **pro uživatele není ve vašem adresáři**.

1. V **vyberte externí služby Azure AD directory** klikněte na tlačítko **přidat adresáře**.

1. Zadejte název domény a vyhledejte externí adresář Azure AD.

1. Ověřte, že je na správný adresář zadaný adresář název a počáteční doména.

    > [!NOTE]
    > Všichni uživatelé v adresáři budou moct požádat o tento balíček přístup. Zahrnuti jsou uživatelé z všechny subdomény, které jsou přidružené k adresáři, ne jenom domény pro hledání použita.

    ![Přístup k balíčku - zásad – Výběr adresáře](./media/active-directory-entitlement-management-policy/policy-select-directories.png)

1. Klikněte na tlačítko **přidat** přidat adresář.

1. Opakujte tento krok a přidejte všechny další adresáře.

1. Po přidání všech adresářů chcete zahrnout do zásady, klikněte na tlačítko **vyberte**.

1. Přejděte dolů k [zásad: Požádat o](#policy-request) oddílu.

### <a name="policy-none-administrator-direct-assignments-only"></a>Zásady: Žádný (pouze přiřazení přímé správce)

Následujícím postupem, potřebujete zásady obejít žádosti o přístup a Povolit správcům přiřadit konkrétní uživatele přímo k přístupu k balíčku. Uživatelé nebudou muset požádat o přístup k balíčku. Stále můžete nastavit nastavení vypršení platnosti, ale nejsou žádná nastavení požadavku.

1. V **uživatelé, kteří můžou požádat o přístup** vyberte **None (správce pouze přímé přiřazení**.

    Když vytvoříte balíček přístup, můžete přiřadit konkrétní interní a externí uživatele přímo k přístupu k balíčku. Pokud zadáte externího uživatele, vytvoří se účet uživatele typu Host ve vašem adresáři.

1. Přejděte dolů k [zásad: Vypršení platnosti](#policy-expiration) oddílu.

### <a name="policy-request"></a>Zásady: Žádost

V části žádosti zadejte nastavení schválení Pokud uživatelé požadují přístup k balíčku.

1. Chcete-li vyžadovat schválení žádostí z vybrané uživatele, nastavte **vyžadovat schválení** přepnutím **Ano**. Pokud chcete, aby automaticky schváleny žádosti o, nastavte přepínač na **ne**.

1. Pokud budete vyžadovat schválení, v **vybrat schvalovatele** klikněte na tlačítko **přidat schvalovatele**.

1. V podokně vyberte schvalovatelé vyberte jeden nebo více uživatelů a/nebo skupiny jako schvalovatele.

    Pouze jeden z Vybraní schvalovatelé musí schválit žádost. Schválení všem schvalovatelům se nevyžaduje. Rozhodnutí o schválení vychází z toho schvalovatel žádost nejdřív zkontroluje.

    ![Přístup k balíčku - schvalovatelů výběru zásady](./media/active-directory-entitlement-management-policy/policy-select-approvers.png)

1. Klikněte na tlačítko **vyberte** Přidání schvalovatele.

1. Klikněte na tlačítko **zobrazit pokročilé nastavení žádostí o** zobrazíte další nastavení.

    ![Přístup k balíčku - zásad – Výběr adresáře](./media/active-directory-entitlement-management-policy/policy-advanced-request.png)

1. Chcete-li vyžadovat, aby uživatelé odůvodnit požádat o přístup k balíčku, nastavte **vyžadovat odůvodnění** k **Ano**.

1. Chcete-li vyžadovat schvalovatele o uveďte její odůvodnění pro schválení žádosti o přístup k balíčku, nastavte **vyžadovat odůvodnění schvalovatelem** k **Ano**.

1. V **časový limit žádosti o schválení (dny)** zadejte množství času schvalovatelů nutné zkontrolovat žádost. Pokud žádné schvalovatelů projedná za tento počet dní, žádost vyprší a uživatel bude muset odeslat další požadavek pro přístup k balíčku.

### <a name="policy-expiration"></a>Zásady: Konec platnosti

V části vypršení platnosti můžete určit, kdy vyprší platnost přiřazení uživatele k přístupu k balíčku.

1. V **vypršení platnosti** nastavte **vyprší platnost přístupu k balíčku** k **datu**, **počet dní**, nebo **nikdy**.

    Pro **datu**, vyberte datum vypršení platnosti v budoucnu.

    Pro **počet dní**, zadejte číslo mezi 0 a 3660 dnů.

    Na základě vašeho výběru, přiřazení uživatele k přístupu k balíčku vyprší v určitý den, počet dnů po schválení, nebo vůbec.

1. Klikněte na tlačítko **zobrazit pokročilé nastavení vypršení platnosti** zobrazíte další nastavení.

1. Chcete-li povolit uživateli rozšířit svá přiřazení, nastavte **umožňují uživatelům rozšířit přístup** k **Ano**.

    Pokud mají být povoleny v zásadách, uživatel dostane e-mailu 14 dnů a také 1 den před jejich přiřazení přístupu k balíčku je nastavena na vypršení platnosti vyzve uživatele k rozšíření přiřazení.

    ![Přístup k balíčku - nastavení zásad vypršení platnosti](./media/active-directory-entitlement-management-policy/policy-expiration.png)

### <a name="policy-enable-policy"></a>Zásady: Povolit zásadu

1. Pokud chcete balíček přístupu budou okamžitě dostupné uživatelům v zásadách, klikněte na tlačítko **Ano** povolit příslušné zásady.

    Můžete vždy povolit ho v budoucnu po dokončení vytváření balíčku přístup.

    ![Přístup k balíčku - nastavení zásad pro povolení zásad](./media/active-directory-entitlement-management-policy/policy-enable.png)

1. Klikněte na tlačítko **Další** nebo **vytvořit**.
