---
title: Seznam přiřazení rolí pomocí Azure RBAC a Azure Portal
description: Naučte se, jak určit, které prostředky uživatelé, skupiny, instanční objekty nebo spravované identity mají přístup k používání řízení přístupu na základě role (RBAC) Azure a Azure Portal.
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
ms.date: 01/23/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 099cf74dd27f39a4289397d5178511125d9ebf6f
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720698"
---
# <a name="list-role-assignments-using-azure-rbac-and-the-azure-portal"></a>Seznam přiřazení rolí pomocí Azure RBAC a Azure Portal

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)] tomto článku se dozvíte, jak zobrazit seznam přiřazení rolí pomocí Azure Portal.

> [!NOTE]
> Pokud má vaše organizace samoobslužné funkce správy pro poskytovatele služeb, který používá [správu delegovaných prostředků Azure](../lighthouse/concepts/azure-delegated-resource-management.md), tady se nezobrazí přiřazení rolí autorizovaných tímto poskytovatelem služeb.

## <a name="list-role-assignments-for-a-user-or-group"></a>Seznam přiřazení rolí pro uživatele nebo skupinu

Nejjednodušší způsob, jak zobrazit role přiřazené uživateli nebo skupině v rámci předplatného, je použít podokno **prostředky Azure** .

1. V Azure Portal klikněte na **všechny služby** a pak vyberte **Uživatelé** nebo **skupiny**.

1. Klikněte na uživatele nebo skupinu, pro kterou chcete zobrazit seznam přiřazení rolí.

1. Klikněte na **prostředky Azure**.

    Zobrazí se seznam rolí přiřazených vybranému uživateli nebo skupině v různých oborech, jako je skupina pro správu, předplatné, skupina prostředků nebo prostředek. Tento seznam obsahuje všechna přiřazení rolí, ke kterým máte oprávnění číst.

    ![Přiřazení rolí pro uživatele](./media/role-assignments-list-portal/azure-resources-user.png)    

1. Chcete-li změnit předplatné, klikněte na seznam **předplatných** .

## <a name="list-owners-of-a-subscription"></a>Seznam vlastníků předplatného

