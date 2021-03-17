---
title: Ukázky scénáře nasazení ve verzi Preview služby Microsoft Connected cache | Microsoft Docs
titleSuffix: Device Update for Azure IoT Hub
description: Kurzy ukázek scénářů pro nasazení ve verzi Preview připojené k mezipaměti Microsoftu
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: ae07926d7d8c768170e945e916367bee41999571
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101664566"
---
# <a name="microsoft-connected-cache-preview-deployment-scenario-samples"></a>Ukázky scénáře nasazení ve verzi Preview připojené k mezipaměti Microsoftu

## <a name="single-level-azure-iot-edge-gateway-no-proxy"></a>Brána Azure IoT Edge bez proxy serveru na úrovni Single

Diagram níže popisuje scénář, ve kterém Azure IoT Edge bránu, která má přímý přístup k prostředkům CDN, a obsahuje zařízení Azure IoT list, jako je například malin. PI, který je internetem izolovaným podřízeným zařízením Azure IoT Edge brány. 

  :::image type="content" source="media/connected-cache-overview/disconnected-device-update.png" alt-text="Aktualizace zařízení odpojené od Microsoftu připojené k mezipaměti" lightbox="media/connected-cache-overview/disconnected-device-update.png":::

1. Přidejte modul Microsoft Connect cache do nasazení zařízení Azure IoT Edge brány ve službě Azure IoT Hub (podrobnosti najdete v tématu o `MCC concepts` tom, jak tento modul získat).
2. Přidejte proměnné prostředí pro nasazení. Níže je uveden příklad proměnných prostředí.

    **Proměnné prostředí**
    
    | Název                 | Hodnota                                       |
    | ----------------------------- | --------------------------------------------| 
    | CACHE_NODE_ID                 | Viz popis proměnné prostředí výše. |
    | CUSTOMER_ID                   | Viz popis proměnné prostředí výše. |
    | CUSTOMER_KEY                  | Viz popis proměnné prostředí výše. |
    | STORAGE_ *N* _SIZE_GB           | N = 5                                       |

3. Přidejte kontejner možnosti vytvoření pro nasazení. Níže je uveden příklad možností vytvoření kontejneru.

### <a name="container-create-options"></a>Možnosti vytvoření kontejneru

```markdown
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
```

Pokud chcete ověřit správnou funkční mezipaměť Microsoft, spusťte následující příkaz v terminálu IoT Edge zařízení hostujícího modul nebo jakékoli zařízení v síti.

```bash
    wget "http://<IOT Edge Gateway IP>/mscomtest/wuidt.gif?cacheHostOrigin=au.download.windowsupdate.com
```

## <a name="single-level-azure-iot-edge-gateway-with-outbound-unauthenticated-proxy"></a>Brána Azure IoT Edge s odchozím neověřeným proxy serverem na úrovni Single

V tomto scénáři je Azure IoT Edge brána, která má přístup k prostředkům CDN prostřednictvím odchozího neověřeného proxy serveru. Mezipaměť propojená Microsoftem je nakonfigurovaná tak, aby obsah mohla ukládat do mezipaměti z vlastního úložiště a aby byla Souhrnná zpráva viditelná pro všechny uživatele v síti. Níže je uveden příklad proměnných prostředí MCC, které by se nastavily.

  :::image type="content" source="media/connected-cache-overview/single-level-proxy.png" alt-text="Proxy servery s jednou úrovní mezipaměti připojené od Microsoftu" lightbox="media/connected-cache-overview/single-level-proxy.png":::

1. Přidejte modul Microsoft Connect cache do nasazení zařízení Azure IoT Edge brány ve službě Azure IoT Hub.
2. Přidejte proměnné prostředí pro nasazení. Níže je uveden příklad proměnných prostředí.

    **Proměnné prostředí**

    | Název                 | Hodnota                                       |
    | ----------------------------- | --------------------------------------------| 
    | CACHE_NODE_ID                 | Viz popis proměnné prostředí výše. |
    | CUSTOMER_ID                   | Viz popis proměnné prostředí výše. |
    | CUSTOMER_KEY                  | Viz popis proměnné prostředí výše. |
    | STORAGE_ *N* _SIZE_GB           | N = 5                                       |
    | CACHEABLE_CUSTOM_1_HOST       | Packagerepo.com:80                          |
    | CACHEABLE_CUSTOM_1_CANONICAL  | Packagerepo.com                             |
    | IS_SUMMARY_ACCESS_UNRESTRICTED| true                                        |
    | UPSTREAM_PROXY                | IP adresa proxy serveru nebo plně kvalifikovaný název domény                     |

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
```

Pokud chcete ověřit správnou funkční mezipaměť Microsoft, spusťte následující příkaz v terminálu Azure IoT Edge zařízení hostujícího modul nebo jakékoli zařízení v síti.

```bash
    wget "http://<Azure IOT Edge Gateway IP>/mscomtest/wuidt.gif?cacheHostOrigin=au.download.windowsupdate.com
```
