---
title: Správa uživatelů v Azure blockchain Workbench
description: Jak spravovat uživatele v Azure blockchain Workbench.
ms.date: 07/15/2020
ms.topic: how-to
ms.reviewer: ravastra
ms.openlocfilehash: be078e7149ba008ab8b7ad44a016a0d1cf82df0e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91263007"
---
# <a name="manage-users-in-azure-blockchain-workbench"></a>Správa uživatelů v Azure blockchain Workbench

Azure blockchain Workbench zahrnuje správu uživatelů pro lidi a organizace, které jsou součástí vaší konsorcia.

## <a name="prerequisites"></a>Požadavky

Vyžaduje se nasazení aplikace blockchain Workbench. Podrobnosti o nasazení najdete v tématu [nasazení Azure blockchain Workbench](deploy.md) .

## <a name="add-azure-ad-users"></a>Přidání uživatelů Azure AD

Azure blockchain Workbench používá pro ověřování, řízení přístupu a role službu Azure Active Directory (Azure AD). Uživatelé v tenantovi Azure AD služby blockchain Workbench můžou ověřit a použít blockchain Workbench. Přidejte uživatele do role aplikace správce k interakci a provádění akcí.

Uživatelé blockchain Workbench musí existovat v tenantovi Azure AD, aby je bylo možné přiřadit k aplikacím a rolím. Pokud chcete přidat uživatele do služby Azure AD, použijte následující postup:

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).
1. V pravém horním rohu vyberte svůj účet a přepněte se na tenanta Azure AD, který je přidružený k blockchain Workbench.
1. Vyberte **Azure Active Directory > uživatelé**. Zobrazí se seznam uživatelů v adresáři.
1. Pokud chcete do adresáře přidat uživatele, vyberte **Nový uživatel**. U externích uživatelů vyberte **Nový uživatel typu Host**.
1. Vyplňte požadovaná pole pro nového uživatele. Vyberte **Vytvořit**.

Další podrobnosti o správě uživatelů v rámci služby Azure AD najdete v dokumentaci k [Azure AD](../../active-directory/fundamentals/add-users-azure-active-directory.md) .

## <a name="manage-blockchain-workbench-administrators"></a>Správa správců blockchain Workbench

Po přidání uživatelů do adresáře si v dalším kroku zvolíte, kteří uživatelé jsou blockchain Workbench Administrators. Uživatelé ve skupině **správce** jsou přidruženi k **roli aplikace Správce** v blockchain Workbench. Správci mohou přidávat nebo odebírat uživatele, přiřazovat uživatele k určitým scénářům a vytvářet nové aplikace.

Chcete-li přidat uživatele do skupiny **Administrators** v adresáři služby Azure AD:

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).
1. Ověřte, že jste v tenantovi Azure AD, který je přidružený k blockchain Workbench, výběrem účtu v pravém horním rohu.
1. Vyberte **Azure Active Directory > podnikových aplikací**.
1. Rozevírací filtr **typu aplikace** změňte na **všechny aplikace** a vyberte **použít**.
1. Vybrat klientskou aplikaci Azure AD pro Azure blockchain Workbench

    ![Registrace všech podnikových aplikací](./media/manage-users/select-blockchain-client-app.png)

1. Vyberte **Uživatelé a skupiny > přidat uživatele**.
1. V části **Přidat přiřazení** vyberte **Uživatelé**. Vyberte nebo vyhledejte uživatele, kterého chcete přidat jako správce. Po dokončení výběru klikněte na **Vybrat** .

    ![Přidat přiřazení](./media/manage-users/add-user-assignment.png)

1. Ověřte, že **role** je nastavená na **správce** .
1. Vyberte **Přiřadit**. Přidaní uživatelé se zobrazí v seznamu s přiřazenou rolí správce.

    ![Uživatelé klientských aplikací blockchain](./media/manage-users/blockchain-admin-list.png)

## <a name="managing-blockchain-workbench-members"></a>Správa členů blockchain Workbench

Pomocí aplikace blockchain Workbench můžete spravovat uživatele a organizace, které jsou součástí vaší konsorcia. Můžete přidat nebo odebrat uživatele pro aplikace a role.

1. V prohlížeči [otevřete aplikaci blockchain Workbench](deploy.md#blockchain-workbench-web-url) a přihlaste se jako správce.

    ![Blockchain Workbench](./media/manage-users/blockchain-workbench-applications.png)

    Do každé aplikace jsou přidány členy. Členové mohou mít jednu nebo více aplikačních rolí pro zahájení smluv nebo provádět akce.

1. Chcete-li spravovat členy pro aplikaci, vyberte dlaždici aplikace v podokně **aplikace** .

    Počet členů přidružených k vybrané aplikaci se projeví v dlaždici členové.

    ![Vybrat aplikaci](./media/manage-users/blockchain-workbench-select-application.png)


#### <a name="add-member-to-application"></a>Přidat člena do aplikace

1. Vyberte dlaždici členů pro zobrazení seznamu aktuálních členů.
1. Vyberte **přidat členy**.

    ![Snímek obrazovky se zobrazí okno členství aplikace s zvýrazněným tlačítkem přidat člena.](./media/manage-users/application-add-members.png)

1. Vyhledejte jméno uživatele.  V seznamu jsou uvedeni pouze uživatelé Azure AD, kteří existují v tenantovi blockchain Workbench. Pokud uživatel není nalezen, je nutné [Přidat uživatele Azure AD](#add-azure-ad-users).

    ![Přidání členů](./media/manage-users/find-user.png)

1. Z rozevíracího seznamu vyberte **roli** .

    ![Vybrat členy role](./media/manage-users/application-select-role.png)

1. Výběrem **Přidat** přidejte člena s přidruženou rolí do aplikace.

#### <a name="remove-member-from-application"></a>Odebrat člena z aplikace

1. Vyberte dlaždici členů pro zobrazení seznamu aktuálních členů.
1. Pro uživatele, kterého chcete odebrat, v rozevíracím seznamu role vyberte **Odebrat** .

    ![Odebrání člena](./media/manage-users/application-remove-member.png)

#### <a name="change-or-add-role"></a>Změnit nebo přidat roli

1. Vyberte dlaždici členů pro zobrazení seznamu aktuálních členů.
1. Pro uživatele, kterého chcete změnit, klikněte na rozevírací seznam a vyberte novou roli.

    ![Změnit roli](./media/manage-users/application-change-role.png)

## <a name="next-steps"></a>Další kroky

V tomto článku se naučíte, jak spravovat uživatele pro Azure blockchain Workbench. Pokud se chcete dozvědět, jak vytvořit aplikaci blockchain, přejděte k dalšímu článku s postupem.

> [!div class="nextstepaction"]
> [Vytvoření aplikace v blockchain v Azure blockchain Workbench](create-app.md)
