---
title: Nasazení na IoT Edge pro Linux ve Windows – Azure
description: Tento článek poskytuje pokyny, jak nasadit na zařízení s Windows na IoT Edge pro Linux.
ms.topic: how-to
ms.date: 02/18/2021
ms.openlocfilehash: 5cf4184857f12065d808d7b528dbfe8258950cd8
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101744776"
---
# <a name="deploy-to-an-iot-edge-for-linux-on-windows-eflow-device"></a>Nasazení na IoT Edge pro Linux v zařízení s Windows (EFLOW)

V tomto článku se dozvíte, jak nasadit Live video Analytics na hraničním zařízení, které má [IoT Edge pro Linux ve Windows (EFLOW)](https://docs.microsoft.com/azure/iot-edge/iot-edge-for-linux-on-windows?view=iotedge-2018-06). Po dokončení kroků v tomto dokumentu budete moct spustit [mediální graf](media-graph-concept.md) , který detekuje pohyb ve videu a vysílá takové události do centra IoT v cloudu. Pak můžete přepnout do Media graphu pro pokročilé scénáře a využít možnosti Live video Analytics pro vaše zařízení IoT Edge se systémem Windows.

## <a name="prerequisites"></a>Požadavky 

* Účet Azure, který má aktivní předplatné. Pokud ho ještě nemáte, [Vytvořte si bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

    > [!NOTE]
    > Budete potřebovat předplatné Azure s oprávněním pro vytváření instančních objektů (Tato **role vlastníka** poskytuje). Pokud nemáte správná oprávnění, obraťte se na správce účtu, abyste vám udělili správná oprávnění.
* [Visual Studio Code](https://code.visualstudio.com/) ve vývojovém počítači. Ujistěte se, že máte [rozšíření Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* Přečtěte si, [co je EFLOW](https://aka.ms/AzEFLOW-docs).

## <a name="deployment-steps"></a>Kroky nasazení

Následující část znázorňuje Celkový tok dokumentu a v 5 jednoduchých krocích, které byste měli mít všechno nastavené pro spouštění živých videí Analytics na zařízení s Windows, které má EFLOW:

:::image type="content" source="./media/deploy-iot-edge-linux-on-windows/eflow.png" alt-text="Diagram IoT Edge pro Linux v systému Windows (EFLOW)":::

1. [Nainstalujte](https://aka.ms/AzEFLOW-install) do zařízení s Windows EFLOW. 

    1. Pokud používáte počítač s Windows, pak na úvodní stránce [centra pro správu systému Windows](https://docs.microsoft.com/windows-server/manage/windows-admin-center/overview) pod seznamem připojení uvidíte připojení k místnímu hostiteli, které představuje počítač, na kterém je spuštěný centrum pro správu systému Windows. 
    1. Tady se zobrazí i všechny další servery, počítače nebo clustery, které spravujete.
    1. Pomocí centra pro správu Windows můžete nainstalovat a spravovat Azure EFLOW na místních zařízeních nebo na vzdálených spravovaných zařízeních. V této příručce se připojení místního hostitele obsluhuje jako cílové zařízení pro nasazení Azure IoT Edge pro Linux ve Windows. Proto vidíte, že je localhost také uvedeno jako zařízení IoT Edge.

    :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/windows-admin-center.png" alt-text="Postup nasazení – centrum pro správu systému Windows":::
1. Pokud se k němu chcete připojit, klikněte na zařízení IoT Edge a měli byste vidět kartu s přehledem a příkazovým prostředím. Karta příkazové prostředí je místo, kde můžete vystavit příkazy pro vaše hraniční zařízení.
 
    :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/azure-iot-edge-manager.png" alt-text="Kroky nasazení – Azure IoT Edge Manager":::
1. V příkazovém prostředí zadejte následující příkaz:
    
    ```bash
    bash -c "$(curl -sL https://aka.ms/lva-edge/prep_device)"
    ```

    Modul Live video Analytics běží na hraničním zařízení s [místními uživatelskými účty](deploy-iot-edge-device.md#create-and-use-local-user-account-for-deployment), které nejsou privilegované. Kromě toho [potřebuje určité místní složky](deploy-iot-edge-device.md#granting-permissions-to-device-storage) pro ukládání konfiguračních dat aplikace. A konečně, v této příručce se naučíme [simulátor RTSP](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) , který přenáší informační kanál v reálném čase do lva modulu pro účely analýzy. Tento simulátor přebírá jako vstup předem zaznamenaný videosoubory soubory ze vstupního adresáře. 
    
    Skript pro přípravu zařízení použitý výše automatizuje tyto úlohy, takže můžete spustit jeden příkaz a mít všechny relevantní vstupní a konfigurační složky, vstupní soubory videa a uživatelské účty s oprávněními vytvořenými bez problémů. Po úspěšném dokončení příkazu byste měli vidět následující složky vytvořené na hraničním zařízení. 
    
    * `/home/lvaedgeuser/samples`
    * `/home/lvaedgeuser/samples/input`
    * `/var/lib/azuremediaservices`
    * `/var/media`
    
    Poznamenejte si videosoubory (*. MKV) ve složce/Home/lvaedgeuser/Samples/Input, která slouží jako vstupní soubory, které se mají analyzovat. 
    
    :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/azure-iot-edge-manager-analysis.png" alt-text="Výsledcích":::
1. Teď, když máte nastavené hraniční zařízení, zaregistrované do centra a úspěšně běží se správnými strukturami složek, dalším krokem je nastavení následujících dalších prostředků Azure a nasazení modulu LVA. 

    * Účet úložiště
    * Účet Azure Media Services

    K nasazení požadovaných prostředků ve vašem předplatném Azure doporučujeme použít [skript pro nastavení prostředků Live video Analytics](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) . To můžete provést pomocí těchto kroků:

    1. Otevřete [Azure Cloud Shell](https://ms.portal.azure.com/#cloudshell/).

        :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/azure-resources-cloud-shell.png" alt-text="Cloud Shell":::
    1. Pokud používáte Cloud Shell poprvé, budete vyzváni k výběru předplatného pro vytvoření účtu úložiště a sdílené složky Microsoft Azure souborů. Vyberte **vytvořit úložiště** a vytvořte účet úložiště pro informace o cloud Shell relaci. Tento účet úložiště je oddělený od účtu, který vytvoří skript pro použití s vaším účtem Azure Media Services.
    1. V rozevírací nabídce na levé straně okna Cloud Shell vyberte bash jako své prostředí.

        :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/bash.png" alt-text="Prostředí bash":::
    1. Spusťte následující příkaz.

        ```bash
        bash -c "$(curl -sL https://aka.ms/lva-edge/setup-resources-for-samples)"
        ```
        
        Po zobrazení výzvy k výběru vlastního hraničního zařízení jako IoT Edge zařízení vyberte možnost **Y** , protože jste předtím vytvořili zařízení i IoT Hub. Zobrazí se také výzva k zadání názvu IoT Hub a IoT Edge ID zařízení. Obojí můžete získat tak, že se přihlásíte do Azure Portal a kliknete na IoT Hub a potom v okně portálu IoT Hub kliknete na možnost IoT Edge.

        :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/iot-edge-devices.png" alt-text="Viz IoT Edge zařízení":::

    Po dokončení se můžete přihlásit zpátky do příkazového prostředí IoT Edge zařízení a spustit následující příkaz.
    
    `sudo iotedge list`
    
    Na hraničním zařízení by se mělo zobrazit následující čtyři moduly nasazené a spuštěné. Všimněte si, že skript pro vytvoření prostředku nasadí modul LVA spolu s modulem IoT Edge (edgeAgent a edgeHub) a modulem simulátoru RTSP pro poskytování simulovaného kanálu videa RTSP.
    
    :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/running.png" alt-text="Zobrazit stav":::
1. S nasazenými a nastavenými moduly jste připraveni spustit první LVA mediální graf na EFLOW. Můžete spustit jednoduchý graf detekce pohybu níže a vizualizovat výsledky provedením následujících kroků:

    :::image type="content" source="./media/analyze-live-video/motion-detection.svg" alt-text="Graf detekce pohybu":::

    1. [Nakonfigurujte](get-started-detect-motion-emit-events-quickstart.md#configure-the-azure-iot-tools-extension) rozšíření Azure IoT Tools.
    
        > [!Note]
        > Použijte následující cestu: `~/clouddrive/lva_byod/appsettings.json. - instead of ~/clouddrive/lva-sample/appsettings.json` .
    1. Nastavte topologii, vytvořte instanci grafu a aktivujte ji pomocí těchto [volání přímých metod](get-started-detect-motion-emit-events-quickstart.md#use-direct-method-calls).
    1. [Sledujte výsledky](get-started-detect-motion-emit-events-quickstart.md#observe-results) v centru.
    1. Vyvolat [metody vyčištění](get-started-detect-motion-emit-events-quickstart.md#invoke-graphinstancedeactivate).
    1. Pokud ještě nepotřebujete, odstraňte prostředky.

        > [!IMPORTANT]
        > Neodstraněné prostředky můžou být pořád aktivní a účtují se náklady na Azure.
    
## <a name="next-steps"></a>Další kroky

* Vyzkoušejte si detekci pohybu spolu se záznamem relevantních videí v cloudu. Postupujte podle kroků v části [detekce pohybu, záznam videoklipů a Media Services](detect-motion-record-video-clips-media-services-quickstart.md#review-the-sample-video) rychlý Start.
* Spustit [AI na živém videu](use-your-model-quickstart#overview) (můžete přeskočit nezbytnou instalaci, protože už byla dokončena výše)
* Použijte naše [rozšíření vs Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.live-video-analytics-edge) k zobrazení dalších mediálních grafů.
* Místo používání simulátoru RTSP použijte [fotoaparát IP](https://en.wikipedia.org/wiki/IP_camera)  , který podporuje RTSP. Kamery protokolu IP, které podporují protokol RTSP, najdete na stránce vyhovující [ONVIF produktů](https://www.onvif.org/conformant-products/) . Vyhledejte zařízení, která jsou v souladu s profily G, S nebo T.

