---
title: Správa přístupu pomocí RBAC a webu Azure Portal | Microsoft Docs
description: Zjistěte, jak pomocí řízení přístupu na základě role (RBAC) a webu Azure Portal spravovat přístup pro uživatele, skupiny a aplikace. To zahrnuje výpis přístupu, udělení přístupu a odebrání přístupu.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8078f366-a2c4-4fbb-a44b-fc39fd89df81
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/05/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 1cac4e4cee408e5208d2d5d84f81b8ad7a89f03b
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47033987"
---
# <a name="manage-access-using-rbac-and-the-azure-portal"></a>Správa přístupu pomocí RBAC a webu Azure Portal

[Řízení přístupu na základě role (RBAC)](overview.md) je způsob správy přístupu k prostředkům v Azure. Tento článek popisuje, jak pomocí RBAC a webu Azure Portal spravovat přístup pro uživatele, skupiny a aplikace.

## <a name="list-roles"></a>Výpis rolí

Definice role je kolekce oprávnění, kterou používáte pro přiřazení rolí. Azure má víc než 70 [předdefinované role](built-in-roles.md). Postupujte podle těchto kroků vytvořte seznam rolí na portálu.

1. Na webu Azure Portal zvolte **Všechny služby** a pak **Předplatná**.

1. Zvolte vaše předplatné.

1. Zvolte **Řízení přístupu (IAM)**.

   ![Možnost Role](./media/role-assignments-portal/list-subscription-access-control.png)

1. Zvolte **Role** a zobrazte seznam všech předdefinovaných a vlastních rolí.

   ![Možnost Role](./media/role-assignments-portal/roles-option.png)

   Zobrazí se počet uživatelů a skupin přiřazených k jednotlivým rolím.

   ![Seznam Role](./media/role-assignments-portal/roles-list.png)

## <a name="list-access"></a>Výpis přístupu

Při správě přístupu chcete vědět, kdo má přístup, jaká jsou jejich oprávnění a na jaké úrovni. Seznam přístupu zobrazíte vypsáním přiřazení rolí. Postupujte podle těchto kroků výpisu přístup pro uživatele a výpisu přístup v různých oborech.

### <a name="list-role-assignments-for-a-user"></a>Výpis přiřazení rolí pro uživatele

1. V navigačním seznamu zvolte **Azure Active Directory**.

1. Zvolte **Uživatelé** a otevřete okno **Všichni uživatelé**.

   ![Azure Active Directory – okno Všichni uživatelé](./media/role-assignments-portal/aad-all-users.png)

1. V seznamu zvolte jednoho uživatele.

1. V části **Správa** zvolte **Prostředky Azure**.

   ![Uživatel Azure Active Directory – Prostředky Azure](./media/role-assignments-portal/aad-user-azure-resources.png)

   V okně prostředků Azure můžete zobrazit přiřazení role pro vybraného uživatele a vybrané předplatné. Tento seznam obsahuje pouze přiřazení rolí pro prostředky, které mají oprávnění ke čtení. Například pokud uživatel má také přiřazení role, které nelze číst, tato přiřazení rolí se nezobrazí v seznamu.

1. Pokud máte více předplatných, můžete **předplatné** rozevíracího seznamu zobrazíte přiřazení rolí v jiném předplatném.

### <a name="list-role-assignments-for-a-resource-group"></a>Výpis přiřazení rolí pro skupinu prostředků

1. V navigačním seznamu zvolte **Skupiny prostředků**.

1. Zvolte skupinu prostředků a pak zvolte **Řízení přístupu (IAM)**.

   V okně Ovládací prvek (IAM) přístupu, označované také jako Správa identit a přístupu můžete zjistit, kdo má přístup k této skupině prostředků. Všimněte si, že některé role mají obor nastavený na **Tento prostředek**, zatímco jiné mají obor **(Zděděný)** z jiného oboru. Přístup je ke skupině prostředků přiřazen přímo, nebo je zděděn od přiřazení nadřazeného předplatného.

   ![Skupiny prostředků](./media/role-assignments-portal/resource-group-access-control.png)

### <a name="list-role-assignments-for-a-subscription"></a>Výpis přiřazení rolí pro předplatné

1. Na webu Azure Portal zvolte **Všechny služby** a pak **Předplatná**.

1. Zvolte vaše předplatné.

1. Zvolte **Řízení přístupu (IAM)**.

    V okně Ovládací prvek (IAM) přístupu můžete zjistit, kdo má přístup k tomuto předplatnému a jejich role.

    ![Okno pro kontrolu (IAM) přístup k předplatnému](./media/role-assignments-portal/subscription-access-control.png)

    Správci v klasickém modelu předplatného a spolupracujících správců jsou považovány za vlastníky předplatného v modelu RBAC.

