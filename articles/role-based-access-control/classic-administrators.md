---
title: Správci předplatného Azure Classic
description: Popisuje, jak přidat nebo změnit role služby Azure Co-Administrator a správce služeb a jak zobrazit správce účtu.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/22/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: dc8a23d8cdb923f86d581d1af2967112d226c847
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2020
ms.locfileid: "92370623"
---
# <a name="azure-classic-subscription-administrators"></a>Správci předplatného Azure Classic

Microsoft doporučuje spravovat přístup k prostředkům Azure pomocí řízení přístupu na základě role Azure (RBAC). Pokud však stále používáte model nasazení Classic, budete muset použít klasickou roli Správce předplatného: Správce služby a spolusprávce. Další informace najdete v tématu [Azure Resource Manager vs. Classic Deployment](../azure-resource-manager/management/deployment-models.md).

Tento článek popisuje, jak přidat nebo změnit role Co-Administrator a správce služeb a jak zobrazit správce účtu.

## <a name="add-a-co-administrator"></a>Přidání spolusprávce

> [!TIP]
> Pokud uživatel potřebuje spravovat nasazení Azure Classic pomocí [modulu Azure Service Management PowerShell](/powershell/module/servicemanagement/azure.service), stačí přidat Co-Administrator. Pokud uživatel používá ke správě klasických prostředků jenom Azure Portal, nebudete muset pro tohoto uživatele přidat klasického správce.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) jako správce služby nebo spolusprávce.

1. Otevřete [Předplatná](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) a vyberte předplatné.

    Co-Administrators lze přiřadit pouze v oboru předplatného.

1. Klikněte na **Řízení přístupu (IAM)** .

1. Klikněte na kartu **Classic Administrators** .

    ![Snímek obrazovky, který otevírá klasické správce](./media/classic-administrators/classic-administrators.png)

1. Kliknutím na **Přidat**  >  **Přidat spolusprávce** otevřete podokno přidat spolusprávce.

    Pokud je možnost Přidat spolusprávce zakázaná, nemáte oprávnění.

1. Vyberte uživatele, kterého chcete přidat, a klikněte na tlačítko **Přidat**.

    ![Snímek obrazovky, který přidává spolusprávce](./media/classic-administrators/add-coadmin.png)

## <a name="add-a-guest-user-as-a-co-administrator"></a>Přidat uživatele typu Host jako Co-Administrator

