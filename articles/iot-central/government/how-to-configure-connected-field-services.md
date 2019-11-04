---
title: Připojení aplikace Azure IoT Central k polím služby Dynamics 365 | Microsoft Docs
description: Naučte se vytvářet ucelená řešení s využitím Azure IoT Central a služby pole Dynamics 365.
author: miriambrus
ms.author: miriamb
ms.date: 10/23/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 957e9337bf8ea5941b140ba4f3266417d36df6a7
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498758"
---
# <a name="build-end-to-end-solution-with-azure-iot-central-and-dynamics-365-field-service"></a>Sestavení uceleného řešení s využitím Azure IoT Central a služby pole Dynamics 365 

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Jako tvůrce můžete povolit integraci vaší aplikace Azure IoT Central do jiných podnikových systémů. 


Například v řešení pro správu připojeného odpadu můžete optimalizovat odesílání vozíků kolekcí odpadků na základě dat ze senzorů IoT z připojených odpadkových přihrádek. V [aplikaci pro správu propojených odpadů v IoT Central](./tutorial-connected-waste-management.md) můžete nakonfigurovat pravidla a akce a nastavit je tak, aby aktivovaly vytváření výstrah ve službě Dynamics Field Service. Tento scénář se dá provést pomocí Microsoft Flow, kterou můžete nakonfigurovat přímo v IoT Central pro automatizaci pracovních postupů napříč aplikacemi a službami. Informace, které jsou založené na aktivitách služby v poli služby, se taky dají odeslat zpátky do Azure IoT Central. 

## <a name="how-to-connect-your-azure-iot-central-application-with-dynamics-365-field-services"></a>Jak připojit vaši aplikaci Azure IoT Central k polím služeb Dynamics 365 

Níže uvedené komplexní integrační procesy je možné snadno implementovat na základě čistého prostředí pro konfiguraci:
* Služba Azure IoT Central může odesílat informace o anomáliích zařízení do služby propojeného pole (jako upozornění IoT) pro diagnostiku.
* Služba připojeného pole může vytvořit případy nebo pracovní příkazy aktivované ze anomálií zařízení.
* Služba připojeného pole může naplánovat techniky na kontrolu, aby nedocházelo k výpadkům.
* Řídicí panel zařízení Azure IoT Central se dá aktualizovat o relevantní informace o službě a plánování.


## <a name="next-steps"></a>Další kroky
* Další informace o [IoT Central státních šablonách](./overview-iot-central-government.md)
* Další informace o [IoT Central](https://docs.microsoft.com/azure/iot-central/core/overview-iot-central)
* Další informace o [službách pro pole Dynamics 365](https://docs.microsoft.com/dynamics365/field-service/cfs-iot-overview)
