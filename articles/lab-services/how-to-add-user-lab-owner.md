---
title: Postup přidání dalších vlastníků do testovacího prostředí v Azure Lab Services
description: V tomto článku se dozvíte, jak může správce přidat uživatele jako vlastníka testovacího prostředí v Azure Lab Services.
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
ms.openlocfilehash: 1438c9c4f33cca45b6c609e90cc2cbe7a762adb0
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/17/2020
ms.locfileid: "84897352"
---
# <a name="how-to-add-additional-owners-to-an-existing-lab-in-azure-lab-services"></a>Postup přidání dalších vlastníků do stávajícího testovacího prostředí v Azure Lab Services
V tomto článku se dozvíte, jak jste jako správce mohli přidat další vlastníky do stávajícího testovacího prostředí.

## <a name="add-user-to-the-reader-role-for-the-lab-account"></a>Přidat uživatele do role čtenář pro účet testovacího prostředí
Pokud chcete přidat uživatele jako dalšího vlastníka k existujícímu testovacímu prostředí, musíte nejdřív uživateli udělit oprávnění **ke čtení** z účtu testovacího prostředí.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. V nabídce vlevo vyberte **všechny služby** . Vyhledejte **služby testovacího prostředí**a vyberte ji.
3. V seznamu vyberte svůj **účet testovacího prostředí** . 
2. Na **stránce účet testovacího prostředí**vyberte v nabídce vlevo možnost **Access Control (IAM)** . 
2. Na stránce **řízení přístupu (IAM)** vyberte **Přidat** na panelu nástrojů a vyberte **Přidat přiřazení role**.

    ![Přiřazení role pro účet testovacího prostředí ](./media/how-to-add-user-lab-owner/lab-account-access-control-page.png)
3. Na stránce **Přidat přiřazení role** proveďte následující kroky: 
    1. Jako **roli**vyberte **Čtenář** . 
    2. Vyberte uživatele. 
    3. Vyberte **Uložit**. 

        ![Přidat uživatele do role čtenář pro účet testovacího prostředí ](./media/how-to-add-user-lab-owner/reader-lab-account.png)

## <a name="add-user-to-the-owner-role-for-the-lab"></a>Přidat uživatele k roli vlastníka testovacího prostředí

1. Zpátky na stránce **testovacího účtu** v nabídce vlevo vyberte **všechny laboratoře** .
2. Vyberte **testovací prostředí** , do kterého chcete přidat uživatele jako vlastníka. 
    
    ![Výběr testovacího prostředí ](./media/how-to-add-user-lab-owner/select-lab.png)    
3. Na stránce **testovací prostředí** v nabídce vlevo vyberte **řízení přístupu (IAM)** .
4. Na stránce **řízení přístupu (IAM)** vyberte **Přidat** na panelu nástrojů a vyberte **Přidat přiřazení role**.
5. Na stránce **Přidat přiřazení role** proveďte následující kroky: 
    1. Jako **roli**vyberte **Owner (vlastník** ). 
    2. Vyberte uživatele. 
    3. Vyberte **Uložit**. 

## <a name="next-steps"></a>Další kroky
Potvrďte, že uživatel uvidí testovací prostředí po přihlášení na [portál služby Lab Services](https://labs.azure.com).