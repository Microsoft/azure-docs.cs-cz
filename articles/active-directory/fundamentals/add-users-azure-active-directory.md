---
title: Přidat nebo odstranit uživatele – Azure Active Directory | Microsoft Docs
description: Pokyny k přidávání nových uživatelů nebo odstraňování stávajících uživatelů pomocí Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: e98b09d8b07c625613e3be149e64ac8f06adc089
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73805550"
---
# <a name="add-or-delete-users-using-azure-active-directory"></a>Přidání nebo odstranění uživatelů pomocí Azure Active Directory
Přidejte nové uživatele nebo odstraňte existující uživatele z vaší organizace Azure Active Directory (Azure AD). Chcete-li přidat nebo odstranit uživatele, musíte být správcem uživatele nebo globálním správcem. 

## <a name="add-a-new-user"></a>Přidat nového uživatele
Nového uživatele můžete vytvořit pomocí portálu Azure Active Directory.

### <a name="to-add-a-new-user"></a>Přidání nového uživatele
1. Přihlaste se k [Azure Portal](https://portal.azure.com/) jako správce uživatele pro organizaci.

2. Vyberte **Azure Active Directory**, vyberte **Uživatelé**a pak vyberte **Nový uživatel**.

    ![Uživatelé – stránka všichni uživatelé s vybraným novým uživatelem](media/add-users-azure-active-directory/new-user-all-users-blade.png)

3. Na stránce **Uživatel** vyplňte požadované informace.

    ![Přidání nového uživatele, stránky uživatele s informacemi o uživateli](media/add-users-azure-active-directory/new-user-user-blade.png)

   - **Název (povinné).** Křestní jméno a příjmení nového uživatele. Například Marie Parker.

   - **Uživatelské jméno (povinné).** Uživatelské jméno nového uživatele. například mary@contoso.com.
    
       Část domény uživatelského jména musí používat počáteční výchozí název domény, <_názevvašídomény_>. Microsoft. com, nebo vlastní název domény, jako je například contoso.com. Další informace o tom, jak vytvořit vlastní název domény, najdete v tématu [Postup přidání vlastního názvu domény do Azure Active Directory](add-custom-domain.md).

   - **Profilu.** Volitelně můžete přidat další informace o uživateli. Informace o uživateli můžete také přidat později. Další informace o přidání informací o uživateli najdete v tématu [jak přidat nebo změnit informace o profilu uživatele](active-directory-users-profile-azure-portal.md).

   - **Skupiny.** Volitelně můžete přidat uživatele do jedné nebo více existujících skupin. Uživatele můžete také přidat do skupin později. Další informace o přidávání uživatelů do skupin najdete v tématu [Postup vytvoření základní skupiny a přidání členů](active-directory-groups-create-azure-portal.md).

   - **Role adresáře.** Volitelně můžete přidat uživatele do role správce Azure AD. Můžete přiřadit uživatele jako globální správce nebo jednu nebo více rolí s omezeným správcem v Azure AD. Další informace o přiřazování rolí najdete v tématu [přiřazení rolí uživatelům](active-directory-users-assign-role-azure-portal.md).

4. Zkopírujte automaticky generované heslo, které jste zadali v poli **heslo** . Toto heslo budete muset zadat uživateli pro počáteční proces přihlášení.

5. Vyberte **Vytvořit**.

    Uživatel se vytvoří a přidá do vašeho tenanta Azure AD.

## <a name="add-a-new-user-within-a-hybrid-environment"></a>Přidání nového uživatele v rámci hybridního prostředí
Pokud máte prostředí s Azure Active Directory (Cloud) i se službou Windows Server Active Directory (místní), můžete přidat nové uživatele synchronizací stávajících dat uživatelského účtu. Další informace o hybridních prostředích a uživatelích najdete v tématu [Integrace místních adresářů s Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

## <a name="delete-a-user"></a>Odstranění uživatele
Existující uživatele můžete odstranit pomocí portálu Azure Active Directory.

### <a name="to-delete-a-user"></a>Odstranění uživatele
1. Přihlaste se k [Azure Portal](https://portal.azure.com/) pomocí účtu správce uživatele pro organizaci.

2. Vyberte **Azure Active Directory**, vyberte **uživatele**a pak vyhledejte a vyberte uživatele, kterého chcete odstranit z tenanta Azure AD. Například _Marie Parker_.

3. Vyberte **Odstranit uživatele**.

    ![Uživatelé – stránka všichni uživatelé s zvýrazněným možností odstranit uživatele](media/add-users-azure-active-directory/delete-user-all-users-blade.png)

    Uživatel se odstraní a už se nezobrazí na stránce **Uživatelé – všichni uživatelé** . Uživatel se může na stránce **Odstraněná uživatelé** zobrazit během následujících 30 dnů a může se v této době obnovit. Další informace o obnovení uživatele najdete v tématu [Postup obnovení nebo trvalého odebrání nedávno odstraněného uživatele](active-directory-users-restore.md). Po odstranění uživatele budou k dispozici všechny licence spotřebované uživatelem pro ostatní uživatele.

    >[!Note]
    >Abyste mohli aktualizovat identitu, kontaktní údaje nebo informace o úlohách pro uživatele, jejichž zdroj oprávnění je Windows Server Active Directory, musíte použít Windows Server Active Directory. Po dokončení aktualizace musíte počkat na dokončení dalšího cyklu synchronizace, než se změny projeví.

## <a name="next-steps"></a>Další kroky

Po přidání uživatelů můžete provádět následující základní procesy:

- [Přidat nebo změnit informace o profilu](active-directory-users-profile-azure-portal.md)

- [Přiřazení rolí uživatelům](active-directory-users-assign-role-azure-portal.md)

- [Vytvoření základní skupiny a přidání členů](active-directory-groups-create-azure-portal.md)

- [Práce s dynamickými skupinami a uživateli](../users-groups-roles/groups-create-rule.md)

Nebo můžete provádět další úlohy správy uživatelů, jako je například [Přidání uživatelů typu host z jiného adresáře](../b2b/what-is-b2b.md) nebo [Obnovení odstraněného uživatele](active-directory-users-restore.md). Další informace o dalších dostupných akcích najdete v tématu [Azure Active Directory dokumentace správy uživatelů](../users-groups-roles/index.yml).
