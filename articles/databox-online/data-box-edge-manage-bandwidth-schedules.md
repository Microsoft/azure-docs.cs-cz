---
title: Azure Data Box Edge spravuje plány šířky pásma | Dokumenty společnosti Microsoft
description: Popisuje, jak používat portál Azure ke správě plánů šířky pásma na azure data boxu Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/22/2019
ms.author: alkohli
ms.openlocfilehash: f7b762d5502986c306de240519688aa639f58445
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60756800"
---
# <a name="use-the-azure-portal-to-manage-bandwidth-schedules-on-your-azure-data-box-edge"></a>Správa plánů šířky pásma na Azure Data Box Edge pomocí portálu Azure Portal  

Tento článek popisuje, jak spravovat uživatele na Azure Data Box Edge. Plány služby Bandwidth vám umožňují nakonfigurovat využití šířky pásma sítě pro více denních plánů. Tyto plány lze použít k operacím nahrávání a stahování ze zařízení do cloudu a naopak.

Plány šířky pásma pro oblast Data Box Edge můžete přidat, upravit nebo odstranit prostřednictvím portálu Azure.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Přidání plánu
> * Úprava plánu
> * Odstranění plánu


## <a name="add-a-schedule"></a>Přidání plánu

Postupujte podle následujících kroků na webu Azure Portal a přidejte plán.

1. Na webu Azure Portal pro prostředek Data Box Edge přejděte na **Šířka pásma**.
2. V pravém podokně vyberte **+ Přidat plán**.

    ![Vybrat šířku pásma](media/data-box-edge-manage-bandwidth-schedules/add-schedule-1.png)

3. V části **Přidání plánu**: 

   1. Zadejte **den spuštění**, **den ukončení**, **čas spuštění** a **čas ukončení** plánu.
   2. Zaškrtněte možnost **Celý den,** pokud by tento plán měl běžet celý den.
   3. **Rychlost šířky pásma** je šířka pásma v megabitech za sekundu (Mb/s), používaná vašim zařízením při operacích zahrnujících cloud (nahrávání i stahování). Zajistěte číslo mezi 20 a 1 000 000 007 pro toto pole.
   4. Pokud nechcete omezit nahrávání a stahování dat, zaškrtněte možnost **Neomezená šířka pásma**.
   5. Vyberte **Přidat**.

      ![Přidat plán](media/data-box-edge-manage-bandwidth-schedules/add-schedule-2.png)

3. Vytvoří se plán se zadanými parametry. Tento plán se následně zobrazí v seznamu plánů služby Bandwidth na portálu.

    ![Aktualizovaný seznam plánů šířky pásma](media/data-box-edge-manage-bandwidth-schedules/add-schedule-3.png)

## <a name="edit-schedule"></a>Úprava plánu

Chcete-li upravit plán služby Bandwidth, postupujte následovně.

1. Na webu Azure Portal přejděte na prostředek Data Box Edge a pak přejděte na **Šířku pásma**. 
2. Ze seznamu plánů šířky pásma vyberte a vyberte plán, který chcete upravit.
    ![Vybrat plán šířky pásma](media/data-box-edge-manage-bandwidth-schedules/modify-schedule-1.png)

3. Proveďte požadované změny a uložte je.

    ![Úprava uživatele](media/data-box-edge-manage-bandwidth-schedules/modify-schedule-2.png)

4. Po úpravě plánu se seznam plánů aktualizuje tak, aby odrážel změněný plán.

    ![Úprava uživatele](media/data-box-edge-manage-bandwidth-schedules/modify-schedule-3.png)


## <a name="delete-a-schedule"></a>Odstranění plánu

Chcete-li odstranit plán šířky pásma přidružený k zařízení Data Box Edge, postupujte podle následujících kroků.

1. Na webu Azure Portal přejděte na prostředek Data Box Edge a pak přejděte na **Šířku pásma**.  

2. V seznamu plánů služby Bandwidth vyberte a klikněte na plán, který chcete odstranit. V **plánu úprav**vyberte **Odstranit**. Po zobrazení výzvy k potvrzení vyberte **možnost Ano**.

   ![Odstranění uživatele](media/data-box-edge-manage-bandwidth-schedules/delete-schedule-2.png)

3. Po odstranění plánu se aktualizuje seznam plánů.


## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [spravovat sdílené složky](data-box-edge-manage-shares.md).
