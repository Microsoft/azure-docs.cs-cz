---
title: Ruční převzetí služeb při selhání služby Azure IoT hub | Microsoft Docs
description: Zjistěte, jak provést ruční převzetí služeb při selhání služby Azure IoT Hub
author: robinsh
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 07/24/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 308e452f33ded9be3b88ff370ed34326de54895c
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877026"
---
# <a name="tutorial-perform-manual-failover-for-an-iot-hub"></a>Kurz: Provedení ručního převzetí služeb při selhání pro Centrum IoT

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

    **Oblast**: Vyberte oblast, která je pro vás blízko. Tento kurz používá oblast `West US 2`. Převzetí služeb při selhání se dá provést jenom mezi geograficky spárovanými oblastmi Azure. Oblast geograficky spárovaná pomocí Západní USA 2 je WestCentralUS.
    
   **Název centra IoT Hub:** Zadejte název vašeho IoT Hubu. Název centra musí být globálně jedinečný. 

   ![Snímek obrazovky s podoknem Základy pro vytvoření IoT Hubu](./media/tutorial-manual-failover/create-hub-02-basics.png)

   Klikněte na **Zkontrolovat a vytvořit**. (Pro velikost a škálování se použijí výchozí hodnoty.) 

4. Zkontrolujte informace a potom kliknutím na **Vytvořit** vytvořte IoT Hub. 

   ![Snímek obrazovky s posledním krokem při vytváření IoT Hubu](./media/tutorial-manual-failover/create-hub-03-create.png)

## <a name="perform-a-manual-failover"></a>Ruční převzetí služeb při selhání

Upozorňujeme, že jedno centrum IoT Hub má limit dvou převzetí služeb při selhání a dvou navrácení služeb po obnovení za den.

1. Klikněte na **Skupiny prostředků** a potom vyberte skupinu prostředků **ManlFailRG**. V seznamu prostředků klikněte na své centrum. 

1. V části **Nastavení** v podokně IoT Hub klikněte na **převzetí služeb při selhání**.

   ![Snímek obrazovky s podoknem vlastností služby IoT Hub](./media/tutorial-manual-failover/trigger-failover-01.png)

1. V podokně ruční převzetí služeb při selhání uvidíte **aktuální umístění** a **umístění převzetí služeb při selhání**. Aktuální umístění vždy indikuje umístění, ve kterém je rozbočovač aktuálně aktivní. Umístění převzetí služeb při selhání je standardní [geograficky spárovaná oblast Azure](../best-practices-availability-paired-regions.md) , která se spáruje s aktuálním umístěním. Hodnoty umístění se nedají změnit. V tomto kurzu je aktuální umístění `West US 2` a `West Central US`umístění převzetí služeb při selhání.

   ![Snímek obrazovky s podoknem Ruční převzetí služeb při selhání](./media/tutorial-manual-failover/trigger-failover-02.png)

1. V horní části podokna ruční převzetí služeb při selhání klikněte na **Spustit převzetí služeb při selhání**. 

1. V podokně potvrzení zadejte název vašeho centra IoT a potvrďte, že se jedná o takové, které chcete převzetí služeb při selhání. Pak klikněte na převzetí služeb při selhánía spusťte převzetí služeb při selhání.

   Doba trvání ručního převzetí služeb při selhání je úměrná počtu zařízení registrovaných ve vašem centru. Pokud třeba máte 100 000 zařízení, může převzetí služeb při selhání trvat 15 minut, ale pokud máte zařízení pět milionů, může trvat hodinu i delší dobu.

   ![Snímek obrazovky s podoknem Ruční převzetí služeb při selhání](./media/tutorial-manual-failover/trigger-failover-03-confirm.png)

   Když je spuštěný proces ručního převzetí služeb při selhání, zobrazí se banner s oznámením, že probíhá ruční převzetí služeb při selhání. 

   ![Snímek obrazovky s probíhajícím ručním převzetím služeb při selhání](./media/tutorial-manual-failover/trigger-failover-04-in-progress.png)

   Pokud zavřete podokno IoT Hub a znovu ho otevřete kliknutím na něj v podokně skupina prostředků, zobrazí se informační zpráva s oznámením, že centrum je uprostřed ručního převzetí služeb při selhání. 

   ![Snímek obrazovky znázorňující probíhající převzetí služeb při selhání IoT Hub](./media/tutorial-manual-failover/trigger-failover-05-hub-inactive.png)

   Po dokončení se Překlopí aktuální oblasti a oblasti převzetí služeb při selhání na stránce ručního převzetí služeb při selhání a centrum bude zase aktivní. V tomto příkladu je aktuální umístění nyní `WestCentralUS` a umístění převzetí služeb při selhání je nyní. `West US 2` 

   ![Snímek obrazovky po dokončení převzetí služeb při selhání](./media/tutorial-manual-failover/trigger-failover-06-finished.png)

   Na stránce Přehled se zobrazí také zpráva oznamující, že se převzetí služeb při selhání dokončilo `West Central US`a IoT Hub běží v.

   ![Snímek obrazovky s kompletním převzetím služeb při selhání na stránce Přehled](./media/tutorial-manual-failover/trigger-failover-06-finished-overview.png)


