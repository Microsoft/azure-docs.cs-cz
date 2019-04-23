---
title: Azure Data Box Edge Spravovat plány šířky pásma | Dokumentace Microsoftu
description: Popisuje, jak pomocí webu Azure portal ke správě plánů šířky pásma na hranici vaší Azure Data Box.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/22/2019
ms.author: alkohli
ms.openlocfilehash: f7b762d5502986c306de240519688aa639f58445
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2019
ms.locfileid: "59999650"
---
# <a name="use-the-azure-portal-to-manage-bandwidth-schedules-on-your-azure-data-box-edge"></a>Pomocí webu Azure portal ke správě plánů šířky pásma na hranici vaší Azure Data Box  

Tento článek popisuje, jak spravovat uživatele na hranici vaší Azure Data Box. Plány služby Bandwidth vám umožňují nakonfigurovat využití šířky pásma sítě pro více denních plánů. Tyto plány lze použít k operacím nahrávání a stahování ze zařízení do cloudu a naopak.

Můžete přidat, upravit nebo odstranit plány šířky pásma pro vaše Data pole hraničních zařízení prostřednictvím webu Azure portal.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Přidání plánu
> * Úprava plánu
> * Odstranění plánu


## <a name="add-a-schedule"></a>Přidání plánu

Proveďte následující kroky na webu Azure Portal přidat plán.

1. Na portálu Azure pro prostředek okraj pole dat, přejděte na **šířky pásma**.
2. V pravém podokně vyberte **+ přidat plán**.

    ![Vyberte šířku pásma](media/data-box-edge-manage-bandwidth-schedules/add-schedule-1.png)

3. V části **Přidání plánu**: 

   1. Zadejte **den spuštění**, **den ukončení**, **čas spuštění** a **čas ukončení** plánu.
   2. Zkontrolujte, **celý den** Pokud tento plán by měl spustit celý den.
   3. **Rychlost šířky pásma** je šířka pásma v megabitech za sekundu (Mb/s), používaná vašim zařízením při operacích zahrnujících cloud (nahrávání i stahování). Zadejte číslo v rozsahu od 20 a 1,000,000,007 pro toto pole.
   4. Pokud nechcete omezit nahrávání a stahování dat, zaškrtněte možnost **Neomezená šířka pásma**.
   5. Vyberte **Přidat**.

      ![Přidat plán](media/data-box-edge-manage-bandwidth-schedules/add-schedule-2.png)

3. Vytvoří se plán se zadanými parametry. Tento plán se následně zobrazí v seznamu plánů služby Bandwidth na portálu.

    ![Aktualizovaný seznam plánů šířky pásma](media/data-box-edge-manage-bandwidth-schedules/add-schedule-3.png)

## <a name="edit-schedule"></a>Úprava plánu

Chcete-li upravit plán služby Bandwidth, postupujte následovně.

1. Na webu Azure Portal, přejděte na váš prostředek okraj pole Data a potom přejděte ke **šířky pásma**. 
2. V seznamu plánů šířky pásma vyberte a vyberte plán, který chcete upravit.
    ![Vybrat plán, šířky pásma](media/data-box-edge-manage-bandwidth-schedules/modify-schedule-1.png)

3. Proveďte požadované změny a uložte je.

    ![Úprava uživatele](media/data-box-edge-manage-bandwidth-schedules/modify-schedule-2.png)

4. Po úpravě plánu se seznam plánů aktualizuje tak, aby odrážel změněný plán.

    ![Úprava uživatele](media/data-box-edge-manage-bandwidth-schedules/modify-schedule-3.png)


## <a name="delete-a-schedule"></a>Odstranění plánu

Proveďte následující kroky k odstranění plánu šířky pásma spojené s vaším zařízením Data Box Edge.

1. Na webu Azure Portal, přejděte na váš prostředek okraj pole Data a potom přejděte ke **šířky pásma**.  

2. V seznamu plánů služby Bandwidth vyberte a klikněte na plán, který chcete odstranit. V **upravit plán**vyberte **odstranit**. Po zobrazení výzvy k potvrzení, vyberte **Ano**.

   ![Odstranění uživatele](media/data-box-edge-manage-bandwidth-schedules/delete-schedule-2.png)

3. Po odstranění plánu se aktualizuje seznam plánů.


## <a name="next-steps"></a>Další postup

- Zjistěte, jak [spravovat sdílené složky](data-box-edge-manage-shares.md).
