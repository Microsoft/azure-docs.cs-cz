---
title: Mezipaměť propojená se dvěma úrovněmi mezipaměti od Microsoftu s odchozím neověřeným proxy serverem (Azure IoT Edge brána) Microsoft Docs
titleSuffix: Device Update for Azure IoT Hub
description: Kurz propojeného mezipamětí Microsoft v mezipaměti dvě úrovně Azure IoT Edge brány s odchozím neověřeným proxy serverem
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 623ce808423f76ae1be079e0424fe3ddf27d1d58
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2021
ms.locfileid: "107811881"
---
# <a name="microsoft-connected-cache-preview-deployment-scenario-sample-two-level-nested-azure-iot-edge-gateway-with-outbound-unauthenticated-proxy"></a>Ukázka scénáře nasazení ve verzi Preview služby Microsoft Connected cache: Azure IoT Edge brána se dvěma úrovněmi a odchozím neověřeným proxy serverem

Diagram níže popisuje scénář, ve kterém jedna Azure IoT Edge brána má přímý přístup k prostředkům CDN a funguje jako nadřazené pro jinou bránu Azure IoT Edge. Podřízená IoT Edge brána funguje jako nadřazená zařízení Azure IoT list, jako je například malin. PI. Podřízená Azure IoT Edge i zařízení Azure IoT jsou izolované v Internetu. Níže uvedený příklad ukazuje konfiguraci pro dvě úrovně Azure IoT Edge bran, ale neexistuje žádné omezení na hloubku nadřazených hostitelů, které bude mezipaměť připojené k Microsoftu podporovat. V předchozích příkladech neexistují žádné rozdíly v možnostech vytvoření kontejneru mezipaměti s připojením společnosti Microsoft.

Další informace o konfiguraci vrstveného nasazení Azure IoT Edge Branch najdete v dokumentaci věnovaném [připojení IoT Edge zařízení – Azure IoT Edge](../iot-edge/how-to-connect-downstream-iot-edge-device.md?preserve-view=true&tabs=azure-portal&view=iotedge-2020-11) . Kromě toho mějte na paměti, že při nasazování Azure IoT Edge, mezipaměti propojeného Microsoft a vlastních modulů se všechny moduly musí nacházet ve stejném registru kontejneru.

>[!Note]
>Při nasazování Azure IoT Edge, mezipaměti propojených se systémem Microsoft a vlastní moduly se všechny moduly musí nacházet ve stejném registru kontejneru.

  :::image type="content" source="media/connected-cache-overview/nested-level-proxy.png" alt-text="Vnořená mezipaměť Microsoft propojená" lightbox="media/connected-cache-overview/nested-level-proxy.png":::

## <a name="parent-gateway-configuration"></a>Konfigurace nadřazené brány
1. Přidejte modul Microsoft Connect cache do nasazení zařízení Azure IoT Edge brány ve službě Azure IoT Hub (podrobné informace o tom, jak tento modul získat, najdete v tématu [Podpora odpojených zařízení](connected-cache-disconnected-device-update.md) ).
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

## <a name="child-gateway-configuration"></a>Konfigurace podřízené brány

>[!Note]
>Pokud jste replikované kontejnery používali v konfiguraci ve vlastním privátním registru, bude nutné provést úpravy nastavení config. toml a modulu runtime v nasazení modulu. Další informace najdete v tématu [připojení k zařízením pro příjem IoT Edge – Azure IoT Edge](../iot-edge/how-to-connect-downstream-iot-edge-device.md?preserve-view=true&tabs=azure-portal&view=iotedge-2020-11#deploy-modules-to-lower-layer-devices) další podrobnosti.


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

4. Přidejte stejnou sadu proměnných prostředí a možnosti vytvoření kontejneru používané v nadřazeném nasazení.
>[!Note]
>CACHE_NODE_ID by měl být jedinečný.  Hodnoty CUSTOMER_ID a CUSTOMER_KEY budou identické s nadřazenou položkou. (Další informace najdete v tématu [Konfigurace mezipaměti připojené k Microsoft](connected-cache-configure.md)

Pokud chcete ověřit správnou funkční mezipaměť Microsoft, spusťte následující příkaz v terminálu IoT Edge zařízení hostujícího modul nebo jakékoli zařízení v síti. Nahraďte \<Azure IoT Edge Gateway IP\> IP adresou nebo názvem hostitele brány IoT Edge. (informace o viditelnosti této sestavy najdete v tématu podrobnosti o proměnné prostředí).

```bash
    wget http://<CHILD Azure IoT Edge Gateway IP>/mscomtest/wuidt.gif?cacheHostOrigin=au.download.windowsupdate.com
```