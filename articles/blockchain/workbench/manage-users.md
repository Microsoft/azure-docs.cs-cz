---
title: Správa uživatelů v pracovní plochy Azure Blockchain
description: Jak spravovat uživatele v Azure Blockchain Workbench.
ms.date: 05/09/2019
ms.topic: article
ms.reviewer: brendal
ms.openlocfilehash: 573ec477a3e75beb91f90da0545fb7d4c0f9bf39
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252178"
---
# <a name="manage-users-in-azure-blockchain-workbench"></a>Správa uživatelů v pracovní plochy Azure Blockchain

Azure Blockchain Workbench zahrnuje správu uživatelů pro lidi a organizace, které jsou součástí vašeho konsorcia.

## <a name="prerequisites"></a>Požadavky

Je vyžadováno nasazení blockchainworkbench. Podrobnosti o nasazení najdete v [tématu Nasazení Azure Blockchain Workbench.](deploy.md)

## <a name="add-azure-ad-users"></a>Přidání uživatelů Azure AD

Azure Blockchain Workbench používá Azure Active Directory (Azure AD) pro ověřování, řízení přístupu a role. Uživatelé v tenantovi Blockchain Workbench Azure AD můžou ověřovat a používat Blockchain Workbench. Přidejte uživatele do role aplikace Správce, aby mohly pracovat a provádět akce.

Uživatelé blockchainworkbench musí existovat v tenantovi Azure AD, než je budete moci přiřadit k aplikacím a rolím. Chcete-li přidat uživatele do služby Azure AD, použijte následující kroky:

1.  Přihlaste se k [portálu Azure](https://portal.azure.com).
2.  Vyberte svůj účet v pravém horním rohu a přepněte na klienta Azure AD přidruženého k Blockchain Workbench.
3.  Vyberte **možnost Azure Active Directory > uživatelé**. V adresáři se zobrazí seznam uživatelů.
4.  Chcete-li přidat uživatele do adresáře, vyberte **možnost Nový uživatel**. Pro externí uživatele vyberte **Nový uživatel typu Host**.

    ![Nový uživatel](./media/manage-users/add-ad-user.png)

5.  Vyplňte požadovaná pole pro nového uživatele. Vyberte **Vytvořit**.

Další informace o tom, jak spravovat uživatele v rámci Azure AD, najdete v dokumentaci k [Azure](../../active-directory/fundamentals/add-users-azure-active-directory.md) AD.

## <a name="manage-blockchain-workbench-administrators"></a>Správa správců blockchainworkbench

Jakmile jsou uživatelé přidáni do adresáře, dalším krokem je vybrat, kteří uživatelé jsou správci Blockchain Workbench. Uživatelé ve skupině **Administrator** jsou přidruženi k **roli aplikace Správce** v blockchainworkbench. Správci mohou přidávat nebo odebírat uživatele, přiřazovat uživatele ke konkrétním scénářům a vytvářet nové aplikace.

Přidání uživatelů do skupiny **Administrator** v adresáři Azure AD:

1.  Přihlaste se k [portálu Azure](https://portal.azure.com).
2.  Ověřte, že jste v tenantovi Azure AD přidruženém k Blockchain Workbench výběrem účtu v pravém horním rohu.
3.  Vyberte **azure active directory > podnikové aplikace**.
4.  Výběr klientské aplikace Azure AD pro Blockchain Workbench
    
    ![Všechny registrace podnikových aplikací](./media/manage-users/select-blockchain-client-app.png)

5.  Vyberte **Možnost Uživatelé a skupiny > Přidat uživatele**.
6.  V části **Přidat přiřazení** vyberte **Uživatelé**. Vyberte nebo vyhledejte uživatele, kterého chcete přidat jako správce. Po dokončení výběru klikněte na **Vybrat.**

    ![Přidání přiřazení](./media/manage-users/add-user-assignment.png)

9.  Ověřit, že **role** je nastavena na **správce.**
10. Vyberte **Přiřadit**. Přidávajíuživatelé jsou zobrazeni v seznamu s přiřazenou rolí správce.

    ![Uživatelé klientských aplikací Blockchain](./media/manage-users/blockchain-admin-list.png)

## <a name="managing-blockchain-workbench-members"></a>Správa členů Blockchain Workbench

Pomocí aplikace Blockchain Workbench můžete spravovat uživatele a organizace, které jsou součástí vašeho konsorcia. Do aplikací a rolí můžete přidávat nebo odebírat uživatele.

1. [Otevřete blockchainworkbench](deploy.md#blockchain-workbench-web-url) ve svém prohlížeči a přihlaste se jako správce.

    ![Blockchain Workbench](./media/manage-users/blockchain-workbench-applications.png)

    Členové jsou přidány do každé aplikace. Členové mohou mít jednu nebo více rolí aplikace k zahájení smluv nebo provádění akcí.

2. Chcete-li spravovat členy aplikace, vyberte dlaždici aplikace v podokně **Aplikace.**

    Počet členů přidružených k vybrané aplikaci se projeví v dlaždici členů.

    ![Vybrat aplikaci](./media/manage-users/blockchain-workbench-select-application.png)


#### <a name="add-member-to-application"></a>Přidat člena do aplikace

1. Vyberte dlaždici člena, chcete-li zobrazit seznam aktuálních členů.
2. Vyberte **Přidat členy**.

    ![Přidání členů](./media/manage-users/application-add-members.png)

3. Vyhledejte jméno uživatele.  Jsou uvedeni jenom uživatelé Azure AD, kteří existují v tenantovi Blockchain Workbench. Pokud uživatel není nalezen, je třeba [přidat uživatele Azure AD](#add-azure-ad-users).

    ![Přidání členů](./media/manage-users/find-user.png)

4. V rozevíracím souboru vyberte **roli.**

    ![Vybrat členy role](./media/manage-users/application-select-role.png)

5. Vyberte **Přidat,** chcete-li přidat člena s přidruženou rolí do aplikace.

#### <a name="remove-member-from-application"></a>Odebrat člena z aplikace

1. Vyberte dlaždici člena, chcete-li zobrazit seznam aktuálních členů.
2. Pro uživatele, kterého chcete odebrat, zvolte **Odebrat** z rozevíracího souboru role.

    ![Odebrání člena](./media/manage-users/application-remove-member.png)

#### <a name="change-or-add-role"></a>Změna nebo přidání role

1. Vyberte dlaždici člena, chcete-li zobrazit seznam aktuálních členů.
2. Pro uživatele, kterého chcete změnit, klikněte na rozevírací soubor a vyberte novou roli.

    ![Změnit roli](./media/manage-users/application-change-role.png)

## <a name="next-steps"></a>Další kroky

V tomto článku s návody jste se naučili, jak spravovat uživatele pro Azure Blockchain Workbench. Chcete-li se dozvědět, jak vytvořit blockchainovou aplikaci, pokračujte dalším článkem s návody.

> [!div class="nextstepaction"]
> [Vytvoření blockchainové aplikace v Azure Blockchain Workbench](create-app.md)
