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
ms.date: 11/12/2019
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: b01eb7730290fbf7340fc0a6d8cac8157498f64a
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2019
ms.locfileid: "74013619"
---
# <a name="add-or-delete-users-using-azure-active-directory"></a>Přidání nebo odstranění uživatelů pomocí služby Azure Active Directory

Přidejte nové uživatele nebo odstraňte existující uživatele z vaší organizace Azure Active Directory (Azure AD). Chcete-li přidat nebo odstranit uživatele, musíte být správcem uživatele nebo globálním správcem.

## <a name="add-a-new-user"></a>Přidání nového uživatele

Můžete vytvořit nového uživatele pomocí portálu Azure Active Directory.

### <a name="to-add-a-new-user"></a>Chcete-li přidat nového uživatele

1. Přihlaste se k [Azure Portal](https://portal.azure.com/) jako správce uživatele pro organizaci.

2. Vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **nového uživatele**.

    ![Uživatelé – všichni uživatelé stránce zvýrazněnou nového uživatele](media/add-users-azure-active-directory/new-user-all-users-blade.png)

3. Na stránce **Nový uživatel** vyberte **vytvořit uživatele** a pak přidejte informace o uživateli.

    ![Přidání nového uživatele, uživatel stránka s informace o uživateli](media/add-users-azure-active-directory/new-user-user-blade.png)

   - **Název (povinné)** : jméno a příjmení nového uživatele. Například Novák zelená.

   - **Uživatelské jméno (povinné)** : uživatelské jméno nového uživatele. Například, chris@contoso.com.

     Část domény uživatelského jména musí používat počáteční výchozí název domény, <_názevvašídomény_>. Microsoft. com nebo vlastní název domény ve vaší organizaci Azure AD, například contoso.com. Můžete vybrat ze seznamu dostupných domén. Seznam můžete také filtrovat zadáním části názvu domény. Další informace o tom, jak vytvořit vlastního názvu domény najdete v tématu [přidání vlastního názvu domény do Azure Active Directory](add-custom-domain.md).

   - **Skupiny**: uživatele můžete přidat do jedné nebo více existujících skupin nebo ho můžete udělat později. Další informace o přidávání uživatelů do skupin najdete v tématu [vytvoření základní skupiny a přidání členů](active-directory-groups-create-azure-portal.md).

   - **Role adresáře**: Pokud pro uživatele požadujete oprávnění správce Azure AD, můžete je přidat do role Azure AD. Můžete přiřadit uživatele jako globální správce nebo jednu nebo více rolí s omezeným správcem v Azure AD. Další informace o přiřazování rolí najdete v tématu [přiřazení rolí uživatelům](active-directory-users-assign-role-azure-portal.md).

   - **Informace o úloze**: tady můžete přidat další informace o uživateli nebo to udělat později. Další informace o přidání informace o uživateli, naleznete v tématu [postup přidání nebo změně informací profilu uživatele](active-directory-users-profile-azure-portal.md).

4. Zkopírujte automaticky vytvořené heslo součástí **heslo** pole. Můžete použít automaticky generované heslo, které jste zadali v poli heslo, nebo si vytvořit vlastní heslo. Bude potřeba předat uživateli toto heslo pro počáteční proces přihlašování.

5. Vyberte **Vytvořit**.

Uživatel se vytvoří a přidá do vaší organizace Azure AD.

## <a name="add-a-new-guest-user"></a>Přidat nového uživatele typu Host

Můžete taky pozvat nového uživatele typu Host, aby spolupracovali s vaší organizací, a to tak, že na stránce **Nový uživatel** vybere možnost **pozvat uživatele** . Pokud jsou nastavení externích spolupráci vaší organizace nakonfigurovaná tak, aby bylo možné pozvat hosty, uživatel bude e-mailem poslat pozvánku, kterou musí přijmout, aby bylo možné začít spolupracovat. Další informace o tom, jak pozvat uživatele na spolupráci B2B, najdete v článku [pozvání uživatelů B2B k Azure Active Directory](../b2b/add-users-administrator.md)

## <a name="add-a-consumer-user"></a>Přidat uživatele příjemce

Můžou nastat scénáře, ve kterých chcete ručně vytvořit uživatelské účty v adresáři Azure Active Directory B2C (Azure AD B2C). Další informace o vytváření uživatelských účtů najdete v tématu [Vytvoření a odstranění uživatelů spotřebitelů v Azure AD B2C](../../active-directory-b2c/manage-users-portal.md).

## <a name="add-a-new-user-within-a-hybrid-environment"></a>Přidání nového uživatele v hybridním prostředí

Pokud máte prostředí s Azure Active Directory (cloud) a Windows Server Active Directory (v místním prostředí), můžete přidat nové uživatele synchronizovat existujících dat účtu uživatele. Další informace o hybridních prostředí a uživatelům, naleznete v tématu [integrace místních adresářů se službou Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

## <a name="delete-a-user"></a>Odstranění uživatele

Můžete odstranit stávajícího uživatele pomocí portálu Azure Active Directory.

### <a name="to-delete-a-user"></a>K odstranění uživatele

1. Přihlaste se k [Azure Portal](https://portal.azure.com/) pomocí účtu správce uživatele pro organizaci.

1. Vyberte **Azure Active Directory**vyberte **uživatelé**a poté vyhledejte a vyberte uživatele, kterou chcete odstranit z vašeho tenanta Azure AD. Například _Mary Parker_.

1. Vyberte **odstranění uživatele**.

    ![Uživatelé – všichni uživatelé stránce se zvýrazněnou odstranění uživatele](media/add-users-azure-active-directory/delete-user-all-users-blade.png)

    Uživatel se odstraní a už se zobrazí na **uživatelé – všichni uživatelé** stránky. Uživatel můžete zobrazit na **odstraněných uživatelů** stránce dalších 30 dní a dají se obnovit během této doby. Další informace o obnovení uživatele najdete v tématu [jak k obnovení nedávno odstraněného uživatele trvale odeberete](active-directory-users-restore.md). Po odstranění uživatele budou k dispozici všechny licence spotřebované uživatelem pro ostatní uživatele.

    >[!Note]
    >Identity a kontaktní údaje nebo informace o úloze pro uživatele, jehož zdrojem autorita je Windows Server Active Directory aktualizovat, musíte použít Windows Server Active Directory. Po dokončení aktualizace, je nutné počkat na další cyklus synchronizace dokončit předtím, než uvidíte změny.

## <a name="next-steps"></a>Další kroky

Po přidání uživatele, můžete provést následující základní procesy:

- [Přidání nebo změně informací profilu](active-directory-users-profile-azure-portal.md)

- [Přiřazení rolí uživatelům](active-directory-users-assign-role-azure-portal.md)

- [Vytvoření základní skupiny a přidání členů](active-directory-groups-create-azure-portal.md)

- [Práce s dynamické skupiny a uživatelé](../users-groups-roles/groups-create-rule.md)

Nebo můžete provádět další úlohy správy uživatelů, jako je například [Přidání uživatelů typu host z jiné organizace Azure AD](../b2b/what-is-b2b.md) nebo [Obnovení odstraněného uživatele](active-directory-users-restore.md). Další informace o dalších dostupných akcí najdete v tématu [dokumentace ke službě Azure Active Directory uživatele management](../users-groups-roles/index.yml).
