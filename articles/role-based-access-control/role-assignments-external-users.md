---
title: Přidání nebo odebrání přiřazení rolí Azure pro externí uživatele pomocí Azure Portal – Azure RBAC
description: Naučte se, jak udělit přístup k prostředkům Azure pro uživatele mimo organizaci pomocí Azure Portal a řízení přístupu na základě role Azure (Azure RBAC).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: how-to
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: skwan
ms.custom: it-pro
ms.openlocfilehash: a18fc3e4851c2daf03c662cf40cef58cc7d9e77a
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2021
ms.locfileid: "98117703"
---
# <a name="add-or-remove-azure-role-assignments-for-external-guest-users-using-the-azure-portal"></a>Přidání nebo odebrání přiřazení rolí Azure pro externí uživatele typu Host pomocí Azure Portal

[Řízení přístupu na základě role Azure (Azure RBAC)](overview.md) umožňuje lepší správu zabezpečení pro velké organizace a malé a středně velké firmy pracující s externími spolupracovníky, dodavateli nebo prodejci, kteří potřebují přístup ke konkrétním prostředkům ve vašem prostředí, ale ne nutně k celé infrastruktuře nebo k žádným oborům souvisejícím s fakturací. Funkce v [Azure Active Directory B2B](../active-directory/external-identities/what-is-b2b.md) můžete využít ke spolupráci s externími uživateli typu Host a můžete použít Azure RBAC k udělení pouze oprávnění, která uživatelé v prostředí potřebují.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [Azure role assignment prerequisites](../../includes/role-based-access-control/prerequisites-role-assignments.md)]

## <a name="when-would-you-invite-guest-users"></a>Kdy byste mohli pozvat uživatele typu Host?

Tady je několik ukázkových scénářů, kdy můžete pozvat uživatele typu Host do vaší organizace a udělit oprávnění:

- Umožněte externímu nezávislému dodavateli, který má jenom e-mailový účet pro přístup k prostředkům Azure pro projekt.
- Umožněte externímu partnerovi spravovat určité prostředky nebo celé předplatné.
- Umožněte pracovníkům podpory, kteří nejsou ve vaší organizaci (například podpora Microsoftu), dočasný přístup k vašemu prostředku Azure, abyste mohli řešit problémy.

## <a name="permission-differences-between-member-users-and-guest-users"></a>Rozdíly v oprávněních mezi členskými uživateli a uživateli typu Host

Nativní členové adresáře (členové uživatelů) mají různá oprávnění, než uživatelé pozvaní z jiného adresáře jako host spolupráce B2B (uživatelé typu Host). Členové mohou například číst téměř všechny informace o adresáři, zatímco uživatelé typu Host mají omezená oprávnění k adresáři. Další informace o členských uživatelích a uživatelích typu Host najdete v tématu [co jsou výchozí oprávnění uživatele v Azure Active Directory?](../active-directory/fundamentals/users-default-permissions.md).

## <a name="add-a-guest-user-to-your-directory"></a>Přidání uživatele typu host do adresáře

Pomocí těchto kroků přidáte uživatele typu Host do adresáře pomocí stránky Azure Active Directory.

1. Ujistěte se, že jsou nakonfigurovaná externí nastavení spolupráce vaší organizace, aby bylo možné pozvat hosty. Další informace najdete v tématu [Povolení externí spolupráce B2B a Správa toho, kdo může pozvat hosty](../active-directory/external-identities/delegate-invitations.md).

1. V Azure Portal klikněte na **Azure Active Directory**  >  **Uživatelé**  >  **Nový uživatel typu Host**.

    ![Nová funkce uživatele typu Host v Azure Portal](./media/role-assignments-external-users/invite-guest-user.png)