Pokud chcete přidat uživatele typu Host jako spolusprávce, postupujte stejným způsobem jako v části předchozí [Přidání spolusprávce](#add-a-co-administrator) . Uživatel typu Host musí splňovat následující kritéria:

- Uživatel typu Host musí mít v adresáři přítomnost. To znamená, že uživatel byl pozván do vašeho adresáře a přijal pozvání.

Další informace o tom, jak přidat uživatele typu Host do adresáře, najdete v tématu [přidání Azure Active Directory uživatelů spolupráce B2B v Azure Portal](../active-directory/b2b/add-users-administrator.md).

### <a name="differences-for-guest-users"></a>Rozdíly pro uživatele typu Host

Uživatelům typu Host, kterým byla přiřazena role Co-Administrator, se může u Co-Administrator role v porovnání s členskými uživateli zobrazit několik rozdílů. Představte si následující scénář:

- Uživatel A s účtem Azure AD (pracovní nebo školní účet) je správce služby pro předplatné Azure.
- Uživatel B účet Microsoft.
- Uživatel A přiřadí roli Co-Administrator uživateli B.
- Uživatel B může dělat skoro všechno, ale nemůže registrovat aplikace ani vyhledávat uživatele v adresáři Azure AD.

Očekáváte, že uživatel B může spravovat všechno. Důvodem tohoto rozdílu je to, že se účet Microsoft do předplatného přidal jako uživatel typu Host, a ne jako uživatel s členem. Uživatelé typu Host mají ve srovnání s členskými uživateli různá výchozí oprávnění ve službě Azure AD. Například členské uživatelé mohou číst jiné uživatele v Azure AD a uživatelé typu Host nemůžou. Členové členů můžou registrovat nové instanční objekty ve službě Azure AD a uživatelé typu Host nemůžou.

Pokud uživatel typu Host potřebuje, aby mohl provádět tyto úlohy, je možné řešení přiřadit konkrétní role Azure AD, které uživatel typu Host potřebuje. Například v předchozím scénáři můžete přiřadit roli [čtenáři adresáře](../active-directory/roles/permissions-reference.md#directory-readers) pro čtení dalších uživatelů a přiřazení role [vývojáře aplikace](../active-directory/roles/permissions-reference.md#application-developer) , aby bylo možné vytvářet instanční objekty. Další informace o členech a uživatelích typu Host a jejich oprávněních najdete v tématu [co jsou výchozí oprávnění uživatele v Azure Active Directory?](../active-directory/fundamentals/users-default-permissions.md). Další informace o tom, jak udělit přístup pro uživatele typu Host, najdete v tématu [Přidání nebo odebrání přiřazení rolí Azure pro externí uživatele typu Host pomocí Azure Portal](role-assignments-external-users.md).

Všimněte si, že [předdefinované role Azure](../role-based-access-control/built-in-roles.md) se liší od [rolí Azure AD](../active-directory/roles/permissions-reference.md). Předdefinované role neudělují žádnému přístupu ke službě Azure AD. Další informace najdete v tématu [pochopení různých rolí](../role-based-access-control/rbac-and-directory-admin-roles.md).

Informace, které porovnávají uživatele členů a uživatele typu Host, najdete v tématu [co jsou výchozí oprávnění uživatele v Azure Active Directory?](../active-directory/fundamentals/users-default-permissions.md).

## <a name="remove-a-co-administrator"></a>Odebrání Co-Administrator

1. Přihlaste se k [Azure Portal](https://portal.azure.com) jako správce služby nebo spolusprávce.

1. Otevřete [Předplatná](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) a vyberte předplatné.

1. Klikněte na **Řízení přístupu (IAM)** .

1. Klikněte na kartu **Classic Administrators** .

1. Přidejte značku zaškrtnutí vedle Co-Administrator, kterou chcete odebrat.

1. Klikněte na **Odebrat**.

1. V zobrazeném okně se zprávou klikněte na **Ano**.

    ![Snímek obrazovky, který odebírá spolusprávce](./media/classic-administrators/remove-coadmin.png)

## <a name="change-the-service-administrator"></a>Změna správce služeb

Správce služby pro předplatné může změnit jenom správce účtu. Ve výchozím nastavení platí, že když se přihlásíte k předplatnému Azure, Správce služby je stejný jako správce účtu. Uživatel s rolí správce účtu nemá přístup k Azure Portal. Uživatel s rolí správce služby má plný přístup k Azure Portal. Pokud je správce účtu a správce služby stejný uživatel a Vy změníte Správce služby na jiného uživatele, správce účtu ztratí přístup k Azure Portal. Správce účtu ale může vždy použít centrum účtů ke změně Správce služby zpátky na sebe.

Pomocí těchto kroků můžete změnit správce služby v **centru účtů**.

### <a name="account-center"></a>Centrum účtů

1. Ujistěte se, že je váš scénář podporovaný, kontrolou [omezení pro změnu Správce služby](#limitations-for-changing-the-service-administrator).

1. Přihlaste se do [centra účtů](https://account.windowsazure.com/subscriptions) jako správce účtu.

1. Klikněte na předplatné.

1. Na pravé straně klikněte na **Upravit podrobnosti předplatného**.

    ![Snímek obrazovky s tlačítkem upravit předplatné v centru účtů](./media/classic-administrators/editsub.png)

1. Do pole **Správce služby** zadejte e-mailovou adresu nového správce služby.

    ![Snímek obrazovky se seznamem, ve kterém se má změnit e-mail Správce služby](./media/classic-administrators/change-service-admin.png)

1. Změny uložíte kliknutím na značku zaškrtnutí.

### <a name="limitations-for-changing-the-service-administrator"></a>Omezení pro změnu Správce služby

V každém předplatném Azure může být jen jeden správce služby. Změna správce služby se bude chovat různě v závislosti na tom, jestli je správce účtu účet Microsoft nebo jestli se jedná o účet Azure AD (pracovní nebo školní účet).

| Účet správce účtu | Může změnit správce služby na jiný účet Microsoft? | Můžete změnit správce služby na účet Azure AD ve stejném adresáři? | Může změnit správce služby na účet Azure AD v jiném adresáři? |
| --- | --- | --- | --- |
| Účet Microsoft | Ano | Ne | Ne |
| Účet Azure AD | Ano | Ano | Ne |

Pokud je správcem účtu účet Azure AD, můžete změnit správce služby na účet Azure AD ve stejném adresáři, ale ne v jiném adresáři. Například abby@contoso.com může změnit správce služby na bob@contoso.com , ale nemůže změnit správce služby na, john@notcontoso.com Pokud john@notcontoso.com nemá přítomnost v adresáři contoso.com.

Další informace o účtech Microsoft a účtech Azure AD najdete v tématu [co je Azure Active Directory?](../active-directory/fundamentals/active-directory-whatis.md).

## <a name="view-the-account-administrator"></a>Zobrazení správce účtu

Správce účtu je uživatel, který původně zaregistroval předplatné Azure, a zodpovídá za vlastníka fakturace daného předplatného. Postup změny správce účtu u předplatného najdete v tématu [přenos vlastnictví předplatného Azure na jiný účet](../cost-management-billing/manage/billing-subscription-transfer.md).

Pomocí těchto kroků zobrazíte správce účtu.

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

1. Otevřete [Předplatná](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) a vyberte předplatné.

1. Klikněte na **Vlastnosti**.

    Účet správce předplatného se zobrazí v poli **správce účtu** .

    ![Snímek obrazovky zobrazující správce účtu](./media/classic-administrators/account-admin.png)

## <a name="next-steps"></a>Další kroky

* [Vysvětlení různých rolí](../role-based-access-control/rbac-and-directory-admin-roles.md)
* [Přidání nebo odebrání přiřazení rolí Azure pomocí portálu Azure Portal](../role-based-access-control/role-assignments-portal.md)
* [Přidávání a změna správců předplatného Azure](../cost-management-billing/manage/add-change-subscription-administrator.md)
