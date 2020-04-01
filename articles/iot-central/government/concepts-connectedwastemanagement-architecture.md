---
title: Referenční architektura pro řešení propojeného nakládání s odpady vytvořená pomocí Azure IoT Central| Dokumenty společnosti Microsoft
description: Seznamte se s koncepty propojeného řešení pro nakládání s odpady, které je vytvořeno s Azure IoT Central.
author: miriambrus
ms.author: miriamb
ms.date: 10/23/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 763fb82e0725e5a98112af3a79856c3703a5c2c3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77016081"
---
# <a name="connected-waste-monitoring-reference-architecture"></a>Referenční architektura propojeného monitorování odpadů 



Propojené řešení pro nakládání s odpady můžete sestavit pomocí **šablony aplikace Azure IoT Central** jako aplikace IoT pro spuštění. Tento článek obsahuje pokyny pro referenční architekturu na vysoké úrovni při vytváření komplexního řešení. 

![Propojená architektura nakládání s odpady](./media/concepts-connectedwastemanagement-architecture/concepts-connectedwastemanagement-architecture1.png)


Koncepty:

1. Zařízení a konektivita  
1. IoT Central 
2. Rozšiřitelnost a integrace
3. Obchodní aplikace

Podívejme se na klíčové komponenty, které obecně hrají roli v řešení monitorování spotřeby vody.

## <a name="devices-and-connectivity"></a>Zařízení a konektivita 
Zařízení používaná v otevřených prostředích, jako jsou odpadkové koše, mohou být připojena prostřednictvím sítí LPWAN (LOW-Power Wide Area Networks) prostřednictvím operátora sítě třetí strany. Pro tyto typy zařízení můžete použít [Azure IoT Central Device Bridge](https://docs.microsoft.com/azure/iot-central/core/howto-build-iotc-device-bridge) k odeslání dat zařízení do aplikace IoT v Azure IoT Central. Případně můžete mít brány zařízení, které jsou ip schopné a můžete se připojit přímo k IoT Central.

## <a name="iot-central"></a>IoT Central 
Azure IoT Central je platforma ioT aplikací, která vám rychle zprovozní vaše řešení IoT. Řešení můžete označit, přizpůsobit a integrovat se službami třetích stran.
Po připojení inteligentních vodních zařízení k IoT Central získáte příkaz a řízení zařízení, monitorování a upozorňování, uživatelské rozhraní s vestavěným RBAC, konfigurovatelné řídicí panely přehledů a možnosti rozšiřitelnosti. 

## <a name="extensibility-and-integrations"></a>Rozšiřitelnost a integrace 
Aplikaci IoT můžete rozšířit ve IoT Central a volitelně:
* transformujte a integrujte data IoT pro pokročilé analýzy, například tréninkové modely strojového učení, prostřednictvím průběžného exportu dat z aplikace IoT Central. 
* automatizujte pracovní postupy v jiných systémech spuštěním akcí pomocí Microsoft Flow nebo webhooků z aplikace IoT Central
* programově přistupovat k aplikaci IoT v IoT Central prostřednictvím ioT centrální chod api.

## <a name="business-applications"></a>Obchodní aplikace 
Data IoT lze použít k napájení různých obchodních aplikací v rámci nástroje pro nakládání s odpady. Chcete-li se dozvědět, jak propojit aplikaci pro nakládání s odpady propojenou službou IoT Central se službami v terénu, postupujte podle pokynů [o integraci se službami Dynamics 365 Field Services](./how-to-configure-connected-field-services.md) 

## <a name="next-steps"></a>Další kroky
* Naučte se [vytvářet propojenou aplikaci](./tutorial-connected-waste-management.md) IoT Central pro nakládání s odpady
* Další informace o [šablonách centrálních vládních ioT](./overview-iot-central-government.md)
* Další informace o IoT Central najdete v tématu [Přehled IoT Central](https://docs.microsoft.com/azure/iot-central/core/overview-iot-central)