1. Pokud chcete přidat nového uživatele typu Host, postupujte podle pokynů. Další informace najdete v tématu [přidání Azure Active Directory uživatelů spolupráce B2B v Azure Portal](../active-directory/external-identities/add-users-administrator.md#add-guest-users-to-the-directory).

Po přidání uživatele typu Host do adresáře můžete buď Odeslat uživateli typu Host přímý odkaz na sdílenou aplikaci, nebo uživatel typu Host může kliknout na adresu URL pro uplatnění v e-mailu s pozvánkou.

![E-mail pro pozvání uživatele typu Host](./media/role-assignments-external-users/invite-email.png)

Aby mohl uživatel typu Host získat přístup k adresáři, musí dokončit proces pozvání.

![Oprávnění pro kontrolu pozvání uživatele typu Host](./media/role-assignments-external-users/invite-review-permissions.png)

Další informace o procesu pozvánky najdete v tématu [Azure Active Directory uplatnění pozvánky B2B pro spolupráci](../active-directory/external-identities/redemption-experience.md).

## <a name="add-a-role-assignment-for-a-guest-user"></a>Přidání přiřazení role pro uživatele typu Host

V Azure RBAC pro udělení přístupu přiřadíte roli. Chcete-li přidat přiřazení role pro uživatele typu Host, postupujte [stejným způsobem](role-assignments-portal.md#add-a-role-assignment) jako u člena, skupiny, instančního objektu nebo spravované identity. Postupujte podle těchto kroků a přidejte přiřazení role pro uživatele typu Host v různých oborech.

1. Na webu Azure Portal klikněte na **Všechny služby**.

1.  Vyberte sadu prostředků, na které se vztahuje přístup, označovaný také jako obor. Můžete například vybrat **skupiny pro správu**, **předplatná**, **skupiny prostředků** nebo prostředek.

1. Klikněte na konkrétní prostředek.

1. Klikněte na **Řízení přístupu (IAM)**.

    Následující snímek obrazovky ukazuje příklad podokna Řízení přístupu (IAM) pro skupinu prostředků. Pokud tady uděláte nějaké změny řízení přístupu, budou platit jenom pro skupinu prostředků.

    ![Okno řízení přístupu (IAM) pro skupinu prostředků](./media/role-assignments-external-users/access-control-resource-group.png)

1. Kliknutím na kartu **přiřazení rolí** zobrazíte všechna přiřazení rolí v tomto oboru.

1. Kliknutím na **Přidat** > **Přidat přiřazení role** otevřete podokno Přidat přiřazení role.

    Pokud nemáte oprávnění k přiřazování rolí, bude možnost přidat přiřazení role zakázaná.

    ![Přidat nabídku přiřazení role](./media/shared/add-role-assignment-menu.png)

    Otevře se podokno Přidat přiřazení role.

1. V rozevíracím seznamu **Role** vyberte roli, například **Přispěvatel virtuálních počítačů**.

1. V seznamu **Vybrat** vyberte uživatele typu Host. Pokud uživatele v seznamu nevidíte, můžete zadat do pole **Vybrat** a vyhledat tak zobrazované názvy, e-mailové adresy a identifikátory objektů v adresáři.

   ![Přidat podokno přiřazení role](./media/role-assignments-external-users/add-role-assignment.png)

1. Kliknutím na **Save (Uložit** ) přiřaďte roli ve vybraném oboru.

    ![Přiřazení role pro přispěvatele virtuálních počítačů](./media/role-assignments-external-users/access-control-role-assignments.png)

## <a name="add-a-role-assignment-for-a-guest-user-not-yet-in-your-directory"></a>Přidejte přiřazení role pro uživatele typu Host, který ještě není ve vašem adresáři.

Chcete-li přidat přiřazení role pro uživatele typu Host, postupujte [stejným způsobem](role-assignments-portal.md#add-a-role-assignment) jako u člena, skupiny, instančního objektu nebo spravované identity.

Pokud uživatel typu Host ještě není ve vašem adresáři, můžete uživatele pozvat přímo z podokna přidat přiřazení role.

1. Na webu Azure Portal klikněte na **Všechny služby**.

1.  Vyberte sadu prostředků, na které se vztahuje přístup, označovaný také jako obor. Můžete například vybrat **skupiny pro správu**, **předplatná**, **skupiny prostředků** nebo prostředek.

1. Klikněte na konkrétní prostředek.

1. Klikněte na **Řízení přístupu (IAM)**.

1. Kliknutím na kartu **přiřazení rolí** zobrazíte všechna přiřazení rolí v tomto oboru.

1. Kliknutím na **Přidat** > **Přidat přiřazení role** otevřete podokno Přidat přiřazení role.

    ![Přidat nabídku přiřazení role](./media/shared/add-role-assignment-menu.png)

    Otevře se podokno Přidat přiřazení role.

1. V rozevíracím seznamu **Role** vyberte roli, například **Přispěvatel virtuálních počítačů**.

1. V seznamu **Vybrat** zadejte e-mailovou adresu osoby, kterou chcete pozvat, a vyberte tuto osobu.

   ![Pozvat uživatele typu Host v podokně Přidat přiřazení role](./media/role-assignments-external-users/add-role-assignment-new-guest.png)

1. Kliknutím na **Uložit** přidáte uživatele typu Host do adresáře, přiřadíte roli a odešlete pozvánku.

    Po chvíli se vám zobrazí oznámení o přiřazení role a informace o pozvánce.

    ![Přiřazení role a oznámení pozvaní uživatelů](./media/role-assignments-external-users/invited-user-notification.png)

1. Pokud chcete uživatele typu Host pozvat ručně, klikněte na něj pravým tlačítkem a zkopírujte odkaz na pozvánku v oznámení. Neklepejte na odkaz na pozvánku, protože spustí proces pozvánky.

    Odkaz na pozvánku bude mít následující formát:

    `https://invitations.microsoft.com/redeem/...`

1. Odešlete odkaz na pozvánku uživateli typu Host, aby bylo možné dokončit proces pozvánky.

    Další informace o procesu pozvánky najdete v tématu [Azure Active Directory uplatnění pozvánky B2B pro spolupráci](../active-directory/external-identities/redemption-experience.md).

## <a name="remove-a-guest-user-from-your-directory"></a>Odebrání uživatele typu host z adresáře

Před odebráním uživatele typu host z adresáře byste nejdřív měli odebrat všechna přiřazení rolí pro tohoto uživatele typu Host. Pomocí těchto kroků odeberte uživatele typu host z adresáře.

1. Otevřete **řízení přístupu (IAM)** v oboru, jako je například skupina pro správu, předplatné, skupina prostředků nebo prostředek, kde uživatel typu host má přiřazení role.

1. Kliknutím na kartu **přiřazení rolí** zobrazíte všechna přiřazení rolí.

1. V seznamu přiřazení rolí přidejte zaškrtnutí vedle uživatele typu host s přiřazením role, kterou chcete odebrat.

   ![Odebrat přiřazení role](./media/role-assignments-external-users/remove-role-assignment-select.png)

1. Klikněte na **Odebrat**.

   ![Zpráva Odebrání přiřazení role](./media/role-assignments-external-users/remove-role-assignment.png)

1. Ve zprávě odebrat přiřazení role, která se zobrazí, klikněte na **Ano**.

1. V levém navigačním panelu klikněte na **Azure Active Directory**  >  **Uživatelé**.

1. Klikněte na uživatele typu Host, kterého chcete odebrat.

1. Klikněte na **Odstranit**.

   ![Odstranit uživatele typu Host](./media/role-assignments-external-users/delete-guest-user.png)

1. Ve zprávě odstranit, která se zobrazí, klikněte na tlačítko **Ano**.

## <a name="troubleshoot"></a>Řešení potíží

### <a name="guest-user-cannot-browse-the-directory"></a>Uživatel typu Host nemůže procházet adresář

Uživatelé typu Host mají omezená oprávnění k adresáři. Uživatel typu Host nemůže například procházet adresář a nemůže Hledat skupiny nebo aplikace. Další informace najdete v tématu [co jsou výchozí uživatelská oprávnění v Azure Active Directory?](../active-directory/fundamentals/users-default-permissions.md).

![Uživatel typu Host nemůže procházet uživatele v adresáři.](./media/role-assignments-external-users/directory-no-users.png)

Pokud uživatel typu Host potřebuje v adresáři další oprávnění, můžete uživateli typu Host přiřadit roli adresáře. Pokud opravdu chcete, aby měl uživatel typu Host úplný přístup pro čtení do vašeho adresáře, můžete přidat uživatele typu Host do role [čtenáři adresáře](../active-directory/roles/permissions-reference.md) v Azure AD. Další informace najdete v tématu [udělení oprávnění uživatelům z partnerských organizací ve vašem tenantovi Azure Active Directory](../active-directory/external-identities/add-guest-to-role.md).

![Přiřadit roli čtenářů adresáře](./media/role-assignments-external-users/directory-roles.png)

### <a name="guest-user-cannot-browse-users-groups-or-service-principals-to-assign-roles"></a>Uživatel typu Host nemůže procházet role uživatelů, skupin nebo objektů služby.

Uživatelé typu Host mají omezená oprávnění k adresáři. I v případě, že uživatel typu Host je [vlastníkem](built-in-roles.md#owner) v oboru, pokud se pokusí přidat přiřazení role, aby udělil přístup někomu jinému, nemůže procházet seznam uživatelů, skupin nebo instančních objektů.

![Uživatel typu Host nemůže procházet objekty zabezpečení a přiřazovat role.](./media/role-assignments-external-users/directory-no-browse.png)

Pokud uživatel typu Host ví v adresáři přesně přihlašovací jméno uživatele, může udělit přístup. Pokud opravdu chcete, aby měl uživatel typu Host úplný přístup pro čtení do vašeho adresáře, můžete přidat uživatele typu Host do role [čtenáři adresáře](../active-directory/roles/permissions-reference.md) v Azure AD. Další informace najdete v tématu [udělení oprávnění uživatelům z partnerských organizací ve vašem tenantovi Azure Active Directory](../active-directory/external-identities/add-guest-to-role.md).

### <a name="guest-user-cannot-register-applications-or-create-service-principals"></a>Uživatel typu Host nemůže registrovat aplikace nebo vytvářet instanční objekty.

Uživatelé typu Host mají omezená oprávnění k adresáři. Pokud uživatel typu Host potřebuje mít možnost Registrovat aplikace nebo vytvářet instanční objekty, můžete přidat uživatele typu Host do role [vývojář aplikace](../active-directory/roles/permissions-reference.md) v Azure AD. Další informace najdete v tématu [udělení oprávnění uživatelům z partnerských organizací ve vašem tenantovi Azure Active Directory](../active-directory/external-identities/add-guest-to-role.md).

![Uživatel typu Host nemůže registrovat aplikace.](./media/role-assignments-external-users/directory-access-denied.png)

### <a name="guest-user-does-not-see-the-new-directory"></a>Uživatel typu Host nevidí nový adresář

Pokud má uživatel typu Host přístup k adresáři, ale nevidí nový adresář uvedený v Azure Portal, když se pokusí přepnout v podokně **adresáře a předplatného** , ujistěte se, že uživatel typu Host dokončil proces pozvánky. Další informace o procesu pozvánky najdete v tématu [Azure Active Directory uplatnění pozvánky B2B pro spolupráci](../active-directory/external-identities/redemption-experience.md).

### <a name="guest-user-does-not-see-resources"></a>Uživatel typu Host nevidí prostředky.

Pokud uživateli typu Host byl udělen přístup k adresáři, ale nevidí jim prostředky, kterým byl udělen přístup v Azure Portal, ujistěte se, že uživatel typu host vybral správný adresář. Uživatel typu Host může mít přístup k několika adresářům. Chcete-li přepnout adresáře, klikněte v levém horním rohu na **adresář + předplatné** a pak klikněte na příslušný adresář.

![Podokno adresáře a odběry v Azure Portal](./media/role-assignments-external-users/directory-subscription.png)

## <a name="next-steps"></a>Další kroky

- [Přidat uživatele Azure Active Directory spolupráce B2B do Azure Portal](../active-directory/external-identities/add-users-administrator.md)
- [Vlastnosti Azure Active Directoryho uživatele spolupráce B2B](../active-directory/external-identities/user-properties.md)
- [Prvky e-mailu s pozvánkou pro spolupráci B2B Azure Active Directory](../active-directory/external-identities/invitation-email-elements.md)
- [Přidání uživatele typu Host jako spolupracujícího správce](classic-administrators.md#add-a-guest-user-as-a-co-administrator)