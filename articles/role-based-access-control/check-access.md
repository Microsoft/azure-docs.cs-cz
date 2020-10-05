---
title: Rychlý Start – zobrazení přístupu uživatele k prostředkům Azure – Azure RBAC
description: V tomto rychlém startu se dozvíte, jak zobrazit přístup k prostředkům uživatele nebo jiného objektu zabezpečení pomocí Azure Portal a řízení přístupu na základě role v Azure (Azure RBAC).
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
ms.openlocfilehash: 9be53aa964e75bab0b90495640537fe927a5af0e
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "82734157"
---
# <a name="quickstart-view-the-access-a-user-has-to-azure-resources"></a>Rychlý Start: zobrazení přístupu uživatele k prostředkům Azure

V okně řízení přístupu **(IAM)** můžete v [řízení přístupu na základě role v Azure (Azure RBAC)](overview.md) zobrazit přístup k prostředkům, které má uživatel nebo jiný objekt zabezpečení, a získat tak přístup k prostředkům Azure. Někdy ale stačí pouze rychle zobrazit přístup pro jednoho uživatele nebo jiný objekt zabezpečení. Nejjednodušší způsob, jak to provést, je použití funkce **check Access** v Azure Portal.

## <a name="view-role-assignments"></a>Zobrazit přiřazení rolí

 Způsob, jakým si zobrazíte přístup pro uživatele, je seznam přiřazení rolí. Pomocí těchto kroků můžete zobrazit přiřazení rolí pro jednoho uživatele, skupinu, instanční objekt nebo spravovanou identitu v oboru předplatného.

1. V Azure Portal klikněte na **všechny služby** a pak na **předplatná**.

1. Klikněte na své předplatné.

1. Klikněte na **Řízení přístupu (IAM)** .

1. Klikněte na kartu **kontrolovat přístup** .

    ![Řízení přístupu – karta přístup pro kontrolu](./media/check-access/access-control-check-access.png)

1. V seznamu **Najít** vyberte typ objektu zabezpečení, pro který chcete ověřit přístup.

1. Do vyhledávacího pole zadejte řetězec, ve kterém budou v adresáři hledána zobrazovaná jména, e-mailové adresy nebo identifikátory objektů.

    ![Zaškrtněte seznam pro výběr přístupu.](./media/check-access/check-access-select.png)

1. Kliknutím na objekt zabezpečení otevřete podokno **přiřazení** .

    ![podokno přiřazení](./media/check-access/check-access-assignments.png)

    V tomto podokně můžete zobrazit role přiřazené k vybranému objektu zabezpečení a oboru. Pokud jsou v tomto oboru k dispozici nějaká přiřazení odepřít nebo zděděná do tohoto oboru, budou uvedena.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz: udělení přístupu uživatele k prostředkům Azure pomocí Azure Portal](quickstart-assign-role-user-portal.md)
