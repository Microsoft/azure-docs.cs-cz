---
title: Referenční architektura pro řešení pro správu propojených odpadů vytvořená s využitím Azure IoT Central | Microsoft Docs
description: Seznamte se s koncepty řešení pro správu propojených odpadů sestavených s využitím Azure IoT Central.
author: miriambrus
ms.author: miriamb
ms.date: 10/23/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 4eb21ca8bc3f2c96cfdaceb5c8bfc3343f138b23
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2019
ms.locfileid: "72956814"
---
# <a name="connected-waste-monitoring-reference-architecture"></a>Referenční architektura monitorování propojených odpadů 


Řešení pro správu propojených odpadů se dá vytvořit pomocí **šablony aplikace Azure IoT Central** jako aplikace IoT Starter. Tento článek poskytuje podrobné pokyny k architektuře referenční architektury pro vytváření kompletního řešení. 

![Architektura správy propojených odpadů](./media/concepts-connectedwastemanagement-architecture/concepts-connectedwastemanagement-architecture1.png)


Koncepty:

1. Zařízení a připojení  
1. IoT Central 
2. Rozšiřitelnost a integrace
3. Obchodní aplikace

Pojďme se podívat na klíčové komponenty, které obecně hrají součást v řešení monitorování spotřeby vody.

## <a name="devices-and-connectivity"></a>Zařízení a připojení 
Zařízení používaná v otevřených prostředích, jako jsou odpadní přihrádky, můžou být obecně připojená prostřednictvím sítě s nízkou spotřebou (LPWAN) prostřednictvím operátoru sítě třetí strany. U těchto typů zařízení můžete využít [most služby Azure IoT Central](https://docs.microsoft.com/azure/iot-central/howto-build-iotc-device-bridge) k posílání dat zařízení do vaší aplikace IoT ve službě Azure IoT Central. Alternativně můžete mít brány zařízení, které jsou schopné IP a můžou se přímo připojit k IoT Central.

## <a name="iot-central"></a>IoT Central 
Azure IoT Central je aplikační platforma IoT, která vám umožní rychle začít pracovat v řešení IoT. Vaše řešení můžete označit, přizpůsobit a integrovat s službami třetích stran.
Po připojení inteligentních zařízení k IoT Central získáte příkazy a ovládání zařízení, monitorování a upozorňování, uživatelské rozhraní s integrovanými RBACmi, konfigurovatelnými řídicími panely a možnostmi rozšíření. 

## <a name="extensibility-and-integrations"></a>Rozšiřitelnost a integrace 
Aplikaci IoT můžete v IoT Central zvětšit a volitelně:
* pomocí průběžného exportu dat z aplikace IoT Central můžete transformovat a integrovat data IoT pro pokročilé analýzy, například školení modelů strojového učení. 
* automatizace pracovních postupů v jiných systémech aktivací akcí prostřednictvím Microsoft Flow nebo webhooků z aplikace IoT Central
* programově se k vaší aplikaci IoT v IoT Central prostřednictvím rozhraní API IoT Central.

## <a name="business-applications"></a>Obchodní aplikace 
Data IoT se dají použít k napájení nejrůznějších podnikových aplikací v rámci programu odpadního prostředí. Další informace o tom, jak připojit aplikaci pro správu s připojeným odpadem IoT Central k polím služby, najdete v kurzu [integrace s poli služby Dynamics 365](./how-to-configure-connected-field-services.md) . 

## <a name="next-steps"></a>Další kroky
* Zjistěte, jak [vytvořit propojenou správu odpadu](./tutorial-connected-waste-management.md) IoT Central aplikaci.
* Další informace o [IoT Central státních šablonách](./overview-iot-central-government.md)
* Další informace o IoT Central najdete v tématu [IoT Central Overview](https://docs.microsoft.com/azure/iot-central/overview-iot-central) .

