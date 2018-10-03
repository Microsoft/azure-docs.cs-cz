---
title: Správa uživatelů v Azure Blockchain Workbench
description: Jak spravovat uživatele v aplikaci Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 10/1/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: adb4b57ce304a3c6e369d6cc68dbb7c5857fb9fe
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2018
ms.locfileid: "48242242"
---
# <a name="manage-users-in-azure-blockchain-workbench"></a>Správa uživatelů v Azure Blockchain Workbench

Azure Blockchain Workbench zahrnuje správu uživatelů pro lidem a organizacím, které jsou součástí vašeho consortium.

## <a name="prerequisites"></a>Požadavky

Blockchain Workbench umožňuje nasazení je povinný. Zobrazit [Azure Blockchain Workbench umožňuje nasazení](deploy.md) podrobnosti o nasazení.

## <a name="add-azure-ad-users"></a>Přidat uživatele Azure AD

Azure Blockchain Workbench použije Azure Active Directory (Azure AD) pro ověřování, řízení přístupu a rolí. Uživatele v tenantovi Azure AD Blockchain Workbench můžete ověřit a používat Blockchain Workbench. Přidání uživatelů do role správce aplikace pro interakci a provádět akce.

Blockchain Workbench uživatelé musí existovat v tenantovi Azure AD, abyste mohli přiřadit do aplikací a rolí. Pro přidání uživatelů do služby Azure AD, použijte následující kroky:

1.  Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2.  V pravém horním rohu vyberte svůj účet a přepněte do tenanta služby Azure AD přidružené k Blockchain Workbench.
3.  Vyberte **Azure Active Directory > Uživatelé**. Zobrazí seznam uživatelů v adresáři.
4.  Chcete-li přidat uživatele do adresáře, **nového uživatele**. Pro externí uživatele, vyberte **nový uživatel typu Host**.

    ![Nový uživatel](./media/manage-users/add-ad-user.png)

5.  Vyplňte požadovaná pole pro nového uživatele. Vyberte **Vytvořit**.

Navštivte [Azure AD](../../active-directory/fundamentals/add-users-azure-active-directory.md) dokumentaci podrobné informace o tom, jak spravovat uživatele v rámci Azure AD.

## <a name="manage-blockchain-workbench-administrators"></a>Správa správců Blockchain Workbench

Po přidání uživatele do adresáře, dalším krokem je vybrat, kteří jsou správci Blockchain Workbench. Uživatelé v **správce** jsou přidružené skupiny **aplikační role správce** v Blockchain Workbench. Správce můžete přidat nebo odebrat uživatele, přiřazení uživatelů ke konkrétní scénáře a vytváření nových aplikací.

Přidání uživatelů do **správce** skupiny v adresáři Azure AD:

1.  Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2.  Ověřte, že máte v tenantovi Azure AD přidružené k Blockchain Workbench výběrem účtu v pravém horním rohu.
3.  Vyberte **Azure Active Directory > podnikové aplikace**.
4.  Vyberte klientské aplikace Azure AD pro Blockchain Workbench
    
    ![Registrace všech podnikových aplikací](./media/manage-users/select-blockchain-client-app.png)

5.  Vyberte **uživatele a skupiny > Přidat uživatele**.
6.  V **přidat přiřazení**vyberte **uživatelé**. Vyberte nebo vyhledejte uživatele, kterého chcete přidat jako správce. Klikněte na tlačítko **vyberte** po dokončení výběru.

    ![Přidat přiřazení](./media/manage-users/add-user-assignment.png)

9.  Ověřte **Role** je nastavena na **správce**
10. Vyberte **Přiřadit**. Přidání uživatelů se zobrazí v seznamu s přiřazenou roli správce.

    ![Uživatelé aplikace klienta Blockchain](./media/manage-users/blockchain-admin-list.png)

## <a name="managing-blockchain-workbench-members"></a>Správa členů Blockchain Workbench

Použijte Blockchain Workbench umožňuje aplikaci pro správu uživatelů a organizací, které jsou součástí vašeho konsorcia. Můžete přidat nebo odebrat uživatele do aplikací a rolí.

1. [Otevřete Blockchain Workbench](deploy.md#blockchain-workbench-web-url) ve svém prohlížeči a přihlaste se jako správce.

    ![Blockchain Workbench](./media/manage-users/blockchain-workbench-applications.png)

    Členové jsou přidány pro danou aplikaci. Členy mohou mít jednu nebo více rolí aplikace spustit kontraktech nebo provést akce.

2. Můžete spravovat členy pro aplikaci, vyberte dlaždici aplikace **aplikací** podokně.

    Počet členů, které jsou přidružené k vybrané aplikace se projeví v dlaždici členy.

    ![Vybrat aplikaci](./media/manage-users/blockchain-workbench-select-application.png)


#### <a name="add-member-to-application"></a>Přidání člena do aplikace

1. Vyberte člena dlaždici zobrazíte seznam aktuálních členů.
2. Vyberte **přidat členy**.

    ![Přidat členy](./media/manage-users/application-add-members.png)

3. Vyhledejte jméno uživatele.  Pouze uživatelé Azure AD, které existují v tenantovi Blockchain Workbench patří. Pokud uživatel není nalezen, je potřeba [přidat uživatele Azure AD](#add-azure-ad-users).

    ![Přidat členy](./media/manage-users/find-user.png)

4. Vyberte **Role** z rozevíracího seznamu.

    ![Vyberte členy role](./media/manage-users/application-select-role.png)

5. Vyberte **přidat** se přidat člena role přidružené k aplikaci.

#### <a name="remove-member-from-application"></a>Odebrání člena z aplikace

1. Vyberte člena dlaždici zobrazíte seznam aktuálních členů.
2. Pro uživatele, které chcete odebrat, zvolte **odebrat** z role rozevíracího seznamu.

    ![Odebrat člena](./media/manage-users/application-remove-member.png)

#### <a name="change-or-add-role"></a>Změnit nebo přidat roli

1. Vyberte člena dlaždici zobrazíte seznam aktuálních členů.
2. Pro uživatele, kterého chcete změnit klikněte na rozevírací seznam a vyberte novou roli.

    ![Změnit roli](./media/manage-users/application-change-role.png)

## <a name="next-steps"></a>Další postup

V tento článek jste se dozvěděli o správě uživatelů pro Azure Blockchain Workbench. Naučíte se vytvářet blockchainové aplikace, pokračujte na další článek.

> [!div class="nextstepaction"]
> [Vytvoření blockchainové aplikace v Azure Blockchain Workbench](create-app.md)
