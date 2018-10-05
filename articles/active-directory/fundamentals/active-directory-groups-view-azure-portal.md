---
title: Rychlý start pro zobrazení skupin a členů vaší organizace ve službě Azure Active Directory | Microsoft Docs
description: Rychlý start s postupem vyhledání a zobrazení skupin vaší organizace a jejich přiřazených členů pomocí webu Azure Portal
services: active-directory
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: quickstart
ms.date: 09/24/2018
ms.author: lizross
ms.custom: it-pro
ms.reviewer: krbain
ms.openlocfilehash: 9da9f055163f5df4ea064bd46caa811e39fba20c
ms.sourcegitcommit: 715813af8cde40407bd3332dd922a918de46a91a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47056399"
---
<!--As a brand-new Azure AD administrator, I need to view my organization’s groups along with the assigned members, so I can manage permissions to apps and services for people in my organization-->

# <a name="quickstart-view-your-organizations-groups-and-members-in-azure-active-directory"></a>Rychlý start: Zobrazení skupin a členů vaší organizace ve službě Azure Active Directory
Existující skupiny a členy skupin vaší organizace můžete zobrazit pomocí webu Azure Portal. Skupiny se používají ke správě uživatelů (členů), kteří potřebují mít stejný přístup a oprávnění pro potenciálně omezené aplikace a služby.

V tomto rychlém startu zobrazíte všechny existující skupiny vaší organizace a přiřazené členy.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete. 

## <a name="prerequisites"></a>Požadavky
Než začnete, budete potřebovat:

- Vytvořit tenanta služby Azure Active Directory. Další informace najdete v článku o [přístupu k portálu služby Azure Active Directory a vytvoření nového tenanta](active-directory-access-create-new-tenant.md).

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal
K webu [Azure Portal](https://portal.azure.com/) se musíte přihlásit pomocí účtu globálního správce daného adresáře.

## <a name="create-a-new-group"></a>Vytvoření nové skupiny 
Vytvořte novou skupinu s názvem _MDM policy – West_. Další informace o vytvoření skupiny najdete v tématu [Jak vytvořit základní skupinu a přidat členy](active-directory-groups-create-azure-portal.md).

1. Vyberte **Azure Active Directory**, **Skupiny** a pak vyberte **Nová skupina**.

2. Vyplňte stránku **Skupina**:
    
    - **Typ skupiny:** Vyberte **Zabezpečení**.
    
    - **Název skupiny:** Zadejte _MDM policy – West_.
    
    - **Typ členství:** Vyberte **Přiřazené**.

3. Vyberte **Vytvořit**.

## <a name="create-a-new-user"></a>Vytvoření nového uživatele
Vytvořte nového uživatele se jménem _Alain Charon_. Uživatel musí existovat, aby ho bylo možné přidat jako člena skupiny. Další informace o vytvoření uživatele najdete v článku o [přidání nebo odstranění uživatelů](add-users-azure-active-directory.md).

1. Vyberte **Azure Active Directory**, **Uživatelé** a pak vyberte **Nový uživatel**.

2. Vyplňte stránku **Uživatel**:

    - **Jméno:** Zadejte _Alain Charon_.

    - **Uživatelské jméno:** Zadejte *alain@contoso.com*.

3. Zkopírujte automaticky vygenerované heslo v poli **Heslo** a pak vyberte **Vytvořit**.

## <a name="add-a-group-member"></a>Přidání člena skupiny
Teď, když máte skupinu a uživatele, můžete _Alaina Charona_ přidat jako člena do skupiny _MDM policy – West_. Další informace o přidání členů skupiny najdete v článku [Přidání nebo odebrání členů skupin](active-directory-groups-members-azure-portal.md).

1. Vyberte **Azure Active Directory** > **Skupiny**.

2. Na stránce **Skupiny – Všechny skupiny** vyhledejte skupinu **MDM policy – West** a vyberte ji.

3. Na stránce s **přehledem skupiny MDM policy – West** vyberte **Členové** v oblasti **Spravovat**.

4. Vyberte **Přidat členy** a pak vyhledejte a vyberte uživatele **Alain Charon**.

5. Zvolte **Vybrat**.

## <a name="view-all-groups"></a>Zobrazení všech skupin
Všechny skupiny vaší organizace si můžete prohlédnout na stránce **Skupiny – Všechny skupiny** webu Azure Portal.

- Vyberte **Azure Active Directory** > **Skupiny**.

    Zobrazí se stránka **Skupiny – Všechny skupiny** se všemi aktivními skupinami.

    ![Stránka Skupiny – Všechny skupiny zobrazující všechny existující skupiny](media/active-directory-groups-view-azure-portal/groups-all-groups-blade-with-all-groups.png)

## <a name="search-for-the-group"></a>Vyhledání skupiny
Prohledáním stránky **Skupiny – Všechny skupiny** najděte skupinu **MDM policy – West**.

1. Na stránce **Skupiny – Všechny skupiny** zadejte do **vyhledávacího** pole text _MDM_.

    Pod **vyhledávacím** polem se zobrazí výsledky hledání včetně skupiny _MDM policy – West_.

    ![Stránka Skupiny – Všechny skupiny s vyplněným vyhledávacím polem](media/active-directory-groups-view-azure-portal/search-for-specific-group.png)

3. Vyberte skupinu **MDM policy – West**.

4. Na stránce s **přehledem skupiny MDM policy – West** uvidíte informace o této skupině včetně počtu jejích členů.

    ![Stránka s přehledem skupiny MDM policy – West s informacemi o členech](media/active-directory-groups-view-azure-portal/group-overview-blade.png)

## <a name="view-group-members"></a>Zobrazení členů skupiny
Teď, když jste tuto skupinu našli, můžete zobrazit všechny přiřazené členy.

- V oblasti **Spravovat** vyberte **Členové** a prohlédněte si úplný seznam se jmény členů přiřazených k této konkrétní skupině včetně _Alaina Charona_.

    ![Seznam členů přiřazených ke skupině MDM policy – West](media/active-directory-groups-view-azure-portal/groups-all-members.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků
Tato skupina se používá v několika návodech, které jsou dostupné v oddílu **Návody** této dokumentace. Pokud ale tuto skupinu nechcete používat, můžete ji a její přiřazené členy odstranit následujícím postupem:

1. Na stránce **Skupiny – Všechny skupiny** vyhledejte skupinu **MDM policy – West**.

2.  Vyberte skupinu **MDM policy – West**.

    Zobrazí se stránka s **přehledem skupiny MDM policy – West**.

3. Vyberte **Odstranit**.

    Tato skupina a její přidružení členové se odstraní.

    ![Stránka s přehledem skupiny MDM policy – West se zvýrazněným odkazem Odstranit](media/active-directory-groups-view-azure-portal/group-overview-blade-delete.png)

    >[!Important]
    >Tím se neodstraní uživatel Alain Charon, ale jen jeho členství v odstraněné skupině.

## <a name="next-steps"></a>Další kroky
Přejděte k dalšímu článku a zjistěte, jak přidružit předplatné k adresáři služby Azure AD.

> [!div class="nextstepaction"]
> [Přidružení předplatného Azure](active-directory-how-subscriptions-associated-directory.md)