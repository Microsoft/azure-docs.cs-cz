---
title: Správa výpočetní sítě na Azure Data Box Edge pro přístup k modulům| Dokumenty společnosti Microsoft
description: Popisuje, jak rozšířit výpočetní síť na vašem Data Box Edge pro přístup k modulům přes externí IP.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 05/17/2019
ms.author: alkohli
ms.openlocfilehash: 907647725dd6795b3b6482476de7442fbbf66114
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "65917232"
---
# <a name="enable-compute-network-on-your-azure-data-box-edge"></a>Povolení výpočetní sítě na Azure Data Box Edge

Tento článek popisuje, jak moduly spuštěné na Azure Data Box Edge přístup výpočetní sítě povolené na zařízení.

Chcete-li síť nakonfigurovat, provedete následující kroky:

- Povolení síťového rozhraní na zařízení Data Box Edge pro výpočetní výkon
- Přidání modulu pro přístup k výpočetní síti na okraji datové schránky
- Ověřte, zda má modul přístup k povolenému síťovému rozhraní.

V tomto kurzu budete používat modul aplikace webového serveru k předvedení scénáře.

## <a name="prerequisites"></a>Požadavky

Než začnete, budete potřebovat:

- Zařízení Data Box Edge s dokončeným nastavením zařízení.
- Krok Konfigurace **výpočetního výkonu** jste dokončili podle [kurzu: Transformace dat pomocí Azure Data Box Edge](data-box-edge-deploy-configure-compute-advanced.md#configure-compute) na vašem zařízení. Vaše zařízení by mělo mít přidružený prostředek služby IoT Hub, zařízení IoT a zařízení IoT Edge.

## <a name="enable-network-interface-for-compute"></a>Povolení síťového rozhraní pro výpočetní výkon

Chcete-li získat přístup k modulům spuštěným v zařízení prostřednictvím externí sítě, budete muset přiřadit IP adresu síťovému rozhraní v zařízení. Tato nastavení výpočetních prostředků můžete spravovat z místního webového uživatelského prostředí.

Chcete-li nakonfigurovat nastavení výpočetních prostředků, postupujte v místním webovém uživatelském rozhraní následujícím postupem.

1. V místním webovém uživatelském prostředí přejděte na **nastavení konfigurace > výpočetních prostředků**.  

2. **Povolte** síťové rozhraní, které chcete použít k připojení k výpočetnímu modulu, který budete na zařízení spouštět.

    - Pokud používáte statické adresy IP, zadejte adresu IP pro síťové rozhraní.
    - Pokud používáte službu DHCP, jsou adresy IP přiřazeny automaticky. Tento příklad používá dhcp.

    ![Povolení nastavení výpočetních prostředků 1](media/data-box-edge-extend-compute-access-modules/enable-compute-setting-1.png)

3. Vyberte **Použít,** chcete-li použít nastavení. Poznamenejte si adresu IP přiřazenou síťovému rozhraní, pokud používáte službu DHCP.

    ![Povolení nastavení výpočetních prostředků](media/data-box-edge-extend-compute-access-modules/enable-compute-setting-2.png)

## <a name="add-webserver-app-module"></a>Přidat modul aplikace webového serveru

Následujícím postupem přidejte do zařízení Data Box Edge modul aplikace webového serveru.

1. Přejděte na prostředek služby IoT Hub přidružený k zařízení Data Box Edge a vyberte **zařízení IoT Edge**.
2. Vyberte zařízení IoT Edge přidružené k vašemu zařízení Data Box Edge. V části **Podrobnosti o zařízení**vyberte **Nastavit moduly**. V **možnosti Přidat moduly**vyberte **+ Přidat** a pak vyberte Modul **IoT Edge .**
3. V okně **vlastních modulů IoT Edge:**

    1. Zadejte **název** modulu aplikace webového serveru, který chcete nasadit.
    2. Zadejte **identifikátor URI obrázku** pro bitovou kopii modulu. Je načten modul odpovídající zadaný název a značky. V takovém `nginx:stable` případě vytáhne stabilní obrázek nginx (označený jako stabilní) z veřejného [úložiště Dockeru](https://hub.docker.com/_/nginx/).
    3. V **možnostech vytvoření kontejneru**vložte následující ukázkový kód:  

        ```
        {
            "HostConfig": {
                "PortBindings": {
                    "80/tcp": [
                        {
                            "HostPort": "8080"
                        }
                    ]
                }
            }
        }
        ```

        Tato konfigurace umožňuje přístup k modulu pomocí ip výpočetní sítě přes *http* na portu TCP 8080 (s výchozím portem webového serveru je 80).

        ![Zadání informací o portu ve vlastním modulu IoT Edge](media/data-box-edge-extend-compute-access-modules/module-information.png)

    4. Vyberte **Uložit**.

## <a name="verify-module-access"></a>Ověření přístupu k modulu

1. Ověřte, zda je modul úspěšně nasazen a je spuštěn. Na stránce **Podrobnosti o zařízení** by měl být na kartě **Moduly** **spuštěn**stav modulu za běhu .  
2. Připojte se k modulu aplikace webového serveru. Otevřete okno prohlížeče a zadejte:

    `http://<compute-network-IP-address>:8080`

    Měli byste vidět, že aplikace webového serveru je spuštěna.

    ![Ověření připojení k modulu přes zadaný port](media/data-box-edge-extend-compute-access-modules/verify-connect-module-1.png)

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [spravovat uživatele pomocí webu Azure Portal](data-box-edge-manage-users.md).
