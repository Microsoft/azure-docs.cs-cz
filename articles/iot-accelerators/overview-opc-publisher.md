---
title: Co je OPC Publisher – Azure | Microsoft Docs
description: Tento článek obsahuje přehled funkcí aplikace OPC Publisher. Umožňuje publikovat kódovaná data telemetrie JSON pomocí datové části JSON do Azure IoT Hub.
author: dominicbetts
ms.author: dobett
ms.date: 06/10/2019
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 49ca12ed4f408e2a3fce1c8e59f541778f35311e
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2021
ms.locfileid: "105646744"
---
# <a name="what-is-opc-publisher"></a>Co je OPC Publisher?

> [!IMPORTANT]
> I když aktualizujeme Tento článek, přečtěte si nejaktuálnější obsah v tématu [Azure Data IoT](https://azure.github.io/Industrial-IoT/) .

Vydavatel OPC je referenční implementace, která ukazuje, jak:

- Připojte se k existujícím serverům OPC UA.
- Publikujte data telemetrie kódovaná pomocí JSON ze serverů OPC UA v OPC UA/sub Format s použitím datové části JSON do Azure IoT Hub.

Můžete použít libovolný přenosový protokol, který podporuje klientská sada SDK pro Azure IoT Hub: HTTPS, AMQP a MQTT.

Referenční implementace zahrnuje:

- *Klient* OPC UA pro připojení k existujícím SERVERŮM OPC UA, které máte ve vaší síti.
- *Server* OPC ua na portu 62222, který můžete použít ke správě co publikovaných a nabízí IoT Hub přímých metod, které se mají provést.

[Referenční implementaci pro vydavatele OPC](https://github.com/Azure/iot-edge-opc-publisher) si můžete stáhnout z GitHubu.

Aplikace je implementována pomocí technologie .NET Core a může běžet na libovolné platformě, kterou podporuje .NET Core.

OPC Publisher implementuje logiku opakování pro navázání připojení k koncovým bodům, které nereagují na určitý počet požadavků Keep Alive. Například pokud server OPC UA přestane reagovat z důvodu výpadku napájení.

U každého jedinečného intervalu publikování na server OPC UA vytvoří aplikace samostatné předplatné, přes které se aktualizují všechny uzly s tímto intervalem publikování.

Vydavatel OPC podporuje dávkování dat odesílaných do IoT Hub, aby se snížilo zatížení sítě. Tato dávkování odešle paket, který se IoT Hub jenom v případě dosažení nakonfigurované velikosti paketu.

Tato aplikace používá referenční zásobník OPC Foundation OPC UA jako balíčky NuGet. Licenční podmínky najdete v tématu [https://opcfoundation.org/license/redistributables/1.3/](https://opcfoundation.org/license/redistributables/1.3/) .

## <a name="next-steps"></a>Další kroky

Nyní jste se seznámili s tím, co je Vydavatel OPC, což je doporučený další krok, kde se dozvíte, jak:

[Konfigurace vydavatele OPC](howto-opc-publisher-configure.md)