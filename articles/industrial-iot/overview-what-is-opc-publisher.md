---
title: Vydavatel Microsoft OPC
description: Tento článek poskytuje přehled modulu OPC Publisher Edge.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: conceptual
ms.date: 3/22/2021
ms.openlocfilehash: 3a44bdbadfe6ecd86a1b98fb7002f2d75c23bb6a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104800529"
---
# <a name="what-is-the-opc-publisher"></a>Co je Vydavatel OPC?

Vydavatel OPC je plně podporovaný produkt společnosti Microsoft vyvinutý v otevřeném prostředí, který přemostěníuje mezeru mezi průmyslovými prostředky a cloudem Microsoft Azure. Provedete to tak, že se připojíte k prostředkům s podporou OPC UA nebo softwaru pro průmyslové připojení a publikujete data telemetrie do Azure IoT Hub v různých formátech, včetně IEC62541 OPC UA PubSub Standard (od verze 2,6 a vyšší).

Spouští se v Azure IoT Edge jako modul nebo v jednoduchém Docker jako kontejner. Vzhledem k tomu, že využívá modul runtime pro více platforem .NET, běží také nativně v systémech Linux a Windows 10.

Vydavatel OPC je referenční implementace, která ukazuje, jak:

- Připojte se k existujícím serverům OPC UA.
- Publikujte data telemetrie kódovaná pomocí JSON ze serverů OPC UA v OPC UA/sub Format s použitím datové části JSON do Azure IoT Hub.

Můžete použít libovolný přenosový protokol, který podporuje klientská sada SDK pro Azure IoT Hub: HTTPS, AMQP a MQTT.

Referenční implementace zahrnuje:

- *Klient* OPC UA pro připojení k existujícím SERVERŮM OPC UA, které máte ve vaší síti.
- *Server* OPC ua na portu 62222, který můžete použít ke správě co publikovaných a nabízí IoT Hub přímých metod, které se mají provést.

[Referenční implementaci pro vydavatele OPC](https://github.com/Azure/iot-edge-opc-publisher) si můžete stáhnout z GitHubu.

Aplikace je implementována pomocí technologie .NET Core a může běžet na libovolné platformě, kterou podporuje .NET Core.

## <a name="what-does-the-opc-publisher-do"></a>Co OPC Publisher?

OPC Publisher implementuje logiku opakování pro navázání připojení k koncovým bodům, které nereagují na určitý počet požadavků Keep Alive. Například pokud server OPC UA přestane reagovat z důvodu výpadku napájení.

U každého jedinečného intervalu publikování na server OPC UA vytvoří aplikace samostatné předplatné, přes které se aktualizují všechny uzly s tímto intervalem publikování.

Vydavatel OPC podporuje dávkování dat odesílaných do IoT Hub, aby se snížilo zatížení sítě. Tato dávkování odešle paket, který se IoT Hub jenom v případě dosažení nakonfigurované velikosti paketu.

Tato aplikace používá referenční zásobník OPC Foundation OPC UA jako balíčky NuGet. Licenční podmínky najdete v tématu [https://opcfoundation.org/license/redistributables/1.3/](https://opcfoundation.org/license/redistributables/1.3/) .

## <a name="next-steps"></a>Další kroky
Teď, když jste se seznámili s tím, co je Vydavatel OPC, můžete začít nasazením:

> [!div class="nextstepaction"]
> [Nasazení vydavatele OPC v samostatném režimu](tutorial-publisher-deploy-opc-publisher-standalone.md)
