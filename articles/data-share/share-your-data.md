---
title: Kurz – sdílet data se zákazníky a partnery pomocí Azure Data sdílené složky ve verzi Preview
description: Kurz – sdílet data se zákazníky a partnery pomocí Azure Data sdílené složky ve verzi Preview
author: joannapea
ms.service: data-share
ms.topic: tutorial
ms.date: 07/10/2019
ms.author: joanpo
ms.openlocfilehash: ebd60607f9a24074e0fa985973dfc35674f0d66c
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2019
ms.locfileid: "67788224"
---
# <a name="tutorial-share-your-data-using-azure-data-share-preview"></a>Kurz: Sdílejte svá data pomocí Azure Data sdílené složky ve verzi Preview

V tomto kurzu se dozvíte, jak vytvořit novou sdílenou složku Azure dat a začněte sdílet vaše data se zákazníky a partnery mimo vaši organizaci Azure. 

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořte sdílenou složku Data.
> * Přidáte datové sady dat sdílené složky.
> * Povolte plán synchronizace pro svou sdílenou složku Data. 
> * Přidáte příjemce dat sdílené složky. 

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure: Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
* Účet služby Azure Storage: Pokud již nemáte, můžete vytvořit [účtu služby Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

## <a name="create-a-data-share-account"></a>Vytvoření účtu Data sdílené složky

Vytvořte prostředek sdílené složky Azure dat ve skupině prostředků Azure.

1. Vyberte tlačítko **Vytvořit prostředek** (+) v levém horním rohu portálu.

1. Vyhledejte *sdílení dat*.

1. Vyberte možnost sdílení dat (preview) a vyberte **vytvořit**.

1. Základní podrobnosti o prostředku sdílení dat Azure s použitím následujících informací vyplňte. 

     **Nastavení** | **Navrhovaná hodnota** | **Popis pole**
    |---|---|---|
    | Name | *datashareacount* | Zadejte název účtu data sdílené složky. |
    | Subscription | Vaše předplatné | Vyberte předplatné Azure, kterou chcete použít pro svůj účet data sdílené složky.|
    | Resource group | *test-resource-group* | Použijte existující skupinu prostředků nebo vytvořte novou skupinu prostředků. |
    | Location | *USA – východ 2* | Vyberte oblast pro účet data sdílené složky.
    | | |

1. Vyberte **vytvořit** ke zřízení účtu data sdílené složky. Zřizování nového účtu sdílení dat obvykle trvá asi 2 minuty nebo méně. 

1. Po dokončení nasazení vyberte **přejít k prostředku**.

## <a name="create-a-data-share"></a>Vytvořit sdílení dat

1. Přejděte na stránku přehled dat sdílené složky.

    ![Sdílet vaše data](./media/share-receive-data.png "sdílet vaše data") 

1. Vyberte **začněte sdílet vaše data**.

1. Vyberte **Vytvořit**.   

1. Vyplňte podrobnosti pro svou sdílenou složku Data. Zadejte název, popis sdílené složky obsahu a podmínky použití (volitelné). 

    ![EnterShareDetails](./media/enter-share-details.png "podrobností zadejte sdílené složky") 

1. Vyberte **pokračovat**

1. Chcete-li přidat datové sady dat sdílené složky, **datové sady přidat**. 

    ![Datové sady](./media/datasets.png "datové sady")

1. Vyberte typ datové sady, který chcete přidat. 

    ![AddDatasets](./media/add-datasets.png "přidat datové sady")    

1. Přejděte na objekt, který chcete sdílet a vyberte "Datové sady přidat". 

    ![SelectDatasets](./media/select-datasets.png "vyberte datové sady")    

1. Na kartě příjemci zadejte e-mailové adresy příjemce vašich dat tak, že vyberete "+ Přidat příjemce". 

    ![AddRecipients](./media/add-recipient.png "přidat příjemce") 

1. Vyberte **pokračovat**

1. Pokud chcete vaše příjemce dat mohli získat přírůstkové aktualizace dat, povolte plán snímku. 

    ![EnableSnapshots](./media/enable-snapshots.png "povolit snímky") 

1. Vyberte počáteční čas a opakování interval. 

1. Vyberte **pokračovat**

1. Revize + vytvořit kartu, zkontrolovat obsah balíčku, nastavení, příjemce a nastavení synchronizace. Vyberte **Vytvořit**.

Byla vytvořena s Azure Data ve sdílené složce a příjemce sdílení vašich dat je teď připravený k pozvánku přijmout. 

## <a name="next-steps"></a>Další kroky

V tomto kurzu učení vytvoření sdílené složky Azure dat a pozvat příjemce. Pokud chcete dozvědět, jak příjemce dat můžete přijmout a přijímat data sdílet, pokračujte [přijmout a přijímat data](subscribe-to-data-share.md) kurzu. 