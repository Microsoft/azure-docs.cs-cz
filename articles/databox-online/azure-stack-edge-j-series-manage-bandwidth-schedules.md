---
title: Azure Stack hraniční GPU spravovat plány šířky pásma | Microsoft Docs
description: Popisuje způsob použití Azure Portal ke správě plánů šířky pásma na procesorovém GPU Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: 92b9edb4f694a5ff7d15b06be41cc20db3b64b97
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/01/2020
ms.locfileid: "89265690"
---
# <a name="use-the-azure-portal-to-manage-bandwidth-schedules-on-your-azure-stack-edge-gpu"></a>Použití Azure Portal ke správě plánů šířky pásma na procesorovém GPU Azure Stack Edge 

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

Tento článek popisuje, jak spravovat plány šířky pásma na Azure Stack hraničních zařízeních. Plány služby Bandwidth vám umožňují nakonfigurovat využití šířky pásma sítě pro více denních plánů. Tyto plány lze použít k operacím nahrávání a stahování ze zařízení do cloudu a naopak.

Plány šířky pásma Azure Stackho okraje můžete přidat, upravit nebo odstranit pomocí Azure Portal.

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

   1. Zadejte **den spuštění**, **den ukončení**, **čas spuštění** a **čas ukončení** plánu.
   2. Pokud má být tento plán běžet celý den, ověřte možnost **všechny dny** .
   3. **Rychlost šířky pásma** je šířka pásma v megabitech za sekundu (Mb/s), používaná vašim zařízením při operacích zahrnujících cloud (nahrávání i stahování). Zadejte číslo mezi 20 a 2 147 483 647 pro toto pole.
   4. Pokud nechcete omezit nahrávání a stahování dat, zaškrtněte možnost **Neomezená šířka pásma**.
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

    ![Úprava uživatele](media/azure-stack-edge-j-series-manage-bandwidth-schedules/modify-schedule-3.png)


## <a name="delete-a-schedule"></a>Odstranění plánu

Pokud chcete odstranit plán šířky pásma přidružený k vašemu Azure Stack hraničnímu zařízení, proveďte následující kroky.

1. V Azure Portal přejít na prostředek Azure Stack Edge a pak přejít na **šířku pásma**.  

2. V seznamu plánů služby Bandwidth vyberte a klikněte na plán, který chcete odstranit. V části **Upravit plán**vyberte **Odstranit**. Po zobrazení výzvy k potvrzení vyberte **Ano**.

   ![Odstranění uživatele](media/azure-stack-edge-j-series-manage-bandwidth-schedules/delete-schedule-2.png)

3. Po odstranění plánu se aktualizuje seznam plánů.


## <a name="next-steps"></a>Další kroky

- Naučte se [Spravovat sdílené složky](azure-stack-edge-j-series-manage-shares.md).
