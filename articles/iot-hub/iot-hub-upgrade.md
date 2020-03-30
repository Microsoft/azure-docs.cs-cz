---
title: Upgrade služby Azure IoT Hub | Dokumenty společnosti Microsoft
description: Změňte úroveň cen a škálování pro Službu IoT Hub, abyste získali další možnosti správy zpráv a zařízení.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: robinsh
ms.openlocfilehash: 96c3a7b2cfda23f173f4caeff4fb7a92b1ddc438
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "61440203"
---
# <a name="how-to-upgrade-your-iot-hub"></a>Postup upgradu služby IoT Hub

Jak vaše řešení IoT roste, Azure IoT Hub je připraven vám pomůže vertikálně navýšit kapacitu. Azure IoT Hub nabízí dvě úrovně, základní (B) a standardní (S), aby vyhovovaly zákazníkům, kteří chtějí používat různé funkce. V rámci každé vrstvy jsou tři velikosti (1, 2 a 3), které určují počet zpráv, které mohou být odeslány každý den.

Pokud máte víc zařízení a potřebujete víc možností, existují tři způsoby, jak upravit centrum IoT tak, aby vyhovovalo vašim potřebám:

* Přidejte jednotky v rámci centra IoT. Například každá další jednotka v centru B1 IoT umožňuje dalších 400 000 zpráv za den.

* Změňte velikost centra IoT hub. Například migrujte z úrovně B1 na úroveň B2, abyste zvýšili počet zpráv, které může každá jednotka podporovat za den.

* Upgradujte na vyšší úroveň. Například upgrade z úrovně B1 na úroveň S1 pro přístup k pokročilým funkcím se stejnou kapacitou zasílání zpráv.

Všechny tyto změny mohou nastat bez přerušení existujících operací.

Pokud chcete snížit úroveň vašeho centra IoT hub, můžete odebrat jednotky a zmenšit velikost centra IoT hub, ale nemůžete přejít na nižší úroveň. Můžete například přejít z úrovně S2 na úroveň S1, ale ne z úrovně S2 na úroveň B1. Pro centrum IoT Hub lze vybrat jenom jeden typ [edice Iot Hub](https://azure.microsoft.com/pricing/details/iot-hub/) v rámci úrovně. Můžete například vytvořit ioT hub s více jednotkami S1, ale ne se kombinací jednotek z různých edic, jako jsou S1 a B3 nebo S1 a S2.

Tyto příklady jsou určeny k vám pomůže pochopit, jak upravit centrum IoT při změně vašeho řešení. Konkrétní informace o možnostech jednotlivých úrovní byste měli vždy odkazovat na [ceny služby Azure IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/).

## <a name="upgrade-your-existing-iot-hub"></a>Upgrade stávajícího centra IoT hub

1. Přihlaste se k [portálu Azure](https://portal.azure.com/) a přejděte do svého centra IoT hub.

2. Vyberte **Ceny a měřítko**.

   ![Ceny a škálování](./media/iot-hub-upgrade/pricing-scale.png)

3. Pokud chcete změnit úroveň svého centra, vyberte **Ceny a škálovat úroveň**. Vyberte novou úroveň a klikněte na **vybrat**.

   ![Cenová úroveň a úroveň škálování](./media/iot-hub-upgrade/select-tier.png)

4. Chcete-li změnit počet jednotek v rozbočovači, zadejte novou hodnotu do **jednotek služby IoT Hub**.

5. Vyberte **Uložit** a uložte tak provedené změny.

Váš ioT hub je teď upravený a vaše konfigurace se nezmění.

Maximální limit oddílu pro základní úroveň IoT Hub a standardní úroveň IoT Hub je 32. Většina ioT hubů potřebuje jenom 4 oddíly. Limit oddílu je vybrán při vytvoření služby IoT Hub a týká se zpráv zařízení cloud s počtem současných čtenářů těchto zpráv. Tato hodnota zůstane beze změny při migraci ze základní vrstvy na úroveň standard.

## <a name="next-steps"></a>Další kroky

Získejte další podrobnosti o [tom, jak vybrat správnou úroveň IoT Hub](iot-hub-scaling.md).