---
title: Jak přidat další vlastníky do testovacího prostředí ve službě Azure Lab Services
description: Tento článek ukazuje, jak správce můžete přidat uživatele jako vlastníka do testovacího prostředí ve službě Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2020
ms.author: spelluru
ms.openlocfilehash: 9302cadf0b900c9faac382bad86b51cea7d828fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77443512"
---
# <a name="how-to-add-additional-owners-to-an-existing-lab-in-azure-lab-services"></a>Jak přidat další vlastníky do existujícího testovacího prostředí ve službě Azure Lab Services
Tento článek ukazuje, jak můžete jako správce přidat další vlastníky do existujícího testovacího prostředí.

## <a name="add-user-to-the-reader-role-for-the-lab-account"></a>Přidání uživatele do role čtečky pro účet testovacího prostředí
Chcete-li přidat uživatele jako dalšího vlastníka do existujícího testovacího prostředí, musíte nejprve **udělit** uživateli oprávnění ke čtení v účtu testovacího prostředí.

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. V levé nabídce vyberte **Všechny služby.** Vyhledejte **služby Lab services**a vyberte ji.
3. Vseznamu **vyberte svůj laboratorní účet** ze seznamu. 
2. Na **stránce Účet testovacího prostředí**vyberte v levé nabídce **položku Řízení přístupu (IAM).** 
2. Na stránce **Access control (IAM)** vyberte **Přidat** na panelu nástrojů a vyberte Přidat **přiřazení role**.

    ![Přiřazení role pro účet testovacího prostředí ](../media/how-to-add-user-lab-owner/lab-account-access-control-page.png)
3. Na stránce **Přidat přiřazení role** postupujte takto: 
    1. Vyberte **čtenář** pro **roli**. 
    2. Vyberte uživatele. 
    3. Vyberte **Uložit**. 

        ![Přidání uživatele do role čtečky pro účet testovacího prostředí ](../media/how-to-add-user-lab-owner/reader-lab-account.png)

## <a name="add-user-to-the-owner-role-for-the-lab"></a>Přidání uživatele do role vlastníka testovacího prostředí

1. Zpět na stránce **Účet testovacího prostředí** vyberte v levé nabídce **všechny testovací prostředí.**
2. Vyberte **testovací prostředí,** do kterého chcete přidat uživatele jako vlastníka. 
    
    ![Výběr testovacího prostředí ](../media/how-to-add-user-lab-owner/select-lab.png)    
3. Na stránce **Lab** vyberte v levé nabídce **ovládací prvek Přístup (IAM).**
4. Na stránce **Access control (IAM)** vyberte **Přidat** na panelu nástrojů a vyberte Přidat **přiřazení role**.
5. Na stránce **Přidat přiřazení role** postupujte takto: 
    1. Vyberte **vlastník** a **role**. 
    2. Vyberte uživatele. 
    3. Vyberte **Uložit**. 

## <a name="next-steps"></a>Další kroky
Potvrďte, že uživatel vidí testovací prostředí po přihlášení na [portál služby Lab](https://labs.azure.com).