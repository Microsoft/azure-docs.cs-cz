---
title: Připojení aplikace Azure IoT Central k polím služby Dynamics 365 | Microsoft Docs
description: Naučte se vytvářet ucelená řešení s využitím Azure IoT Central a služby pole Dynamics 365.
author: miriambrus
ms.author: miriamb
ms.date: 12/11/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 1d098f56bbadfe115620580c8d93fb6dd021550d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97586670"
---
# <a name="build-end-to-end-solution-with-azure-iot-central-and-dynamics-365-field-service"></a>Sestavení uceleného řešení s využitím Azure IoT Central a služby pole Dynamics 365 
Jako tvůrce můžete povolit integraci vaší aplikace Azure IoT Central do jiných podnikových systémů. 

Například v řešení pro správu propojených odpadů můžete optimalizovat odesílání nákladových kolekcí odpadků. Optimalizace se dá udělat na základě dat ze senzorů IoT z připojených přihrádek odpadu. V [aplikaci pro správu propojených odpadů v IoT Central](./tutorial-connected-waste-management.md) můžete nakonfigurovat pravidla a akce a nastavit je tak, aby aktivovaly vytváření výstrah ve službě Dynamics Field Service. Tento scénář je možné provést pomocí Power Automate, který se nakonfiguruje přímo v IoT Central pro automatizaci pracovních postupů napříč aplikacemi a službami. Kromě toho je možné informace na základě aktivit služeb v poli služby odeslat zpět do Azure IoT Central. 

## <a name="how-to-connect-your-azure-iot-central-application-with-dynamics-365-field-services"></a>Jak připojit vaši aplikaci Azure IoT Central k polím služeb Dynamics 365 

Níže uvedené procesy integrace lze snadno implementovat na základě čistého prostředí pro konfiguraci:
* Služba Azure IoT Central může odesílat informace o anomáliích zařízení do služby propojeného pole (jako upozornění IoT) pro diagnostiku.
* Služba připojeného pole může vytvořit případy nebo pracovní příkazy aktivované ze anomálií zařízení.
* Služba připojeného pole může naplánovat techniky na kontrolu, aby nedocházelo k výpadkům.
* Řídicí panel zařízení Azure IoT Central se dá aktualizovat o relevantní informace o službě a plánování.


## <a name="next-steps"></a>Další kroky
* Další informace o [IoT Central státních šablonách](./overview-iot-central-government.md)
* Další informace o [IoT Central](../core/overview-iot-central.md)
* Další informace o [službách pro pole Dynamics 365](/dynamics365/field-service/cfs-iot-overview)
