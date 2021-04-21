---
title: Vydavatel Microsoft OPC
description: Tento článek poskytuje přehled modulu OPC Publisher Edge.
author: v-condav
ms.author: jemorina
ms.service: industrial-iot
ms.topic: conceptual
ms.date: 3/22/2021
ms.openlocfilehash: 6df39c93e9bcfca522ac61a863c87269216cc592
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816165"
---
# <a name="what-is-the-opc-publisher"></a>Co je Vydavatel OPC?

Vydavatel OPC je plně podporovaný produkt Microsoftu, který prochází mezeru mezi průmyslovými prostředky a cloudem Microsoft Azure. Provedete to tak, že k vašemu Microsoft Azure cloudu připojíte prostředky s podporou OPC UA nebo software pro průmyslové připojení. Publikuje data telemetrie do Azure IoT Hub v různých formátech, včetně standardního formátu IEC62541 OPC UA PubSub (verze 2,6 a vyšší). OPC Publisher běží na Azure IoT Edge jako modul nebo v jednoduchém Docker jako kontejner. Protože využívá modul runtime pro více platforem .NET, běží nativně na systémech Linux i Windows 10.

Vydavatel OPC je referenční implementace, která ukazuje, jak:

- Připojte se k existujícím serverům OPC UA.
- Publikujte data telemetrie kódovaná pomocí JSON ze serverů OPC UA ve formátu Pub/sub v OPC UA a použijte datovou část JSON pro IoT Hub Azure.

Můžete použít libovolný přenosový protokol, který podporuje klientská sada SDK pro Azure IoT Hub, jako je například HTTPS, AMQP a MQTT.

Referenční implementace zahrnuje následující.

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
