---
title: Referenční architektura pro řešení pro správu propojených odpadů vytvořená s využitím Azure IoT Central | Microsoft Docs
description: Seznamte se s koncepty řešení pro správu propojených odpadů sestavených s využitím Azure IoT Central.
author: miriambrus
ms.author: miriamb
ms.date: 12/11/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 2d6c8c693db42789a965ee896a6d913d6ee20ae0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "99831667"
---
# <a name="connected-waste-monitoring-reference-architecture"></a>Referenční architektura propojeného monitorování odpadů 

Řešení pro správu propojených odpadů se dá vytvořit pomocí **šablony aplikace Azure IoT Central** jako aplikace IoT Starter. Tento článek poskytuje podrobné pokyny k architektuře referenční architektury pro vytváření kompletního řešení. 

![Architektura správy propojených odpadů](./media/concepts-connectedwastemanagement-architecture/concepts-connectedwastemanagement-architecture1.png)


Koncepty:

1. Zařízení a připojení  
1. IoT Central 
1. Rozšiřitelnost a integrace
1. Obchodní aplikace

Pojďme se podívat na klíčové komponenty, které obecně hrají součást v řešení monitorování spotřeby vody.

## <a name="devices-and-connectivity"></a>Zařízení a připojení 
Zařízení používaná v otevřených prostředích, jako jsou odpadní přihrádky, se můžou prostřednictvím operátoru sítě třetí strany propojit prostřednictvím sítě WAN (LPWAN) s nízkou spotřebou. U těchto typů zařízení můžete k posílání dat zařízení do vaší aplikace IoT v Azure IoT Central použít [most IoT Central pro zařízení Azure](../core/howto-build-iotc-device-bridge.md) . Alternativně můžete mít brány zařízení, které jsou schopné IP a můžou se přímo připojit k IoT Central.

## <a name="iot-central"></a>IoT Central 
Azure IoT Central je aplikační platforma IoT, která vám umožní rychle začít pracovat v řešení IoT. Vaše řešení můžete označit, přizpůsobit a integrovat s službami třetích stran.
Po připojení inteligentních zařízení k IoT Central získáte příkazy a ovládání zařízení, monitorování a upozorňování, uživatelské rozhraní s integrovanými RBACmi, konfigurovatelnými řídicími panely a možnostmi rozšiřitelnosti. 

## <a name="extensibility-and-integrations"></a>Rozšiřitelnost a integrace
Aplikaci IoT můžete v IoT Central zvětšit a volitelně:
* pomocí průběžného exportu dat z aplikace IoT Central můžete transformovat a integrovat data IoT pro pokročilé analýzy, například školení modelů strojového učení.
* automatizace pracovních postupů v jiných systémech aktivací akcí pomocí Power automatu nebo webhooků z IoT Central aplikace
* programově se k vaší aplikaci IoT v IoT Central prostřednictvím rozhraní API IoT Central.

## <a name="business-applications"></a>Obchodní aplikace 
Data IoT se dají použít k napájení nejrůznějších podnikových aplikací v rámci programu odpadního prostředí. Další informace o tom, jak připojit aplikaci pro správu s připojeným odpadem IoT Central k polím služby, najdete v kurzu [integrace s poli služby Dynamics 365](./how-to-configure-connected-field-services.md) . 

## <a name="next-steps"></a>Další kroky
* Zjistěte, jak [vytvořit propojenou správu odpadu](./tutorial-connected-waste-management.md) IoT Central aplikaci.
* Další informace o [IoT Central státních šablonách](./overview-iot-central-government.md)
* Další informace o IoT Central najdete v tématu [IoT Central Overview](../core/overview-iot-central.md) .
