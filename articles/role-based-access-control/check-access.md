---
title: Úvodní příručka – zobrazení přístupu, který má uživatel k prostředkům Azure
description: V tomto rychlém startu se dozvíte, jak zobrazit přístup uživatele nebo jiného objektu zabezpečení k prostředkům Azure pomocí řízení přístupu na základě rolí (RBAC) a portálu Azure.
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
ms.openlocfilehash: b23c10fc2a551b8044b208911dbc048968b06564
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "74419620"
---
# <a name="quickstart-view-the-access-a-user-has-to-azure-resources"></a>Úvodní příručka: Zobrazení přístupu, který má uživatel k prostředkům Azure

Okno řízení **přístupu (IAM)** v [řízení přístupu na základě rolí (RBAC)](overview.md) můžete použít k zobrazení přístupu uživatele nebo jiného objektu zabezpečení má prostředky Azure. Někdy však stačí rychle zobrazit přístup pro jednoho uživatele nebo jiný objekt zabezpečení. Nejjednodušší způsob, jak to udělat, je použít funkci **Zkontrolovat přístup** na webu Azure Portal.

## <a name="view-role-assignments"></a>Zobrazit přiřazení rolí

 Způsob zobrazení přístupu pro uživatele je seznam jejich rolí přiřazení. Podle těchto kroků zobrazte přiřazení rolí pro jednoho uživatele, skupinu, instanční objekt nebo spravovanou identitu v oboru předplatného.

1. Na webu Azure Portal klikněte na **Všechny služby** a potom na **Předplatná**.

1. Klikněte na předplatné.

1. Klikněte na **Řízení přístupu (IAM)**.

1. Klikněte na kartu **Zkontrolovat přístup.**

    ![Řízení přístupu – kontrola karty přístupu](./media/check-access/access-control-check-access.png)

1. V seznamu **Najít** vyberte typ zaregistrovaný objekt zabezpečení, pro který chcete zkontrolovat přístup.

1. Do vyhledávacího pole zadejte řetězec pro vyhledávání zobrazované názvy, e-mailové adresy nebo identifikátory objektů v adresáři.

    ![Zkontrolovat výběrový seznam přístupu](./media/check-access/check-access-select.png)

1. Kliknutím na objekt zabezpečení otevřete podokno **přiřazení.**

    ![podokno přiřazení](./media/check-access/check-access-assignments.png)

    V tomto podokně se zobrazí role přiřazené vybranému objektu zabezpečení a oboru. Pokud existují všechna odepření přiřazení v tomto oboru nebo zděděné do tohoto oboru, budou uvedeny.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz: Udělení přístupu uživateli k prostředkům Azure pomocí RBAC a portálu Azure](quickstart-assign-role-user-portal.md)
