---
title: Použití místního úložiště zařízení IoT Edge z modulu – Azure IoT Edge | Dokumenty společnosti Microsoft
description: Použijte proměnné prostředí a vytvořte možnosti, které umožní přístup modulu k místnímu úložišti zařízení IoT Edge.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/18/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 079d5845917e63fadcf0466e5a744ed637d704ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75434527"
---
# <a name="give-modules-access-to-a-devices-local-storage"></a>Udělení přístupu k místnímu úložišti zařízení pro moduly

Kromě ukládání dat pomocí služeb úložiště Azure nebo v úložišti kontejnerů vašeho zařízení můžete také vyhradit úložiště na samotném hostitelském zařízení IoT Edge pro lepší spolehlivost, zejména při práci offline.

## <a name="link-module-storage-to-device-storage"></a>Úložiště modulu propojení s úložištěm zařízení

Chcete-li povolit propojení z úložiště modulu do úložiště v hostitelském systému, vytvořte proměnnou prostředí pro váš modul, která odkazuje na složku úložiště v kontejneru. Potom použijte možnosti vytvoření svázat tuto složku úložiště do složky v hostitelském počítači.

Pokud jste například chtěli povolit centru IoT Edge ukládat zprávy do místního úložiště vašeho zařízení a později je načítat, můžete nakonfigurovat proměnné prostředí a možnosti vytvoření na portálu Azure v části **Nastavení běhu.**

1. Pro centrum IoT Edge a agenta IoT Edge přidejte proměnnou prostředí nazvanou **storageFolder,** která odkazuje na adresář v modulu.
1. Pro centrum IoT Edge a agenta IoT Edge přidejte vazby pro připojení místního adresáře na hostitelském počítači k adresáři v modulu. Například:

   ![Přidání možností vytváření a proměnných prostředí pro místní úložiště](./media/how-to-access-host-storage-from-module/offline-storage.png)

Nebo můžete nakonfigurovat místní úložiště přímo v manifestu nasazení. Například:

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

Nahraďte `<HostStoragePath>` a `<ModuleStoragePath>` s hostitelem a platformou úložiště modulu; obě hodnoty musí být absolutní cestou.

Například v systému `"Binds":["/etc/iotedge/storage/:/iotedge/storage/"]` Linux znamená, že adresář **/etc/iotedge/storage** v hostitelském systému je mapován na adresář **/iotedge/storage/** v kontejneru. V systému Windows, jako `"Binds":["C:\\temp:C:\\contemp"]` další příklad, znamená, že adresář **C:\\temp** v hostitelském systému je mapován do adresáře **C:\\kontemp** v kontejneru.

Navíc na zařízeních S Linuxem se ujistěte, že profil uživatele pro váš modul má požadovaná oprávnění pro čtení, zápis a spouštění do adresáře hostitelského systému. Vrátíme-li se k předchozímu příkladu povolení služby IoT Edge hub ukládat zprávy v místním úložišti vašeho zařízení, musíte udělit oprávnění k jeho profilu uživatele, UID 1000. (Agent IoT Edge funguje jako root, takže nepotřebuje další oprávnění.) Existuje několik způsobů, jak spravovat oprávnění k `chown` adresářům v systémech `chmod` Linux, včetně použití ke změně vlastníka adresáře a následné změně oprávnění, například:

```bash
sudo chown 1000 <HostStoragePath>
sudo chmod 700 <HostStoragePath>
```

Další podrobnosti o možnostech vytváření z [dock docs](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate).

## <a name="next-steps"></a>Další kroky

Další příklad přístupu k hostitelskému úložišti z modulu najdete v tématu [Ukládání dat na hraničních zařízeních pomocí azure blob storage na IoT Edge](how-to-store-data-blob.md).
