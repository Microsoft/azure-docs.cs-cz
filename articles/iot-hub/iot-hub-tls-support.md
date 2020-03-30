---
title: Podpora TLS služby Azure IoT Hub
description: Doporučené postupy při používání zabezpečených připojení TLS pro zařízení a služby komunikující se službou IoT Hub
services: iot-hub
author: rezasherafat
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 01/10/2020
ms.author: rezas
ms.openlocfilehash: 7ab3b48d22f116a707f68cbf6284928c7d2557e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409491"
---
# <a name="tls-support-in-iot-hub"></a>Podpora TLS v ioT hubu

IoT Hub používá zabezpečení transportní vrstvy (TLS) k zabezpečení připojení ze zařízení a služeb IoT. V současné době jsou podporovány tři verze protokolu TLS, konkrétně verze 1.0, 1.1 a 1.2.

TLS 1.0 a 1.1 jsou považovány za starší a jsou plánovány pro vyřazení. Další informace naleznete [v tématu Zastaralé TLS 1.0 a 1.1 pro IoT Hub](iot-hub-tls-deprecating-1-0-and-1-1.md). Důrazně doporučujeme použít TLS 1.2 jako upřednostňovanou verzi TLS při připojování k IoT Hubu.

## <a name="restrict-connections-to-tls-12-in-your-iot-hub-resource"></a>Omezení připojení k TLS 1.2 v prostředku služby IoT Hub

Pro zvýšení zabezpečení se doporučuje nakonfigurovat služby IoT Hubs *tak,* aby umožňovaly pouze klientská připojení používající protokol TLS verze 1.2 a vynucovaly použití [doporučených šifer](#recommended-ciphers).

Za tímto účelem zřiďte nové centrum IoT `minTlsVersion` Hub `1.2` v kterékoli z [podporovaných oblastí](#supported-regions) a nastavte vlastnost ve specifikaci prostředků centra IoT šablony Azure:

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

Vytvořený prostředek služby IoT Hub používající tuto konfiguraci odmítne klienty zařízení a služeb, kteří se pokoušejí připojit pomocí tls verzí 1.0 a 1.1. Podobně tls handshake bude odmítnut, pokud zpráva HELLO klienta neuvádí žádnou z [doporučených šifer](#recommended-ciphers).

> [!NOTE]
> Vlastnost `minTlsVersion` je jen pro čtení a nelze ji změnit po vytvoření prostředku centra IoT Hub. Proto je nezbytné, abyste předem řádně otestovali a ověřili, zda jsou *všechna* vaše zařízení a služby IoT kompatibilní s TLS 1.2 a [doporučenými šiframi.](#recommended-ciphers)

### <a name="supported-regions"></a>Podporované oblasti

IoT Huby, které vyžadují použití TLS 1.2, lze vytvořit v následujících oblastech:

* USA – východ
* USA – středojih
* USA – západ 2
* USA (Gov) – Arizona
* USA (Gov) – Virginia

> [!NOTE]
> Po převzetí služeb `minTlsVersion` při selhání zůstane vlastnost vašeho centra IoT Hub v geograficky spárované oblasti po převzetí služeb při selhání.

### <a name="recommended-ciphers"></a>Doporučené šifry

IoT Huby, které jsou nakonfigurované tak, aby přijímaly jenom TLS 1.2, budou také vynucovat použití následujících doporučených šifer:

* `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`

### <a name="use-tls-12-in-your-iot-hub-sdks"></a>Použití tls 1.2 ve sadách SDK centra IoT Hub

Pomocí níže uvedených odkazů nakonfigurujte tls 1.2 a povolené šifry v sadách SDK klienta IoT Hub.

| Jazyk | Verze podporující TLS 1.2 | Dokumentace |
|----------|------------------------------------|---------------|
| C        | Značka 2019-12-11 nebo novější            | [Odkaz](https://aka.ms/Tls_C_SDK_IoT) |
| Python   | Verze 2.0.0 nebo novější             | [Odkaz](https://aka.ms/Tls_Python_SDK_IoT) |
| C#       | Verze 1.21.4 nebo novější            | [Odkaz](https://aka.ms/Tls_CSharp_SDK_IoT) |
| Java     | Verze 1.19.0 nebo novější            | [Odkaz](https://aka.ms/Tls_Java_SDK_IoT) |
| NodeJS   | Verze 1.12.2 nebo novější            | [Odkaz](https://aka.ms/Tls_Node_SDK_IoT) |


### <a name="use-tls-12-in-your-iot-edge-setup"></a>Použití TLS 1.2 v nastavení IoT Edge

Zařízení IoT Edge lze nakonfigurovat tak, aby při komunikaci s IoT Hubem používala TLS 1.2. Pro tento účel použijte [stránku dokumentace ioT Edge](https://github.com/Azure/iotedge/blob/master/edge-modules/edgehub-proxy/README.md).