## <a name="perform-a-failback"></a>Navrácení služeb po obnovení 

Po provedení ručního převzetí služeb při selhání můžete vrátit provoz centra do původní primární oblasti. Tomuto kroku se říká navrácení služeb po obnovení. Pokud jste právě provedli převzetí služeb při selhání, budete muset asi hodinu počkat, než bude možné požádat o navrácení služeb po obnovení. Pokud se pokusíte provést navrácení služeb po obnovení za kratší dobu, zobrazí se chybová zpráva.

Navrácení služeb po obnovení se provádí stejným způsobem jako ruční převzetí služeb při selhání. Proveďte tyto kroky: 

1. Pokud chcete provést navrácení služeb po obnovení, vraťte se do podokna IoT Hub pro vaše centrum IoT Hub.

2. V části **Nastavení** v podokně IoT Hub klikněte na **převzetí služeb při selhání**. 

3. V horní části podokna ruční převzetí služeb při selhání klikněte na **Spustit převzetí služeb při selhání**. 

4. V podokně potvrzení zadejte název vašeho centra IoT a potvrďte, že se jedná o tu, kterou chcete navrátit zpět. Navrácení služeb po obnovení zahájíte kliknutím na OK. 

   ![Snímek obrazovky s požadavkem na navrácení služeb po obnovení](./media/tutorial-manual-failover/trigger-failover-03-confirm.png)

   Bannery se zobrazují, jak je vysvětleno v části provedení převzetí služeb při selhání. Po navrácení služeb po obnovení se znovu zobrazí `West US 2` jako aktuální umístění a `West Central US` jako umístění pro převzetí služeb při selhání, jak je nastavené jako původní.

## <a name="clean-up-resources"></a>Vyčištění prostředků 

Pokud chcete odebrat prostředky, které jste v tomto kurzu vytvořili, odstraňte skupinu prostředků. Tato akce odstraní všechny prostředky, které skupina obsahuje. V tomto případě se odebere IoT Hub i samotná skupina prostředků. 

1. Klikněte na **Skupiny prostředků**. 

2. Vyhledejte a vyberte skupinu prostředků **ManlFailRG**. Klikněte na něj a otevřete ho. 

3. Klikněte na **Odstranit skupinu prostředků**. Po zobrazení výzvy zadejte název skupiny prostředků a potvrďte volbu kliknutím na **Odstranit**. 

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se dozvěděli, jak nakonfigurovat a provést ruční převzetí služeb při selhání a jak požádat o navrácení služeb po obnovení. Provedli jste při tom tyto úlohy:

> [!div class="checklist"]
> * Vytvoření IoT Hubu pomocí webu Azure Portal 
> * Převzetí služeb při selhání 
> * Zobrazení IoT Hubu běžícího v sekundárním umístění
> * Navrácení služeb po obnovení, aby se provoz IoT Hubu vrátil do primárního umístění 
> * Ověření správného chodu IoT Hubu ve správném umístění

V dalším kurzu se dozvíte, jak spravovat stav zařízení IoT. 

> [!div class="nextstepaction"]
> [Správa stavu zařízení IoT](tutorial-device-twins.md)
