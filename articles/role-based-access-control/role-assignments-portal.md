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
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/13/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 8f2c77a366c96455016894c042868d080551bc6a
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2018
ms.locfileid: "36295860"
---
# <a name="manage-access-using-rbac-and-the-azure-portal"></a>Správa přístupu pomocí RBAC a webu Azure Portal

[Řízení přístupu na základě role (RBAC)](overview.md) je způsob správy přístupu k prostředkům v Azure. Tento článek popisuje, jak pomocí RBAC a webu Azure Portal spravovat přístup pro uživatele, skupiny a aplikace.

## <a name="list-roles"></a>Výpis rolí

Definice role je kolekce oprávnění, kterou používáte pro přiřazení rolí. Azure obsahuje více než 60 [předdefinovaných rolí](built-in-roles.md).

1. Na webu Azure Portal zvolte **Všechny služby** a pak **Předplatná**.

1. Zvolte vaše předplatné.

1. Zvolte **Řízení přístupu (IAM)**.

   ![Možnost Role](./media/role-assignments-portal/list-subscription-access-control.png)

1. Zvolte **Role** a zobrazte seznam všech předdefinovaných a vlastních rolí.

   ![Možnost Role](./media/role-assignments-portal/roles-option.png)

   Zobrazí se počet uživatelů a skupin přiřazených k jednotlivým rolím.

   ![Seznam Role](./media/role-assignments-portal/roles-list.png)

## <a name="list-access"></a>Výpis přístupu

Při správě přístupu chcete vědět, kdo má přístup, jaká jsou jejich oprávnění a na jaké úrovni. Seznam přístupu zobrazíte vypsáním přiřazení rolí.

### <a name="list-role-assignments-for-a-subscription"></a>Výpis přiřazení rolí pro předplatné

1. Na webu Azure Portal zvolte **Všechny služby** a pak **Předplatná**.

1. Zvolte vaše předplatné.

1. Zvolte **Řízení přístupu (IAM)**.

    V okně Řízení přístupu (IAM), označovaném také jako Správa identit a přístupu, se zobrazí, kdo má přístup k tomuto předplatnému a jejich role.

    ![Okno Řízení přístupu (IAM)](./media/role-assignments-portal/subscription-access-control.png)

    Klasičtí správci a spolusprávci předplatného se v modelu RBAC považují za vlastníky předplatného.


### <a name="list-role-assignments-for-a-resource-group"></a>Výpis přiřazení rolí pro skupinu prostředků

1. V navigačním seznamu zvolte **Skupiny prostředků**.

1. Zvolte skupinu prostředků a pak zvolte **Řízení přístupu (IAM)**.

   V okně Řízení přístupu (IAM) se zobrazí, kdo má přístup k této skupině prostředků. Všimněte si, že některé role mají obor nastavený na **Tento prostředek**, zatímco jiné mají obor **(Zděděný)** z jiného oboru. Přístup je ke skupině prostředků přiřazen přímo, nebo je zděděn od přiřazení nadřazeného předplatného.

   ![Skupiny prostředků](./media/role-assignments-portal/resource-group-access-control.png)

### <a name="list-role-assignments-for-a-user"></a>Výpis přiřazení rolí pro uživatele

1. V navigačním seznamu zvolte **Azure Active Directory**.

1. Zvolte **Uživatelé** a otevřete okno **Všichni uživatelé**.

   ![Azure Active Directory – okno Všichni uživatelé](./media/role-assignments-portal/aad-all-users.png)

1. V seznamu zvolte jednoho uživatele.

1. V části **Správa** zvolte **Prostředky Azure**.

   ![Uživatel Azure Active Directory – Prostředky Azure](./media/role-assignments-portal/aad-user-azure-resources.png)

   V okně Prostředky Azure se zobrazí přiřazení rolí pro vybraného uživatele. Tento seznam obsahuje pouze přiřazení rolí pro prostředky, ke kterým máte oprávnění ke čtení. Pokud má uživatel například přiřazení rolí také v jiném předplatném, ke kterému nemáte oprávnění ke čtení, v seznamu se takové přiřazení rolí nezobrazí.

## <a name="grant-access"></a>Udělení přístupu

V RBAC se přístup uděluje vytvořením přiřazení role.

### <a name="create-a-role-assignment-at-a-subscription-scope"></a>Vytvoření přiřazení role v oboru předplatného

1. Na webu Azure Portal zvolte **Všechny služby** a pak **Předplatná**.

1. Zvolte vaše předplatné.

