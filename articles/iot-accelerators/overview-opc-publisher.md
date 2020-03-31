---
title: Co je Vydavatel OPC – Azure | Dokumenty společnosti Microsoft
description: Tento článek obsahuje přehled funkcí Vydavatele OPC. Umožňuje publikovat kódovaná telemetrická data JSON pomocí datové části JSON do služby Azure IoT Hub.
author: dominicbetts
ms.author: dobett
ms.date: 06/10/2019
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 2310a81d3cfaeff203134af9968bc2d5caea3e9c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "73824783"
---
# <a name="what-is-opc-publisher"></a>Co je Vydavatel OPC?

OPC Publisher je referenční implementace, která ukazuje, jak:

- Připojte se ke stávajícím serverům OPC UA.
- Publikujte telemetrická data kódovaná JSON ze serverů OPC UA ve formátu OPC UA Pub/Sub pomocí datové části JSON do azure IoT hubu.

Můžete použít libovolný přenosový protokol, který podporuje sada Azure IoT Hub client SDK: HTTPS, AMQP a MQTT.

Referenční implementace zahrnuje:

- Klient OPC *UA* pro připojení ke stávajícím serverům OPC UA, které máte v síti.
- Server OPC *UA* na portu 62222, který můžete použít ke správě publikovaného publikovaného a nabízí přímé metody služby IoT Hub, které mají provést totéž.

Můžete si stáhnout [referenční implementaci OPC Publisher](https://github.com/Azure/iot-edge-opc-publisher) z GitHubu.

Aplikace je implementována pomocí technologie .NET Core a může běžet na libovolné platformě podporované rozhraním .NET Core.

OPC Publisher implementuje logiku opakování k navázání připojení ke koncovým bodům, které nereagují na určitý počet požadavků keep alive. Například pokud server OPC UA přestane reagovat z důvodu výpadku napájení.

Pro každý odlišný interval publikování na serveru OPC UA aplikace vytvoří samostatné předplatné, přes které jsou aktualizovány všechny uzly s tímto intervalem publikování.

Vydavatel OPC podporuje dávkování dat odeslaných do služby IoT Hub za účelem snížení zatížení sítě. Toto dávkování odešle paket do služby IoT Hub pouze v případě, že je dosaženo nakonfigurované velikosti paketu.

Tato aplikace používá Reference Zásobník OPC OPC OA jako Balíčky NuGet. Viz [https://opcfoundation.org/license/redistributables/1.3/](https://opcfoundation.org/license/redistributables/1.3/) licenční podmínky.

### <a name="next-steps"></a>Další kroky

Nyní jste se dozvěděli, co Je OPC Publisher, navrhovaný další krok je naučit se [konfigurovat OPC Publisher](howto-opc-publisher-configure.md).
