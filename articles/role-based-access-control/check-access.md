---
title: Rychlý start – zobrazení přístupu uživatel má k prostředkům Azure | Dokumentace Microsoftu
description: Zjistěte, jak zobrazit přístup uživatele nebo jiné objekt zabezpečení má k prostředkům Azure pomocí řízení přístupu na základě role (RBAC) a webu Azure portal.
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
ms.openlocfilehash: f388215b2829066906ee7faf41abb17307bf3fff
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2019
ms.locfileid: "56337941"
---
# <a name="quickstart-view-the-access-a-user-has-to-azure-resources"></a>Rychlý start: Zobrazit přístup, který má uživatel k prostředkům Azure

Můžete použít **řízení přístupu (IAM)** okna [řízení přístupu na základě role (RBAC)](overview.md) zobrazíte přístup uživatele nebo jiného objektu zabezpečení má k prostředkům Azure. Nicméně v některých případech stačí rychle zobrazit přístup pro jednoho uživatele nebo jiného objektu zabezpečení. Nejjednodušší způsob je použít **zkontrolovat přístup** funkce na webu Azure Portal.

## <a name="view-role-assignments"></a>Zobrazit přiřazení rolí

 Způsob zobrazení přístup pro uživatele je výpis svá přiřazení rolí. Následujícím postupem zobrazíte přiřazení rolí pro jednoho uživatele, skupiny, instanční objekt nebo spravovaná identita v oboru předplatného.

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
> [Kurz: Udělení přístupu uživatelů k prostředkům Azure pomocí RBAC a webu Azure portal](quickstart-assign-role-user-portal.md)
