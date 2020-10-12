---
title: Přidat nebo odstranit uživatele – Azure Active Directory | Microsoft Docs
description: Pokyny k přidávání nových uživatelů nebo odstraňování stávajících uživatelů pomocí Azure Active Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 11/12/2019
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9fbdccfa7ff16bd63dda1f7c21c07e6d20e086e1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89321085"
---
# <a name="add-or-delete-users-using-azure-active-directory"></a>Přidání nebo odstranění uživatelů pomocí Azure Active Directory

Přidejte nové uživatele nebo odstraňte existující uživatele z vaší organizace Azure Active Directory (Azure AD). Chcete-li přidat nebo odstranit uživatele, musíte být správcem uživatele nebo globálním správcem.

## <a name="add-a-new-user"></a>Přidání nového uživatele

Nového uživatele můžete vytvořit pomocí portálu Azure Active Directory.

Chcete-li přidat nového uživatele, postupujte podle následujících kroků:

1. Přihlaste se k [Azure Portal](https://portal.azure.com/) jako správce uživatele pro organizaci.

1. Vyhledejte a vyberte *Azure Active Directory* z libovolné stránky.

1. Vyberte **Uživatelé**a potom vyberte **Nový uživatel**.

    ![Přidání uživatele prostřednictvím uživatelů – všichni uživatelé v Azure AD](media/add-users-azure-active-directory/add-user-in-users-all-users.png)

1. Na stránce **uživatel** zadejte informace pro tohoto uživatele:

   - **Název:** Povinná hodnota. Křestní jméno a příjmení nového uživatele. Například *Marie Parker*.

   - **Uživatelské jméno**. Povinná hodnota. Uživatelské jméno nového uživatele. Například, `mary@contoso.com`.

     Část domény uživatelského jména musí používat počáteční výchozí název domény, * \<yourdomainname> . onmicrosoft.com*nebo vlastní název domény, jako je například *contoso.com*. Další informace o tom, jak vytvořit vlastní název domény, najdete v tématu [Přidání vlastního názvu domény pomocí portálu Azure Active Directory](add-custom-domain.md).

   - **Skupiny**. Volitelně můžete přidat uživatele do jedné nebo více existujících skupin. Uživatele můžete také přidat do skupin později. Další informace o přidávání uživatelů do skupin najdete v tématu [Vytvoření základní skupiny a přidání členů pomocí Azure Active Directory](active-directory-groups-create-azure-portal.md).

   - **Role adresáře**: Pokud pro uživatele požadujete oprávnění správce Azure AD, můžete je přidat do role Azure AD. Můžete přiřadit uživatele jako globální správce nebo jednu nebo více rolí s omezeným správcem v Azure AD. Další informace o přiřazování rolí najdete v tématu [přiřazení rolí uživatelům](active-directory-users-assign-role-azure-portal.md).

   - **Informace o úloze**: tady můžete přidat další informace o uživateli nebo to udělat později. Další informace o přidání informací o uživateli najdete v tématu [jak přidat nebo změnit informace o profilu uživatele](active-directory-users-profile-azure-portal.md).

1. Zkopírujte automaticky vygenerované heslo, které jste zadali v poli **heslo** . Toto heslo budete muset zadat pro uživatele, aby se přihlásil poprvé.

1. Vyberte **Vytvořit**.

Uživatel se vytvoří a přidá do vaší organizace Azure AD.

## <a name="add-a-new-guest-user"></a>Přidat nového uživatele typu Host

Můžete taky pozvat nového uživatele typu Host, aby spolupracovali s vaší organizací, a to tak, že na stránce **Nový uživatel** vybere možnost **pozvat uživatele** . Pokud jsou nastavení externích spolupráci vaší organizace nakonfigurovaná tak, aby bylo možné pozvat hosty, uživatel bude e-mailem poslat pozvánku, kterou musí přijmout, aby bylo možné začít spolupracovat. Další informace o tom, jak pozvat uživatele na spolupráci B2B, najdete v článku [pozvání uživatelů B2B k Azure Active Directory](../external-identities/add-users-administrator.md)

## <a name="add-a-consumer-user"></a>Přidat uživatele příjemce

Můžou nastat scénáře, ve kterých chcete ručně vytvořit uživatelské účty v adresáři Azure Active Directory B2C (Azure AD B2C). Další informace o vytváření uživatelských účtů najdete v tématu [Vytvoření a odstranění uživatelů spotřebitelů v Azure AD B2C](../../active-directory-b2c/manage-users-portal.md).

## <a name="add-a-new-user-within-a-hybrid-environment"></a>Přidání nového uživatele v rámci hybridního prostředí

Pokud máte prostředí s Azure Active Directory (Cloud) i se službou Windows Server Active Directory (místní), můžete přidat nové uživatele synchronizací stávajících dat uživatelského účtu. Další informace o hybridních prostředích a uživatelích najdete v tématu [Integrace místních adresářů s Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

## <a name="delete-a-user"></a>Odstranění uživatele

Existující uživatele můžete odstranit pomocí portálu Azure Active Directory.

Pokud chcete uživatele odstranit, postupujte podle těchto kroků:

1. Přihlaste se k [Azure Portal](https://portal.azure.com/) pomocí účtu správce uživatele pro organizaci.

1. Vyhledejte a vyberte *Azure Active Directory* z libovolné stránky.

1. Vyhledejte a vyberte uživatele, kterého chcete odstranit z tenanta Azure AD. Například _Marie Parker_.

1. Vyberte **Odstranit uživatele**.

    ![Uživatelé – stránka všichni uživatelé s zvýrazněným možností odstranit uživatele](media/add-users-azure-active-directory/delete-user-all-users-blade.png)

Uživatel se odstraní a už se nezobrazí na stránce **Uživatelé – všichni uživatelé** . Uživatel se může na stránce **Odstraněná uživatelé** zobrazit během následujících 30 dnů a může se v této době obnovit. Další informace o obnovení uživatele najdete v tématu [obnovení nebo odebrání nedávno odstraněného uživatele pomocí Azure Active Directory](active-directory-users-restore.md).

Po odstranění uživatele budou k dispozici všechny licence spotřebované uživatelem pro ostatní uživatele.

>[!Note]
>K aktualizaci identity, kontaktních informací nebo informací o úlohách pro uživatele, jejichž zdroj oprávnění je Windows Server Active Directory, musíte použít Windows Server Active Directory. Po dokončení aktualizace musíte počkat na dokončení dalšího cyklu synchronizace, než se změny projeví.

## <a name="next-steps"></a>Další kroky

Po přidání uživatelů můžete provádět následující základní procesy:

- [Přidat nebo změnit informace o profilu](active-directory-users-profile-azure-portal.md)

- [Přiřazení rolí uživatelům](active-directory-users-assign-role-azure-portal.md)

- [Vytvoření základní skupiny a přidání členů](active-directory-groups-create-azure-portal.md)

- [Práce s dynamickými skupinami a uživateli](../users-groups-roles/groups-create-rule.md)

Nebo můžete provádět další úlohy správy uživatelů, jako je například [Přidání uživatelů typu host z jiného adresáře](../external-identities/what-is-b2b.md) nebo [Obnovení odstraněného uživatele](active-directory-users-restore.md). Další informace o dalších dostupných akcích najdete v tématu [Azure Active Directory dokumentace správy uživatelů](../users-groups-roles/index.yml).