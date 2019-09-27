---
title: 'Kurz: Sdílení mimo vaši organizaci – Preview Azure Data Share Preview'
description: Kurz – sdílení dat se zákazníky a partnery pomocí Azure Data Share Preview
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: tutorial
ms.date: 07/10/2019
ms.openlocfilehash: f7df46a6a6f149ef0228fda8c967469a25dc3d50
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2019
ms.locfileid: "71327415"
---
# <a name="tutorial-share-your-data-using-azure-data-share-preview"></a>Kurz: Sdílení dat pomocí Azure Data Share Preview

V tomto kurzu se naučíte, jak nastavit novou sdílenou složku Azure a začít sdílet data se zákazníky a partnery mimo vaši organizaci Azure. 

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořte sdílenou složku dat.
> * Přidejte datové sady do sdílené složky dat.
> * Povolte pro sdílenou složku dat plán synchronizace. 
> * Přidejte příjemce do sdílené složky dat. 

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure: Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
* Účet Azure Storage: Pokud ho ještě nemáte, můžete vytvořit [účet Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* Oprávnění k přidání přiřazení role k účtu úložiště, který je k dispozici ve *službě Microsoft. Authorization/přiřazení role/* oprávnění k zápisu. Toto oprávnění existuje v roli vlastníka. 
* Vaši příjemci přihlašovací e-mailovou adresu Azure (pomocí e-mailového aliasu nebudou fungovat).

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

## <a name="create-a-data-share-account"></a>Vytvoření účtu pro sdílení dat

Vytvořte prostředek sdílené složky Azure ve skupině prostředků Azure.

1. Vyberte tlačítko **Vytvořit prostředek** (+) v levém horním rohu portálu.

1. Vyhledejte *sdílenou složku dat*.

1. Vyberte sdílet data (Preview) a vyberte **vytvořit**.

1. Vyplňte základní podrobnosti prostředku Azure Data Share s následujícími informacemi. 

     **Nastavení** | **Navrhovaná hodnota** | **Popis pole**
    |---|---|---|
    | Name | *datashareacount* | Zadejte název vašeho účtu pro sdílení dat. |
    | Subscription | Vaše předplatné | Vyberte předplatné Azure, které chcete použít pro svůj účet pro sdílení dat.|
    | Resource group | *test-resource-group* | Použijte existující skupinu prostředků nebo vytvořte novou skupinu prostředků. |
    | Location | *Východní USA 2* | Vyberte oblast pro svůj účet pro sdílení dat.
    | | |

1. Vyberte **vytvořit** a zřiďte svůj účet pro sdílení dat. Zřizování nového účtu pro sdílení dat obvykle trvá přibližně 2 minuty nebo méně. 

1. Po dokončení nasazení vyberte **Přejít k prostředku**.

## <a name="create-a-data-share"></a>Vytvoření sdílené složky dat

1. Přejděte na stránku s přehledem sdílení dat.

    ![Sdílení vašich dat](./media/share-receive-data.png "a sdílení dat") 

1. Vyberte možnost **začít sdílet data**.

1. Vyberte **Vytvořit**.   

1. Vyplňte podrobnosti pro vaši sdílenou složku dat. Zadejte název, popis obsahu sdílení a podmínek použití (volitelné). 

    ![EnterShareDetails](./media/enter-share-details.png "zadat podrobnosti o sdílené složce") 

1. Vybrat **pokračovat**

1. Pokud chcete do sdílené složky dat přidat datové sady, vyberte **přidat datové sady**. 

    Datové sady ![datových sad](./media/datasets.png "")

1. Vyberte typ datové sady, který chcete přidat. 

    ![AddDatasets](./media/add-datasets.png "přidat datové sady")    

1. Přejděte k objektu, který chcete sdílet, a vyberte přidat datové sady. 

    ![SelectDatasets](./media/select-datasets.png "Vybrat datové sady")    

1. Na kartě příjemci zadejte do e-mailových adres vašeho příjemce dat a vyberte + Přidat příjemce. 

    ![](./media/add-recipient.png "Přidat příjemce") AddRecipients 

1. Vybrat **pokračovat**

1. Pokud byste chtěli, aby váš příjemce dat mohl získat přírůstkové aktualizace vašich dat, povolte plán snímků. 

    ![EnableSnapshots](./media/enable-snapshots.png "Povolit snímky") 

1. Vyberte čas spuštění a interval opakování. 

1. Vybrat **pokračovat**

1. Na kartě Revize + vytvořit zkontrolujte obsah balíčku, nastavení, příjemce a nastavení synchronizace. Vyberte **Vytvořit**.

Vaše sdílená složka Azure je teď vytvořená a příjemce vaší sdílené složky je teď připravený přijmout vaše pozvání. 

## <a name="next-steps"></a>Další kroky

V tomto kurzu se naučíte, jak vytvořit sdílenou složku Azure a pozvat příjemce. Další informace o tom, jak může příjemce dat přijmout a přijmout sdílená data, najdete v kurzu [přijímání a přijímání dat](subscribe-to-data-share.md) . 
