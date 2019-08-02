---
title: Přidání nebo odstranění uživatelů – Azure Active Directory | Dokumentace Microsoftu
description: Pokyny ohledně toho, jak přidat nové uživatele nebo odstranit stávající uživatele pomocí služby Azure Active Directory.
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
ms.openlocfilehash: a8b436fbdb0d70318e6820d3f59f1e198c639e5a
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561697"
---
# <a name="add-or-delete-users-using-azure-active-directory"></a>Přidání nebo odstranění uživatelů pomocí služby Azure Active Directory
Přidejte nové uživatele nebo odstraňte existující uživatele z vaší organizace Azure Active Directory (Azure AD).

## <a name="add-a-new-user"></a>Přidání nového uživatele
Můžete vytvořit nového uživatele pomocí portálu Azure Active Directory.

### <a name="to-add-a-new-user"></a>Chcete-li přidat nového uživatele
1. Přihlaste se k [Azure Portal](https://portal.azure.com/) jako správce uživatele pro organizaci.

2. Vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **nového uživatele**.

    ![Uživatelé – všichni uživatelé stránce zvýrazněnou nového uživatele](media/add-users-azure-active-directory/new-user-all-users-blade.png)

3. Na **uživatele** stránce, vyplňte požadované informace.

    ![Přidání nového uživatele, uživatel stránka s informace o uživateli](media/add-users-azure-active-directory/new-user-user-blade.png)

   - **Název (povinné).** První a poslední název nového uživatele. Například Mary Parker.

   - **Uživatelské jméno (povinné).** Uživatelské jméno nového uživatele. Například, mary@contoso.com.
    
       Součást domény uživatelské jméno musí používat buď počáteční výchozí název domény, <_názevvašídomény_>. onmicrosoft.com, nebo vlastní název domény, třeba contoso.com. Další informace o tom, jak vytvořit vlastního názvu domény najdete v tématu [přidání vlastního názvu domény do Azure Active Directory](add-custom-domain.md).

   - **Profil.** Volitelně můžete přidat další informace o uživateli. Později můžete také přidat informace o uživateli. Další informace o přidání informace o uživateli, naleznete v tématu [postup přidání nebo změně informací profilu uživatele](active-directory-users-profile-azure-portal.md).

   - **Skupiny.** Volitelně můžete přidat uživatele do jednoho nebo více existujících skupin. Později můžete také přidat uživatele do skupin. Další informace o přidávání uživatelů do skupin najdete v tématu [vytvoření základní skupiny a přidání členů](active-directory-groups-create-azure-portal.md).

   - **Role adresáře.** Volitelně můžete přidat uživatele do role správce Azure AD. Můžete přiřadit uživatele jako globální správce nebo jednu nebo více rolí s omezeným správcem v Azure AD. Další informace o přiřazování rolí najdete v tématu [přiřazení rolí uživatelům](active-directory-users-assign-role-azure-portal.md).

4. Zkopírujte automaticky vytvořené heslo součástí **heslo** pole. Bude potřeba předat uživateli toto heslo pro počáteční proces přihlašování.

5. Vyberte **Vytvořit**.

    Uživatel je vytvořen a přidán do vašeho tenanta Azure AD.

## <a name="add-a-new-user-within-a-hybrid-environment"></a>Přidání nového uživatele v hybridním prostředí
Pokud máte prostředí s Azure Active Directory (cloud) a Windows Server Active Directory (v místním prostředí), můžete přidat nové uživatele synchronizovat existujících dat účtu uživatele. Další informace o hybridních prostředí a uživatelům, naleznete v tématu [integrace místních adresářů se službou Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

## <a name="delete-a-user"></a>Odstranění uživatele
Můžete odstranit stávajícího uživatele pomocí portálu Azure Active Directory.

### <a name="to-delete-a-user"></a>K odstranění uživatele
1. Přihlaste se k [Azure Portal](https://portal.azure.com/) pomocí účtu správce uživatele pro organizaci.

2. Vyberte **Azure Active Directory**vyberte **uživatelé**a poté vyhledejte a vyberte uživatele, kterou chcete odstranit z vašeho tenanta Azure AD. Například _Mary Parker_.

3. Vyberte **odstranění uživatele**.

    ![Uživatelé – všichni uživatelé stránce se zvýrazněnou odstranění uživatele](media/add-users-azure-active-directory/delete-user-all-users-blade.png)

    Uživatel se odstraní a už se zobrazí na **uživatelé – všichni uživatelé** stránky. Uživatel můžete zobrazit na **odstraněných uživatelů** stránce dalších 30 dní a dají se obnovit během této doby. Další informace o obnovení uživatele najdete v tématu [jak k obnovení nedávno odstraněného uživatele trvale odeberete](active-directory-users-restore.md). Po odstranění uživatele budou k dispozici všechny licence spotřebované uživatelem pro ostatní uživatele.

    >[!Note]
    >Identity a kontaktní údaje nebo informace o úloze pro uživatele, jehož zdrojem autorita je Windows Server Active Directory aktualizovat, musíte použít Windows Server Active Directory. Po dokončení aktualizace, je nutné počkat na další cyklus synchronizace dokončit předtím, než uvidíte změny.

## <a name="next-steps"></a>Další postup

Po přidání uživatele, můžete provést následující základní procesy:

- [Přidání nebo změně informací profilu](active-directory-users-profile-azure-portal.md)

- [Přiřazení rolí uživatelům](active-directory-users-assign-role-azure-portal.md)

- [Vytvoření základní skupiny a přidání členů](active-directory-groups-create-azure-portal.md)

- [Práce s dynamické skupiny a uživatelé](../users-groups-roles/groups-create-rule.md)

Nebo můžete provádět další úkoly při správě uživatelů, jako například [přidání uživatele typu Host z jiného adresáře](../b2b/what-is-b2b.md) nebo [obnovení odstraněného uživatele](active-directory-users-restore.md). Další informace o dalších dostupných akcí najdete v tématu [dokumentace ke službě Azure Active Directory uživatele management](../users-groups-roles/index.yml).
