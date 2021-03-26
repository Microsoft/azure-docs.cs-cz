---
title: Mezipaměť propojená se dvěma úrovněmi mezipaměti od Microsoftu s odchozím neověřeným proxy serverem (Azure IoT Edge brána) Microsoft Docs
titleSuffix: Device Update for Azure IoT Hub
description: Kurz propojeného mezipamětí Microsoft v mezipaměti dvě úrovně Azure IoT Edge brány s odchozím neověřeným proxy serverem
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 7facb74cd407c576b2a7b119f19427dcd185f04e
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2021
ms.locfileid: "105568813"
---
# <a name="microsoft-connected-cache-preview-deployment-scenario-sample-two-level-nested-azure-iot-edge-gateway-with-outbound-unauthenticated-proxy"></a>Ukázka scénáře nasazení ve verzi Preview služby Microsoft Connected cache: Azure IoT Edge brána se dvěma úrovněmi a odchozím neověřeným proxy serverem

V následujícím diagramu se v tomto scénáři nachází brána Azure IoT Edge a zařízení Azure IoT Edge pro příjem dat, jedna Azure IoT Edge brána nadřazená druhé bráně Azure IoT Edge a proxy server na IT DMZ. Níže je uveden příklad proměnných prostředí mezipaměti s připojením společnosti Microsoft, které by byly nastaveny v uživatelském rozhraní Azure Portal pro oba moduly MCC nasazené do Azure IoT Edgech bran. Zobrazený příklad ukazuje konfiguraci pro dvě úrovně Azure IoT Edge bran, ale neexistuje žádné omezení na hloubku nadřazených hostitelů, které bude mezipaměť připojené k Microsoftu podporovat. V předchozích příkladech není žádný rozdíl v možnostech vytvoření kontejneru MCC.

Další informace o konfiguraci vrstveného nasazení Azure IoT Edge Branch najdete v dokumentaci věnovaném [připojení IoT Edge zařízení – Azure IoT Edge](../iot-edge/how-to-connect-downstream-iot-edge-device.md?preserve-view=true&tabs=azure-portal&view=iotedge-2020-11) . Kromě toho mějte na paměti, že při nasazování Azure IoT Edge, mezipaměti propojeného Microsoft a vlastních modulů se všechny moduly musí nacházet ve stejném registru kontejneru.

Diagram níže popisuje situaci, kdy jedna Azure IoT Edge brána jako přímý přístup k prostředkům CDN funguje jako nadřazená k jiné bráně Azure IoT Edge, která funguje jako nadřazená zařízení Azure IoT list, jako je například Malinová Pi. Jenom nadřazený objekt brány Azure IoT Edge má připojení k Internetu k prostředkům CDN a podřízeným Azure IoT Edge i zařízením Azure IoT jsou izolované v Internetu. 

  :::image type="content" source="media/connected-cache-overview/nested-level-proxy.png" alt-text="Vnořená mezipaměť Microsoft propojená" lightbox="media/connected-cache-overview/nested-level-proxy.png":::

## <a name="parent-gateway-configuration"></a>Konfigurace nadřazené brány

1. Přidejte modul Microsoft Connect cache do nasazení zařízení Azure IoT Edge brány ve službě Azure IoT Hub.
2. Přidejte proměnné prostředí pro nasazení. Níže je uveden příklad proměnných prostředí.

    **Proměnné prostředí**

    | Name                 | Hodnota                                       |
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

## <a name="child-gateway-configuration"></a>Konfigurace podřízené brány

>[!Note]
>Pokud jste replikované kontejnery používali v konfiguraci ve vlastním privátním registru, bude nutné provést úpravy nastavení config. toml a modulu runtime v nasazení modulu. Další informace najdete v tématu [kurz – vytvoření hierarchie IoT Edge zařízení – Azure IoT Edge](../iot-edge/tutorial-nested-iot-edge.md?preserve-view=true&tabs=azure-portal&view=iotedge-2020-11#deploy-modules-to-the-lower-layer-device) další podrobnosti.

1. Upravte cestu k bitové kopii pro agenta Edge, jak je znázorněno v následujícím příkladu:

```markdown
[agent]
name = "edgeAgent"
type = "docker"
env = {}
[agent.config]
image = "<parent_device_fqdn_or_ip>:8000/iotedge/azureiotedge-agent:1.2.0-rc2"
auth = {}
```
2. V nasazení Azure IoT Edge upravte nastavení modulu Edge hraničního centra a hraničního agenta, jak je znázorněno v tomto příkladu:
    
    * V části hraniční centrum v poli obrázek zadejte ```$upstream:8000/iotedge/azureiotedge-hub:1.2.0-rc2```
    * V části hraniční agent v poli Image zadejte ```$upstream:8000/iotedge/azureiotedge-agent:1.2.0-rc2```

3. Přidejte modul Microsoft Connect cache do nasazení zařízení Azure IoT Edge brány ve službě Azure IoT Hub.

   * Vyberte název pro svůj modul: ```ConnectedCache```
   * Upravte identifikátor URI Image: ```$upstream:8000/mcc/linux/iot/mcc-ubuntu-iot-amd64:latest```

4. Přidejte stejné proměnné prostředí a možnosti vytvoření kontejneru používané v nadřazeném nasazení.

Pokud chcete ověřit správnou funkční mezipaměť Microsoft, spusťte následující příkaz v terminálu IoT Edge zařízení hostujícího modul nebo jakékoli zařízení v síti.

```bash
    wget "http://<CHILD Azure IoT Edge Gateway IP>/mscomtest/wuidt.gif?cacheHostOrigin=au.download.windowsupdate.com
```