---
title: Co je vydavatel OPC – Azure | Dokumentace Microsoftu
description: Přehled vydavatele OPC
author: dominicbetts
ms.author: dobett
ms.date: 06/10/2019
ms.topic: overview
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 99b3f6b4495ae5981322c90fce251b9b0a18bb5d
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67450909"
---
# <a name="what-is-opc-publisher"></a>Co je vydavatel OPC?

Vydavatel OPC se referenční implementaci, který ukazuje, jak:

- Připojte se k existující servery OPC UA.
- Publikování JSON kódovaný telemetrických dat ze serverů OPC UA ve formátu OPC UA Pub/Sub, pomocí datovou část JSON pro službu Azure IoT Hub.

Můžete použít některý z přenosové protokoly, které podporuje Klientská sada SDK Azure IoT Hub: Protokol HTTPS, AMQP a protokolu MQTT.

Obsahuje referenční implementace:

- OPC UA *klienta* pro připojení k existující servery OPC UA, budete mít ve vaší síti.
- OPC UA *server* na portu 62222, můžete použít ke správě, co je publikována a nabízí služby IoT Hub přímé metody škol.

Můžete stáhnout [referenční implementace vydavatele OPC](https://github.com/Azure/iot-edge-opc-publisher) z Githubu.

Aplikace je implementována pomocí technologie .NET Core a můžou běžet na žádnou platformu podporovanou nástrojem .NET Core.

Vydavatel OPC implementuje logika opakovaných pokusů pro navázání připojení ke koncovým bodům, které není reagují na určitý počet keep alive požadavky. Například, pokud server OPC UA přestane reagovat kvůli výpadku napájení.

Pro každý jedinečných publikování interval k serveru OPC UA aplikace vytvoří samostatné předplatné, jehož prostřednictvím se aktualizují všechny uzly s Tento interval publikování.

Vydavatel OPC podporuje dávkování dat odesílaných do služby IoT Hub ke snížení zatížení sítě. Tento dávkování odešle paket do služby IoT Hub, pouze v případě, že je dosaženo velikost paketu nakonfigurované.

Tato aplikace používá odkaz na zásobníku OPC Foundation OPC UA jako balíčky NuGet. Zobrazit [ https://opcfoundation.org/license/redistributables/1.3/ ](https://opcfoundation.org/license/redistributables/1.3/) pro licenční podmínky.

### <a name="next-steps"></a>Další postup

Teď když jste se naučili, co je vydavatel OPC, navrhované dalším krokem je další způsob [konfigurace vydavatele OPC](howto-opc-publisher-configure.md).
