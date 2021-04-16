---
title: Správa tenantů ve fakturačním účtu Microsoft Customer Agreement – Azure
description: Tento článek vám pomůže pochopit a spravovat klienty přidružené k fakturačnímu účtu smlouvy Microsoft Customer.
author: bandersmsft
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 04/06/2021
ms.author: banders
ms.reviewer: baolcsva
ms.openlocfilehash: dc34d0f12430838be29897ccc5cbeee382ecaa2b
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2021
ms.locfileid: "107493055"
---
# <a name="manage-tenants-in-your-microsoft-customer-agreement-billing-account"></a>Správa tenantů ve fakturačním účtu zákaznických smluv Microsoftu

Tento článek vám pomůže pochopit a spravovat klienty přidružené k fakturačnímu účtu smlouvy Microsoft Customer. Pomocí těchto informací můžete spravovat klienty, převádět předplatná a spravovat vlastnictví fakturace a přitom zajistit zabezpečený přístup k fakturačnímu prostředí.

## <a name="whats-a-tenant"></a>Co je tenant?

Tenant je digitální reprezentace vaší organizace a primárně je přidružená k doméně, jako je Microsoft.com. Jedná se o prostředí spravované prostřednictvím Azure Active Directory, které umožňuje přiřadit uživatelům oprávnění ke správě prostředků a fakturace Azure.

Každý tenant je jedinečný a oddělený od ostatních tenantů, ale můžete uživatelům typu host z jiných tenantů dovolit přístup k vašemu tenantovi a sledovat náklady a spravovat fakturaci.

## <a name="how-tenants-and-subscriptions-relate-to-billing-account"></a>Jak se klienti a odběry vztahují k fakturačnímu účtu

Pomocí smlouvy Microsoft Customer Agreement (fakturační účet) můžete sledovat náklady a spravovat fakturaci. Každý fakturační účet má alespoň jeden Fakturační profil. Fakturační profil vám umožní spravovat způsob fakturace a platby. Každý Fakturační profil obsahuje ve výchozím nastavení oddíl jedna faktura. V případě potřeby můžete vytvořit další oddíly faktury pro seskupování, sledování a správu nákladů v podrobnější úrovni.

- Fakturační účet je přidružený k jednomu tenantovi. Znamená to, že k fakturačnímu účtu mají přístup jenom uživatelé, kteří jsou součástí tenanta.
- Když vytvoříte nové předplatné Azure pro fakturační účet, vytvoří se vždy ve vašem tenantovi fakturačního účtu. Můžete však přesunout odběry do jiných tenantů. Stávající odběry můžete propojit taky s ostatními klienty na fakturační účet. Umožňuje centrálně spravovat fakturace prostřednictvím jednoho tenanta a zároveň uchovávat prostředky a odběry v jiných klientech podle vašich potřeb.

Následující diagram znázorňuje, jak se fakturující účet a odběry propojují s klienty. Fakturační účet MCA společnosti Contoso je přidružený k Tenantovi 1, zatímco je účet contoso PAYG přidružený ke klientovi 2. Řekněme, že společnost Contoso chce platit za předplatné PAYG prostřednictvím jejich fakturačního účtu MCA, může použít přenos vlastnictví fakturace k propojení předplatného s jejich fakturačním účtem MCA. Předplatné a jeho prostředky budou stále přidruženy k Tenantovi 2, ale budou platit za použití fakturačního účtu MCA.

:::image type="content" source="./media/manage-tenants/billing-hierarchy-example.png" alt-text="Diagram znázorňující ukázkovou hierarchii fakturace" border="false" lightbox="./media/manage-tenants/billing-hierarchy-example.png":::

## <a name="manage-subscriptions-under-multiple-tenants-in-a-single-microsoft-customer-agreement"></a>Správa předplatných v rámci více tenantů v jedné smlouvě pro zákazníky Microsoftu

