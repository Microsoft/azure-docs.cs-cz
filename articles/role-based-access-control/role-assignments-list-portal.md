---
title: Seznam přiřazení rolí pomocí Azure RBAC a portálu Azure
description: Zjistěte, k jakým prostředkům mají uživatelé, skupiny, instanční objekty nebo spravované identity přístup pomocí řízení přístupu azure založeného na rolích (RBAC) a portálu Azure.
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
ms.date: 03/18/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 03a3d3c7d572d7ec5b8d3ac3d527d0d59e649bc2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062245"
---
# <a name="list-role-assignments-using-azure-rbac-and-the-azure-portal"></a>Seznam přiřazení rolí pomocí Azure RBAC a portálu Azure

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)]Tento článek popisuje, jak seznam přiřazení rolí pomocí portálu Azure.

> [!NOTE]
> Pokud vaše organizace zadala funkce správy poskytovateli služeb, který používá [azure delegovanou správu prostředků](../lighthouse/concepts/azure-delegated-resource-management.md), přiřazení rolí autorizovaná tímto poskytovatelem služeb se zde nezobrazí.

## <a name="list-role-assignments-for-a-user-or-group"></a>Seznam přiřazení rolí pro uživatele nebo skupinu

Nejjednodušší způsob, jak zobrazit role přiřazené uživateli nebo skupině v předplatném, je použít podokno **prostředků Azure.**

1. Na webu Azure Portal klikněte na **Všechny služby** a vyberte **Uživatelé** nebo **skupiny**.

1. Klikněte na uživatele nebo skupinu, pro kterou chcete uvést přiřazení rolí.

1. Klikněte na **prostředky Azure**.

    Zobrazí se seznam rolí přiřazených vybranému uživateli nebo skupině v různých oborech, jako je skupina pro správu, předplatné, skupina prostředků nebo prostředek. Tento seznam obsahuje všechna přiřazení rolí, ke které máte oprávnění ke čtení.

    ![Přiřazení rolí pro uživatele](./media/role-assignments-list-portal/azure-resources-user.png)    

1. Pokud chcete předplatné změnit, klikněte na seznam **Odběry.**

## <a name="list-owners-of-a-subscription"></a>Seznam vlastníků předplatného

