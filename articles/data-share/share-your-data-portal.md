---
title: Sdílení mimo vaši organizaci (Azure Portal) – rychlý Start pro Azure Data Share
description: Naučte se sdílet data se zákazníky a partnery přes Azure Data Share v tomto rychlém startu.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: quickstart
ms.date: 08/19/2020
ms.openlocfilehash: 41598c04af78d4366435259357d8f897ac178942
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "89489931"
---
# <a name="quickstart-share-data-using-azure-data-share-in-the-azure-portal"></a>Rychlý Start: sdílení dat pomocí Azure Data Share v Azure Portal

V tomto rychlém startu se dozvíte, jak nastavit novou sdílenou složku Azure pomocí Azure Portal.

## <a name="prerequisites"></a>Předpoklady

Předplatné Azure: Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.


## <a name="create-a-data-share-account"></a>Vytvoření účtu pro sdílení dat

Vytvořte prostředek sdílené složky Azure ve skupině prostředků Azure.

1. Přihlaste se na web [Azure Portal](https://portal.azure.com/).

1. V levém horním rohu portálu vyberte tlačítko **vytvořit prostředek** (+).

1. Vyhledejte *sdílenou složku dat*.

1. Vyberte **sdílet data** a vyberte **vytvořit**.

1. Vyplňte základní podrobnosti prostředku Azure Data Share s následujícími informacemi. 

   **Nastavení** | **Navrhovaná hodnota** | **Popis pole**
   |---|---|---|
   | Předplatné | Vaše předplatné | Vyberte předplatné Azure, které chcete použít pro svůj účet pro sdílení dat.|
   | Skupina prostředků | *test-Resource-Group* | Použijte existující skupinu prostředků nebo vytvořte novou skupinu prostředků. |
   | Umístění | *USA – východ 2* | Vyberte oblast pro svůj účet pro sdílení dat.
   | Name | *datashareaccount* | Zadejte název vašeho účtu pro sdílení dat. |

1. Vyberte **zkontrolovat + vytvořit**a pak **vytvořte** a zřiďte svůj účet pro sdílení dat. Zřizování nového účtu pro sdílení dat obvykle trvá přibližně 2 minuty nebo méně.

1. Po dokončení nasazení vyberte **Přejít k prostředku**.

## <a name="create-a-share"></a>Vytvoření sdílené složky

1. Přejděte na stránku s přehledem sdílení dat.

   ![Sdílení dat](./media/share-receive-data.png "Sdílení dat") 

1. Vyberte možnost **začít sdílet data**.

1. Vyberte **Vytvořit**.

1. Vyplňte podrobnosti pro vaši sdílenou složku. Zadejte název, typ sdílení, popis obsahu sdílení a podmínek použití (volitelné). 

   ![EnterShareDetails](./media/enter-share-details.png "Zadat podrobnosti o sdílené složce") 

1. Vyberte **Pokračovat**.

1. Chcete-li do sdílené složky přidat datové sady, vyberte možnost **přidat datové sady**. 

   ![Přidání datových sad do sdílené složky](./media/datasets.png "Datové sady")

1. Vyberte typ datové sady, který chcete přidat. V závislosti na typu sdílené položky (snímku nebo na místě), který jste vybrali v předchozím kroku, se zobrazí jiný seznam typů datových sad. Pokud sdílíte z Azure SQL Database nebo Azure synapse Analytics, budete vyzváni k zadání některých přihlašovacích údajů SQL. Proveďte ověření pomocí uživatele, který jste vytvořili jako součást požadavků.

   ![AddDatasets](./media/add-datasets.png "Přidat datové sady")    

1. Přejděte k objektu, který chcete sdílet, a vyberte přidat datové sady. 

   ![SelectDatasets](./media/select-datasets.png "Vybrat datové sady")    

1. Na kartě příjemci zadejte do e-mailových adres vašeho příjemce dat a vyberte + Přidat příjemce.

   ![AddRecipients](./media/add-recipient.png "Přidání příjemců") 

1. Vyberte **Pokračovat**.

1. Pokud jste vybrali typ sdílené složky snímků, můžete nakonfigurovat plán snímků pro poskytování aktualizací vašich dat příjemci dat. 

   ![EnableSnapshots](./media/enable-snapshots.png "Povolit snímky") 

1. Vyberte čas spuštění a interval opakování. 

1. Vyberte **Pokračovat**.

1. Na kartě Revize + vytvořit zkontrolujte obsah balíčku, nastavení, příjemce a nastavení synchronizace. Vyberte **Vytvořit**.

Vaše sdílená složka Azure je teď vytvořená a příjemce vaší sdílené složky je teď připravený přijmout vaše pozvání.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už prostředek nepotřebujete, přejdete na stránku s **přehledem sdílení dat** a výběrem **Odstranit** ho odeberete.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak vytvořit sdílenou složku Azure Data. Další informace o tom, jak může příjemce dat přijmout a přijmout sdílená data, najdete v kurzu [přijímání a přijímání dat](subscribe-to-data-share.md) . 
