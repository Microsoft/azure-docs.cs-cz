---
title: Azure Stack Edge pro GPU Správa plánů šířky pásma | Microsoft Docs
description: Popisuje, jak použít Azure Portal ke správě plánů šířky pásma na grafickém procesoru Azure Stack Edge pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: b3ae2986b49cf6854e0d9a6b99aabdfc2dd6b713
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2021
ms.locfileid: "102436647"
---
# <a name="use-the-azure-portal-to-manage-bandwidth-schedules-on-your-azure-stack-edge-pro-gpu"></a>Použití Azure Portal ke správě plánů šířky pásma na procesorovém GPU Azure Stack Edge pro 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Tento článek popisuje, jak spravovat plány šířky pásma na Azure Stack Edge pro. Plány služby Bandwidth vám umožňují nakonfigurovat využití šířky pásma sítě pro více denních plánů. Tyto plány lze použít k operacím nahrávání a stahování ze zařízení do cloudu a naopak.

Plány šířky pásma pro Azure Stack Edge pro můžete přidat, upravit nebo odstranit pomocí Azure Portal.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Přidání plánu
> * Úprava plánu
> * Odstranění plánu


## <a name="add-a-schedule"></a>Přidání plánu

Chcete-li přidat plán, proveďte následující kroky v Azure Portal.

1. V Azure Portal pro prostředek Azure Stack Edge přejít na **šířku pásma**.
2. V pravém podokně vyberte **+ Přidat plán**.

    ![Výběr šířky pásma](media/azure-stack-edge-j-series-manage-bandwidth-schedules/add-schedule-1.png)

3. V části **Přidání plánu**:

   1. Zadejte **počáteční den**, **Koncový den**, **čas spuštění** a **čas ukončení** plánu.
   2. Pokud má být tento plán běžet celý den, ověřte možnost **všechny dny** .
   3. **Rychlost šířky pásma** je šířka pásma v megabajtech za sekundu (MB/s), kterou vaše zařízení používá v operacích, které se týkají cloudu (odesílání a stahování). Zadejte číslo mezi 20 a 2 147 483 647 pro toto pole.
   4. Vyberte **neomezenou šířku pásma** , pokud nechcete omezit nahrávání a stahování data.
   5. Vyberte **Přidat**.

      ![Přidat plán](media/azure-stack-edge-j-series-manage-bandwidth-schedules/add-schedule-2.png)

3. Vytvoří se plán se zadanými parametry. Tento plán se následně zobrazí v seznamu plánů služby Bandwidth na portálu.

    ![Aktualizovaný seznam plánů šířky pásma](media/azure-stack-edge-j-series-manage-bandwidth-schedules/add-schedule-3.png)

## <a name="edit-schedule"></a>Úprava plánu

Chcete-li upravit plán služby Bandwidth, postupujte následovně.

1. V Azure Portal přejít na prostředek Azure Stack Edge a pak přejít na **šířku pásma**.
2. V seznamu plánů šířky pásma vyberte plán, který chcete upravit.

   ![Vybrat plán šířky pásma](media/azure-stack-edge-j-series-manage-bandwidth-schedules/modify-schedule-1.png)

3. Proveďte požadované změny a uložte je.

    ![Úprava uživatele](media/azure-stack-edge-j-series-manage-bandwidth-schedules/modify-schedule-2.png)

4. Po úpravě plánu se seznam plánů aktualizuje tak, aby odrážel změněný plán.

    ![Upravit uživatele 2](media/azure-stack-edge-j-series-manage-bandwidth-schedules/modify-schedule-3.png)


## <a name="delete-a-schedule"></a>Odstranění plánu

Pomocí následujících kroků odstraňte plán šířky pásma přidružený k vašemu zařízení Azure Stack Edge pro.

1. V Azure Portal přejít na prostředek Azure Stack Edge a pak přejít na **šířku pásma**.  

2. V seznamu plánů služby Bandwidth vyberte a klikněte na plán, který chcete odstranit. V části **Upravit plán** vyberte **Odstranit**. Po zobrazení výzvy k potvrzení vyberte **Ano**.

   ![Odstranění uživatele](media/azure-stack-edge-j-series-manage-bandwidth-schedules/delete-schedule-2.png)

3. Po odstranění plánu se aktualizuje seznam plánů.


## <a name="next-steps"></a>Další kroky

- Naučte se [Spravovat sdílené složky](azure-stack-edge-j-series-manage-shares.md).
