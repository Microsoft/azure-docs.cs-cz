---
title: Správa přístupu k Azure resoruces pomocí RBAC a webu Azure portal | Dokumentace Microsoftu
description: Další informace o správě přístupu k prostředkům Azure pro uživatele, skupiny, instančních objektů a spravované identity, řízení přístupu na základě role (RBAC) a webu Azure portal. To zahrnuje výpis přístupu, udělení přístupu a odebrání přístupu.
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
ms.date: 11/30/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 1e3057108ef179af2f4692c061091fbdf59f0af2
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2019
ms.locfileid: "56342333"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-the-azure-portal"></a>Správa přístupu k prostředkům Azure pomocí RBAC a webu Azure portal

[Řízení přístupu na základě role (RBAC)](overview.md) je způsob, která můžete spravovat přístup k prostředkům Azure. Tento článek popisuje, jak spravovat přístup pro uživatele, skupiny, instančních objektů a spravovaných identit pomocí RBAC a webu Azure portal.

## <a name="open-access-control-iam"></a>Otevřete řízení přístupu (IAM)

**Řízení přístupu (IAM)** otevře se okno, označované také jako Správa identit a přístupu v rámci portálu. K zobrazení nebo Správa přístupu na portálu, je první věcí, kterou to obvykle děláte za otevřete okno ovládacího prvku (IAM) přístupu v oboru, ve které chcete zobrazit nebo změnit.

1. Na webu Azure Portal, klikněte na tlačítko **všechny služby** a pak vyberte obor nebo prostředku, kterou chcete zobrazit nebo spravovat. Například můžete vybrat **skupin pro správu**, **předplatná**, **skupiny prostředků**, nebo prostředek.

1. Klikněte na tento konkrétní prostředek, který chcete zobrazit nebo spravovat.

1. Klikněte na tlačítko **řízení přístupu (IAM)**.

    Následuje příklad přístup okna ovládacího prvku (IAM) předplatného.

    ![Okno pro kontrolu (IAM) přístup k předplatnému](./media/role-assignments-portal/access-control-subscription.png)

## <a name="view-roles-and-permissions"></a>Zobrazit role a oprávnění

Definice role je kolekce oprávnění, kterou používáte pro přiřazení rolí. Azure má víc než 70 [předdefinované role pro prostředky Azure](built-in-roles.md). Postupujte podle těchto kroků pro zobrazení, rolí a oprávnění, které lze provést u správy a rovina dat.

1. Otevřít **řízení přístupu (IAM)** v oboru, jako je například skupiny pro správu, předplatné, skupinu prostředků nebo prostředek, ve které chcete zobrazit role a oprávnění.

1. Klikněte na tlačítko **role** kartu pro zobrazení seznamu všechny předdefinované a vlastní role.

   Zobrazí počet uživatelů a skupin, které jsou přiřazeny ke každé roli v tomto oboru.

   ![Seznam Role](./media/role-assignments-portal/roles-list.png)

1. Klikněte na jednotlivé role zobrazíte, kterému byla přiřazena tato role a také zobrazit oprávnění pro roli.

   ![Přiřazení rolí](./media/role-assignments-portal/role-assignments.png)

## <a name="view-role-assignments"></a>Zobrazit přiřazení rolí

Při správě přístupu chcete vědět, kdo má přístup, jaká jsou jejich oprávnění a na jaké úrovni. Pro přístup k seznamu pro uživatele, skupiny, instanční objekt nebo spravovaná identita prohlížení přiřazení rolí.

### <a name="view-role-assignments-for-a-single-user"></a>Zobrazení přiřazení rolí pro jednoho uživatele.

Následujícím postupem zobrazíte přístup pro jednoho uživatele, skupiny, instanční objekt nebo spravovaná identita v určitém rozsahu.

1. Otevřít **řízení přístupu (IAM)** v oboru, jako je například skupiny pro správu, předplatné, skupinu prostředků nebo prostředek, ve které chcete zobrazit přístup.

1. Klikněte na tlačítko **zkontrolovat přístup** kartu.

    ![Řízení přístupu – karta Kontrola přístupu](./media/role-assignments-portal/access-control-check-access.png)

1. V **najít** seznamu, vyberte typ, který chcete zkontrolovat přístup k objektu zabezpečení.

1. Do vyhledávacího pole zadejte adresář pro zobrazovaná jména, e-mailové adresy nebo identifikátorů objektů hledaný řetězec.

    ![Zkontrolovat přístup k seznamu příkazu select](./media/role-assignments-portal/check-access-select.png)

1. Klikněte na objekt zabezpečení otevřít **přiřazení** podokně.

    ![Podokno přiřazení](./media/role-assignments-portal/check-access-assignments.png)

    V tomto podokně se zobrazí role přiřazené k vybraný objekt zabezpečení a obor. Pokud jsou všechny zamítnout přiřazení v tomto oboru nebo zděděné do tohoto oboru, budou uvedené.

### <a name="view-all-role-assignments-at-a-scope"></a>Zobrazit všechna přiřazení rolí v oboru

1. Otevřít **řízení přístupu (IAM)** v oboru, jako je například skupiny pro správu, předplatné, skupinu prostředků nebo prostředek, ve které chcete zobrazit přístup.

1. Klikněte na tlačítko **přiřazení rolí** kartu (nebo klikněte na tlačítko **zobrazení** tlačítko na dlaždici přiřazení role zobrazení) Chcete-li zobrazit všechna přiřazení rolí v tomto oboru.

   ![Řízení přístupu – karta přiřazení Role](./media/role-assignments-portal/access-control-role-assignments.png)

   Na kartě přiřazení Role se zobrazí, kdo má přístup na tento obor. Všimněte si, že některé role mají obor nastavený na **Tento prostředek**, zatímco jiné mají obor **(Zděděný)** z jiného oboru. Přístup přiřazený speciálně pro tento prostředek nebo zděděn od přiřazení do nadřazeného oboru.

