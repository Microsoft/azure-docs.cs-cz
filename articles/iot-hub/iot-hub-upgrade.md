---
title: Upgradovat Azure IoT Hub | Microsoft Docs
description: Změnou úrovně cen a škálování IoT Hub získáte další možnosti správy zpráv a zařízení.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: robinsh
ms.openlocfilehash: 96c3a7b2cfda23f173f4caeff4fb7a92b1ddc438
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "61440203"
---
# <a name="how-to-upgrade-your-iot-hub"></a>Postup upgradu služby IoT Hub

Jak vaše řešení IoT roste, je Azure IoT Hub připraven, abychom vám pomohli škálovat. Azure IoT Hub nabízí dvě úrovně Basic (B) a Standard (y), které budou vyhovovat zákazníkům, kteří chtějí používat různé funkce. V rámci každé úrovně jsou tři velikosti (1, 2 a 3), které určují počet zpráv, které se dají každý den odeslat.

Pokud máte více zařízení a potřebujete více možností, můžete Centrum IoT upravit tak, aby vyhovovalo vašim potřebám:

* Přidejte jednotky ve službě IoT Hub. Například každá další jednotka v centru IoT Hub umožňuje další zprávy 400 000 za den.

* Změňte velikost centra IoT. Například migrujete z vrstvy B1 na úroveň B2 a zvyšte počet zpráv, které mohou jednotlivé jednotky podporovat za den.

* Upgradujte na vyšší úroveň. Například upgrade z vrstvy B1 na úroveň S1 pro přístup k pokročilým funkcím se stejnou kapacitou pro zasílání zpráv.

K těmto změnám může dojít, aniž by došlo k přerušení stávajících operací.

Pokud chcete downgrade ve službě IoT Hub, můžete odebrat jednotky a zmenšit velikost služby IoT Hub, ale nemůžete downgradovat na nižší úroveň. Můžete například přesunout z vrstvy S2 do vrstvy S1, ale ne z vrstvy S2 do vrstvy B1. Pro jednu IoT Hub lze vybrat pouze jeden typ [edice služby IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/) v rámci jedné úrovně. Můžete například vytvořit IoT Hub s více jednotkami S1, ale ne se směsí jednotek z různých edic, například S1 a B3 nebo S1 a S2.

Tyto příklady jsou určené k tomu, aby vám pomohly pochopit, jak vaše řešení mění vaše centrum IoT. Konkrétní informace o možnostech jednotlivých úrovní najdete v tématu [ceny služby Azure IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/).

## <a name="upgrade-your-existing-iot-hub"></a>Upgrade stávajícího centra IoT Hub

1. Přihlaste se k [Azure Portal](https://portal.azure.com/) a přejděte do služby IoT Hub.

2. Vyberte **ceny a škálování**.

   ![Ceny a škálování](./media/iot-hub-upgrade/pricing-scale.png)

3. Pokud chcete změnit úroveň svého centra, vyberte **ceny a úroveň škálování**. Zvolte novou vrstvu a pak klikněte na **Vybrat**.

   ![Cenová úroveň a úroveň škálování](./media/iot-hub-upgrade/select-tier.png)

4. Pokud chcete změnit počet jednotek ve vašem centru, zadejte novou hodnotu pod **IoT Hub jednotky**.

5. Vyberte **Uložit** a uložte tak provedené změny.

Vaše centrum IoT se teď upraví a vaše konfigurace se nezměnily.

Maximální omezení oddílu IoT Hub úrovně Basic a úrovně Standard IoT Hub je 32. Většina rozbočovačů IoT potřebuje jenom 4 oddíly. Limit oddílu se volí při vytvoření IoT Hub a týká se zpráv typu zařízení-Cloud s počtem souběžných čtenářů těchto zpráv. Tato hodnota zůstává beze změny při migraci z úrovně Basic na úroveň Standard.

## <a name="next-steps"></a>Další kroky

Získejte další informace o [tom, jak zvolit správnou IoT Hub vrstvu](iot-hub-scaling.md).