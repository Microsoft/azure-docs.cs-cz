---
title: Kurz – vytvoření aplikace pro video Analytics – objekt a detekce pohybu v Azure IoT Central (YOLO V3)
description: V tomto kurzu se dozvíte, jak vytvořit aplikaci video Analytics v IoT Central. Vytvoříte ho, přizpůsobíte ho a připojíte ho k ostatním službám Azure. V tomto kurzu se používá systém detekce objektů YOLO V3 v reálném čase.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
author: KishorIoT
ms.author: nandab
ms.date: 10/06/2020
ms.openlocfilehash: ecc32908aea2fb474d2ebe5bd94f556527eda814
ms.sourcegitcommit: d6e92295e1f161a547da33999ad66c94cf334563
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/07/2020
ms.locfileid: "96763402"
---
# <a name="tutorial-create-a-video-analytics---object-and-motion-detection-application-in-azure-iot-central-yolo-v3"></a>Kurz: Vytvoření aplikace pro video Analytics – objekt a detekce pohybu v Azure IoT Central (YOLO V3)

Jako tvůrce řešení se naučíte, jak vytvořit aplikaci video Analytics pomocí šablony aplikace IoT Central *video Analytics – objekt a detekce pohybu* , Azure IoT Edge zařízení, Azure Media Services a Yolo V3 v reálném čase a systému detekce pohybů. Řešení používá prodejny v maloobchodě, které ukazují, jak se bude jednat o běžný podnik potřebný k monitorování bezpečnostních kamer. Toto řešení využívá automatické rozpoznávání objektů v informačním kanálu videa k rychlé identifikaci a hledání zajímavých událostí.

> [!TIP]
> Pokud chcete použít OpenVINO &trade; místo Yolo v3 pro objekt k detekci pohybu, přečtěte si [kurz: Vytvoření aplikace video Analytics – objekt a detekce pohybu v Azure IoT Central (OpenVINO &trade; )](tutorial-video-analytics-create-app-openvino.md).

[!INCLUDE [iot-central-video-analytics-part1](../../../includes/iot-central-video-analytics-part1.md)]

- [Scratchpad.txt](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/Scratchpad.txt) – tento soubor vám pomůže zaznamenat různé možnosti konfigurace, které potřebujete při práci s těmito kurzy.
- [deployment.amd64.jsna](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/deployment.amd64.json)
- [LvaEdgeGatewayDcm.jsna](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/LvaEdgeGatewayDcm.json)
- [state.jszapnuto](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/state.json) , stačí stáhnout tento soubor jenom v případě, že v druhém kurzu plánujete použít zařízení Intel nuc.

> [!NOTE]
> Úložiště GitHub obsahuje také zdrojový kód pro moduly **LvaEdgeGatewayModule** a **lvaYolov3** IoT Edge. Další informace o práci se zdrojovým kódem najdete v tématu [sestavování modulů brány lva](tutorial-video-analytics-build-module.md).

[!INCLUDE [iot-central-video-analytics-part2](../../../includes/iot-central-video-analytics-part2.md)]

## <a name="edit-the-deployment-manifest"></a>Úprava manifestu nasazení

Nasadíte IoT Edge modul pomocí manifestu nasazení. V IoT Central můžete manifest importovat jako šablonu zařízení a potom nechat IoT Central spravovat nasazení modulu.

Příprava manifestu nasazení:

1. Otevřete *deployment.amd64.jsv* souboru, který jste uložili do složky *lva-Configuration* pomocí textového editoru.

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

V tomto kurzu nakonfigurujeme vaše řešení tak, aby používalo modul YOLO v3 pro detekci objektů a pohybů. Následující fragment kódu ukazuje konfiguraci modulu:

```json
"lvaYolov3": {
    "settings": {
        "image": "mcr.microsoft.com/lva-utilities/yolov3-onnx:1.0"
    },
    "type": "docker",
    "status": "running",
    "restartPolicy": "always",
    "version": "1.0"
}
```

[!INCLUDE [iot-central-video-analytics-part3](../../../includes/iot-central-video-analytics-part3.md)]

### <a name="replace-the-manifest"></a>Nahradit manifest

Na stránce **lva Edge Gateway v2** vyberte **+ nahradit manifest**.

:::image type="content" source="./media/tutorial-video-analytics-create-app-yolo-v3/replace-manifest.png" alt-text="Nahradit manifest":::

Přejděte do složky *lva-Configuration* a vyberte *deployment.amd64.jsv* souboru manifestu, který jste upravovali dříve. Vyberte **Nahrát**. Po dokončení ověření vyberte **nahradit**.

[!INCLUDE [iot-central-video-analytics-part4](../../../includes/iot-central-video-analytics-part4.md)]
