---
title: Referenční architektura pro řešení monitorování spotřeby vody vytvořená pomocí Azure IoT Central| Dokumenty společnosti Microsoft
description: Seznamte se s koncepty pro řešení monitorování spotřeby vody vytvořené pomocí Azure IoT Central.
author: miriambrus
ms.author: miriamb
ms.date: 10/23/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 3a64ca5b0c2a092f895873e097ea6beb9a235a37
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77017696"
---
# <a name="water-consumption-monitoring-reference-architecture"></a>Referenční architektura monitorování spotřeby vody 



Řešení monitorování spotřeby vody můžete sestavit pomocí **šablony aplikace Azure IoT Central** jako aplikace IoT pro spuštění. Tento článek obsahuje pokyny pro referenční architekturu na vysoké úrovni při vytváření komplexního řešení. 

![Architektura monitorování spotřeby vody](./media/concepts-waterconsumptionmonitoring-architecture/concepts-waterconsumptionmonitoring-architecture1.png)

Koncepty:

1. Zařízení a konektivita  
1. IoT Central 
2. Rozšiřitelnost a integrace
3. Obchodní aplikace

Podívejme se na klíčové komponenty, které obecně hrají roli v řešení monitorování spotřeby vody.

## <a name="devices-and-connectivity"></a>Zařízení a konektivita 
Tato část se týká zařízení používaných pro inteligentní vodní řešení, jako je monitorování kvality vody nebo monitorování spotřeby vody, obecně jako inteligentní vodní zařízení. Inteligentní vodní zařízení mohou být průtokoměry, monitory kvality vody, inteligentní ventily, detektory netěsnosti a tak dále.

Zařízení používaná v inteligentních vodních řešeních budou obecně připojena prostřednictvím sítí LPWAN s nízkou spotřebou energie prostřednictvím operátora sítě třetí strany. Pro tyto typy zařízení můžete použít [Azure IoT Central Device Bridge](https://docs.microsoft.com/azure/iot-central/core/howto-build-iotc-device-bridge) k odeslání dat zařízení do aplikace IoT v Azure IoT Central. Případně můžete mít brány zařízení, které jsou ip schopné a můžete se připojit přímo k IoT Central.

## <a name="iot-central"></a>IoT Central 
Azure IoT Central je platforma ioT aplikací, která vám rychle zprovozní vaše řešení IoT. Řešení můžete označit, přizpůsobit a integrovat se službami třetích stran.
Po připojení inteligentních vodních zařízení k IoT Central získáte příkaz a řízení zařízení, monitorování a upozorňování, uživatelské rozhraní s vestavěným RBAC, konfigurovatelné řídicí panely přehledů a možnosti rozšiřitelnosti. 


## <a name="extensibility-and-integrations"></a>Rozšiřitelnost a integrace 
Aplikaci IoT můžete rozšířit ve IoT Central a volitelně:
* transformovat a integrovat data IoT pro pokročilé analýzy, například školení modelů strojového učení, prostřednictvím nepřetržitého exportu dat z aplikace IoT Central
* automatizujte pracovní postupy v jiných systémech spuštěním akcí pomocí Microsoft Flow nebo webhooků z aplikace IoT Central
* programově přistupovat k aplikaci IoT v IoT Central prostřednictvím centrálních api IoT

## <a name="business-applications"></a>Podnikové aplikace 
Data IoT lze použít k napájení různých druhů obchodních aplikací v rámci vodního nástroje. Chcete-li se dozvědět, jak propojit aplikaci pro monitorování spotřeby vody ioT Central se službami v terénu, postupujte podle pokynů [o integraci se službami Dynamics 365 Field Services](./how-to-configure-connected-field-services.md) 


## <a name="next-steps"></a>Další kroky
* Naučte se [vytvářet aplikaci IoT Central spotřebou vody](./tutorial-water-consumption-monitoring.md)
* Další informace o [šablonách centrálních vládních ioT](./overview-iot-central-government.md)
* Další informace o IoT Central najdete v tématu [Přehled IoT Central](https://docs.microsoft.com/azure/iot-central/core/overview-iot-central)
