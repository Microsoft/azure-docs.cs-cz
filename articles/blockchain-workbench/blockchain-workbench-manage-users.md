---
title: Správa uživatelů v Azure Blockchain Workbench
description: Jak spravovat uživatele v Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 4/26/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: c17a406a0fd6f1172599ac0379c6afc139403148
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="manage-users-in-azure-blockchain-workbench"></a>Správa uživatelů v Azure Blockchain Workbench

Azure Blockchain Workbench zahrnuje správu uživatelů pro osoby a organizace, které jsou součástí vaší consortium.

## <a name="prerequisites"></a>Požadavky

Nasazení Blockchain Workbench je vyžadován. V tématu [nasazení Azure Blockchain Workbench](blockchain-workbench-deploy.md) podrobnosti o nasazení.

## <a name="add-azure-ad-users"></a>Přidat uživatele Azure AD

Azure Blockchain Workbench používá Azure Active Directory (Azure AD) pro ověřování, řízení přístupu a rolí. Uživatelé v klientovi Blockchain Workbench Azure AD můžete ověřit a použít Blockchain Workbench. Přidáte uživatele k roli správce aplikace komunikovat a provádět akce.

Uživatelé Blockchain Workbench musí existovat v klientovi Azure AD, než bude možné přiřadit k aplikacím a rolí. Chcete-li přidat uživatele do služby Azure AD, použijte následující kroky:

1.  Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2.  V pravém horním rohu vyberte svůj účet a přepněte do klienta Azure AD, které jsou přidružené k Blockchain Workbench.
3.  Vyberte **Azure Active Directory > Uživatelé**. Zobrazí seznam uživatelů ve vašem adresáři.
4.  Chcete-li přidat uživatele do adresáře, vyberte **nového uživatele**. Pro externí uživatele, vyberte **nového uživatele guest**.

    ![Nový uživatel](media/blockchain-workbench-manage-users/add-ad-user.png)

5.  Vyplňte požadovaná pole pro nového uživatele. Vyberte **Vytvořit**.

Navštivte [Azure AD](../active-directory/add-users-azure-active-directory.md) dokumentace pro další podrobnosti o tom, jak spravovat uživatele v rámci Azure AD.

## <a name="manage-blockchain-workbench-administrators"></a>Správa správců Blockchain Workbench

Po přidání uživatelů do adresáře, dalším krokem je vybrat uživatele, kteří jsou správci Blockchain Workbench. Uživatelé v **správci** skupiny jsou přidružené **správce aplikační role** v Blockchain Workbench. Správci mohou přidat nebo odebrat uživatele, uživatelům přiřadit konkrétní scénáře a vytvoření nové aplikace.

Přidání uživatelů do **správci** skupiny v adresáři služby Azure AD:

1.  Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2.  Ověřte, zda že jsou v klientovi Azure AD, které jsou přidružené k Blockchain Workbench výběrem účtu v pravém horním rohu.
3.  Vyberte **Azure Active Directory > podnikové aplikace, které**.
4.  Vyberte aplikaci klienta Azure AD pro Blockchain Workbench
    
    ![Všechny registrace aplikace enterprise](media/blockchain-workbench-manage-users/select-blockchain-client-app.png)

5.  Vyberte **uživatelů a skupin > Přidat uživatele**.
6.  V **přidat přiřazení**, vyberte **uživatelé**. Vyberte nebo vyhledejte uživatele, který chcete přidat jako správce. Klikněte na tlačítko **vyberte** po dokončení výběru.

    ![Přidat přiřazení](media/blockchain-workbench-manage-users/add-user-assignment.png)

9.  Ověřte **Role** je nastaven na **správce**
10. Vyberte **Přiřadit**. Přidaní uživatelé se zobrazí v seznamu s rolí správce, který je přiřazen.

    ![Blockchain uživatelů aplikace klienta](media/blockchain-workbench-manage-users/blockchain-admin-list.png)

## <a name="managing-blockchain-workbench-members"></a>Správa Blockchain Workbench členy

Pomocí aplikace Blockchain Workbench pro správu uživatelů a organizací, které jsou součástí vaší consortium. Můžete přidat nebo odebrat uživatelům aplikací a rolí.

[Otevřete Blockchain Workbench](blockchain-workbench-deploy.md#blockchain-workbench-web-url) v váš prohlížeč a přihlaste se jako správce.

![Blockchain Workbench](media/blockchain-workbench-manage-users/blockchain-workbench-applications.png)

### <a name="managing-members-in-applications"></a>Správa členy v aplikacích

Členové se přidají do všech aplikací. Jednu nebo více rolí aplikace k zahájení kontrakty nebo provést akce, mohou mít členové.

Chcete-li přidat členy do aplikace, vyberte dlaždici aplikace v **aplikace** podokně.

![Vybrat aplikaci](media/blockchain-workbench-manage-users/blockchain-workbench-select-application.png)

> [!NOTE]
> Počet členů, které jsou přidružené k vybrané aplikace se odrazí v dlaždici členy.

#### <a name="add-member-to-application"></a>Přidání člena do aplikace

1. Vyberte dlaždici člen zobrazíte seznam aktuální členů.
2. Vyberte **přidat členy**.

    ![Přidat členy](media/blockchain-workbench-manage-users/application-add-members.png)

3. Vyhledejte uživatelské jméno.  Pouze jsou uvedeny Azure AD Uživatelé, kteří existují v klientovi Blockchain Workbench. Pokud uživatel není nalezen, budete muset [přidat uživatele Azure AD](#add-azure-ad-users).

    ![Přidat členy](media/blockchain-workbench-manage-users/find-user.png)

4. Vyberte **Role** z rozevíracího seznamu.

    ![Vyberte členy role](media/blockchain-workbench-manage-users/application-select-role.png)

5. Vyberte **přidat** o přidání člena k roli přidružené k aplikaci.

#### <a name="remove-member-from-application"></a>Odebrání člena z aplikace

1. Vyberte dlaždici člen zobrazíte seznam aktuální členů.
2. Pro uživatele, kterou chcete odebrat, zvolte **odebrat** z role rozevíracího seznamu.

    ![Odebrat člena](media/blockchain-workbench-manage-users/application-remove-member.png)

#### <a name="change-or-add-role"></a>Změna nebo přidání role

1. Vyberte dlaždici člen zobrazíte seznam aktuální členů.
2. Pro uživatele, kterého chcete změnit klikněte na rozevírací nabídku a vyberte novou roli.

    ![Změny role](media/blockchain-workbench-manage-users/application-change-role.png)

## <a name="next-steps"></a>Další postup

[Vytvoření aplikace blockchain v Azure Blockchain Workbench](blockchain-workbench-create-app.md)
