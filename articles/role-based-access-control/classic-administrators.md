---
title: Správci předplatného Azure classic | Dokumentace Microsoftu
description: Popisuje postup přidání nebo změna rolí Azure spolupracujícího správce a Správce služby a postup zobrazení správce účtu.
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
ms.date: 02/10/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: f5049a5a829ce8e94b62409c9d3b1439e8431fb1
ms.sourcegitcommit: 39397603c8534d3d0623ae4efbeca153df8ed791
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56101115"
---
# <a name="azure-classic-subscription-administrators"></a>Správci předplatného Azure classic

Microsoft doporučuje při správě přístupu k prostředkům pomocí řízení přístupu na základě role (RBAC). Pokud stále používáte model nasazení classic, budete však muset použít roli správce klasickém modelu předplatného: Správce služeb a spolupracujících správců. Další informace najdete v tématu [Azure Resource Manageru a klasického nasazení](../azure-resource-manager/resource-manager-deployment-model.md).

Tento článek popisuje postup přidání nebo změna role spolupracujícího správce a Správce služby a jak zobrazit správce účtu.

## <a name="add-a-co-administrator"></a>Přidat jako Spolusprávce

> [!TIP]
> Stačí přidat jako Spolusprávce, je-li uživatel potřebuje ke správě nasazení Azure classic. Doporučujeme, abyste pomocí RBAC pro jiné účely.

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) jako správce služeb.

1. Otevřít [předplatná](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) a vyberte předplatné.

    Spolusprávci můžete přiřadit jenom v oboru předplatného.

1. Klikněte na tlačítko **řízení přístupu (IAM)**.

1. Klikněte na tlačítko **klasického správce** kartu.

    ![Snímek obrazovky, který otevře správce modelu Classic](./media/classic-administrators/classic-administrators.png)

1. Klikněte na tlačítko **přidat** > **spolupracující správce přidat** a otevřete tak podokno další spolupracující správce přidat.

    Pokud je zakázaná možnost spolupracující správce přidat, nemáte oprávnění.

1. Vyberte uživatele, který chcete přidat a klikněte na tlačítko **přidat**.

    ![Snímek obrazovky, který přidá spolusprávce](./media/classic-administrators/add-coadmin.png)

### <a name="adding-a-guest-user-as-a-co-administrator"></a>Přidání uživatele typu Host jako Spolusprávce

[Uživatelé typu Host](../active-directory/b2b/b2b-quickstart-add-guest-users-portal.md) , který byl přiřazen spolupracující správce role může se zobrazit některé rozdíly ve srovnání s různými členské uživatele k roli Spolusprávce. Vezměte v úvahu následující scénář:

- Uživatel A pomocí Azure AD pracovního nebo školního účtu je Správce služeb pro předplatné Azure.
- Uživatel B se účtem Microsoft.
- Uživatel A přiřadí roli Spolusprávce uživateli služby serveru B.
- Uživatel B téměř vše, co můžete dělat, ale nemůže k registraci aplikací nebo vyhledání uživatele v adresáři Azure AD.

