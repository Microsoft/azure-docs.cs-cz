---
title: Správa účtu úložiště GPU pro Azure Stack Edge pro Microsoft Docs
description: Popisuje, jak použít Azure Portal ke správě účtu úložiště na Azure Stack Edge pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: d3378b08ace73b2bc9a10286abf5a48f3063ba7f
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91743501"
---
# <a name="use-the-azure-portal-to-manage-edge-storage-accounts-on-your-azure-stack-edge-pro"></a>Použití Azure Portal ke správě účtů hraničního úložiště na Azure Stack Edge pro

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

Tento článek popisuje, jak spravovat hraniční účty úložiště na Azure Stack Edge pro. Azure Stack Edge pro můžete spravovat přes Azure Portal nebo prostřednictvím místního webového uživatelského rozhraní. K přidání nebo odstranění hraničních účtů úložiště v zařízení použijte Azure Portal.

## <a name="about-edge-storage-accounts"></a>Informace o účtech úložiště Edge

Data ze zařízení Azure Stack Edge pro můžete přenést pomocí protokolů SMB, NFS nebo REST. Pokud chcete přenášet data do úložiště objektů BLOB pomocí rozhraní REST API, musíte vytvořit hraniční účty úložiště na Azure Stack Edge pro. 

Hraniční účty úložiště, které přidáte na zařízení Azure Stack Edge pro, jsou namapovány na účty Azure Storage. Všechna data zapsaná do hraničních účtů úložiště se automaticky odešlou do cloudu.

Diagram podrobně popisuje dva typy účtů a informace o tom, jak se zobrazují data z jednotlivých účtů do Azure:

![Diagram pro účty BLOB Storage](media/azure-stack-edge-j-series-manage-storage-accounts/ase-blob-storage.svg)

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Přidat hraniční účet úložiště
> * Odstranění hraničního účtu úložiště


## <a name="add-an-edge-storage-account"></a>Přidat hraniční účet úložiště

Chcete-li vytvořit hraniční účet úložiště, proveďte následující postup:

[!INCLUDE [Add an Edge storage account](../../includes/azure-stack-edge-gateway-add-storage-account.md)]

## <a name="delete-an-edge-storage-account"></a>Odstranění hraničního účtu úložiště

K odstranění hraničního účtu úložiště proveďte následující kroky.

1. V prostředku přejdete na **konfigurační > účty úložiště** . V seznamu účtů úložiště vyberte účet úložiště, který chcete odstranit. V horním panelu příkazů vyberte **Odstranit účet úložiště**.

    ![Přejít na seznam účtů úložiště](media/azure-stack-edge-j-series-manage-storage-accounts/delete-edge-storage-account-1.png)

2. V okně **Odstranit účet úložiště** potvrďte účet úložiště, který se má odstranit, a vyberte **Odstranit**.

    ![Potvrdit a odstranit účet úložiště](media/azure-stack-edge-j-series-manage-storage-accounts/delete-edge-storage-account-2.png)

Seznam účtů úložiště se aktualizuje tak, aby odrážel odstranění.


## <a name="add-delete-a-container"></a>Přidání, odstranění kontejneru

Můžete také přidat nebo odstranit kontejnery pro tyto účty úložiště.

Chcete-li přidat kontejner, proveďte následující kroky:

1. Vyberte účet úložiště, který chcete spravovat. V horním panelu příkazů vyberte **+ Přidat kontejner**.

    ![Vyberte účet úložiště pro přidání kontejneru.](media/azure-stack-edge-j-series-manage-storage-accounts/add-container-1.png)

2. Zadejte název svého kontejneru. Tento kontejner se vytvoří v účtu hraničního úložiště a také účet úložiště Azure namapovaný na tento účet. 

    ![Přidat kontejner Edge](media/azure-stack-edge-j-series-manage-storage-accounts/add-container-2.png)

Seznam kontejnerů se aktualizuje tak, aby odrážel nově přidaný kontejner.

![Aktualizovaný seznam kontejnerů](media/azure-stack-edge-j-series-manage-storage-accounts/add-container-4.png)

Nyní můžete vybrat kontejner z tohoto seznamu a vybrat **+ Odstranit kontejner** na horním panelu příkazů. 

![Odstranění kontejneru](media/azure-stack-edge-j-series-manage-storage-accounts/add-container-3.png)

## <a name="sync-storage-keys"></a>Synchronizace klíčů úložiště

Přístupové klíče pro účty úložiště Edge (místní) můžete synchronizovat na svém zařízení. 

Pokud chcete synchronizovat přístupový klíč účtu úložiště, proveďte následující kroky:

1. V prostředku vyberte účet úložiště, který chcete spravovat. Na horním panelu příkazů vyberte **synchronizace klíč úložiště**.

    ![Vybrat synchronizační klíč úložiště](media/azure-stack-edge-j-series-manage-storage-accounts/sync-storage-key-1.png)

2. Po zobrazení výzvy k potvrzení vyberte **Ano**.

    ![Vybrat synchronizační klíč úložiště 2](media/azure-stack-edge-j-series-manage-storage-accounts/sync-storage-key-2.png)

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [spravovat uživatele pomocí webu Azure Portal](azure-stack-edge-j-series-manage-users.md).
