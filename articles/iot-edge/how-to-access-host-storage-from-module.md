---
title: Použití místního úložiště IoT Edge zařízení z modulu-Azure IoT Edge | Microsoft Docs
description: Použijte proměnné prostředí a vytvořte možnosti, které povolí přístup k modulu IoT Edge místní úložiště zařízení.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/12/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 2526f33f0053b5805394a4a898af88d86187066c
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2019
ms.locfileid: "72301279"
---
# <a name="give-modules-access-to-a-devices-local-storage"></a>Udělení přístupu k místnímu úložišti zařízení v modulech

Kromě ukládání dat pomocí služeb Azure Storage nebo v úložišti kontejnerů vašeho zařízení můžete také vyhradit úložiště na hostiteli IoT Edge samotném zařízení, aby se zlepšila spolehlivost, obzvláště když pracujete offline.

Pokud chcete nastavit úložiště v hostitelském systému, vytvořte pro svůj modul proměnnou prostředí, která odkazuje na složku úložiště v kontejneru. Pak použijte možnosti vytvořit k navázání této složky úložiště do složky na hostitelském počítači.

Pokud jste například chtěli povolit, aby Centrum IoT Edge ukládalo zprávy do místního úložiště vašeho zařízení a načetli je později, můžete nakonfigurovat proměnné prostředí a možnosti vytvoření v Azure Portal v **nastavení modulu runtime konfigurace pokročilého Edge.** oddíl.

1. U IoT Edgeového centra i agenta IoT Edge přidejte proměnnou prostředí s názvem **storageFolder** , která odkazuje na adresář v modulu.
1. U IoT Edgeového centra i agenta IoT Edge přidejte vazby pro připojení místního adresáře na hostitelském počítači k adresáři v modulu. Příklad:

   ![Přidání možností vytvoření a proměnných prostředí pro místní úložiště](./media/how-to-access-host-storage-from-module/offline-storage.png)

Nebo můžete nakonfigurovat místní úložiště přímo v manifestu nasazení. Příklad:

```json
"systemModules": {
    "edgeAgent": {
        "settings": {
            "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
            "createOptions": {
                "HostConfig": {
                    "Binds":["<HostStoragePath>:<ModuleStoragePath>"]
                }
            }
        },
        "type": "docker",
        "env": {
            "storageFolder": {
                "value": "<ModuleStoragePath>"
            }
        }
    },
    "edgeHub": {
        "settings": {
            "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
            "createOptions": {
                "HostConfig": {
                    "Binds":["<HostStoragePath>:<ModuleStoragePath>"],
                    "PortBindings":{"5671/tcp":[{"HostPort":"5671"}],"8883/tcp":[{"HostPort":"8883"}],"443/tcp":[{"HostPort":"443"}]}}}
        },
        "type": "docker",
        "env": {
            "storageFolder": {
                "value": "<ModuleStoragePath>"
            }
        },
        "status": "running",
        "restartPolicy": "always"
    }
}
```

Nahraďte `<HostStoragePath>` a `<ModuleStoragePath>` vaším hostitelem a cestou úložiště modulu; obě hodnoty musí být absolutní cesta.

Například v systému Linux `"Binds":["/etc/iotedge/storage/:/iotedge/storage/"]` znamená, že se adresář **/etc/iotedge/Storage** v hostitelském systému mapuje na adresář **/iotedge/Storage/** v kontejneru. V systému Windows jako jiný příklad `"Binds":["C:\\temp:C:\\contemp"]` znamená, že adresář **c: \\temp** v hostitelském systému je namapován na adresář **c: \\contemp** v kontejneru.

Na zařízeních se systémem Linux se navíc ujistěte, že má uživatelský profil pro váš modul potřebná oprávnění ke čtení, zápisu a spouštění pro adresář hostitelského systému. Když se vrátíte na předchozí příklad povolení IoT Edge centra pro ukládání zpráv do místního úložiště vašeho zařízení, musíte udělit oprávnění k profilu uživatele, UID 1000. (Agent IoT Edge funguje jako kořenový, takže nepotřebuje další oprávnění.) Existuje několik způsobů, jak spravovat oprávnění adresářů v systémech Linux, včetně použití `chown` ke změně vlastníka adresáře a následnému `chmod` ke změně oprávnění, jako například:

```bash
sudo chown 1000 <HostStoragePath>
sudo chmod 700 <HostStoragePath>
```

Další podrobnosti o možnostech vytváření najdete v dokumentaci k [Docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate).
