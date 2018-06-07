---
title: Upgrade služby Azure IoT Hub | Microsoft Docs
description: Změňte úroveň cen a škálování pro IoT Hub získat další možnosti správy zařízení a zasílání zpráv.
author: kgremban
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/02/2018
ms.author: kgremban
ms.openlocfilehash: 472115f166adc5385b6f46b2f3ac5ef75a6cde92
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34637272"
---
# <a name="how-to-upgrade-your-iot-hub"></a>Postup upgradu služby IoT hub

S růstem IoT řešení Azure IoT Hub je připraven k můžete škálovat. Azure IoT Hub nabízí dvě úrovně, basic (B) a standard (S), aby dokázala pojmout zákazníků, které chcete používat různé funkce. V jednotlivých vrstvách jsou tři velikosti (1, 2 a 3), které určují počet zpráv, které mohou být odesílány každý den. 

Pokud máte více zařízení a potřebujete další funkce, upravte služby IoT hub, aby vyhovovaly potřebám vaší třemi způsoby:

* Přidejte jednotky ve službě IoT hub. Každý další jednotky ve B1 IoT hub například umožňuje další 400 000 zpráv za den. 
* Změníte velikost služby IoT hub. Například migrujte z vrstvy B1 do vrstvy B2 zvýšit množství zpráv, které podporují jednotlivé jednotky za den.
* Upgrade na vyšší úroveň. Například upgradujte z vrstvy B1 k vrstvě S1 pro zasílání zpráv kapacitu, ale s pokročilé funkce, které jsou ve standardní vrstvě.

Tyto změny můžou nastat bez přerušení existující operace.

Pokud chcete přejít na starší verzi služby IoT hub, můžete odebrat jednotky a zmenšit velikost služby IoT hub. Není ale možné snížit nižší úrovně. Můžete například přesunout z vrstvy S2 k vrstvě S1, ale nikoli z vrstvy S2 do vrstvy B1. 

Tyto příklady jsou určené vám pomohou pochopit, jak upravit služby IoT hub jako řešení změny. Konkrétní informace o možnostech jednotlivých úrovní jste měli vždy najdete v části [ceny služby Azure IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/). 

## <a name="upgrade-your-existing-iot-hub"></a>Upgrade stávající služby IoT hub 

1. Přihlaste se k [portál Azure](https://portal.azure.com/) a přejděte do služby IoT hub. 
2. Vyberte **cenovou a škálovací**. 

   ![Ceny a škálování](./media/iot-hub-upgrade/pricing-scale.png)

3. Chcete-li změnit úroveň pro vaše centrum, vyberte **cenovou a škálovací úroveň**. Vyberte novou vrstvu a pak klikněte na **vyberte**.

   ![Ceny a škálování](./media/iot-hub-upgrade/select-tier.png)

4. Chcete-li změnit počet jednotek v centru, zadejte novou hodnotu v rámci **jednotky služby IoT Hub**. 
5. Vyberte **Uložit** uložte provedené změny. 

Služby IoT hub je nyní upravit, a vaše konfigurace jsou stejné. 

## <a name="next-steps"></a>Další postup

Získání dalších podrobností [jak vybrat správné úrovně služby IoT Hub](iot-hub-scaling.md). 

