---
title: Rychlý start – zobrazit role přiřazené uživateli pomocí webu Azure portal | Dokumentace Microsoftu
description: Zjistěte, jak zobrazit oprávnění řízení přístupu na základě role přiřazené uživateli, skupině, instanční objekt nebo spravovanou identitu pomocí webu Azure portal.
services: role-based-access-control
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: quickstart
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 11/30/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: b755dd6223c21084cafea82a1c8857f9f54b03b5
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2018
ms.locfileid: "52638651"
---
# <a name="quickstart-view-roles-assigned-to-a-user-using-the-azure-portal"></a>Rychlý start: Zobrazit role přiřazené uživateli pomocí webu Azure portal

Můžete použít **řízení přístupu (IAM)** okna [řízení přístupu na základě role (RBAC)](overview.md) zobrazit přiřazení rolí pro více uživatelů, skupiny a instanční objekty a spravované identity, ale někdy Stačí, když chcete rychle zobrazit přiřazení rolí pro jednoho uživatele, skupiny, instanční objekt nebo spravovaná identita. Nejjednodušší způsob je použít **zkontrolovat přístup** funkce na webu Azure Portal.

## <a name="view-role-assignments"></a>Zobrazení přiřazení rolí

Následujícím postupem zobrazíte přiřazení rolí pro jednoho uživatele, skupiny, instanční objekt nebo spravovaná identita v oboru předplatného.

1. Na webu Azure Portal, klikněte na tlačítko **všechny služby** a potom **předplatná**.

1. Kliknutím na své předplatné.

1. Klikněte na tlačítko **řízení přístupu (IAM)**.

1. Klikněte na tlačítko **zkontrolovat přístup** kartu.

    ![Řízení přístupu – karta Kontrola přístupu](./media/check-access/access-control-check-access.png)

1. V **najít** seznamu, vyberte typ, který chcete zkontrolovat přístup k objektu zabezpečení.

1. Do vyhledávacího pole zadejte adresář pro zobrazovaná jména, e-mailové adresy nebo identifikátorů objektů hledaný řetězec.

    ![Zkontrolovat přístup k seznamu příkazu select](./media/check-access/check-access-select.png)

1. Klikněte na objekt zabezpečení otevřít **přiřazení** podokně.

    ![Podokno přiřazení](./media/check-access/check-access-assignments.png)

    V tomto podokně se zobrazí role přiřazené k vybraný objekt zabezpečení a obor. Pokud jsou všechny zamítnout přiřazení v tomto oboru nebo zděděné do tohoto oboru, budou uvedené.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Kurz: Udělení přístupu pro uživatele pomocí RBAC a webu Azure portal](quickstart-assign-role-user-portal.md)