Uživatelé, kterým byla přiřazena role [vlastníka](built-in-roles.md#owner) pro předplatné, můžou spravovat všechno v rámci předplatného. Pomocí těchto kroků můžete zobrazit seznam vlastníků předplatného.

1. V Azure Portal klikněte na **všechny služby** a pak na **předplatná**.

1. Klikněte na předplatné, pro které chcete zobrazit seznam vlastníků.

1. Klikněte na **Řízení přístupu (IAM)** .

1. Kliknutím na kartu **Přiřazení rolí** zobrazte všechna přiřazení rolí pro toto předplatné.

1. Přejděte do části **vlastníci** a zobrazte všechny uživatele, kterým byla přiřazena role vlastníka pro toto předplatné.

   ![Řízení přístupu k předplatnému – karta přiřazení rolí](./media/role-assignments-list-portal/access-control-role-assignments-subscription.png)

## <a name="list-role-assignments-at-a-scope"></a>Seznam přiřazení rolí v oboru

1. V Azure Portal klikněte na **všechny služby** a pak vyberte obor. Můžete například vybrat **skupiny pro správu**, **předplatná**, **skupiny prostředků**nebo prostředek.

1. Klikněte na konkrétní prostředek.

1. Klikněte na **Řízení přístupu (IAM)** .

1. Kliknutím na kartu **přiřazení rolí** zobrazíte všechna přiřazení rolí v tomto oboru.

   ![Řízení přístupu – karta přiřazení rolí](./media/role-assignments-list-portal/access-control-role-assignments.png)

   Na kartě přiřazení rolí vidíte, kdo má v tomto oboru přístup. Všimněte si, že některé role mají obor nastavený na **Tento prostředek**, zatímco jiné mají obor **(Zděděný)** z jiného oboru. Přístup je buď přiřazen konkrétně tomuto prostředku, nebo zděděn z přiřazení do nadřazeného oboru.

## <a name="list-role-assignments-for-a-user-at-a-scope"></a>Seznam přiřazení rolí pro uživatele v oboru

Pokud chcete vypsat přístup pro uživatele, skupinu, instanční objekt nebo spravovanou identitu, uveďte jejich přiřazení rolí. Pomocí těchto kroků můžete zobrazit seznam přiřazení rolí pro jednoho uživatele, skupinu, instanční objekt nebo spravovanou identitu v konkrétním oboru.

1. V Azure Portal klikněte na **všechny služby** a pak vyberte obor. Můžete například vybrat **skupiny pro správu**, **předplatná**, **skupiny prostředků**nebo prostředek.

1. Klikněte na konkrétní prostředek.

1. Klikněte na **Řízení přístupu (IAM)** .

1. Klikněte na kartu **kontrolovat přístup** .

    ![Řízení přístupu – karta přístup pro kontrolu](./media/role-assignments-list-portal/access-control-check-access.png)

1. V seznamu **Najít** vyberte typ objektu zabezpečení, pro který chcete ověřit přístup.

1. Do vyhledávacího pole zadejte řetězec, ve kterém budou v adresáři hledána zobrazovaná jména, e-mailové adresy nebo identifikátory objektů.

    ![Zaškrtněte seznam pro výběr přístupu.](./media/role-assignments-list-portal/check-access-select.png)

1. Kliknutím na objekt zabezpečení otevřete podokno **přiřazení** .

    ![podokno přiřazení](./media/role-assignments-list-portal/check-access-assignments.png)

    V tomto podokně můžete zobrazit role přiřazené k vybranému objektu zabezpečení a oboru. Pokud jsou v tomto oboru k dispozici nějaká přiřazení odepřít nebo zděděná do tohoto oboru, budou uvedena.

## <a name="list-role-assignments-for-a-system-assigned-managed-identity"></a>Seznam přiřazení rolí pro spravovanou identitu přiřazenou systémem

1. V Azure Portal otevřete spravovanou identitu přiřazenou systémem.

1. V nabídce vlevo klikněte na možnost **Identita**.

    ![Spravovaná identita přiřazená systémem](./media/role-assignments-list-portal/identity-system-assigned.png)

1. V části **přiřazení rolí**klikněte na **Zobrazit role Azure RBAC přiřazené této spravované identitě**.

    Zobrazí se seznam rolí přiřazených k vybrané spravované identitě přiřazené systémem v různých oborech, jako je skupina pro správu, předplatné, skupina prostředků nebo prostředek. Tento seznam obsahuje všechna přiřazení rolí, ke kterým máte oprávnění číst.

    ![Přiřazení rolí pro spravovanou identitu přiřazenou systémem](./media/role-assignments-list-portal/azure-resources-system-assigned.png)

## <a name="list-role-assignments-for-a-user-assigned-managed-identity"></a>Seznam přiřazení rolí pro uživatelem přiřazenou spravovanou identitu

1. V Azure Portal otevřete spravovanou identitu přiřazenou systémem.

1. Klikněte na **prostředky Azure**.

    Zobrazí se seznam rolí přiřazených k vybrané spravované identitě přiřazené uživatelem v různých oborech, jako je skupina pro správu, předplatné, skupina prostředků nebo prostředek. Tento seznam obsahuje všechna přiřazení rolí, ke kterým máte oprávnění číst.

    ![Přiřazení rolí pro spravovanou identitu přiřazenou systémem](./media/role-assignments-list-portal/azure-resources-user-assigned.png)

1. Chcete-li změnit předplatné, klikněte na seznam **předplatných** .

## <a name="list-number-of-role-assignments"></a>Výpis počtu přiřazení rolí

V každém předplatném můžete mít až **2000** přiřazení rolí. Aby vám pomohly sledovat tato omezení, karta **přiřazení rolí** obsahuje graf, který uvádí aktuální počet přiřazení rolí.

![Řízení přístupu – počet grafů přiřazení rolí](./media/role-assignments-list-portal/access-control-role-assignments-chart.png)

Pokud se blížíte k maximálnímu počtu a pokusíte se přidat další přiřazení role, zobrazí se upozornění v podokně **Přidat přiřazení role** . Počet přiřazení rolí můžete snížit odstraněním přiřazení rolí, které už nepotřebujete, nebo přiřazením rolí k skupinám místo jednotlivých uživatelů.

![Řízení přístupu – upozornění přidání přiřazení role](./media/role-assignments-list-portal/add-role-assignment-warning.png)

## <a name="next-steps"></a>Další kroky

- [Přidání nebo odebrání přiřazení rolí pomocí Azure RBAC a Azure Portal](role-assignments-portal.md)
- [Řešení potíží s RBAC pro prostředky Azure](troubleshooting.md)