## <a name="add-a-role-assignment"></a>Přidat přiřazení role

V RBAC udělit přístup, můžete přiřadit role pro uživatele, skupiny, instanční objekt nebo spravovaná identita. Postupujte podle těchto kroků k udělení přístupu v různých oborech.

### <a name="assign-a-role-at-a-scope"></a>Přiřazení role v oboru

1. Otevřít **řízení přístupu (IAM)** v oboru, jako je například skupiny pro správu, předplatné, skupinu prostředků nebo prostředek, ve které chcete udělit přístup.

1. Klikněte na tlačítko **přiřazení rolí** kartu zobrazíte všechna přiřazení rolí v tomto oboru.

1. Klikněte na tlačítko **přidat přiřazení role** otevřete podokno Přidat přiřazení role.

   Pokud nemáte oprávnění k přiřazování rolí, možnost Přidat přiřazení role se deaktivuje.

   ![Přidání podokna přiřazení role](./media/role-assignments-portal/add-role-assignment.png)

1. V rozevíracím seznamu **Role** vyberte roli, například **Přispěvatel virtuálních počítačů**.

1. V **vyberte** vyberte uživatele, skupiny, instanční objekt nebo spravovaná identita. Pokud se objekt zabezpečení v seznamu nezobrazí, pomocí pole **Vybrat** můžete v adresáři prohledat zobrazované názvy, e-mailové adresy a identifikátory objektů.

1. Klikněte na tlačítko **Uložit** přiřazení role.

   Po chvíli se objekt zabezpečení je přiřazena role na vybraný obor.

### <a name="assign-a-user-as-an-administrator-of-a-subscription"></a>Přiřadit uživatele jako správce předplatného

Chcete-li uživatel správcem předplatného Azure, přiřaďte jim [vlastníka](built-in-roles.md#owner) role v oboru předplatného. Role vlastníka poskytuje úplný přístup uživatelů ke všem prostředkům v rámci předplatného, včetně práva na delegovat přístup ostatním uživatelům. Tyto kroky jsou stejné jako ostatní přiřazení role.

1. Na webu Azure Portal, klikněte na tlačítko **všechny služby** a potom **předplatná**.

1. Klikněte na předplatné, ve které chcete udělit přístup.

1. Klikněte na tlačítko **řízení přístupu (IAM)**.

1. Klikněte na tlačítko **přiřazení rolí** kartu zobrazíte všechna přiřazení rolí pro toto předplatné.

1. Klikněte na tlačítko **přidat přiřazení role** otevřete podokno Přidat přiřazení role.

   Pokud nemáte oprávnění k přiřazování rolí, možnost Přidat přiřazení role se deaktivuje.

   ![Přidání podokna přiřazení role](./media/role-assignments-portal/add-role-assignment.png)

1. V **Role** rozevíracího seznamu, vyberte **vlastníka** role.

1. V **vyberte** seznamu, vyberte uživatele. Pokud nevidíte uživatele v seznamu, můžete zadat **vyberte** pole Hledat v adresáři zobrazovaná jména a e-mailové adresy.

1. Klikněte na tlačítko **Uložit** přiřazení role.

   Po chvíli se má uživatel přiřazenou roli vlastník v oboru předplatného.

## <a name="remove-role-assignments"></a>Odebrat přiřazení role

V RBAC se přístup odebírá odebrání přiřazení role. Postupujte podle těchto kroků k odebrání přístupu.

1. Otevřít **řízení přístupu (IAM)** v oboru, jako je například skupiny pro správu, předplatné, skupinu prostředků nebo prostředek, ve které chcete odebrat přístup.

1. Klikněte na tlačítko **přiřazení rolí** kartu zobrazíte všechna přiřazení rolí pro toto předplatné.

1. V seznamu přiřazení rolí přidejte značku zaškrtnutí vedle objektu zabezpečení s přiřazením role, které chcete odebrat.

   ![Zpráva Odebrání přiřazení role](./media/role-assignments-portal/remove-role-assignment-select.png)

1. Klikněte na tlačítko **odebrat**.

   ![Zpráva Odebrání přiřazení role](./media/role-assignments-portal/remove-role-assignment.png)

1. Ve zprávě přiřazení role odebrat, která se zobrazí, klikněte na tlačítko **Ano**.

    Zděděná přiřazení rolí nejde odebrat. Pokud potřebujete odebrat zděděné přiřazení role, musíte to provést v oboru, ve kterém bylo přiřazení role vytvořené. V **oboru** sloupce, další **(zděděné)** je odkaz, který vás přesměruje do oboru, ve kterém byla role přiřazená. Přejděte k oboru, který je zde uvedený, a odeberte přiřazení role.

   ![Zpráva Odebrání přiřazení role](./media/role-assignments-portal/remove-role-assignment-inherited.png)

## <a name="next-steps"></a>Další postup

* [Kurz: Udělení přístupu uživatelů k prostředkům Azure pomocí RBAC a webu Azure portal](quickstart-assign-role-user-portal.md)
* [Kurz: Udělení přístupu uživatelů k prostředkům Azure pomocí RBAC a Azure Powershellu](tutorial-role-assignments-user-powershell.md)
* [Řešení potíží s RBAC pro prostředky Azure](troubleshooting.md)
* [Uspořádání prostředků se skupinami pro správu Azure](../governance/management-groups/index.md)
