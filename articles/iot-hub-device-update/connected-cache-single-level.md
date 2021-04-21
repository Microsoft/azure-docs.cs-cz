---
title: Ukázky scénáře nasazení ve verzi Preview služby Microsoft Connected cache | Microsoft Docs
titleSuffix: Device Update for Azure IoT Hub
description: Kurzy ukázek scénářů pro nasazení ve verzi Preview připojené k mezipaměti Microsoftu
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: c116bbf5ea9f5fc6e58962e02c93c630fc747d9e
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2021
ms.locfileid: "107811719"
---
# <a name="microsoft-connected-cache-preview-deployment-scenario-samples"></a>Ukázky scénáře nasazení ve verzi Preview připojené k mezipaměti Microsoftu

## <a name="single-level-azure-iot-edge-gateway-no-proxy"></a>Brána Azure IoT Edge bez proxy serveru na úrovni Single

Diagram níže popisuje scénář, ve kterém Azure IoT Edge bránu, která má přímý přístup k prostředkům CDN, a obsahuje zařízení Azure IoT list, jako je například malin. PI, který je internetem izolovaným podřízeným zařízením Azure IoT Edge brány. 

  :::image type="content" source="media/connected-cache-overview/disconnected-device-update.png" alt-text="Aktualizace zařízení odpojené od Microsoftu připojené k mezipaměti" lightbox="media/connected-cache-overview/disconnected-device-update.png":::

1. Přidejte modul Microsoft Connect cache do nasazení zařízení Azure IoT Edge brány ve službě Azure IoT Hub (podrobné informace o tom, jak tento modul získat, najdete v tématu [Podpora odpojených zařízení](connected-cache-disconnected-device-update.md) ).
2. Přidejte proměnné prostředí pro nasazení. Níže je uveden příklad proměnných prostředí.

    **Proměnné prostředí**
    
    | Name                          | Hodnota                                                                 |
    | ----------------------------- | ----------------------------------------------------------------------| 
    | CACHE_NODE_ID                 | Viz popisy [proměnných prostředí](connected-cache-configure.md) |
    | CUSTOMER_ID                   | Viz popisy [proměnných prostředí](connected-cache-configure.md) |
    | CUSTOMER_KEY                  | Viz popisy [proměnných prostředí](connected-cache-configure.md) |
    | STORAGE_1_SIZE_GB             | 10                                                                    |

3. Přidejte kontejner možnosti vytvoření pro nasazení. Níže je uveden příklad možností vytvoření kontejneru.

### <a name="container-create-options"></a>Možnosti vytvoření kontejneru

```json
{
    "HostConfig": {
        "Binds": [
            "/MicrosoftConnectedCache1/:/nginx/cache1/"
        ],
        "PortBindings": {
            "8081/tcp": [
                {
                    "HostPort": "80"
                }
            ],
            "5000/tcp": [
                {
                    "HostPort": "5100"
                }
            ]
        }
    }
}
```

Pokud chcete ověřit správnou funkční mezipaměť Microsoft, spusťte následující příkaz v terminálu IoT Edge zařízení hostujícího modul nebo jakékoli zařízení v síti. Nahraďte \<Azure IoT Edge Gateway IP\> IP adresou nebo názvem hostitele brány IoT Edge. (informace o viditelnosti této sestavy najdete v tématu podrobnosti o proměnné prostředí).

```bash
    wget http://<IoT Edge Gateway IP>/mscomtest/wuidt.gif?cacheHostOrigin=au.download.windowsupdate.com
```

## <a name="single-level-azure-iot-edge-gateway-with-outbound-unauthenticated-proxy"></a>Brána Azure IoT Edge s odchozím neověřeným proxy serverem na úrovni Single

V tomto scénáři je Azure IoT Edge brána, která má přístup k prostředkům CDN prostřednictvím odchozího neověřeného proxy serveru. Mezipaměť propojená Microsoftem je nakonfigurovaná tak, aby obsah mohla ukládat do mezipaměti z vlastního úložiště a aby byla Souhrnná zpráva viditelná pro všechny uživatele v síti. Níže je uveden příklad proměnných prostředí MCC, které by se nastavily.

  :::image type="content" source="media/connected-cache-overview/single-level-proxy.png" alt-text="Proxy servery s jednou úrovní mezipaměti připojené od Microsoftu" lightbox="media/connected-cache-overview/single-level-proxy.png":::

1. Přidejte modul Microsoft Connect cache do nasazení zařízení Azure IoT Edge brány ve službě Azure IoT Hub.
2. Přidejte proměnné prostředí pro nasazení. Níže je uveden příklad proměnných prostředí.

    **Proměnné prostředí**

    | Name                          | Hodnota                                                                 |
    | ----------------------------- | ----------------------------------------------------------------------| 
    | CACHE_NODE_ID                 | Viz popisy [proměnných prostředí](connected-cache-configure.md) |
    | CUSTOMER_ID                   | Viz popisy [proměnných prostředí](connected-cache-configure.md) |
    | CUSTOMER_KEY                  | Viz popisy [proměnných prostředí](connected-cache-configure.md) |
    | STORAGE_1_SIZE_GB             | 10                                                                    |
    | CACHEABLE_CUSTOM_1_HOST       | Packagerepo.com:80                                                    |
    | CACHEABLE_CUSTOM_1_CANONICAL  | Packagerepo.com                                                       |
    | IS_SUMMARY_ACCESS_UNRESTRICTED| true                                                                  |
    | UPSTREAM_PROXY                | Vaše proxy serverá IP adresa nebo plně kvalifikovaný název domény                                          |

3. Přidejte kontejner možnosti vytvoření pro nasazení. V předchozím příkladu není žádný rozdíl v možnostech vytvoření kontejneru MCC. Níže je uveden příklad možností vytvoření kontejneru.

### <a name="container-create-options"></a>Možnosti vytvoření kontejneru

```json
{
    "HostConfig": {
        "Binds": [
            "/MicrosoftConnectedCache1/:/nginx/cache1/"
        ],
        "PortBindings": {
            "8081/tcp": [
                {
                    "HostPort": "80"
                }
            ],
            "5000/tcp": [
                {
                    "HostPort": "5100"
                }
            ]
        }
    }
}
```

Pokud chcete ověřit správnou funkční mezipaměť Microsoft, spusťte následující příkaz v terminálu Azure IoT Edge zařízení hostujícího modul nebo jakékoli zařízení v síti. Nahraďte \<Azure IoT Edge Gateway IP\> IP adresou nebo názvem hostitele brány IoT Edge. (informace o viditelnosti této sestavy najdete v tématu podrobnosti o proměnné prostředí).

```bash
    wget http://<Azure IoT Edge Gateway IP>/mscomtest/wuidt.gif?cacheHostOrigin=au.download.windowsupdate.com 
```
