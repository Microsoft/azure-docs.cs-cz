---
title: Platforma Azure pro průmyslové IoT
description: Tento článek poskytuje přehled platformy IoT pro průmysl a její komponenty.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: conceptual
ms.date: 3/22/2021
ms.openlocfilehash: 24932b17c630190cb3121d5310a865758fa6a920
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104801549"
---
# <a name="what-is-the-industrial-iot-iiot-platform"></a>Co je platforma pro průmyslový IoT (IIoT)?

Platforma Azure pro průmyslové IoT je sada modulů a služeb od Microsoftu, které jsou nasazené v Azure. V těchto modulech a službách se plně vystavila otevřená. Konkrétně použijeme nabídku spravované platformy jako služby (PaaS) Azure, open source software licencovaný prostřednictvím licence MIT, otevřete mezinárodní standardy pro komunikaci (OPC UA, AMQP, MQTT, HTTP) a rozhraní (Open API) a otevřete modely průmyslových datových modelů (OPC UA) na hraničních zařízeních a v cloudu.

## <a name="enabling-shopfloor-connectivity"></a>Povolení připojení provozní 

Platforma Azure pro průmyslové IoT pokrývá průmyslové připojení prostředků Provozní (včetně zjišťování prostředků s povoleným OPC UA), normalizuje jejich data ve formátu OPC UA a přenáší data telemetrie assetů do Azure ve formátu OPC UA PubSub. Tam ukládá data telemetrie do cloudové databáze. Kromě toho platforma umožňuje zabezpečený přístup k prostředkům provozní prostřednictvím OPC UA z cloudu. Integrované jsou taky možnosti správy zařízení (včetně konfigurace zabezpečení). Funkce OPC UA se vytvořila pomocí technologie Docker Container k snadnému nasazení a správě. U prostředků, které nejsou s povoleným OPC UA, jsme spolupracuje s předními poskytovateli pro průmyslové připojení a pomohl jim portovat software adaptéru OPC UA, aby Azure IoT Edge. Tyto adaptéry jsou k dispozici v Azure Marketplace.

## <a name="industrial-iot-components-iot-edge-modules-and-cloud-microservices"></a>Komponenty pro průmyslové IoT: IoT Edge moduly a cloudové mikroslužby

Hraniční služby se implementují jako Azure IoT Edge moduly a běží místně. Cloudové mikroslužby jsou implementovány jako mikroslužby ASP.NET s rozhraním REST a běží na spravovaných službách Azure Kubernetes nebo samostatně na Azure App Service. U hraničních i cloudových služeb jsme na Microsoft Container Registry (MCR) zavedli předem připravené kontejnery Docker a odebrali jsme tento krok pro zákazníka. Hraniční a cloudové služby se vzájemně využívají a musí se používat společně. K dispozici jsme také snadno použitelné skripty nasazení, které umožňují nasadit celou platformu jediným příkazem.

## <a name="next-steps"></a>Další kroky

Teď, když jste se seznámili s tím, co je platforma Azure pro průmyslové IoT, se můžete dozvědět o vydavateli OPC:

> [!div class="nextstepaction"]
> [Co je Vydavatel OPC?](overview-what-is-opc-publisher.md)