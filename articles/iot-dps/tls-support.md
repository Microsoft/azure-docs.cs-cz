---
title: Podpora protokolu TLS služby Azure IoT Device Provisioning (DPS)
description: Osvědčené postupy při použití zabezpečených připojení TLS pro zařízení a služby, které komunikují se službou IoT Device Provisioning (DPS)
services: iot-dps
author: wesmc7777
ms.service: iot-dps
ms.topic: conceptual
ms.date: 06/04/2020
ms.author: wesmc
ms.openlocfilehash: 0daddd2fb1368819c8f7b4cf0183c90a8c6c065e
ms.sourcegitcommit: 8e5b4e2207daee21a60e6581528401a96bfd3184
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2020
ms.locfileid: "84417969"
---
# <a name="tls-support-in-azure-iot-hub-device-provisioning-service-dps"></a>Podpora TLS v Azure IoT Hub Device Provisioning Service (DPS)

DPS k zabezpečení připojení ze zařízení IoT používá protokol TLS (Transport Layer Security). Verze protokolu TLS, které podporuje DPS, zahrnují protokol TLS 1,2.

TLS 1,0 a 1,1 se považují za starší verze a plánuje se jejich vyřazení. Další informace najdete v tématu [zastaralé TLS 1,0 a 1,1 pro IoT Hub](../iot-hub/iot-hub-tls-deprecating-1-0-and-1-1.md). 

## <a name="restrict-connections-to-tls-12"></a>Omezení připojení k TLS 1,2

Pro zvýšení zabezpečení doporučujeme nakonfigurovat instance DPS tak, aby povolovaly *jenom* připojení klientů zařízení, která používají protokol TLS verze 1,2, a vynutili použití [doporučených šifr](#recommended-ciphers).

Provedete to tak, že zřídíte nový prostředek DPS v některé z [podporovaných oblastí](#supported-regions) a nastavíte `minTlsVersion` vlastnost na hodnotu `1.2` ve specifikaci prostředku DPS pro šablonu Azure Resource Manager. Následující příklad šablony JSON Určuje `minTlsVersion` vlastnost pro novou instanci DPS.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.Devices/ProvisioningServices",
            "apiVersion": "2020-01-01",
            "name": "<provide-a-valid-DPS-resource-name>",
            "location": "<any-of-supported-regions-below>",
            "properties": {
                "minTlsVersion": "1.2"
            },
            "sku": {
                "name": "S1",
                "capacity": 1
            },
        }     
    ]
}
```

Šablonu můžete nasadit pomocí následujícího příkazu rozhraní příkazového řádku Azure CLI. 

```azurecli
az deployment group create -g <your resource group name> --template-file template.json
```

Další informace o vytváření prostředků DPS pomocí šablon Správce prostředků najdete v tématu [Nastavení DPS pomocí šablony Azure Resource Manager](quick-setup-auto-provision-rm.md).

Prostředek DPS, který se vytvořil pomocí této konfigurace, odmítne zařízení, která se pokusí připojit pomocí TLS verze 1,0 a 1,1. Obdobně se protokol TLS handshake odmítne, pokud zpráva HELLo klienta zařízení neuvádí žádné [Doporučené šifry](#recommended-ciphers).

> [!NOTE]
> `minTlsVersion`Vlastnost je jen pro čtení a po vytvoření prostředku DPS ji nelze změnit. Proto je důležité, abyste správně otestovali a ověřili, jestli jsou *všechna* vaše zařízení IoT kompatibilní s TLS 1,2 a předem [doporučenými šiframi](#recommended-ciphers) .

## <a name="supported-regions"></a>Podporované oblasti

Instance IoT DPS, které vyžadují použití protokolu TLS 1,2, se dají vytvořit v následujících oblastech:

* USA (Gov) – Arizona
* USA (Gov) – Virginia

> [!NOTE]
> Po převzetí služeb při selhání `minTlsVersion` zůstane vlastnost DPS platná v případě převzetí služeb při selhání v geograficky spárované oblasti.

## <a name="recommended-ciphers"></a>Doporučené šifry

Instance DPS, které jsou nakonfigurované tak, aby přijímaly jenom TLS 1,2, vynutily používání následujících doporučených šifr:

* `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`

## <a name="use-tls-12-in-the-iot-sdks"></a>Použití TLS 1,2 v sadách IoT SDK

Pomocí níže uvedených odkazů nakonfigurujte TLS 1,2 a povolená šifra v sadách SDK klienta Azure IoT.

| Jazyk | Verze podporující TLS 1,2 | Dokumentace |
|----------|------------------------------------|---------------|
| C        | Tag 2019-12-11 nebo novější            | [Odkaz](https://aka.ms/Tls_C_SDK_IoT) |
| Python   | Verze 2.0.0 nebo novější             | [Odkaz](https://aka.ms/Tls_Python_SDK_IoT) |
| C#       | Verze 1.21.4 nebo novější            | [Odkaz](https://aka.ms/Tls_CSharp_SDK_IoT) |
| Java     | Verze 1.19.0 nebo novější            | [Odkaz](https://aka.ms/Tls_Java_SDK_IoT) |
| NodeJS   | Verze 1.12.2 nebo novější            | [Odkaz](https://aka.ms/Tls_Node_SDK_IoT) |

## <a name="use-tls-12-with-iot-hub"></a>Použití TLS 1,2 s IoT Hub

IoT Hub se dá nakonfigurovat tak, aby při komunikaci se zařízeními používala TLS 1,2. Další informace najdete v tématu [zastaralé TLS 1,0 a 1,1 pro IoT Hub](../iot-hub/iot-hub-tls-deprecating-1-0-and-1-1.md).

## <a name="use-tls-12-with-iot-edge"></a>Použití TLS 1,2 s IoT Edge

IoT Edge zařízení je možné nakonfigurovat tak, aby při komunikaci s IoT Hub a DPS používala TLS 1,2. Další informace najdete na [stránce dokumentace IoT Edge](https://github.com/Azure/iotedge/blob/master/edge-modules/edgehub-proxy/README.md).