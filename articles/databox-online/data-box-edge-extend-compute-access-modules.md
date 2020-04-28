---
title: Správa výpočetní sítě v Azure Data Box Edge pro přístup k modulům | Microsoft Docs
description: V této části najdete popis postupu rozšiřování výpočetní sítě v Data Box Edge pro přístup k modulům prostřednictvím externí IP adresy.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 05/17/2019
ms.author: alkohli
ms.openlocfilehash: 907647725dd6795b3b6482476de7442fbbf66114
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "65917232"
---
# <a name="enable-compute-network-on-your-azure-data-box-edge"></a>Povolte ve svém Azure Data Box Edge výpočetní síť.

Tento článek popisuje, jak můžou moduly běžící na vašem Azure Data Box Edge přistupovat k výpočetní síti povolené na zařízení.

Chcete-li konfigurovat síť, proveďte následující kroky:

- Povolení síťového rozhraní na zařízení Data Box Edge pro výpočetní výkon
- Přidejte modul pro přístup k výpočetní síti na Data Box Edge
- Ověřte, zda má modul přístup k aktivovanému síťovému rozhraní.

V tomto kurzu použijete modul aplikace webserver k předvedení scénáře.

## <a name="prerequisites"></a>Požadavky

Než začnete, budete potřebovat:

- Zařízení Data Box Edge s nastavením zařízení bylo dokončeno.
- Dokončili jste **konfiguraci výpočetního** kroku podle [kurzu: transformace dat pomocí Azure Data box Edge](data-box-edge-deploy-configure-compute-advanced.md#configure-compute) na vašem zařízení. Vaše zařízení by mělo mít přidružený prostředek IoT Hub, zařízení IoT a IoT Edge zařízení.

## <a name="enable-network-interface-for-compute"></a>Povolit síťové rozhraní pro výpočetní prostředky

Pokud chcete získat přístup k modulům běžícím na vašem zařízení přes externí síť, musíte na svém zařízení přiřadit IP adresu k síťovému rozhraní. Tato výpočetní nastavení můžete spravovat z místního webového uživatelského rozhraní.

Proveďte následující kroky na svém místním webovém uživatelském rozhraní pro konfiguraci výpočetních nastavení.

1. V místním webovém uživatelském rozhraní přejdete do **nastavení konfigurace > COMPUTE**.  

2. **Povolte** síťové rozhraní, které chcete použít pro připojení ke výpočetnímu modulu, který budete spouštět na zařízení.

    - Pokud používáte statické IP adresy, zadejte IP adresu pro síťové rozhraní.
    - Pokud používáte protokol DHCP, IP adresy se přiřadí automaticky. V tomto příkladu se používá protokol DHCP.

    ![Povolit výpočetní nastavení 1](media/data-box-edge-extend-compute-access-modules/enable-compute-setting-1.png)

3. Chcete-li použít nastavení, klikněte na tlačítko **použít** . Pokud používáte protokol DHCP, poznamenejte si IP adresu přiřazenou k síťovému rozhraní.

    ![Povolit nastavení výpočtů](media/data-box-edge-extend-compute-access-modules/enable-compute-setting-2.png)

## <a name="add-webserver-app-module"></a>Přidat modul aplikace pro webserver

Pokud chcete přidat modul aplikace webserver do zařízení Data Box Edge, proveďte následující kroky.

1. Přejít na prostředek IoT Hub přidružený k vašemu zařízení Data Box Edge a pak vyberte **IoT Edge zařízení**.
2. Vyberte zařízení IoT Edge přidružené k vašemu zařízení Data Box Edge. V části **Podrobnosti o zařízení**vyberte **nastavit moduly**. V nabídce **přidat moduly**vyberte **+ Přidat** a pak vyberte **IoT Edge modul**.
3. V okně **IoT Edge vlastní moduly** :

    1. Zadejte **název** modulu aplikace webserver, který chcete nasadit.
    2. Zadejte **identifikátor URI image** pro Image modulu. Načte se modul, který odpovídá zadanému názvu a značkám. V takovém případě `nginx:stable` načte stabilní image Nginx (označená jako stabilní) z veřejného [úložiště Docker](https://hub.docker.com/_/nginx/).
    3. V **možnostech vytvoření kontejneru**vložte následující vzorový kód:  

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

        Tato konfigurace umožňuje přístup k modulu pomocí výpočetní síťové IP adresy přes *http* na portu TCP 8080 (výchozí port serveru WebServer je 80).

        ![Zadání informací o portu v okně IoT Edge vlastní modul](media/data-box-edge-extend-compute-access-modules/module-information.png)

    4. Vyberte **Uložit**.

## <a name="verify-module-access"></a>Ověřit přístup k modulu

1. Ověřte, že je modul úspěšně nasazený a běží. Na stránce **Podrobnosti o zařízení** na kartě **moduly** by měl být **spuštěný**běhový stav modulu.  
2. Připojte se k modulu Web Server App. Otevřete okno prohlížeče a zadejte:

    `http://<compute-network-IP-address>:8080`

    Měla by se zobrazit, že je spuštěná aplikace na serveru.

    ![Ověřit připojení k modulu přes zadaný port](media/data-box-edge-extend-compute-access-modules/verify-connect-module-1.png)

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [spravovat uživatele pomocí webu Azure Portal](data-box-edge-manage-users.md).