### <a name="list-role-assignments-for-a-management-group"></a>Seznam přiřazení rolí pro skupinu pro správu

1. Na webu Azure Portal, zvolte **všechny služby** a potom **skupin pro správu**.

1. Zvolte vaši skupinu pro správu.

1. Zvolte **(podrobnosti)** vaše vybrané pro skupinu pro správu.

    ![Skupiny pro správu](./media/role-assignments-portal/management-groups-list.png)

1. Zvolte **Řízení přístupu (IAM)**.

    V okně Ovládací prvek (IAM) přístupu můžete zjistit, kdo má přístup k této skupině pro správu a jejich role.

    ![Okna ovládacího prvku (IAM) přístupu pro skupinu pro správu](./media/role-assignments-portal/management-groups-access-control.png)

## <a name="grant-access"></a>Udělení přístupu

V RBAC, chcete-li udělit přístup, přiřaďte roli. Postupujte podle těchto kroků k udělení přístupu v různých oborech.

### <a name="assign-a-role-at-a-resource-group-scope"></a>Přiřazení role v oboru skupiny prostředků

1. V navigačním seznamu zvolte **Skupiny prostředků**.

1. Zvolte skupinu prostředků.

1. Zvolte **Řízení přístupu (IAM)** a zobrazte aktuální seznam přiřazení rolí v oboru skupiny prostředků.

   ![Okna ovládacího prvku (IAM) přístupu pro skupinu prostředků](./media/role-assignments-portal/grant-resource-group-access-control.png)

1. Zvolte **Přidat** a otevřete podokno **Přidat oprávnění**.

   Pokud nemáte oprávnění přiřazovat role, možnost **Přidat** se nezobrazí.

   ![Podokno Přidat oprávnění](./media/role-assignments-portal/add-permissions.png)

1. V rozevíracím seznamu **Role** vyberte roli, například **Přispěvatel virtuálních počítačů**.

1. V seznamu **Vybrat** vyberte uživatele, skupinu nebo aplikaci. Pokud se objekt zabezpečení v seznamu nezobrazí, pomocí pole **Vybrat** můžete v adresáři prohledat zobrazované názvy, e-mailové adresy a identifikátory objektů.

1. Zvolte **Uložit** přiřazení role.

   Za chvíli se objektu zabezpečení přiřadí role v oboru skupiny předplatného.

### <a name="assign-a-role-at-a-subscription-scope"></a>Přiřazení role v oboru předplatného

1. Na webu Azure Portal zvolte **Všechny služby** a pak **Předplatná**.

1. Zvolte vaše předplatné.

1. Zvolte **Řízení přístupu (IAM)** a zobrazte aktuální seznam přiřazení rolí v oboru předplatného.

   ![Okno pro kontrolu (IAM) přístup k předplatnému](./media/role-assignments-portal/grant-subscription-access-control.png)

1. Zvolte **Přidat** a otevřete podokno **Přidat oprávnění**.

   Pokud nemáte oprávnění přiřazovat role, možnost **Přidat** se nezobrazí.

   ![Podokno Přidat oprávnění](./media/role-assignments-portal/add-permissions.png)

1. V rozevíracím seznamu **Role** vyberte roli, například **Přispěvatel virtuálních počítačů**.

1. V seznamu **Vybrat** vyberte uživatele, skupinu nebo aplikaci. Pokud se objekt zabezpečení v seznamu nezobrazí, pomocí pole **Vybrat** můžete v adresáři prohledat zobrazované názvy, e-mailové adresy a identifikátory objektů.

1. Zvolte **Uložit** přiřazení role.

   Za chvíli se objektu zabezpečení přiřadí role v oboru předplatného.

### <a name="assign-a-user-as-an-administrator-of-a-subscription"></a>Přiřadit uživatele jako správce předplatného

