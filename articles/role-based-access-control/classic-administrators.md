---
title: Správci předplatného Azure classic | Dokumenty společnosti Microsoft
description: Popisuje, jak přidat nebo změnit role spolusprávce Azure a správce služeb a jak zobrazit správce účtu.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/22/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 2493e893f9afda0642bd838c94538dd0b984bce5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243481"
---
# <a name="azure-classic-subscription-administrators"></a>Správci předplatného Azure classic

Společnost Microsoft doporučuje spravovat přístup k prostředkům Azure pomocí řízení přístupu na základě rolí (RBAC). Pokud však stále používáte klasický model nasazení, budete muset použít klasickou roli správce předplatného: Správce služeb a spolusprávce. Další informace najdete [v tématu Azure Resource Manager vs. klasické nasazení](../azure-resource-manager/management/deployment-models.md).

Tento článek popisuje, jak přidat nebo změnit role spolusprávce a správce služeb a jak zobrazit správce účtu.

## <a name="add-a-co-administrator"></a>Přidání spolusprávce

> [!TIP]
> Co-Administrator a další správce je potřeba přidat jenom v případě, že uživatel potřebuje spravovat klasická nasazení Azure pomocí [modulu Azure Service Management PowerShell Module](https://docs.microsoft.com/powershell/module/servicemanagement/azure). Pokud uživatel používá ke správě klasických prostředků jenom portál Azure, nebudete muset pro uživatele přidávat klasického správce.

1. Přihlaste se k [portálu Azure](https://portal.azure.com) jako správce služeb nebo spolusprávce.

1. Otevřete [Předplatná](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) a vyberte předplatné.

    Spolusprávci lze přiřadit pouze v oboru předplatného.

1. Klikněte na **Řízení přístupu (IAM)**.

1. Klikněte na kartu **Klasickí správci.**

    ![Snímek obrazovky, který otevírá klasické správce](./media/classic-administrators/classic-administrators.png)

1. Kliknutím na **Přidat** > **spolusprávce** otevřete podokno Přidat spolusprávce.

    Pokud je možnost Přidat spolusprávce zakázána, nemáte oprávnění.

1. Vyberte uživatele, kterého chcete přidat, a klepněte na tlačítko **Přidat**.

    ![Snímek obrazovky, který přidává spolusprávce](./media/classic-administrators/add-coadmin.png)

## <a name="add-a-guest-user-as-a-co-administrator"></a>Přidání uživatele typu Host jako spolusprávce

Chcete-li přidat uživatele typu Host jako spolusprávce, postupujte stejným způsobem jako v předchozím [oddílu Přidat spolusprávce.](#add-a-co-administrator) Uživatel typu Host musí splňovat následující kritéria:

- Uživatel typu Host musí mít přítomnost ve vašem adresáři. To znamená, že uživatel byl pozván do vašeho adresáře a pozvání přijal.

Další informace o tom, jak přidat uživatele typu Host do adresáře, najdete v tématu [Přidání uživatelů spolupráce Služby Azure AD B2B na webu Azure Portal](../active-directory/b2b/add-users-administrator.md).

### <a name="differences-for-guest-users"></a>Rozdíly pro uživatele typu Host

Uživatelé typu Host, kterým byla přiřazena role Spolusprávce, mohou zobrazit určité rozdíly ve srovnání s členskými uživateli s rolí Spolusprávce. Představte si následující scénář:

- Uživatel A s účtem Azure AD (pracovní nebo školní účet) je správce služeb pro předplatné Azure.
- Uživatel B má účet Microsoft.
- Uživatel A přiřadí roli spolusprávce uživateli B.
- Uživatel B můžete dělat téměř všechno, ale není schopen zaregistrovat aplikace nebo vyhledat uživatele v adresáři Azure AD.

Dalo by se očekávat, že uživatel B mohl spravovat všechno. Důvodem tohoto rozdílu je, že účet Microsoft je přidán do předplatného jako uživatel typu Host namísto členského uživatele. Uživatelé typu Host mají ve službě Azure AD ve srovnání s členskými uživateli různá výchozí oprávnění. Například uživatelé členů můžete číst ostatní uživatele ve službě Azure AD a uživatelé typu Host nemůže. Uživatelé členských uživatelů můžete zaregistrovat nové instanční objekty ve službě Azure AD a uživatelé typu Host nelze.

Pokud uživatel typu Host potřebuje být schopen provádět tyto úkoly, možným řešením je přiřadit konkrétní role správce Azure AD, které uživatel hostpotřebuje potřebuje. Například v předchozím scénáři můžete přiřadit roli [Čtečky adresářů](../active-directory/users-groups-roles/directory-assign-admin-roles.md#directory-readers) ke čtení ostatních uživatelů a přiřadit roli [Vývojář aplikace,](../active-directory/users-groups-roles/directory-assign-admin-roles.md#application-developer) abyste mohli vytvářet instanční objekty. Další informace o členech a uživatelích typu Host a jejich oprávněních najdete [v tématu Jaká výchozí uživatelská oprávnění ve službě Azure Active Directory?](../active-directory/fundamentals/users-default-permissions.md) Další informace o udělení přístupu uživatelům typu Host najdete v [tématu Správa přístupu k prostředkům Azure pro externí uživatele typu Host pomocí RBAC](role-assignments-external-users.md).

Všimněte si, že [předdefinované role pro prostředky Azure](../role-based-access-control/built-in-roles.md) se liší od rolí správce Azure [AD](../active-directory/users-groups-roles/directory-assign-admin-roles.md). Předdefinované role neudělit žádný přístup k Azure AD. Další informace naleznete [v tématu Principy různých rolí](../role-based-access-control/rbac-and-directory-admin-roles.md).

Informace, které porovnávají uživatele členů a uživatele typu Host, najdete [v tématu Jaká výchozí uživatelská oprávnění ve službě Azure Active Directory?](../active-directory/fundamentals/users-default-permissions.md)

## <a name="remove-a-co-administrator"></a>Odebrání spolusprávce

1. Přihlaste se k [portálu Azure](https://portal.azure.com) jako správce služeb nebo spolusprávce.

1. Otevřete [Předplatná](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) a vyberte předplatné.

1. Klikněte na **Řízení přístupu (IAM)**.

1. Klikněte na kartu **Klasickí správci.**

1. Vedle spolusprávce, kterého chcete odebrat, přidejte zaškrtnutí.

1. Klikněte na **Odebrat**.

1. V posuzovacím poli se zprávou klepněte na tlačítko **Ano**.

    ![Snímek obrazovky, který odebere spolusprávce](./media/classic-administrators/remove-coadmin.png)

## <a name="change-the-service-administrator"></a>Změna správce služeb

Správce účtu může změnit pouze správce služeb pro předplatné. Ve výchozím nastavení je správce služeb při registraci předplatného Azure stejný jako správce účtu. Uživatel s rolí Správce účtu nemá přístup k portálu Azure. Uživatel s rolí Správce služeb má úplný přístup k portálu Azure. Pokud správce účtu a správce služeb jsou stejný uživatel a změníte správce služeb na jiného uživatele, správce účtu ztratí přístup k portálu Azure Portal. Správce účtu však může vždy použít Centrum účtů ke změně správce služeb zpět na sebe.

Následujícím postupem změňte správce služeb v **Centru účtů**.

### <a name="account-center"></a>Centrum účtů

1. Zkontrolujte, zda je váš scénář podporován kontrolou [omezení pro změnu správce služeb](#limitations-for-changing-the-service-administrator).

1. Přihlaste se do [Centra účtů](https://account.windowsazure.com/subscriptions) jako správce účtu.

1. Klikněte na předplatné.

1. Na pravé straně klikněte na **Upravit podrobnosti o předplatném**.

    ![Snímek obrazovky s tlačítkem Upravit odběr v Centru účtů](./media/classic-administrators/editsub.png)

1. Do pole **SPRÁVCE SLUŽBY** zadejte e-mailovou adresu nového správce služeb.

    ![Snímek obrazovky s polem pro změnu e-mailu správce služby](./media/classic-administrators/change-service-admin.png)

1. Kliknutím na zaškrtnutí uložte změnu.

### <a name="limitations-for-changing-the-service-administrator"></a>Omezení pro změnu správce služby

Na jedno předplatné Azure může být jenom jeden správce služeb. Změna správce služeb se bude chovat odlišně v závislosti na tom, zda správce účtu je účet Microsoft nebo zda se jedná o účet Azure AD (pracovní nebo školní účet).

| Účet správce účtu | Můžete změnit správce služeb na jiný účet Microsoft? | Můžete změnit správce služby na účet Azure AD ve stejném adresáři? | Můžete změnit správce služby na účet Azure AD v jiném adresáři? |
| --- | --- | --- | --- |
| Účet Microsoft | Ano | Ne | Ne |
| Účet Azure AD | Ano | Ano | Ne |

Pokud správce účtu je účet Azure AD, můžete změnit správce služby na účet Azure AD ve stejném adresáři, ale ne v jiném adresáři. Správce abby@contoso.com služby můžete například bob@contoso.comzměnit na , ale john@notcontoso.com john@notcontoso.com správce služby změnit na ji, pokud není v contoso.com adresáři.

Další informace o účtech Microsoft a Azure AD najdete v tématu [Co je Azure Active Directory?](../active-directory/fundamentals/active-directory-whatis.md).

## <a name="view-the-account-administrator"></a>Zobrazení správce účtu

Správce účtu je uživatel, který se původně zaregistroval k předplatnému Azure a je zodpovědný jako vlastník fakturace předplatného. Pokud chcete změnit správce účtu předplatného, přečtěte si část [Převod vlastnictví předplatného Azure na jiný účet](../cost-management-billing/manage/billing-subscription-transfer.md).

Chcete-li zobrazit správce účtu, postupujte takto.

1. Přihlaste se k [portálu Azure](https://portal.azure.com).

1. Otevřete [Předplatná](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) a vyberte předplatné.

1. Klikněte na **Vlastnosti**.

    Správce účtu předplatného se zobrazí v poli **Správce účtu.**

    ![Snímek obrazovky se správcem účtu](./media/classic-administrators/account-admin.png)

## <a name="next-steps"></a>Další kroky

* [Vysvětlení různých rolí v Azure](../role-based-access-control/rbac-and-directory-admin-roles.md)
* [Správa přístupu k prostředkům Azure pomocí RBAC a webu Azure Portal](../role-based-access-control/role-assignments-portal.md)
* [Přidání nebo změna správců předplatného Azure](../cost-management-billing/manage/add-change-subscription-administrator.md)
