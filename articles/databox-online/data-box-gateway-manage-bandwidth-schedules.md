---
title: Správa plánů šířky pásma na Azure Data Box Gateway | Microsoft Docs
description: Popisuje používání webu Azure Portal ke správě plánů služby Bandwidth ve službě Azure Data Box Gateway.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: how-to
ms.date: 03/25/2019
ms.author: alkohli
ms.openlocfilehash: 25f74fb78979313acc068320d842d09a982d8d16
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/03/2020
ms.locfileid: "84339140"
---
# <a name="use-the-azure-portal-to-manage-bandwidth-schedules-on-your-azure-data-box-gateway"></a>Web Azure Portal můžete použít ke správě plánů služby Bandwidth ve službě Azure Data Box Gateway  

Tento článek popisuje postup správy uživatelů ve službě Azure Data Box Gateway. Plány služby Bandwidth vám umožňují nakonfigurovat využití šířky pásma sítě pro více denních plánů. Tyto plány lze použít k operacím nahrávání a stahování ze zařízení do cloudu a naopak.

Prostřednictvím webu Azure Portal můžete přidávat, upravovat nebo odstraňovat plány služby Bandwidth pro vaši službu Data Box Gateway.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
>
> * Přidání plánu
> * Úprava plánu
> * Odstranění plánu

## <a name="add-a-schedule"></a>Přidání plánu

Pokud chcete přidat uživatele, proveďte na webu Azure Portal následující kroky.

1. Na webu Azure Portal pro váš prostředek služby Data Box Gateway přejděte na **Bandwidth**.
2. V pravém podokně klikněte na tlačítko **+ Přidat plán**.

    ![Přidání uživatele kliknutím](media/data-box-gateway-manage-bandwidth-schedules/add-schedule-1.png)

3. V části **Přidání plánu**: 

   1. Zadejte **den spuštění**, **den ukončení**, **čas spuštění** a **čas ukončení** plánu. 
   2. Pokud tento plán běží celý den, můžete zaškrtnout možnost **Celý den**. 
   3. **Rychlost šířky pásma** je šířka pásma v megabitech za sekundu (Mb/s), používaná vašim zařízením při operacích zahrnujících cloud (nahrávání i stahování). Do tohoto pole zadejte číslo od 1 do 1 000. 
   4. Pokud nechcete omezit nahrávání a stahování dat, zaškrtněte možnost **Neomezená šířka pásma**. 
   5. Klikněte na tlačítko **Add** (Přidat).

      ![Přidání uživatele kliknutím](media/data-box-gateway-manage-bandwidth-schedules/add-schedule-2.png)

3. Vytvoří se plán se zadanými parametry. Tento plán se následně zobrazí v seznamu plánů služby Bandwidth na portálu.


## <a name="edit-schedule"></a>Úprava plánu

Chcete-li upravit plán služby Bandwidth, postupujte následovně. 

1. Na webu Azure Portal přejděte k prostředku služby Data Box Gateway a pak přejděte na Bandwidth. 
2. V seznamu plánů služby Bandwidth vyberte a klikněte na plán, který chcete upravit.
    ![Úprava uživatele](media/data-box-gateway-manage-bandwidth-schedules/modify-schedule-1.png)

3. Proveďte požadované změny a uložte je.

    ![Úprava uživatele](media/data-box-gateway-manage-bandwidth-schedules/modify-schedule-2.png)

4. Po úpravě plánu se seznam plánů aktualizuje tak, aby odrážel změněný plán.

    ![Úprava uživatele](media/data-box-gateway-manage-bandwidth-schedules/modify-schedule-3.png)


## <a name="delete-a-schedule"></a>Odstranění plánu

Chcete-li odstranit plán služby Bandwidth spojený s vaším zařízením služby Data Box Gateway, postupujte následovně.

1. Na webu Azure Portal přejděte k prostředku služby Data Box Gateway a pak přejděte na **Bandwidth**.  

2. V seznamu plánů služby Bandwidth vyberte a klikněte na plán, který chcete odstranit. Kliknutím pravým tlačítkem si zobrazte kontextovou nabídku a klikněte na **Odstranit**. 

   ![Odstranění uživatele](media/data-box-gateway-manage-bandwidth-schedules/delete-schedule-1.png)

3.  Po odstranění plánu se aktualizuje seznam plánů.



## <a name="next-steps"></a>Další kroky

- Další informace o [správě šířky pásma](data-box-gateway-manage-bandwidth-schedules.md). 