1. Zvolte **Řízení přístupu (IAM)** a zobrazte aktuální seznam přiřazení rolí v oboru předplatného.

   ![Okno Řízení přístupu (IAM) pro skupinu prostředků](./media/role-assignments-portal/grant-subscription-access-control.png)

1. Zvolte **Přidat** a otevřete podokno **Přidat oprávnění**.

   Pokud nemáte oprávnění přiřazovat role, možnost **Přidat** se nezobrazí.

   ![Podokno Přidat oprávnění](./media/role-assignments-portal/add-permissions.png)

1. V rozevíracím seznamu **Role** vyberte roli, například **Přispěvatel virtuálních počítačů**.

1. V seznamu **Vybrat** vyberte uživatele, skupinu nebo aplikaci. Pokud se objekt zabezpečení v seznamu nezobrazí, pomocí pole **Vybrat** můžete v adresáři prohledat zobrazované názvy, e-mailové adresy a identifikátory objektů.

1. Zvolte **Uložit** a vytvořte přiřazení role.

   Za chvíli se objektu zabezpečení přiřadí role v oboru předplatného.

### <a name="create-a-role-assignment-at-a-resource-group-scope"></a>Vytvoření přiřazení role v oboru skupiny prostředků

1. V navigačním seznamu zvolte **Skupiny prostředků**.

1. Zvolte skupinu prostředků.

1. Zvolte **Řízení přístupu (IAM)** a zobrazte aktuální seznam přiřazení rolí v oboru skupiny prostředků.

   ![Okno Řízení přístupu (IAM) pro skupinu prostředků](./media/role-assignments-portal/grant-resource-group-access-control.png)

1. Zvolte **Přidat** a otevřete podokno **Přidat oprávnění**.

   Pokud nemáte oprávnění přiřazovat role, možnost **Přidat** se nezobrazí.

   ![Podokno Přidat oprávnění](./media/role-assignments-portal/add-permissions.png)

1. V rozevíracím seznamu **Role** vyberte roli, například **Přispěvatel virtuálních počítačů**.

1. V seznamu **Vybrat** vyberte uživatele, skupinu nebo aplikaci. Pokud se objekt zabezpečení v seznamu nezobrazí, pomocí pole **Vybrat** můžete v adresáři prohledat zobrazované názvy, e-mailové adresy a identifikátory objektů.

1. Zvolte **Uložit** a vytvořte přiřazení role.

   Za chvíli se objektu zabezpečení přiřadí role v oboru skupiny předplatného.

## <a name="remove-access"></a>Odebrání přístupu

V RBAC se přístup odebírá odebrání přiřazení role.

### <a name="remove-a-role-assignment"></a>Odebrání přiřazení role

1. Otevřete okno **Řízení přístupu (IAM)** pro předplatné, skupinu prostředků nebo prostředek s přiřazením role, které chcete odebrat.

1. V seznamu přiřazení rolí přidejte značku zaškrtnutí vedle objektu zabezpečení s přiřazením role, které chcete odebrat.

   ![Zpráva Odebrání přiřazení role](./media/role-assignments-portal/remove-role-assignment-select.png)

1. Zvolte **Odebrat**.

   ![Zpráva Odebrání přiřazení role](./media/role-assignments-portal/remove-role-assignment.png)

1. Ve zprávě Odebrání přiřazení role, která se zobrazí, zvolte **Ano**.

Zděděná přiřazení rolí nejde odebrat. Pokud potřebujete odebrat zděděné přiřazení role, musíte to provést v oboru, ve kterém bylo přiřazení role vytvořené. Ve sloupci **Obor** vedle sloupce **Zděděný** je odkaz, pomocí kterého můžete přejít k prostředkům, kde byla role přiřazená. Přejděte k oboru, který je zde uvedený, a odeberte přiřazení role.

## <a name="other-tools-to-manage-access"></a>Další nástroje pro správu přístupu

Přiřazování rolí a správu přístupu pomocí řízení přístupu na základě role v Azure lze provádět i v jiných nástrojích než portálu Azure. Další informace najdete na následujících odkazech:

* [Azure PowerShell](role-assignments-powershell.md)
* [Azure CLI](role-assignments-cli.md)
* [REST API](role-assignments-rest.md)

## <a name="next-steps"></a>Další kroky

* [Rychlý start: Udělení přístupu pro uživatele pomocí RBAC a webu Azure Portal](quickstart-assign-role-user-portal.md)
* [Kurz: Udělení přístupu pro uživatele pomocí RBAC a Azure PowerShellu](tutorial-role-assignments-user-powershell.md)
* [Předdefinované role](built-in-roles.md)