Očekáváte, že tento uživatel B může spravovat všechno. Důvod pro tento rozdíl je, že účet Microsoft je přidán do předplatného jako uživatele typu Host místo uživatel členem. Uživatelé typu Host mají různá výchozí oprávnění ve službě Azure AD porovnání s členské uživatele. Například člen uživatelé mohou číst jiných uživatelů ve službě Azure AD a uživatele typu Host nemůže. Člen uživatelé můžou registrovat nový instanční objekty ve službě Azure AD a uživatele typu Host nemůže. Pokud uživatel typu Host musí mít možnost provádět tyto úkoly, je možné řešení přiřadit konkrétní role správce Azure AD potřebuje uživatel typu Host. Například v předchozím scénáři, můžete přiřadit [uživatelé Čtoucí z adresáře](../active-directory/users-groups-roles/directory-assign-admin-roles.md#directory-readers) role ke čtení jiných uživatelů a přiřazení [vývojář aplikace](../active-directory/users-groups-roles/directory-assign-admin-roles.md#application-developer) role, která bude moct vytvořit instanční objekty. Další informace o členských uživatelů typu Host a jejich oprávnění najdete v tématu [co jsou výchozí oprávnění uživatelů ve službě Azure Active Directory?](../active-directory/fundamentals/users-default-permissions.md).

Všimněte si, že [předdefinované role pro prostředky Azure](../role-based-access-control/built-in-roles.md) se liší od [role správce Azure AD](../active-directory/users-groups-roles/directory-assign-admin-roles.md). Předdefinované role není udělit přístup ke službě Azure AD. Další informace najdete v tématu [pochopit různé role](../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="remove-a-co-administrator"></a>Odebrat Spolusprávce

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) jako správce služeb.

1. Otevřít [předplatná](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) a vyberte předplatné.

1. Klikněte na tlačítko **řízení přístupu (IAM)**.

1. Klikněte na tlačítko **klasického správce** kartu.

1. Přidáte značku zaškrtnutí vedle spolupracující správce, které chcete odebrat.

1. Klikněte na tlačítko **odebrat**.

1. V okně se zprávou, která se zobrazí, klikněte na tlačítko **Ano**.

    ![Snímek obrazovky, který odebere spolusprávce](./media/classic-administrators/remove-coadmin.png)

## <a name="change-the-service-administrator"></a>Změna správce služeb

Pouze správce účtu může změnit správce služeb pro předplatné. Ve výchozím nastavení při registraci předplatného Azure, Správce služeb je stejný jako účet správce. Uživatel s rolí správce účet nemá přístup k webu Azure portal. Uživatel s rolí Správce služeb má úplný přístup k webu Azure portal. Pokud účet správce a Správce služby se stejným uživatelem a změnit správce služeb na jiného uživatele, správce účtu ztratí přístup k webu Azure portal. Však správce účtu můžete vždy použít centra pro účty změnit správce služeb samy na sebe.

Existují dva způsoby, jak změním Správce služeb. Můžete změnit v **webu Azure portal** nebo **centra pro účty**.

### <a name="azure-portal"></a>portál Azure

1. Ujistěte se, že váš scénář podporuje kontrolu [omezení pro změnu Správci služeb](#limits).

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) jako správce účtu.

1. Otevřít [předplatná](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) a vyberte předplatné.

1. Klikněte na **Vlastnosti**.

    ![Snímek obrazovky zobrazující účet správce](./media/classic-administrators/account-admin.png)

1. V horní části stránky, klikněte na tlačítko **Správce služby** a otevřete tak podokno Správce služby.

    Tlačítko Správce služby je zakázáno, nemáte oprávnění. Pouze uživatel, který je správcem účtu můžete změnit správce služeb.

1. Vyberte nový správce služeb a pak klikněte na tlačítko **Uložit**.

### <a name="account-center"></a>Centra pro účty

1. Ujistěte se, že váš scénář podporuje kontrolu [omezení pro změnu Správci služeb](#limits).

1. Přihlaste se k [centra pro účty](https://account.windowsazure.com/subscriptions) jako správce účtu.

1. Klikněte na předplatné.

1. Na pravé straně, klikněte na tlačítko **upravit podrobnosti o předplatném**.

    ![Snímek obrazovky zobrazující tlačítko Upravit předplatné v centru účtů](./media/classic-administrators/editsub.png)

1. V **Správce služeb** zadejte e-mailová adresa nového správce služeb.

    ![Snímek obrazovky zobrazující pole, chcete-li změnit e-mailu Správce služby](./media/classic-administrators/change-service-admin.png)

1. Kliknutím na značku zaškrtnutí uložte změnu.

### <a name="limitations-for-changing-the-service-administrator"></a>Omezení při změně správce služby

Každé předplatné je přidružený k adresáři Azure AD. Chcete-li najít adresář předplatného je přidružený, otevřete **předplatná** webu Azure portal a potom vyberte předplatné zobrazíte adresáři.

Pokud jste přihlášení pomocí pracovního nebo školního účtu, můžete přidat další účty ve vaší organizaci jako správce služeb. Například abby@contoso.com můžete přidat bob@contoso.com jako správce služeb, ale nelze přidat john@notcontoso.com Pokud john@notcontoso.com má přítomnost v adresáři contoso.com. Uživatelům přihlášení pomocí pracovních nebo školních účtů můžete nadále přidat jako správce služeb uživatele účtu Microsoft.

  | Metoda přihlašování | Přidat uživatelský účet Microsoft jako správce služeb? | Přidat pracovní nebo školní účet v rámci stejné organizace jako správce služeb? | Přidat pracovní nebo školní účet v jiné organizaci jako správce služeb? |
  | --- | --- | --- | --- |
  |  Účet Microsoft |Ano |Ne |Ne |
  |  Pracovní nebo školní účet |Ano |Ano |Ne |

## <a name="view-the-account-administrator"></a>Zobrazit správce účtu

Účet správce je uživatel, který původně zaregistrovali předplatné Azure a je zodpovědný jako fakturace vlastníkem předplatného. Chcete-li změnit správce účtu spravujete předplatné [přenos vlastnictví předplatného Azure na jiný účet](../billing/billing-subscription-transfer.md).

Následujícím postupem zobrazíte správce účtu.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. Otevřít [předplatná](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) a vyberte předplatné.

1. Klikněte na **Vlastnosti**.

    Správce účtu předplatného se zobrazí v **správce účtu** pole.

    ![Snímek obrazovky zobrazující účet správce](./media/classic-administrators/account-admin.png)

## <a name="next-steps"></a>Další postup

* [Vysvětlení různých rolí v Azure](../role-based-access-control/rbac-and-directory-admin-roles.md)
* [Správa přístupu pomocí RBAC a portálu Azure Portal](../role-based-access-control/role-assignments-portal.md)
