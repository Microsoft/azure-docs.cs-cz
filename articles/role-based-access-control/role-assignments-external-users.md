---
title: Přidání nebo odebrání přiřazení rolí pro externí uživatele pomocí RBAC a portálu Azure
description: Zjistěte, jak udělit přístup k prostředkům Azure pro uživatele mimo organizaci pomocí řízení přístupu na základě rolí Azure (RBAC).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: skwan
ms.custom: it-pro
ms.openlocfilehash: 1c440b85f792ac5bb1336f4d20f930aafc38ad7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245639"
---
# <a name="add-or-remove-role-assignments-for-external-guest-users-using-azure-rbac-and-the-azure-portal"></a>Přidání nebo odebrání přiřazení rolí pro externí uživatele typu Host pomocí Azure RBAC a portálu Azure

[Řízení přístupu na základě rolí Azure (RBAC)](overview.md) umožňuje lepší správu zabezpečení pro velké organizace a pro malé a střední firmy, které pracují s externími spolupracovníky, dodavateli nebo nezávislými pracovníky, kteří potřebují přístup ke konkrétním prostředkům ve vašem prostředí, ale ne nutně k celé infrastruktuře nebo jakýmkoli oborům souvisejícím s fakturací. Možnosti služby [Azure Active Directory B2B](../active-directory/b2b/what-is-b2b.md) můžete využít ke spolupráci s externími uživateli typu Host a pomocí nástroje RBAC můžete udělit pouze oprávnění, která uživatelé typu Host potřebují ve vašem prostředí.

## <a name="prerequisites"></a>Požadavky

Chcete-li přidat nebo odebrat přiřazení rolí, musíte mít:

