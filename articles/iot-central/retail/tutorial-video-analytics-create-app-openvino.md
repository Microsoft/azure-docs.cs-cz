---
title: Kurz – vytvoření aplikace pro video Analytics – objekt a detekce pohybu v Azure IoT Central (OpenVINO)
description: V tomto kurzu se dozvíte, jak vytvořit aplikaci video Analytics v IoT Central. Vytvoříte ho, přizpůsobíte ho a připojíte ho k ostatním službám Azure. V tomto kurzu se používá sada Intel OpenVINO &trade; Toolkit pro detekci objektů v reálném čase.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
author: KishorIoT
ms.author: nandab
ms.date: 10/06/2020
ms.openlocfilehash: a201a0300cb4ae0fba1a41b5f64838c17904fa83
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99832092"
---
# <a name="tutorial-create-a-video-analytics---object-and-motion-detection-application-in-azure-iot-central-openvinotrade"></a>Kurz: Vytvoření aplikace pro video Analytics – objekt a detekce pohybu v Azure IoT Central (OpenVINO &trade; )

Jako tvůrce řešení se naučíte, jak vytvořit aplikaci video Analytics pomocí šablony aplikace IoT Central *video Analytics – objekt a detekce pohybu* , Azure IoT Edge zařízení, Azure Media Services a Intel optimalizované hardwarem OpenVINO &trade; pro rozpoznávání objektů a pohybů. Řešení používá prodejny v maloobchodě, které ukazují, jak se bude jednat o běžný podnik potřebný k monitorování bezpečnostních kamer. Toto řešení využívá automatické rozpoznávání objektů v informačním kanálu videa k rychlé identifikaci a hledání zajímavých událostí.

> [!TIP]
> Pokud chcete použít YOLO V3 namísto OpenVINO &trade; pro objekt detekce pohybu, přečtěte si [kurz: Vytvoření aplikace pro video Analytics – objekt a detekce pohybu v Azure IoT Central (Yolo V3)](tutorial-video-analytics-create-app-yolo-v3.md).

[!INCLUDE [iot-central-video-analytics-part1](../../../includes/iot-central-video-analytics-part1.md)]

- [Scratchpad.txt](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/Scratchpad.txt) – tento soubor vám pomůže zaznamenat různé možnosti konfigurace, které potřebujete při práci s těmito kurzy.
- [deployment.openvino.amd64.jsna](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/deployment.openvino.amd64.json)
- [LvaEdgeGatewayDcm.jsna](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/LvaEdgeGatewayDcm.json)
- [state.jszapnuto](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/state.json) , stačí stáhnout tento soubor jenom v případě, že v druhém kurzu plánujete použít zařízení Intel nuc.

[!INCLUDE [iot-central-video-analytics-part2](../../../includes/iot-central-video-analytics-part2.md)]

## <a name="edit-the-deployment-manifest"></a>Úprava manifestu nasazení

Nasadíte IoT Edge modul pomocí manifestu nasazení. V IoT Central můžete manifest importovat jako šablonu zařízení a potom nechat IoT Central spravovat nasazení modulu.

Příprava manifestu nasazení:

1. Otevřete *deployment.openvino.amd64.jsv* souboru, který jste uložili do složky *lva-Configuration* pomocí textového editoru.

1. Vyhledejte nastavení a ujistěte se, `LvaEdgeGatewayModule` že je název obrázku uveden v následujícím fragmentu kódu:

    ```json
    "LvaEdgeGatewayModule": {
        "settings": {
            "image": "mcr.microsoft.com/lva-utilities/lva-edge-iotc-gateway:1.0-amd64",
    ```

1. Přidejte název účtu Media Services do `env` uzlu v `LvaEdgeGatewayModule` části. Poznamenali jste název Media Services účtu v souboru *scratchpad.txt* :

    ```json
    "env": {
        "lvaEdgeModuleId": {
            "value": "lvaEdge"
        },
        "amsAccountName": {
            "value": "<YOUR_AZURE_MEDIA_SERVICES_ACCOUNT_NAME>"
        }
    }
    ```