Uživatelé, kterým byla přiřazena role [vlastníka](built-in-roles.md#owner) pro předplatné, můžou spravovat vše v předplatném. Chcete-li vypsat vlastníky předplatného, postupujte takto.

1. Na webu Azure Portal klikněte na **Všechny služby** a potom na **Předplatná**.

1. Klikněte na předplatné, jehož seznam chcete uvést.

1. Klikněte na **Řízení přístupu (IAM)**.

1. Kliknutím na kartu **Přiřazení rolí** zobrazte všechna přiřazení rolí pro toto předplatné.

1. Přejděte do části **Vlastníci** a podívejte se na všechny uživatele, kterým byla pro toto předplatné přiřazena role Vlastník.

   ![Řízení přístupu k předplatnému – karta Přiřazení rolí](./media/role-assignments-list-portal/access-control-role-assignments-subscription.png)

## <a name="list-role-assignments-at-a-scope"></a>Seznam přiřazení rolí v oboru

1. Na webu Azure Portal klikněte na **Všechny služby** a vyberte obor. Můžete například vybrat **skupiny pro správu**, **Odběry**, **Skupiny prostředků**nebo prostředek.

1. Klikněte na konkrétní zdroj.

1. Klikněte na **Řízení přístupu (IAM)**.

1. Kliknutím na kartu **Přiřazení rolí** zobrazíte všechna přiřazení rolí v tomto oboru.

   ![Řízení přístupu – karta Přiřazení rolí](./media/role-assignments-list-portal/access-control-role-assignments.png)

   Na kartě Přiřazení rolí můžete vidět, kdo má přístup k tomuto oboru. Všimněte si, že některé role mají obor nastavený na **Tento prostředek**, zatímco jiné mají obor **(Zděděný)** z jiného oboru. Aplikace Access je přiřazena konkrétně k tomuto prostředku nebo zděděna z přiřazení k nadřazenému oboru.

## <a name="list-role-assignments-for-a-user-at-a-scope"></a>Seznam přiřazení rolí pro uživatele v oboru

Chcete-li vypsat přístup pro uživatele, skupinu, instanční objekt nebo spravovanou identitu, uveďte jejich přiřazení rolí. Následujícím postupem zobrazíte seznam přiřazení rolí pro jednoho uživatele, skupinu, instanční objekt nebo spravovanou identitu v určitém oboru.

1. Na webu Azure Portal klikněte na **Všechny služby** a vyberte obor. Můžete například vybrat **skupiny pro správu**, **Odběry**, **Skupiny prostředků**nebo prostředek.

1. Klikněte na konkrétní zdroj.

1. Klikněte na **Řízení přístupu (IAM)**.

1. Klikněte na kartu **Zkontrolovat přístup.**

    ![Řízení přístupu – kontrola karty přístupu](./media/role-assignments-list-portal/access-control-check-access.png)

1. V seznamu **Najít** vyberte typ zaregistrovaný objekt zabezpečení, pro který chcete zkontrolovat přístup.

1. Do vyhledávacího pole zadejte řetězec pro vyhledávání zobrazované názvy, e-mailové adresy nebo identifikátory objektů v adresáři.

    ![Zkontrolovat výběrový seznam přístupu](./media/role-assignments-list-portal/check-access-select.png)

1. Kliknutím na objekt zabezpečení otevřete podokno **přiřazení.**

    ![podokno přiřazení](./media/role-assignments-list-portal/check-access-assignments.png)

    V tomto podokně se zobrazí role přiřazené vybranému objektu zabezpečení a oboru. Pokud existují všechna odepření přiřazení v tomto oboru nebo zděděné do tohoto oboru, budou uvedeny.

## <a name="list-role-assignments-for-a-system-assigned-managed-identity"></a>Seznam přiřazení rolí pro spravovanou identitu přiřazenou systémem

1. Na webu Azure Portal otevřete spravovanou identitu přiřazenou systémem.

1. V levé nabídce klepněte na **položku Identita**.

    ![Spravovaná identita přiřazená systémem](./media/role-assignments-list-portal/identity-system-assigned.png)

1. V **části Přiřazení rolí**klikněte na Zobrazit role Azure **RBAC přiřazené k této spravované identitě**.

    Zobrazí se seznam rolí přiřazených k vybrané spravované identitě přiřazené k systému v různých oborech, jako je skupina pro správu, předplatné, skupina prostředků nebo prostředek. Tento seznam obsahuje všechna přiřazení rolí, ke které máte oprávnění ke čtení.

    ![Přiřazení rolí pro spravovanou identitu přiřazenou systémem](./media/role-assignments-list-portal/azure-resources-system-assigned.png)

## <a name="list-role-assignments-for-a-user-assigned-managed-identity"></a>Seznam přiřazení rolí pro spravovanou identitu přiřazenou uživateli

1. Na webu Azure Portal otevřete spravovanou identitu přiřazenou uživatelem.

1. Klikněte na **prostředky Azure**.

    Zobrazí se seznam rolí přiřazených k vybrané spravované identitě přiřazené uživateli v různých oborech, jako je skupina pro správu, předplatné, skupina prostředků nebo prostředek. Tento seznam obsahuje všechna přiřazení rolí, ke které máte oprávnění ke čtení.

    ![Přiřazení rolí pro spravovanou identitu přiřazenou systémem](./media/role-assignments-list-portal/azure-resources-user-assigned.png)

1. Pokud chcete předplatné změnit, klikněte na seznam **Odběry.**

## <a name="list-number-of-role-assignments"></a>Seznam přiřazení rolí

V každém předplatném můžete mít až **2000** přiřazení rolí. Karta **Přiřazení rolí** obsahuje graf, který uvádí počet přiřazení rolí pro aktuální předplatné, abyste měli přehled o tomto limitu.

![Řízení přístupu – graf počtu přiřazení rolí](./media/role-assignments-list-portal/access-control-role-assignments-chart.png)

Pokud se blížíte maximálnímu počtu a pokusíte se přidat další přiřazení rolí, zobrazí se v podokně **Přidat přiřazení role** upozornění. Způsoby, jak můžete snížit počet přiřazení rolí, naleznete [v tématu Poradce při potížích Azure RBAC](troubleshooting.md#azure-role-assignments-limit).

![Řízení přístupu – upozornění na přidání přiřazení role](./media/role-assignments-list-portal/add-role-assignment-warning.png)

## <a name="next-steps"></a>Další kroky

- [Přidání nebo odebrání přiřazení rolí pomocí Azure RBAC a portálu Azure](role-assignments-portal.md)
- [Poradce při potížích s RBAC pro prostředky Azure](troubleshooting.md)
