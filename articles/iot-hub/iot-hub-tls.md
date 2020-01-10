---
title: Podpora Azure IoT Hub TLS
description: Osvědčené postupy při použití zabezpečených připojení TLS pro zařízení a služby komunikující s IoT Hub
services: iot-hub
author: rezasherafat
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 01/10/2020
ms.author: rezas
ms.custom: Azure IoT Hub TLS
ms.openlocfilehash: 62fdfc4277b44a502206eb37466e6409521186a5
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2020
ms.locfileid: "75839527"
---
# <a name="tls-support-in-iot-hub"></a>Podpora TLS v IoT Hub

IoT Hub používá protokol TLS (Transport Layer Security) k zabezpečení připojení ze zařízení a služeb IoT. V současné době jsou podporovány tři verze protokolu TLS, a to verze 1,0, 1,1 a 1,2.

TLS 1,0 a 1,1 se považují za starší verze a [plánuje](./tls-1.2-everywhere.md)se jejich vyřazení. Při připojování k IoT Hub se proto doporučuje používat protokol TLS 1,2 jako upřednostňovaná verze protokolu TLS.


## <a name="restrict-connections-to-tls-12-in-your-iot-hub-resource"></a>Omezení připojení k TLS 1,2 v prostředku IoT Hub

Pro zvýšení zabezpečení doporučujeme nakonfigurovat vaše centra IoT tak, aby povolovala _jenom_ připojení klientů, která používají protokol TLS verze 1,2, a vynutili používání [doporučených šifr](#recommended-ciphers).

Pro tento účel zřiďte novou IoT Hub v některé z [podporovaných oblastí](#supported-regions) a nastavte vlastnost `minTlsVersion` na `1.2` ve specifikaci prostředků služby IoT Hub vaší šablony Azure Resource Manager:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.Devices/IotHubs",
            "apiVersion": "2020-01-01",
            "name": "<provide-a-valid-resource-name>",
            "location": "<any-of-supported-regions-below>",
            "properties": {
                "minTlsVersion": "1.2"
            },
            "sku": {
                "name": "<your-hubs-SKU-name>",
                "tier": "<your-hubs-SKU-tier>",
                "capacity": 1
            }
        }
    ]
}
```

Vytvořený prostředek IoT Hub pomocí této konfigurace odmítne klienty zařízení a služeb, kteří se pokusí připojit pomocí protokolu TLS verze 1,0 a 1,1. Podobně platí, že pokud zpráva HELLo klienta neuvádí žádné [Doporučené šifry](#recommended-ciphers), dojde k odmítnutí handshake TLS.

Všimněte si, že vlastnost `minTlsVersion` je určena jen pro čtení a nelze ji změnit po vytvoření prostředku IoT Hub. Proto je důležité, abyste správně otestovali a ověřili, že _všechna_ vaše zařízení a služby IoT jsou kompatibilní s TLS 1,2 a předem [doporučenými šiframi](#recommended-ciphers) .


### <a name="supported-regions"></a>Podporované oblasti

Centra IoT, která vyžadují použití protokolu TLS 1,2, se dají vytvořit v následujících oblastech:

* Východní USA
* Středojižní USA
* Západní USA 2

> [!NOTE]
> Po převzetí služeb při selhání bude vlastnost `minTlsVersion` vašich IoT Hub platit i po převzetí služeb při selhání v geograficky spárované oblasti.



### <a name="recommended-ciphers"></a>Doporučené šifry

Centra IoT, která jsou nakonfigurovaná tak, aby přijímala jenom TLS 1,2, taky vynutila použití následujících doporučených šifr:

* `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`


### <a name="use-tls-12-in-your-iot-edge-setup"></a>Použití TLS 1,2 v nastavení IoT Edge

IoT Edge zařízení je možné nakonfigurovat tak, aby při komunikaci s IoT Hub používala TLS 1,2. Pro účely tohoto účelu použijte [stránku dokumentace IoT Edge](https://github.com/Azure/iotedge/blob/master/edge-modules/edgehub-proxy/README.md).