---
title: Spravovat výpočetní sítě v Azure Data Box Edge k modulům přístupu | Dokumentace Microsoftu
description: Popisuje, jak rozšířit síť výpočetní prostředky na vaší hraničními zařízeními pole Data a přistupovat k modulům přes externí IP adresa.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 05/17/2019
ms.author: alkohli
ms.openlocfilehash: 907647725dd6795b3b6482476de7442fbbf66114
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2019
ms.locfileid: "65917232"
---
# <a name="enable-compute-network-on-your-azure-data-box-edge"></a>Povolit network výpočetní prostředky na hranici vaší Azure Data Box

Tento článek popisuje, jak moduly, které běží na hranici vaší Azure Data Box můžete přístup k síti výpočetní prostředky na zařízení povolená.

Ke konfiguraci sítě, provedete následující kroky:

- Povolení síťového rozhraní na zařízení Data Box Edge pro výpočetní prostředky
- Přidat modul do sítě výpočetní přístup na hranici vaší datové pole
- Ověřte, zda modul můžete získat přístup k povolené síťové rozhraní

V tomto kurzu použijete modul webovým serverem aplikace předvádí scénář.

## <a name="prerequisites"></a>Požadavky

Než začnete, budete potřebovat:

- Zařízení Data Box Edge s dokončit nastavení zařízení.
- Dokončili jste **konfigurace výpočtů** kroku, jak je uvedeno [kurzu: Transformace dat pomocí Azure Data Box Edge](data-box-edge-deploy-configure-compute-advanced.md#configure-compute) na vašem zařízení. Vaše zařízení by mělo mít přidružený prostředek služby IoT Hub, zařízení IoT a zařízení IoT Edge.

## <a name="enable-network-interface-for-compute"></a>Povolení síťového rozhraní pro službu compute

Pro přístup k modulů spuštěných ve vašem zařízení prostřednictvím externí síť, musíte přiřadit IP adresu do síťového rozhraní na zařízení. Můžete spravovat tyto compute nastavení z vašeho místního webového uživatelského rozhraní.

Proveďte následující kroky na vaší místního webového uživatelského rozhraní a zadejte nastavení výpočetní prostředky.

1. V místním webovém uživatelském rozhraní, přejděte na **konfigurace > Compute nastavení**.  

2. **Povolit** síťové rozhraní, které chcete použít pro připojení k výpočetní modul, který je potřeba spustit na zařízení.

    - Pokud používáte statické IP adresy, zadejte IP adresu pro síťové rozhraní.
    - Pokud používáte DHCP, se automaticky přiřadí IP adresy. Tento příklad používá protokol DHCP.

    ![Povolení nastavení výpočetní 1](media/data-box-edge-extend-compute-access-modules/enable-compute-setting-1.png)

3. Vyberte **použít** aplikaci nastavení. Poznamenejte si IP adresy přiřazené k síťovému rozhraní, pokud používáte DHCP.

    ![Povolení nastavení výpočetní prostředky](media/data-box-edge-extend-compute-access-modules/enable-compute-setting-2.png)

## <a name="add-webserver-app-module"></a>Přidat modul webového serveru aplikace

Proveďte následující kroky k přidání modulu aplikace webového serveru na vašem zařízení Data Box Edge.

1. Přejít k prostředku služby IoT Hub spojené s vaším zařízením Data Box Edge a pak vyberte **zařízení IoT Edge**.
2. Vyberte zařízení IoT Edge, které jsou spojené s vaším zařízením Data Box Edge. Na **podrobnosti o zařízení**vyberte **nastavit moduly**. Na **přidat moduly**vyberte **+ přidat** a pak vyberte **modul IoT Edge**.
3. V **vlastní moduly IoT Edge** okno:

    1. Zadejte **název** pro modul aplikace webového serveru, který chcete nasadit.
    2. Poskytují **identifikátor URI Image** pro bitové kopie modulu. Je načten modul odpovídající zadaný název a značky. V takovém případě `nginx:stable` přetáhne image s stabilní nginx (označené jako stabilní verze) před veřejností [úložiště Dockeru](https://hub.docker.com/_/nginx/).
    3. V **možnosti vytvoření kontejneru**, vložte následující vzorový kód:  

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

        Tato konfigurace vám umožní přístup k modulu s použitím IP network výpočetní přes *http* na TCP port 8080 (s webovým serverem výchozí port je 80).

        ![Zadejte informace o portu v okně vlastní modul IoT Edge](media/data-box-edge-extend-compute-access-modules/module-information.png)

    4. Vyberte **Uložit**.

## <a name="verify-module-access"></a>Ověření přístupu pro modul

1. Ověřte v modulu je úspěšně nasazená a běží. Na **podrobnosti o zařízení** na stránce **moduly** karty, by měla být stav modulu runtime **systémem**.  
2. Připojte se k aplikaci modulu webového serveru. Otevřete okno prohlížeče a zadejte:

    `http://<compute-network-IP-address>:8080`

    Měli byste vidět, že je spuštěna aplikace webového serveru.

    ![Ověření připojení k modulu přes zadaný port](media/data-box-edge-extend-compute-access-modules/verify-connect-module-1.png)

## <a name="next-steps"></a>Další postup

- Přečtěte si, jak [spravovat uživatele pomocí webu Azure Portal](data-box-edge-manage-users.md).