1. Šablona nezveřejňuje tyto vlastnosti v IoT Central, takže je nutné přidat hodnoty konfigurace Media Services do manifestu nasazení. Vyhledejte `lvaEdge` modul a nahraďte zástupné symboly hodnotami, které jste si poznamenali v souboru *scratchpad.txt* při vytváření účtu Media Services.

    `azureMediaServicesArmId`Je **ID prostředku** , které jste si poznamenali v souboru *scratchpad.txt* při vytváření účtu Media Services.

    Následující tabulka ukazuje hodnoty z **rozhraní API pro připojení k Media Services (JSON)** v souboru *scratchpad.txt* , který byste měli použít v manifestu nasazení:

    | Manifest nasazení       | Scratchpad  |
    | ------------------------- | ----------- |
    | aadTenantId               | AadTenantId |
    | aadServicePrincipalAppId  | AadClientId |
    | aadServicePrincipalSecret | AadSecret   |

    > [!CAUTION]
    > Použijte předchozí tabulku a ujistěte se, že jste přidali správné hodnoty v manifestu nasazení, jinak nebude zařízení fungovat.

    ```json
    {
        "lvaEdge":{
        "properties.desired": {
            "applicationDataDirectory": "/var/lib/azuremediaservices",
            "azureMediaServicesArmId": "[Resource ID from scratchpad]",
            "aadTenantId": "[AADTenantID from scratchpad]",
            "aadServicePrincipalAppId": "[AadClientId from scratchpad]",
            "aadServicePrincipalSecret": "[AadSecret from scratchpad]",
            "aadEndpoint": "https://login.microsoftonline.com",
            "aadResourceId": "https://management.core.windows.net/",
            "armEndpoint": "https://management.azure.com/",
            "diagnosticsEventsOutputName": "AmsDiagnostics",
            "operationalMetricsOutputName": "AmsOperational",
            "logCategories": "Application,Event",
            "AllowUnsecuredEndpoints": "true",
            "TelemetryOptOut": false
            }
        }
    }
    ```

1. Uložte změny.

V tomto kurzu nakonfigurujeme vaše řešení tak, aby používalo &trade; modul OpenVINO pro detekci objektů a pohybů. Následující fragment kódu ukazuje konfiguraci modulu:

```json
"OpenVINOModelServerEdgeAIExtensionModule": {
    "settings": {
        "image": "marketplace.azurecr.io/intel_corporation/open_vino",
        "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"4000/tcp\":[{\"HostPort\":\"4000\"}]}},\"Cmd\":[\"/ams_wrapper/start_ams.py\",\"--ams_port=4000\",\"--ovms_port=9000\"]}"
    },
    "type": "docker",
    "version": "1.0",
    "status": "running",
    "restartPolicy": "always"
}
```

[!INCLUDE [iot-central-video-analytics-part3](../../../includes/iot-central-video-analytics-part3.md)]

### <a name="replace-the-manifest"></a>Nahradit manifest

Na stránce **lva Edge Gateway v2** vyberte **+ nahradit manifest**.

:::image type="content" source="./media/tutorial-video-analytics-create-app-openvino/replace-manifest.png" alt-text="Nahradit manifest":::

Přejděte do složky *lva-Configuration* a vyberte *deployment.openvino.amd64.jsv* souboru manifestu, který jste upravovali dříve. Vyberte **Nahrát**. Po dokončení ověření vyberte **nahradit**.

[!INCLUDE [iot-central-video-analytics-part4](../../../includes/iot-central-video-analytics-part4.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud jste s aplikací hotoví, můžete odebrat všechny prostředky, které jste vytvořili následujícím způsobem:

1. V aplikaci IoT Central přejděte na stránku **aplikace** v části **Správa** . Vyberte **Odstranit**.
1. V Azure Portal odstraňte skupinu prostředků **lva-RG** .
1. V místním počítači zastavte kontejner Docker pro **amp Viewer** .

## <a name="next-steps"></a>Další kroky

Nyní jste vytvořili aplikaci IoT Central pomocí šablony aplikace **video Analytics – objekt a detekce pohybu** , vytvořili jste šablonu zařízení pro zařízení brány a do aplikace jste přidali zařízení brány.

Pokud chcete vyzkoušet video Analytics – objekt a detekci pohybu pomocí IoT Edge modulů, na kterých běží cloudový virtuální počítač s simulovanými video streamy:

> [!div class="nextstepaction"]
> [Vytvoření instance IoT Edge pro video Analytics (virtuální počítač se systémem Linux)](tutorial-video-analytics-iot-edge-vm.md)

Pokud si chcete vyzkoušet video Analytics – objekt a detekci pohybu pomocí IoT Edgech modulů, na kterých běží reálné zařízení s reálným **ONVIF** fotoaparátem:

> [!div class="nextstepaction"]
> [Vytvoření instance IoT Edge pro video Analytics (Intel NUC)](tutorial-video-analytics-iot-edge-nuc.md)