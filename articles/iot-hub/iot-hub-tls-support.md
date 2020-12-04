---
title: Podpora Azure IoT Hub TLS
description: Informace o použití zabezpečených připojení TLS pro zařízení a služby, které komunikují s IoT Hub
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 11/25/2020
ms.author: jlian
ms.openlocfilehash: ddb89f60c9fe380012c299afaafb6046bf6849c9
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2020
ms.locfileid: "96602746"
---
# <a name="transport-layer-security-tls-support-in-iot-hub"></a>Podpora protokolu TLS (Transport Layer Security) v IoT Hub

IoT Hub používá protokol TLS (Transport Layer Security) k zabezpečení připojení ze zařízení a služeb IoT. V současné době jsou podporovány tři verze protokolu TLS, a to verze 1,0, 1,1 a 1,2.

TLS 1,0 a 1,1 se považují za starší verze a plánuje se jejich vyřazení. Další informace najdete v tématu [zastaralé TLS 1,0 a 1,1 pro IoT Hub](iot-hub-tls-deprecating-1-0-and-1-1.md). Aby nedocházelo k budoucím problémům, při připojování k IoT Hub použijte protokol TLS 1,2 jako jedinou verzi TLS.

## <a name="iot-hubs-server-tls-certificate"></a>Certifikát TLS serveru IoT Hub