Chcete-li uživatel správcem předplatného Azure, přiřaďte jim [vlastníka](built-in-roles.md#owner) role v oboru předplatného. Role vlastníka poskytuje úplný přístup uživatelů ke všem prostředkům v rámci předplatného, včetně práva na delegovat přístup ostatním uživatelům. Tyto kroky jsou stejné jako ostatní přiřazení role.

1. Na webu Azure Portal zvolte **Všechny služby** a pak **Předplatná**.

1. Zvolte vaše předplatné.

1. Zvolte **Řízení přístupu (IAM)** a zobrazte aktuální seznam přiřazení rolí v oboru předplatného.

   ![Okno pro kontrolu (IAM) přístup k předplatnému](./media/role-assignments-portal/grant-subscription-access-control.png)

1. Zvolte **Přidat** a otevřete podokno **Přidat oprávnění**.

   Pokud nemáte oprávnění přiřazovat role, možnost **Přidat** se nezobrazí.

   ![Podokno Přidat oprávnění](./media/role-assignments-portal/add-permissions.png)

1. V **Role** rozevíracího seznamu, vyberte **vlastníka** role.

1. V **vyberte** seznamu, vyberte uživatele. Pokud nevidíte uživatele v seznamu, můžete zadat **vyberte** pole Hledat v adresáři zobrazovaná jména a e-mailové adresy.

1. Zvolte **Uložit** přiřazení role.

   Po chvíli se má uživatel přiřazenou roli vlastník v oboru předplatného.

### <a name="assign-a-role-at-a-management-group-scope"></a>Přiřazení role v oboru skupiny pro správu

1. Na webu Azure Portal, zvolte **všechny služby** a potom **skupin pro správu**.

1. Zvolte vaši skupinu pro správu.

1. Zvolte **(podrobnosti)** vaše vybrané pro skupinu pro správu.

    ![Skupiny pro správu](./media/role-assignments-portal/management-groups-list.png)

1. Zvolte **Řízení přístupu (IAM)** a zobrazte aktuální seznam přiřazení rolí v oboru předplatného.

   ![Okna ovládacího prvku (IAM) přístupu pro skupinu pro správu](./media/role-assignments-portal/grant-management-groups-access-control.png)

1. Zvolte **Přidat** a otevřete podokno **Přidat oprávnění**.

   Pokud nemáte oprávnění přiřazovat role, možnost **Přidat** se nezobrazí.

   ![Podokno Přidat oprávnění](./media/role-assignments-portal/add-permissions-management-groups.png)

1. V **Role** rozevíracího seznamu, vyberte roli, jako **správu skupiny přispěvatelů**.

    Informace o podporovaných akcí pro různé role skupin pro správu najdete v tématu [uspořádání prostředků se skupinami pro správu Azure](../governance/management-groups/index.md#management-group-access).

1. V seznamu **Vybrat** vyberte uživatele, skupinu nebo aplikaci. Pokud se objekt zabezpečení v seznamu nezobrazí, pomocí pole **Vybrat** můžete v adresáři prohledat zobrazované názvy, e-mailové adresy a identifikátory objektů.

1. Zvolte **Uložit** přiřazení role.

   Po chvíli se objekt zabezpečení je přiřazena role v oboru skupiny pro správu.

## <a name="remove-access"></a>Odebrání přístupu

V RBAC se přístup odebírá odebrání přiřazení role. Postupujte podle těchto kroků k odebrání přístupu.

### <a name="remove-a-role-assignment"></a>Odebrání přiřazení role

1. Otevřít **řízení přístupu (IAM)** okně pro skupinu pro správu, předplatné, skupinu prostředků nebo prostředek, který má přiřazení role, kterou chcete odebrat.

1. V seznamu přiřazení rolí přidejte značku zaškrtnutí vedle objektu zabezpečení s přiřazením role, které chcete odebrat.

   ![Zpráva Odebrání přiřazení role](./media/role-assignments-portal/remove-role-assignment-select.png)

1. Zvolte **Odebrat**.

   ![Zpráva Odebrání přiřazení role](./media/role-assignments-portal/remove-role-assignment.png)

1. Ve zprávě Odebrání přiřazení role, která se zobrazí, zvolte **Ano**.

    Zděděná přiřazení rolí nejde odebrat. Pokud potřebujete odebrat zděděné přiřazení role, musíte to provést v oboru, ve kterém bylo přiřazení role vytvořené. V **oboru** sloupce, další **(zděděné)** je odkaz, který vás přesměruje do oboru, ve kterém byla role přiřazená. Přejděte k oboru, který je zde uvedený, a odeberte přiřazení role.

   ![Zpráva Odebrání přiřazení role](./media/role-assignments-portal/remove-role-assignment-inherited.png)

## <a name="next-steps"></a>Další postup

* [Rychlý start: Udělení přístupu pro uživatele pomocí RBAC a webu Azure Portal](quickstart-assign-role-user-portal.md)
* [Kurz: Udělení přístupu pro uživatele pomocí RBAC a Azure PowerShellu](tutorial-role-assignments-user-powershell.md)
* [Předdefinované role](built-in-roles.md)
* [Uspořádání prostředků se skupinami pro správu Azure](../azure-resource-manager/management-groups-overview.md)