Vlastníci fakturace můžou vytvářet předplatná, když mají [příslušná oprávnění](../manage/understand-mca-roles.md#subscription-billing-roles-and-tasks) k fakturačnímu účtu. Ve výchozím nastavení jsou všechna nová předplatná, která jsou vytvořená v rámci smlouvy Microsoft Customer Agreement, v tenantovi smlouvy Microsoft Customer Agreement.

- Předplatné z jiných tenantů můžete propojit s fakturačním účtem smlouvy Microsoft Customer Agreement. Když vezmete vlastnictví fakturace předplatného, změní se jenom fakturační ujednání. Nemá vliv na tenanta služby nebo role Azure RBAC.
- Chcete-li změnit vlastníka předplatného v tenantovi služby, je nutné přenést [odběr do jiného adresáře Azure Active Directory](../../role-based-access-control/transfer-subscription.md).

## <a name="add-guest-users-to-your-microsoft-customer-agreement-tenant"></a>Přidání uživatelů typu Host do tenanta zákaznické smlouvy Microsoftu

Uživatelé, kteří byli přidáni do svého tenanta pro fakturaci smlouvy o zákaznících Microsoftu, se musí podílet jako host.

Pro pozvání někoho jako hosta musí mít uživatel existující e-mailovou adresu s doménou, která se liší od vaší domény Azure Active Directory (AD). Azure AD pošle uživateli typu Host e-mail s odkazem na ověření.

:::image type="content" source="./media/manage-tenants/guest-invitation-email.png" alt-text="Snímek obrazovky s ukázkovým e-mailovým pozvánkou" lightbox="./media/manage-tenants/guest-invitation-email.png" :::

Když se uživatel přidá do tenanta Microsoft Customer Agreement, musí [pozvánku přijmout](../../active-directory/external-identities/b2b-quickstart-add-guest-users-portal.md#accept-the-invitation).

Když vyberou odkaz **přijmout pozvánku** , zobrazí se výzva k ověření pomocí Azure.

:::image type="content" source="./media/manage-tenants/authenticate.png" alt-text="Snímek obrazovky s výzvou k ověření pomocí Azure" lightbox="./media/manage-tenants/authenticate.png" :::

Pak vyberte **přijmout**.

:::image type="content" source="./media/manage-tenants/accept-invitation.png" alt-text="Snímek obrazovky s výzvou k přijetí pozvánky" lightbox="./media/manage-tenants/accept-invitation.png" :::

Po přijetí si můžou [Zobrazit účet pro fakturaci zákaznické smlouvy Microsoft pod cost management + fakturace](../understand/mca-overview.md#check-access-to-a-microsoft-customer-agreement).

:::image type="content" source="./media/manage-tenants/billing-microsoft-customer-agreement-in-list.png" alt-text="Snímek obrazovky se smlouvou o zákaznících Microsoftu v seznamu fakturačních účtů" lightbox="./media/manage-tenants/billing-microsoft-customer-agreement-in-list.png" :::

Autorizace pro pozvání uživatelů typu Host je řízena nastavením služby Azure AD. Hodnota nastavení se zobrazí v části **Nastavení** na stránce **vztahy organizace** . Ujistěte se, že je vybrané nastavení, jinak se Pozvánka nezdařila. Další informace najdete v tématu [omezení oprávnění přístupu uživatele typu Host](../../active-directory/enterprise-users/users-restrict-guest-permissions.md).

:::image type="content" source="./media/manage-tenants/external-collaboration-settings.png" alt-text="Snímek obrazovky zobrazující nastavení externí spolupráce" lightbox="./media/manage-tenants/external-collaboration-settings.png" :::

> [!IMPORTANT]
> Uživatelé typu Host získají přístup k tenantovi zákaznických smluv od Microsoftu, což může potenciálně znamenat zabezpečení. Další informace najdete v tématu o [omezení výchozích oprávnění uživatelů typu Host](../../active-directory/fundamentals/users-default-permissions.md#restrict-member-users-default-permissions).

## <a name="manage-multiple-microsoft-cloud-services-under-an-azure-ad-tenant"></a>Správa více cloudových služeb Microsoftu v rámci tenanta Azure AD

V rámci jednoho tenanta Azure AD můžete spravovat několik cloudových služeb pro vaši organizaci. Uživatelské účty pro všechny nabídky cloudu Microsoftu se ukládají v tenantovi Azure AD, které obsahuje uživatelské účty a skupiny. Následující diagram znázorňuje příklad organizace s více službami pomocí společného tenanta Azure AD obsahujícího účty. Každá služba má svůj vlastní portál, v modrém textu, kde uživatelé spravují své služby.

:::image type="content" source="./media/manage-tenants/diagram-multiple-services-common-azure-ad-tenant-accounts.png" alt-text="Diagram znázorňující příklad organizace s více službami pomocí společného tenanta Azure AD obsahujícího účty." border="false" lightbox="./media/manage-tenants/diagram-multiple-services-common-azure-ad-tenant-accounts.png":::

## <a name="next-steps"></a>Další kroky

Přečtěte si následující články, kde se dozvíte, jak spravovat flexibilní vlastnictví fakturace a zajistit zabezpečený přístup k vaší smlouvě o zákaznících Microsoftu.

- [Jak nastavit tenanta](../../active-directory/develop/quickstart-create-new-tenant.md)
- [Předdefinované role Azure](../../role-based-access-control/built-in-roles.md)
- [Přenos předplatného Azure do jiného adresáře Azure AD](../../role-based-access-control/transfer-subscription.md)
- [Omezení oprávnění k přístupu hosta (Preview) v Azure Active Directory](../../active-directory/enterprise-users/users-restrict-guest-permissions.md)
- [Přidání uživatele typu host do adresáře na webu Azure Portal](../../active-directory/external-identities/b2b-quickstart-add-guest-users-portal.md#accept-the-invitation)
- [Jaká jsou výchozí uživatelská oprávnění v Azure Active Directory?](../../active-directory/external-identities/b2b-quickstart-add-guest-users-portal.md#accept-the-invitation)
- [Co je Azure Active Directory?](../../active-directory/fundamentals/active-directory-whatis.md)