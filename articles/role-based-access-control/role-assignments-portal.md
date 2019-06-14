---
title: Správa přístupu k prostředkům Azure pomocí RBAC a webu Azure portal | Dokumentace Microsoftu
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
ms.date: 02/24/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: bb23cbc275e01eab5361504c547c020b0a29f4c3
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60533034"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-the-azure-portal"></a>Správa přístupu k prostředkům Azure pomocí RBAC a webu Azure portal

[Řízení přístupu na základě role (RBAC)](overview.md) je způsob, která můžete spravovat přístup k prostředkům Azure. Tento článek popisuje, jak spravovat přístup pomocí webu Azure portal. Pokud potřebujete ke správě přístupu k Azure Active Directory, přečtěte si [zobrazení a přiřazení rolí správce ve službě Azure Active Directory](../active-directory/users-groups-roles/directory-manage-roles-portal.md).

## <a name="prerequisites"></a>Požadavky

Přidávat a odebírat přiřazení rolí, musíte mít:

- `Microsoft.Authorization/roleAssignments/write` a `Microsoft.Authorization/roleAssignments/delete` oprávnění, jako například [správce uživatelských přístupů](built-in-roles.md#user-access-administrator) nebo [vlastníka](built-in-roles.md#owner)

## <a name="overview-of-access-control-iam"></a>Přehled řízení přístupu (IAM)

**Řízení přístupu (IAM)** je okno, které používáte ke správě přístupu k prostředkům Azure. Je také označován jako Správa identit a přístupu a zobrazí se v několika míst na portálu Azure portal. Následuje příklad přístup okna ovládacího prvku (IAM) předplatného.

![Okno pro kontrolu (IAM) přístup k předplatnému](./media/role-assignments-portal/access-control-numbers.png)

Následující tabulka popisuje, co některé prvky se používá pro:

| # | Element | Co se použije pro |
| --- | --- | --- |
| 1 | Prostředků, ve kterém je otevřen řízení přístupu (IAM) | Určení oboru (předplatné v tomto příkladu) |
| 2 | **Přidat** tlačítko | Přidání přiřazení role |
| 3 | **Zkontrolovat přístup** kartu | Zobrazit přiřazení rolí pro jednoho uživatele. |
| 4 | **Přiřazení rolí** kartu | Zobrazit přiřazení rolí v aktuálním oboru |
| 5 | **Role** kartu | Zobrazit všechny role a oprávnění |

Bude nejefektivnější se v okně řízení (IAM) přístupu a pomáhá v případě, že můžete odpovědět na následující tři otázky, pokud se pokoušíte spravovat přístup:

1. **Kdo potřebuje přístup?**

    Který odkazuje na uživatele, skupiny, instanční objekt nebo spravovaná identita. To se také nazývá *objektu zabezpečení*.

1. **Jaká oprávnění potřebují?**

    Oprávnění jsou seskupeny do rolí. Můžete vybrat ze seznamu několik předdefinovaných rolí.

1. **Pokud potřebují přístup?**

    Kde odkazuje na sadu prostředků, které se vztahuje na přístup. Kde lze skupiny pro správu, předplatné, skupinu prostředků nebo jeden prostředek, jako je například účet úložiště. Tento postup se nazývá *oboru*.

## <a name="open-access-control-iam"></a>Otevřete řízení přístupu (IAM)

První věc, kterou musíte se rozhodnout je tam, kde otevřete okno ovládacího prvku (IAM) přístup. To závisí na tom, jaké prostředky, které chcete spravovat přístup pro. Chcete spravovat přístup ke všemu, co je ve skupině pro správu, všechno v rámci předplatného, všechno, co je ve skupině prostředků nebo jediný prostředek?

1. Na webu Azure Portal, klikněte na tlačítko **všechny služby** a pak vyberte obor. Například můžete vybrat **skupin pro správu**, **předplatná**, **skupiny prostředků**, nebo prostředek.

1. Klikněte na tento konkrétní prostředek.

1. Klikněte na tlačítko **řízení přístupu (IAM)** .

    Následuje příklad přístup okna ovládacího prvku (IAM) předplatného. Pokud provedete změny řízení přístupu tady, by se vztahují na celé předplatné.

    ![Okno pro kontrolu (IAM) přístup k předplatnému](./media/role-assignments-portal/access-control-subscription.png)

## <a name="view-roles-and-permissions"></a>Zobrazení rolí a oprávnění

Definice role je kolekce oprávnění, kterou používáte pro přiřazení rolí. Azure má víc než 70 [předdefinované role pro prostředky Azure](built-in-roles.md). Postupujte podle těchto kroků k zobrazení dostupných rolí a oprávnění.

1. Otevřít **řízení přístupu (IAM)** v oboru.

1. Klikněte na tlačítko **role** kartu pro zobrazení seznamu všechny předdefinované a vlastní role.

   Zobrazí počet uživatelů a skupin, které jsou přiřazeny ke každé roli v aktuálním oboru.

   ![Seznam Role](./media/role-assignments-portal/roles-list.png)

1. Klikněte na jednotlivé role zobrazíte, kterému byla přiřazena tato role a také zobrazit oprávnění pro roli.

   ![Přiřazení rolí](./media/role-assignments-portal/role-assignments.png)

## <a name="view-role-assignments"></a>Zobrazení přiřazení rolí

Při správě přístupu, budete chtít vědět, kdo má přístup, jaké jsou jejich oprávnění a v jaké oboru. Pro přístup k seznamu pro uživatele, skupiny, instanční objekt nebo spravovanou identitu zobrazit svá přiřazení rolí.

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

1. Klikněte na tlačítko **přiřazení rolí** kartu zobrazíte všechna přiřazení rolí v tomto oboru.

   ![Řízení přístupu – karta přiřazení Role](./media/role-assignments-portal/access-control-role-assignments.png)

   Na kartě přiřazení Role se zobrazí, kdo má přístup na tento obor. Všimněte si, že některé role mají obor nastavený na **Tento prostředek**, zatímco jiné mají obor **(Zděděný)** z jiného oboru. Přístup přiřazený speciálně pro tento prostředek nebo zděděn od přiřazení do nadřazeného oboru.

## <a name="add-a-role-assignment"></a>Přidání přiřazení role

V RBAC udělit přístup, můžete přiřadit role pro uživatele, skupiny, instanční objekt nebo spravovaná identita. Postupujte podle těchto kroků k udělení přístupu v různých oborech.

### <a name="assign-a-role-at-a-scope"></a>Přiřazení role v oboru

1. Otevřít **řízení přístupu (IAM)** v oboru, jako je například skupiny pro správu, předplatné, skupinu prostředků nebo prostředek, ve které chcete udělit přístup.

1. Klikněte na tlačítko **přiřazení rolí** kartu zobrazíte všechna přiřazení rolí v tomto oboru.

1. Klikněte na tlačítko **přidat** > **přidat přiřazení role** otevřete podokno Přidat přiřazení role.

   Pokud nemáte oprávnění k přiřazování rolí, možnost Přidat přiřazení role se deaktivuje.

   ![Přidat nabídku](./media/role-assignments-portal/add-menu.png)

   ![Přidání podokna přiřazení role](./media/role-assignments-portal/add-role-assignment.png)

1. V rozevíracím seznamu **Role** vyberte roli, například **Přispěvatel virtuálních počítačů**.

1. V **vyberte** vyberte uživatele, skupiny, instanční objekt nebo spravovaná identita. Pokud se objekt zabezpečení v seznamu nezobrazí, pomocí pole **Vybrat** můžete v adresáři prohledat zobrazované názvy, e-mailové adresy a identifikátory objektů.

1. Klikněte na tlačítko **Uložit** přiřazení role.

   Po chvíli se objekt zabezpečení je přiřazena role na vybraný obor.

### <a name="assign-a-user-as-an-administrator-of-a-subscription"></a>Přiřadit uživatele jako správce předplatného

Chcete-li uživatel správcem předplatného Azure, přiřaďte jim [vlastníka](built-in-roles.md#owner) role v oboru předplatného. Role vlastníka poskytuje úplný přístup uživatelů ke všem prostředkům v rámci předplatného, včetně práva na delegovat přístup ostatním uživatelům. Tyto kroky jsou stejné jako ostatní přiřazení role.

1. Na webu Azure Portal, klikněte na tlačítko **všechny služby** a potom **předplatná**.

1. Klikněte na předplatné, ve které chcete udělit přístup.

1. Klikněte na tlačítko **řízení přístupu (IAM)** .

1. Klikněte na tlačítko **přiřazení rolí** kartu zobrazíte všechna přiřazení rolí pro toto předplatné.

1. Klikněte na tlačítko **přidat** > **přidat přiřazení role** otevřete podokno Přidat přiřazení role.

   Pokud nemáte oprávnění k přiřazování rolí, možnost Přidat přiřazení role se deaktivuje.

   ![Přidat nabídku](./media/role-assignments-portal/add-menu.png)

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