- `Microsoft.Authorization/roleAssignments/write`a `Microsoft.Authorization/roleAssignments/delete` oprávnění, například [Správce přístupu uživatelů](built-in-roles.md#user-access-administrator) nebo [Vlastník](built-in-roles.md#owner)

## <a name="when-would-you-invite-guest-users"></a>Kdy byste pozvali uživatele typu Host?

Zde je několik příkladů scénářů, kdy můžete pozvat uživatele typu Host do vaší organizace a udělit oprávnění:

- Povolte externímu samostatně výdělečně činnému dodavateli, který má jenom e-mailový účet pro přístup k prostředkům Azure pro projekt.
- Umožněte externímu partnerovi spravovat určité prostředky nebo celé předplatné.
- Povolte technikům podpory, kteří nejsou ve vaší organizaci (například podpora Microsoftu), dočasně přistupovat k vašemu prostředku Azure k řešení problémů.

## <a name="permission-differences-between-member-users-and-guest-users"></a>Rozdíly mezi oprávněními mezi členskými uživateli a uživateli typu Host

Nativní členové adresáře (členové uživatelé) mají jiná oprávnění než uživatelé pozvaní z jiného adresáře jako host spolupráce B2B (uživatelé typu Host). Například uživatel éčleny může číst téměř všechny informace o adresáři, zatímco uživatelé typu Host mají omezená oprávnění adresáře. Další informace o členných uživatelích a uživatelích typu Host najdete [v tématu Jaká výchozí uživatelská oprávnění ve službě Azure Active Directory?](../active-directory/fundamentals/users-default-permissions.md)

## <a name="add-a-guest-user-to-your-directory"></a>Přidání uživatele typu host do adresáře

Podle těchto kroků přidejte uživatele typu Host do adresáře pomocí stránky Azure Active Directory.

1. Ujistěte se, že nastavení externí spolupráce vaší organizace je nakonfigurované tak, abyste mohli pozvat hosty. Další informace naleznete [v tématu Povolení externí spolupráce B2B a správa toho, kdo může pozvat hosty](../active-directory/b2b/delegate-invitations.md).

1. Na webu Azure Portal klikněte na **Azure Active Directory** > **Users** > **New Guest user**.

    ![Nová funkce hosta na webu Azure Portal](./media/role-assignments-external-users/invite-guest-user.png)

1. Podle pokynů přidejte nového uživatele typu Host. Další informace najdete v tématu [Přidání uživatelů spolupráce Služby Azure Active Directory B2B na webu Azure Portal](../active-directory/b2b/add-users-administrator.md#add-guest-users-to-the-directory).

Po přidání uživatele typu Host do adresáře můžete buď odeslat uživateli typu Host přímý odkaz na sdílenou aplikaci, nebo může uživatel typu Host kliknout na adresu URL pro využití v e-mailu s pozvánkou.

![E-mail s pozvánkou pro uživatele typu Host](./media/role-assignments-external-users/invite-email.png)

Aby měl uživatel typu Host přístup k vašemu adresáři, musí dokončit proces pozvání.

![Oprávnění k prohlížení pozvaných uživatelem typu Host](./media/role-assignments-external-users/invite-review-permissions.png)

Další informace o procesu pozvání najdete v [tématu Využití pozvánky ke spolupráci služby Azure Active Directory B2B](../active-directory/b2b/redemption-experience.md).

## <a name="add-a-role-assignment-for-a-guest-user"></a>Přidání přiřazení role pro uživatele typu Host

V RBAC, chcete-li udělit přístup, přiřadit roli. Chcete-li přidat přiřazení role pro uživatele typu Host, postupujte [stejným způsobem](role-assignments-portal.md#add-a-role-assignment) jako u členského uživatele, skupiny, instančního objektu nebo spravované identity. Postupujte takto přidat přiřazení role pro uživatele typu Host v různých oborech.

1. Na webu Azure Portal klikněte na **Všechny služby**.

1.  Vyberte sadu prostředků, které přístup platí pro, označované také jako obor. Můžete například vybrat **skupiny pro správu**, **Odběry**, **Skupiny prostředků**nebo prostředek.

1. Klikněte na konkrétní zdroj.

1. Klikněte na **Řízení přístupu (IAM)**.

    Následující snímek obrazovky ukazuje příklad okna řízení přístupu (IAM) pro skupinu prostředků. Pokud zde provedete změny řízení přístupu, budou se vztahovat pouze na skupinu prostředků.

    ![Okno řízení přístupu (IAM) pro skupinu prostředků](./media/role-assignments-external-users/access-control-resource-group.png)

1. Kliknutím na kartu **Přiřazení rolí** zobrazíte všechna přiřazení rolí v tomto oboru.

1. Kliknutím na **Přidat** > **přiřazení role** otevřete podokno Přidat přiřazení role.

    Pokud nemáte oprávnění k přiřazování rolí, bude možnost Přidat přiřazení role zakázána.

    ![Nabídka Přidat](./media/role-assignments-external-users/add-menu.png)

1. V rozevíracím seznamu **Role** vyberte roli, například **Přispěvatel virtuálních počítačů**.

1. V seznamu **Vybrat** vyberte uživatele typu Host. Pokud uživatele v seznamu nevidíte, můžete do pole **Vybrat zadat,** kde vyhledáte zobrazovaná jména, e-mailové adresy a identifikátory objektů v adresáři.

   ![Podokno přiřazení role](./media/role-assignments-external-users/add-role-assignment.png)

1. Kliknutím na **Uložit** přiřadíte roli ve vybraném oboru.

    ![Přiřazení role pro přispěvatele virtuálního počítače](./media/role-assignments-external-users/access-control-role-assignments.png)

## <a name="add-a-role-assignment-for-a-guest-user-not-yet-in-your-directory"></a>Přidání přiřazení role pro uživatele typu Host, který ještě není ve vašem adresáři

Chcete-li přidat přiřazení role pro uživatele typu Host, postupujte [stejným způsobem](role-assignments-portal.md#add-a-role-assignment) jako u členského uživatele, skupiny, instančního objektu nebo spravované identity.

Pokud uživatel typu Host ještě není ve vašem adresáři, můžete ho pozvat přímo z podokna Přidat přiřazení role.

1. Na webu Azure Portal klikněte na **Všechny služby**.

1.  Vyberte sadu prostředků, které přístup platí pro, označované také jako obor. Můžete například vybrat **skupiny pro správu**, **Odběry**, **Skupiny prostředků**nebo prostředek.

1. Klikněte na konkrétní zdroj.

1. Klikněte na **Řízení přístupu (IAM)**.

1. Kliknutím na kartu **Přiřazení rolí** zobrazíte všechna přiřazení rolí v tomto oboru.

1. Kliknutím na **Přidat** > **přiřazení role** otevřete podokno Přidat přiřazení role.

    ![Nabídka Přidat](./media/role-assignments-external-users/add-menu.png)

1. V rozevíracím seznamu **Role** vyberte roli, například **Přispěvatel virtuálních počítačů**.

1. Do seznamu **Vybrat** zadejte e-mailovou adresu osoby, kterou chcete pozvat, a vyberte ji.

   ![Pozvat uživatele typu Host v podokně Přidat přiřazení rolí](./media/role-assignments-external-users/add-role-assignment-new-guest.png)

1. Kliknutím na **Uložit** přidáte uživatele typu Host do adresáře, přiřadíte roli a odešlete pozvánku.

    Po chvíli se zobrazí oznámení o přiřazení role a informace o pozvánce.

    ![Přiřazení role a oznámení pozvaných uživatelů](./media/role-assignments-external-users/invited-user-notification.png)

1. Chcete-li uživatele hosta pozvat ručně, klikněte pravým tlačítkem myši na odkaz pozvánky v oznámení a zkopírujte jej. Neklikejte na odkaz pozvánky, protože spustí proces pozvánky.

    Odkaz na pozvánku bude mít následující formát:

    `https://invitations.microsoft.com/redeem/...`

1. Odešlete odkaz na pozvánku uživateli hosta k dokončení procesu pozvání.

    Další informace o procesu pozvání najdete v [tématu Využití pozvánky ke spolupráci služby Azure Active Directory B2B](../active-directory/b2b/redemption-experience.md).

## <a name="remove-a-guest-user-from-your-directory"></a>Odebrání uživatele typu Host z adresáře

Před odebráním uživatele typu Host z adresáře byste měli nejprve odebrat všechna přiřazení rolí pro tohoto uživatele typu Host. Chcete-li odebrat uživatele typu Host z adresáře, postupujte takto.

1. Otevřít **řízení přístupu (IAM)** v oboru, jako je například skupina pro správu, předplatné, skupina prostředků nebo prostředek, kde má uživatel typu Host přiřazení role.

1. Kliknutím na kartu **Přiřazení rolí** zobrazíte všechna přiřazení rolí.

1. V seznamu přiřazení rolí přidejte vedle uživatele typu Host zaškrtnutí přiřazení role, které chcete odebrat.

   ![Odebrání přiřazení role](./media/role-assignments-external-users/remove-role-assignment-select.png)

1. Klikněte na **Odebrat**.

   ![Zpráva Odebrání přiřazení role](./media/role-assignments-external-users/remove-role-assignment.png)

1. Ve zprávě odebrat přiřazení role, která se zobrazí, klepněte na tlačítko **Ano**.

1. Na levém navigačním panelu klikněte na**položku Uživatelé** **služby Azure Active Directory** > .

1. Klikněte na uživatele typu Host, kterého chcete odebrat.

1. Klepněte na **tlačítko Odstranit**.

   ![Odstranit uživatele typu Host](./media/role-assignments-external-users/delete-guest-user.png)

1. V odstraněné zprávě, která se zobrazí, klepněte na tlačítko **Ano**.

## <a name="troubleshoot"></a>Řešení potíží

### <a name="guest-user-cannot-browse-the-directory"></a>Uživatel typu Host nemůže procházet adresář.

Uživatelé typu Host mají omezená oprávnění adresáře. Uživatelé typu Host například nemohou procházet adresář a nemohou vyhledávat skupiny nebo aplikace. Další informace naleznete v [tématu Jaká výchozí uživatelská oprávnění ve službě Azure Active Directory?](../active-directory/fundamentals/users-default-permissions.md)

![Uživatel typu Host nemůže procházet uživatele v adresáři.](./media/role-assignments-external-users/directory-no-users.png)

Pokud uživatel typu Host potřebuje další oprávnění v adresáři, můžete přiřadit roli adresáře uživateli typu Host. Pokud opravdu chcete, aby uživatel typu Host měl úplný přístup pro čtení do vašeho adresáře, můžete přidat uživatele typu Host do role [Čtečky adresářů](../active-directory/users-groups-roles/directory-assign-admin-roles.md) ve službě Azure AD. Další informace najdete v tématu [Udělení oprávnění uživatelům z partnerských organizací v tenantovi služby Azure Active Directory](../active-directory/b2b/add-guest-to-role.md).

![Přiřadit roli čtečky adresářů](./media/role-assignments-external-users/directory-roles.png)

### <a name="guest-user-cannot-browse-users-groups-or-service-principals-to-assign-roles"></a>Uživatel typu Host nemůže procházet uživatele, skupiny nebo instanční objekty pro přiřazení rolí.

Uživatelé typu Host mají omezená oprávnění adresáře. I v případě, že uživatel typu Host je [vlastníkem](built-in-roles.md#owner) v oboru, pokud se pokusí přidat přiřazení role udělit někomu jinému přístup, nemohou procházet seznam uživatelů, skupin nebo instančních objektů.

![Uživatel typu Host nemůže procházet objekty zabezpečení pro přiřazení rolí.](./media/role-assignments-external-users/directory-no-browse.png)

Pokud uživatel typu Host zná v adresáři přesné přihlašovací jméno uživatele, může udělit přístup. Pokud opravdu chcete, aby uživatel typu Host měl úplný přístup pro čtení do vašeho adresáře, můžete přidat uživatele typu Host do role [Čtečky adresářů](../active-directory/users-groups-roles/directory-assign-admin-roles.md) ve službě Azure AD. Další informace najdete v tématu [Udělení oprávnění uživatelům z partnerských organizací v tenantovi služby Azure Active Directory](../active-directory/b2b/add-guest-to-role.md).

### <a name="guest-user-cannot-register-applications-or-create-service-principals"></a>Uživatel typu Host nemůže zaregistrovat aplikace ani vytvořit instanční objekty.

Uživatelé typu Host mají omezená oprávnění adresáře. Pokud uživatel typu Host potřebuje mít možnost zaregistrovat aplikace nebo vytvořit instanční objekty, můžete přidat uživatele typu Host do role [Vývojář aplikací](../active-directory/users-groups-roles/directory-assign-admin-roles.md) ve službě Azure AD. Další informace najdete v tématu [Udělení oprávnění uživatelům z partnerských organizací v tenantovi služby Azure Active Directory](../active-directory/b2b/add-guest-to-role.md).

![Uživatel typu Host nemůže zaregistrovat aplikace.](./media/role-assignments-external-users/directory-access-denied.png)

### <a name="guest-user-does-not-see-the-new-directory"></a>Uživatel typu Host neuvidí nový adresář

Pokud uživateli typu Host byl udělen přístup k adresáři, ale při pokusu o přepnutí v podokně **Služby pro odběr Adresář +** se mu nezobrazí nový adresář uvedený na portálu Azure, ujistěte se, že uživatel typu Host dokončil proces pozvání. Další informace o procesu pozvání najdete v [tématu Využití pozvánky ke spolupráci služby Azure Active Directory B2B](../active-directory/b2b/redemption-experience.md).

### <a name="guest-user-does-not-see-resources"></a>Uživatel typu Host nevidí prostředky

Pokud uživateli typu Host byl udělen přístup k adresáři, ale nezobrazují prostředky, ke kterým byl udělen přístup na webu Azure Portal, ujistěte se, že uživatel typu Host vybral správný adresář. Uživatel typu Host může mít přístup k více adresářům. Pokud chcete přepnout adresáře, klikněte v levém horním rohu na **Adresář + odběr**a potom klikněte na příslušný adresář.

![Podokno Adresáře + předplatná na webu Azure Portal](./media/role-assignments-external-users/directory-subscription.png)

## <a name="next-steps"></a>Další kroky

- [Přidání uživatelů spolupráce služby Azure Active Directory B2B na webu Azure Portal](../active-directory/b2b/add-users-administrator.md)
- [Vlastnosti uživatele spolupráce služby Azure Active Directory B2B](../active-directory/b2b/user-properties.md)
- [Prvky e-mailu s pozvánkou na spolupráci B2B – Azure Active Directory](../active-directory/b2b/invitation-email-elements.md)
- [Přidání uživatele typu Host jako spolusprávce](classic-administrators.md#add-a-guest-user-as-a-co-administrator)