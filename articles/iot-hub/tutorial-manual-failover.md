---
title: Ruční převzetí služeb při selhání služby Azure IoT hub | Microsoft Docs
description: Zjistěte, jak provést ruční převzetí služeb při selhání služby Azure IoT Hub
author: robinsh
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 07/11/2018
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: f0e8bf922f142b795dd1a2ded4b3ec265c43481a
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/25/2018
ms.locfileid: "39249937"
---
# <a name="tutorial-perform-manual-failover-for-an-iot-hub-public-preview"></a>Kurz: Ruční převzetí služeb při selhání služby IoT Hub (Public Preview)

Ruční převzetí služeb při selhání je funkce služby IoT Hub, která zákazníkům umožňuje [převzetí služeb při selhání](https://en.wikipedia.org/wiki/Failover) provozu jejich centra z primární oblasti do odpovídající geograficky spárované oblasti Azure. Ruční převzetí služeb při selhání se dá provést v případě havárie nebo delšího výpadku služeb v oblasti. Můžete také provést plánované převzetí služeb při selhání a otestovat si své možnosti zotavení po havárii, doporučujeme ale použít spíš IoT Hub v testovacím prostředí, ne v produkčním. Funkci ručního převzetí služeb při selhání mají zákazníci k dispozici bez dalších poplatků.

V tomto kurzu provedete následující úlohy:

> [!div class="checklist"]
> * Vytvoření IoT Hubu pomocí webu Azure Portal 
> * Převzetí služeb při selhání 
> * Zobrazení IoT Hubu běžícího v sekundárním umístění
> * Navrácení služeb po obnovení, aby se provoz IoT Hubu vrátil do primárního umístění 
> * Ověření správného chodu IoT Hubu ve správném umístění

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

1. Přihlaste se na [Azure Portal](https://portal.azure.com). 

2. Klikněte na **+ Vytvořit prostředek**, **Internet věcí** a nakonec **IoT Hub**.

   ![Snímek obrazovky znázorňující vytváření IoT Hubu](./media/tutorial-manual-failover/create-hub-01.png)

3. Vyberte kartu **Základy**. Vyplňte následující pole.

    **Předplatné:** Vyberte předplatné Azure, které chcete použít.

    **Skupina prostředků:** Klikněte na **Vytvořit nový** a jako název skupiny prostředků zadejte **ManlFailRG**.

    **Oblast:** Vyberte oblast ve vaší blízkosti, která je součástí verze Preview. Tento kurz používá oblast `westus2`. Převzetí služeb při selhání se dá provést jenom mezi geograficky spárovanými oblastmi Azure. S oblastí westus2 je geograficky spárovaná oblast WestCentralUS.
    
   > [!NOTE]
   > Ruční převzetí služeb při selhání je momentálně ve verzi Public Preview a *není* dostupné v těchto oblastech Azure: USA – východ, Západní USA, Severní Evropa, Západní Evropa, Brazílie – jih a Střed USA – jih.

   **Název centra IoT Hub:** Zadejte název vašeho IoT Hubu. Název centra musí být globálně jedinečný. 

   ![Snímek obrazovky s podoknem Základy pro vytvoření IoT Hubu](./media/tutorial-manual-failover/create-hub-02-basics.png)

   Klikněte na **Zkontrolovat a vytvořit**. (Pro velikost a škálování se použijí výchozí hodnoty.) 

4. Zkontrolujte informace a potom kliknutím na **Vytvořit** vytvořte IoT Hub. 

   ![Snímek obrazovky s posledním krokem při vytváření IoT Hubu](./media/tutorial-manual-failover/create-hub-03-create.png)

## <a name="perform-a-manual-failover"></a>Ruční převzetí služeb při selhání

Upozorňujeme, že jedno centrum IoT Hub má limit dvou převzetí služeb při selhání a dvou navrácení služeb po obnovení za den.

1. Klikněte na **Skupiny prostředků** a potom vyberte skupinu prostředků **ManlFailRG**. V seznamu prostředků klikněte na své centrum. 

2. V části **Odolnost** v podokně IoT Hub klikněte na **Ruční převzetí služeb při selhání (Preview)**. Upozorňujeme, že pokud centrum nevytvoříte v platné oblasti, bude možnost ručního převzetí služeb při selhání zakázaná.

   ![Snímek obrazovky s podoknem vlastností služby IoT Hub](./media/tutorial-manual-failover/trigger-failover-01.png)

3. V podokně Ruční převzetí služeb při selhání se zobrazuje **Primární umístění IoT Hubu** a **Sekundární umístění IoT Hubu**. Primární umístění je nejdřív nastavené na umístění, které jste zadali při vytváření IoT Hubu, a vždycky udává umístění, ve kterém je centrum momentálně aktivní. Sekundární umístění je standardní [geograficky spárovaná oblast Azure](../best-practices-availability-paired-regions.md) přidružená k primárnímu umístění. Hodnoty umístění se nedají změnit. Pro účely tohoto kurzu je primární umístění `westus2` a sekundární umístění je `WestCentralUS`.

   ![Snímek obrazovky s podoknem Ruční převzetí služeb při selhání](./media/tutorial-manual-failover/trigger-failover-02.png)

3. V horní části podokna Ruční převzetí služeb při selhání klikněte na **Spustit převzetí služeb při selhání**. Zobrazí se podokno **Potvrdit ruční převzetí služeb při selhání**. Zadejte název IoT Hubu, abyste potvrdili, že u něj chcete provést převzetí služeb při selhání. Potom spusťte převzetí služeb při selhání kliknutím na **OK**.

   Doba trvání ručního převzetí služeb při selhání je úměrná počtu zařízení registrovaných ve vašem centru. Pokud třeba máte 100 000 zařízení, může převzetí služeb při selhání trvat 15 minut, ale pokud máte zařízení pět milionů, může trvat hodinu i delší dobu.

4. V podokně **Potvrdit ruční převzetí služeb při selhání** zadejte název IoT Hubu, abyste potvrdili, že u něj chcete provést převzetí služeb při selhání. Potom spusťte převzetí služeb při selhání kliknutím na OK. 

   ![Snímek obrazovky s podoknem Ruční převzetí služeb při selhání](./media/tutorial-manual-failover/trigger-failover-03-confirm.png)

   V průběhu procesu ručního převzetí služeb při selhání se v podokně Ruční převzetí služeb při selhání zobrazuje zpráva, že probíhá ruční převzetí služeb při selhání. 

   ![Snímek obrazovky s probíhajícím ručním převzetím služeb při selhání](./media/tutorial-manual-failover/trigger-failover-04-in-progress.png)

   Když zavřete podokno IoT Hub a znovu na něj kliknete v podokně Skupina prostředků, aby se otevřelo, zobrazí se zpráva, že centrum není aktivní. 

   ![Snímek obrazovky se zprávou, že IoT Hub není aktivní](./media/tutorial-manual-failover/trigger-failover-05-hub-inactive.png)

   Po dokončení dojde k záměně primární a sekundární oblasti na stránce Ruční převzetí služeb při selhání a centrum bude zase aktivní. V tomto příkladu je teď primární umístění `WestCentralUS` a sekundární umístění je `westus2`. 

   ![Snímek obrazovky po dokončení převzetí služeb při selhání](./media/tutorial-manual-failover/trigger-failover-06-finished.png)

## <a name="perform-a-failback"></a>Navrácení služeb po obnovení 

Po provedení ručního převzetí služeb při selhání můžete vrátit provoz centra do původní primární oblasti. Tomuto kroku se říká navrácení služeb po obnovení. Pokud jste právě provedli převzetí služeb při selhání, budete muset asi hodinu počkat, než bude možné požádat o navrácení služeb po obnovení. Pokud se pokusíte provést navrácení služeb po obnovení za kratší dobu, zobrazí se chybová zpráva.

Navrácení služeb po obnovení se provádí stejným způsobem jako ruční převzetí služeb při selhání. Proveďte tyto kroky: 

1. Pokud chcete provést navrácení služeb po obnovení, vraťte se do podokna IoT Hub pro vaše centrum IoT Hub.

2. V části **Odolnost** v podokně IoT Hub klikněte na **Ruční převzetí služeb při selhání (Preview)**. 

3. V horní části podokna Ruční převzetí služeb při selhání klikněte na **Spustit převzetí služeb při selhání**. Zobrazí se podokno **Potvrdit ruční převzetí služeb při selhání**. 

4. V podokně **Potvrdit ruční převzetí služeb při selhání** zadejte název IoT Hubu, abyste potvrdili, že u něj chcete provést navrácení služeb po obnovení. Navrácení služeb po obnovení zahájíte kliknutím na OK. 

   ![Snímek obrazovky s požadavkem na navrácení služeb po obnovení](./media/tutorial-manual-failover/trigger-failback-01-regions.png)

   Zobrazují se podobné zprávy jako ty, které jsou popsané v části [Převzetí služeb při selhání](#perform-a-failover). Po dokončení navrácení služeb po obnovení se jako primární umístění znovu zobrazí `westus2` a jako sekundární umístění `WestCentralUS`.

## <a name="clean-up-resources"></a>Vyčištění prostředků 

Pokud chcete odebrat prostředky, které jste v tomto kurzu vytvořili, odstraňte skupinu prostředků. Tato akce odstraní všechny prostředky, které skupina obsahuje. V tomto případě se odebere IoT Hub i samotná skupina prostředků. 

1. Klikněte na **Skupiny prostředků**. 

2. Vyhledejte a vyberte skupinu prostředků **ManlFailRG**. Klikněte na něj a otevřete ho. 

3. Klikněte na **Odstranit skupinu prostředků**. Po zobrazení výzvy zadejte název skupiny prostředků a potvrďte volbu kliknutím na **Odstranit**. 

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli, jak nakonfigurovat a provést ruční převzetí služeb při selhání a jak požádat o navrácení služeb po obnovení. Provedli jste při tom tyto úlohy:

> [!div class="checklist"]
> * Vytvoření IoT Hubu pomocí webu Azure Portal 
> * Převzetí služeb při selhání 
> * Zobrazení IoT Hubu běžícího v sekundárním umístění
> * Navrácení služeb po obnovení, aby se provoz IoT Hubu vrátil do primárního umístění 
> * Ověření správného chodu IoT Hubu ve správném umístění

V dalším kurzu se dozvíte, jak spravovat stav zařízení IoT. 

> [!div class="nextstepaction"]
[Správa stavu zařízení IoT](tutorial-device-twins.md)