Během protokolu TLS handshake IoT Hub prezentuje certifikát serveru RSA pro připojení klientů. Kořenovou certifikační autoritou Baltimore CyberTrust. V poslední době došlo ke změně v emitentech novými zprostředkujícími certifikačními autoritami (ICAs). Další informace najdete v tématu [IoT Hub aktualizace certifikátu TLS](https://azure.microsoft.com/updates/iot-hub-tls-certificate-update/) .

### <a name="elliptic-curve-cryptography-ecc-server-tls-certificate-preview"></a>Certifikát TLS serveru s protokolem ECC (ve verzi Preview)

Certifikát TLS serveru IoT Hub ECC je k dispozici pro verzi Public Preview. I když nabízíte podobné zabezpečení certifikátům RSA, ověřování certifikátu ECC (s šifrovacími sadami pouze ECC) využívá až 40% méně výpočetních prostředků, paměti a šířky pásma. Tyto úspory jsou pro zařízení IoT důležité kvůli jejich menšímu počtu profilů a paměti a k podpoře případů použití v prostředích s omezeným počtem šířky pásma sítě. 

Pro náhled certifikátu serveru IoT Hub ECC:

1. [Vytvořte nové centrum IoT s režimem náhledu zapnuto](iot-hub-preview-mode.md).
1. [Nakonfigurujte klienta](#tls-configuration-for-sdk-and-iot-edge) tak, aby zahrnoval *jenom* šifrovací sady ECDSA a *vyloučil* žádné z nich. Toto jsou šifrovací sady pro certifikát ECC verze Public Preview:
    - `TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256`
    - `TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384`
    - `TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256`
    - `TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384`
1. Připojte svého klienta k službě IoT Hub ve verzi Preview.

## <a name="tls-12-enforcement-available-in-select-regions"></a>V výběru oblastí je k dispozici vynucení TLS 1,2

Pro zvýšení zabezpečení nakonfigurujte centra IoT tak, aby povolovala *jenom* připojení klientů, která používají protokol TLS verze 1,2 a vynutila používání [šifrovacích sad](#cipher-suites). Tato funkce je podporována pouze v těchto oblastech:

* East US
* Středojižní USA
* Západní USA 2
* USA (Gov) – Arizona
* USA (Gov) – Virginia

Pro tento účel zřiďte novou IoT Hub v některé z podporovaných oblastí a nastavte `minTlsVersion` vlastnost na `1.2` specifikaci prostředků služby IoT Hub pro Azure Resource Manager Template:

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

Vytvořený prostředek IoT Hub pomocí této konfigurace odmítne klienty zařízení a služeb, kteří se pokusí připojit pomocí protokolu TLS verze 1,0 a 1,1. Podobně platí, že pokud se ve `ClientHello` zprávě nezobrazí žádné [Doporučené šifry](#cipher-suites), bude Metoda handshake protokolu TLS odmítnutá.

> [!NOTE]
> `minTlsVersion`Vlastnost je určena jen pro čtení a nelze ji změnit po vytvoření prostředku IoT Hub. Proto je důležité, abyste správně otestovali a ověřili, že *všechna* vaše zařízení a služby IoT jsou kompatibilní s TLS 1,2 a předem [doporučenými šiframi](#cipher-suites) .
> 
> Po převzetí služeb při selhání `minTlsVersion` bude vlastnost IoT Hub v rámci převzetí služeb při selhání v geograficky spárovaném regionu platit.

## <a name="cipher-suites"></a>Šifrovací sady

Centra IoT, která jsou nakonfigurovaná tak, aby přijímala jenom TLS 1,2, taky vynutila použití následujících doporučených šifrovacích sad:

* `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`

Pro služby IoT Hub, které nejsou nakonfigurované pro vynucování TLS 1,2, bude TLS 1,2 stále fungovat s následujícími šifrovacími sadami:

* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_DHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_DHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_RSA_WITH_AES_256_CBC_SHA256`
* `TLS_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_AES_256_CBC_SHA`
* `TLS_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_3DES_EDE_CBC_SHA`

Klient může navrhnout seznam vyšších šifrovacích sad, které budou použity během `ClientHello` . Některé z nich však nemusí být podporovány IoT Hub (například `ECDHE-ECDSA-AES256-GCM-SHA384` ). V takovém případě se IoT Hub pokusí postupovat podle preference klienta, ale nakonec vyjednávat šifrovací sadu pomocí `ServerHello` .

## <a name="tls-configuration-for-sdk-and-iot-edge"></a>Konfigurace TLS pro sadu SDK a IoT Edge

Pomocí níže uvedených odkazů nakonfigurujte TLS 1,2 a povolená šifra v IoT Hub klientských sadách SDK.

| Jazyk | Verze podporující TLS 1,2 | Dokumentace |
|----------|------------------------------------|---------------|
| C        | Tag 2019-12-11 nebo novější            | [Propojit](https://aka.ms/Tls_C_SDK_IoT) |
| Python   | Verze 2.0.0 nebo novější             | [Propojit](https://aka.ms/Tls_Python_SDK_IoT) |
| C#       | Verze 1.21.4 nebo novější            | [Propojit](https://aka.ms/Tls_CSharp_SDK_IoT) |
| Java     | Verze 1.19.0 nebo novější            | [Propojit](https://aka.ms/Tls_Java_SDK_IoT) |
| NodeJS   | Verze 1.12.2 nebo novější            | [Propojit](https://aka.ms/Tls_Node_SDK_IoT) |

IoT Edge zařízení je možné nakonfigurovat tak, aby při komunikaci s IoT Hub používala TLS 1,2. Pro účely tohoto účelu použijte [stránku dokumentace IoT Edge](https://github.com/Azure/iotedge/blob/master/edge-modules/edgehub-proxy/README.md).

## <a name="device-authentication"></a>Ověřování zařízení

Po úspěšném ověření TLS metodou handshake IoT Hub může ověřit zařízení pomocí symetrického klíče nebo certifikátu X. 509. U ověřování založeného na certifikátech může to být libovolný certifikát X. 509, včetně ECC. IoT Hub ověří certifikát proti kryptografickému otisku nebo certifikační autoritě (CA), kterou zadáte. Další informace najdete v tématu [podporované certifikáty X. 509](iot-hub-devguide-security.md#supported-x509-certificates).

## <a name="tls-maximum-fragment-length-negotiation-preview"></a>Maximální délka fragmentu protokolu TLS (Preview)

IoT Hub také podporuje vyjednávání s maximální délkou fragmentu protokolu TLS, což se někdy označuje jako vyjednávání velikosti rámce TLS. Tato funkce je ve verzi Public Preview. 

Pomocí této funkce lze zadat maximální délku fragmentu nešifrovaného textu na hodnotu menší, než je výchozí hodnota 2 ^ 14 bajtů. Po vyjednání IoT Hub a klient zahájí fragmentaci zpráv, aby všechny fragmenty byly menší než vyjednaná délka. Toto chování je užitečné pro výpočetní nebo paměťově omezená zařízení. Další informace najdete v [oficiální specifikaci rozšíření TLS](https://tools.ietf.org/html/rfc6066#section-4).

Oficiální podpora sady SDK pro tuto funkci veřejné verze Preview není zatím k dispozici. Začněte tím, že

1. [Vytvořte nové centrum IoT s režimem náhledu zapnuto](iot-hub-preview-mode.md).
1. Nakonfigurujte klienta tak, aby byl nastavený `SSL_CTX_set_tlsext_max_fragment_length` na jednu z těchto hodnot: 2 ^ 9, 2 ^ 10, 2 ^ 11 a 2 ^ 12.
1. Připojte klienta k IoT Hub verze Preview.

## <a name="next-steps"></a>Další kroky

- Další informace o IoT Hub zabezpečení a řízení přístupu najdete v tématu [řízení přístupu k IoT Hub](iot-hub-devguide-security.md).
- Další informace o použití certifikátu x509 pro ověřování zařízení najdete v tématu [ověřování zařízení pomocí certifikátů certifikační autority X. 509.](iot-hub-x509ca-overview.md)