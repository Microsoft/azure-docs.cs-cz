---
title: Podpora protokolu TLS služby Azure IoT Device Provisioning (DPS)
description: Osvědčené postupy při použití zabezpečených připojení TLS pro zařízení a služby, které komunikují se službou IoT Device Provisioning (DPS)
services: iot-dps
author: wesmc7777
ms.service: iot-dps
ms.topic: conceptual
ms.date: 09/14/2020
ms.author: wesmc
ms.openlocfilehash: 3a8910cf0e81bd041d74ef95f45220f1c1e0b34c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91761290"
---
# <a name="tls-support-in-azure-iot-hub-device-provisioning-service-dps"></a>Podpora TLS v Azure IoT Hub Device Provisioning Service (DPS)

DPS k zabezpečení připojení ze zařízení IoT používá [protokol TLS (Transport Layer Security)](http://wikipedia.org/wiki/Transport_Layer_Security) . 

Aktuální verze protokolu TLS podporované službou DPS jsou: 
* TLS 1.2

TLS 1,0 a 1,1 se považují za starší verze a plánuje se jejich vyřazení. Další informace najdete v tématu [zastaralé TLS 1,0 a 1,1 pro IoT Hub](../iot-hub/iot-hub-tls-deprecating-1-0-and-1-1.md). 

## <a name="restrict-connections-to-tls-12"></a>Omezení připojení k TLS 1,2

Pro zvýšení zabezpečení doporučujeme nakonfigurovat instance DPS tak, aby povolovaly *jenom* připojení klientů zařízení, která používají protokol TLS verze 1,2, a vynutili použití [doporučených šifr](#recommended-ciphers).

Provedete to tak, že zřídíte nové nastavení prostředku DPS s nastavením `minTlsVersion` vlastnosti `1.2` ve specifikaci prostředku DPS Azure Resource Manager vaší šabloně. Následující příklad šablony JSON Určuje `minTlsVersion` vlastnost pro novou instanci DPS.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.Devices/ProvisioningServices",
            "apiVersion": "2020-01-01",
            "name": "<provide-a-valid-DPS-resource-name>",
            "location": "<any-region>",
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


> [!NOTE]
> Po převzetí služeb při selhání `minTlsVersion` zůstane vlastnost DPS platná v případě převzetí služeb při selhání v geograficky spárované oblasti.

## <a name="recommended-ciphers"></a>Doporučené šifry

Instance DPS, které jsou nakonfigurované tak, aby přijímaly jenom TLS 1,2, vynutily používání následujících šifrovacích sad:


| Doporučené šifrovací sady TLS 1,2 |
| :--- |
| `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`<br>`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`<br>`TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`<br>`TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256` |


### <a name="legacy-cipher-suites"></a>Zastaralé šifrovací sady 

Tyto šifrovací sady jsou v současné době i nadále podporovány v DPS, ale budou odepsány. Pokud je to možné, používejte doporučené šifrovací sady výše.

| Možnost #1 (lepší zabezpečení) |
| :--- |
| `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P384   (uses SHA-1)`<br>`TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P256   (uses SHA-1)`<br>`TLS_RSA_WITH_AES_256_GCM_SHA384           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_128_GCM_SHA256           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_256_CBC_SHA256           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_128_CBC_SHA256           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_256_CBC_SHA              (uses SHA-1, lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_128_CBC_SHA              (uses SHA-1, lack of Perfect Forward Secrecy)` |

| Možnost #2 (lepší výkon) |
| :--- |
| `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P256   (uses SHA-1)`<br>`TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P384   (uses SHA-1)`<br>`TLS_RSA_WITH_AES_128_GCM_SHA256           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_256_GCM_SHA384           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_128_CBC_SHA256           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_256_CBC_SHA256           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_128_CBC_SHA              (uses SHA-1, lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_256_CBC_SHA              (uses SHA-1, lack of Perfect Forward Secrecy)` |


## <a name="use-tls-12-in-the-iot-sdks"></a>Použití TLS 1,2 v sadách IoT SDK

Pomocí níže uvedených odkazů nakonfigurujte TLS 1,2 a povolená šifra v sadách SDK klienta Azure IoT.

| Jazyk | Verze podporující TLS 1,2 | Dokumentace |
|----------|------------------------------------|---------------|
| C        | Tag 2019-12-11 nebo novější            | [Propojit](https://aka.ms/Tls_C_SDK_IoT) |
| Python   | Verze 2.0.0 nebo novější             | [Propojit](https://aka.ms/Tls_Python_SDK_IoT) |
| C#       | Verze 1.21.4 nebo novější            | [Propojit](https://aka.ms/Tls_CSharp_SDK_IoT) |
| Java     | Verze 1.19.0 nebo novější            | [Propojit](https://aka.ms/Tls_Java_SDK_IoT) |
| NodeJS   | Verze 1.12.2 nebo novější            | [Propojit](https://aka.ms/Tls_Node_SDK_IoT) |

## <a name="use-tls-12-with-iot-hub"></a>Použití TLS 1,2 s IoT Hub

IoT Hub se dá nakonfigurovat tak, aby při komunikaci se zařízeními používala TLS 1,2. Další informace najdete v tématu [zastaralé TLS 1,0 a 1,1 pro IoT Hub](../iot-hub/iot-hub-tls-deprecating-1-0-and-1-1.md).

## <a name="use-tls-12-with-iot-edge"></a>Použití TLS 1,2 s IoT Edge

IoT Edge zařízení je možné nakonfigurovat tak, aby při komunikaci s IoT Hub a DPS používala TLS 1,2. Další informace najdete na [stránce dokumentace IoT Edge](https://github.com/Azure/iotedge/blob/master/edge-modules/edgehub-proxy/README.md).
