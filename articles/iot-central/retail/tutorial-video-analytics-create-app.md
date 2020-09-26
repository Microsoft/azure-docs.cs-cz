---
title: Kurz – vytvoření aplikace pro video Analytics – objekt a detekce pohybu v Azure IoT Central
description: V tomto kurzu se dozvíte, jak vytvořit aplikaci video Analytics v IoT Central. Vytvoříte ho, přizpůsobíte ho a připojíte ho k ostatním službám Azure.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
author: KishorIoT
ms.author: nandab
ms.date: 07/31/2020
ms.openlocfilehash: c7ddff70d24fe87380c4bf1439811042418ac76a
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91369565"
---
# <a name="tutorial-create-a-video-analytics---object-and-motion-detection-application-in-azure-iot-central"></a>Kurz: Vytvoření aplikace video Analytics – objekt a detekce pohybu v Azure IoT Central

Jako tvůrce řešení se naučíte, jak vytvořit aplikaci video Analytics pomocí šablony aplikace IoT Central *video Analytics – objekt a detekce pohybu* , Azure IoT Edge zařízení a Azure Media Services. Řešení používá prodejny v maloobchodě, které ukazují, jak se bude jednat o běžný podnik potřebný k monitorování bezpečnostních kamer. Toto řešení využívá automatické rozpoznávání objektů v informačním kanálu videa k rychlé identifikaci a hledání zajímavých událostí.

Ukázková aplikace zahrnuje dvě simulovaná zařízení a jednu IoT Edge bránu. V následujících kurzech se dozvíte dva přístupy k experimentování s funkcemi brány a jejich pochopení:

* Vytvořte bránu IoT Edge na virtuálním počítači Azure a připojte simulovanou kameru.
* Vytvořte bránu IoT Edge na reálném zařízení, jako je Intel NUC, a připojte skutečnou kameru.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Použití šablony aplikace Azure IoT Central video Analytics k vytvoření aplikace pro maloobchodní obchod
> * Přizpůsobení nastavení aplikace
> * Vytvoření šablony zařízení pro zařízení IoT Edge brány
> * Přidání zařízení brány do aplikace IoT Central

## <a name="prerequisites"></a>Požadavky

K dokončení této série kurzů potřebujete:

* Předplatné Azure. Pokud nemáte předplatné Azure, můžete ho vytvořit na [stránce registrace do Azure](https://aka.ms/createazuresubscription).
* Pokud používáte skutečnou kameru, potřebujete připojení mezi IoT Edgeovým zařízením a fotoaparátem a potřebujete kanál **protokolu streamování v reálném čase** .

## <a name="initial-setup"></a>Počáteční nastavení

V těchto kurzech aktualizujete a použijete několik konfiguračních souborů. Počáteční verze těchto souborů jsou k dispozici v úložišti GitHub [lva-Gateway](https://github.com/Azure/live-video-analytics/tree/master/ref-apps/lva-edge-iot-central-gateway) . Úložiště obsahuje také textový soubor scratchpad, který můžete stáhnout a použít k nahrání hodnot konfigurace ze služeb, které nasazujete.

Vytvořte složku s názvem *lva-Configuration* na místním počítači pro uložení kopií těchto souborů. Potom klikněte pravým tlačítkem na každý z následujících odkazů a zvolte **Uložit jako** a uložte soubor do složky *lva-Configuration* :

- [Scratchpad.txt](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/Scratchpad.txt)
- [deployment.amd64.jsna](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/deployment.amd64.json)
- [LvaEdgeGatewayDcm.jsna](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/LvaEdgeGatewayDcm.json)
- [state.jsna](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/state.json)

> [!NOTE]
> Úložiště GitHub obsahuje také zdrojový kód pro moduly **LvaEdgeGatewayModule** a **lvaYolov3** IoT Edge. Další informace o práci se zdrojovým kódem najdete v tématu [sestavování modulů brány lva](tutorial-video-analytics-build-module.md).

## <a name="deploy-and-configure-azure-media-services"></a>Nasazení a konfigurace Azure Media Services

Řešení používá účet Azure Media Services k uložení videoklipů detekce objektů, které provedla zařízení IoT Edge brány.

Když vytváříte účet Media Services:

- Je potřeba zadat název účtu, předplatné Azure, umístění, skupinu prostředků a účet úložiště. Při vytváření účtu Media Services vytvořte nový účet úložiště s použitím výchozích nastavení.

- Vyberte oblast **východní USA** pro dané umístění.

- Vytvořte novou skupinu prostředků s názvem *lva-RG* v oblasti **Východní USA** pro účty Media Services a úložiště. Po dokončení kurzů je snadné odebrat všechny prostředky odstraněním skupiny prostředků *lva-RG* .

Vytvořte [účet Media Services v Azure Portal](https://portal.azure.com/?r=1#create/Microsoft.MediaService).

> [!TIP]
> Tyto kurzy používají **východní USA** oblasti ve všech příkladech. V případě potřeby můžete použít nejbližší oblast.

Poznamenejte si název **Media Services** účtu v souboru *scratchpad.txt* .

Po dokončení nasazení přejděte na stránku **vlastnosti** pro váš účet **Media Services** . Poznamenejte si **ID prostředku** v souboru *scratchpad.txt* . tuto hodnotu použijete později při konfiguraci modulu IoT Edge.

Dále nakonfigurujte Azure Active Directory instančního objektu pro prostředek Media Services. Vyberte **přístup přes rozhraní API** a pak **ověřování instančního objektu**. Vytvořte novou aplikaci Azure Active Directory se stejným názvem jako má prostředek Media Services a vytvořte tajný klíč s popisem *IoT Edge přístupu*.

:::image type="content" source="./media/tutorial-video-analytics-create-app/media-service-authentication.png" alt-text="Konfigurace aplikace AAD pro AMS":::

Po vygenerování tajného klíče se posuňte dolů k části **kopírování přihlašovacích údajů pro připojení k oddílu aplikace instančního objektu** . Pak vyberte **JSON**. Všechny informace o přihlašovacích údajích můžete kopírovat odsud v jednom z nich. Poznamenejte si tyto informace v souboru *scratchpad.txt* , později je použijete při konfiguraci zařízení IoT Edge.

> [!WARNING]
> Tato možnost se dá zobrazit a uložit v tajnosti. Pokud ho ztratíte, budete muset vygenerovat další tajný klíč.

## <a name="create-the-azure-iot-central-application"></a>Vytvoření aplikace Azure IoT Central

V této části vytvoříte novou aplikaci Azure IoT Central ze šablony. Tuto aplikaci použijete v rámci série kurzů k sestavení kompletního řešení.

Vytvoření nové aplikace Azure IoT Central:

1. Přejděte na web [Azure IoT Central Správce aplikací](https://aka.ms/iotcentral) .

1. Přihlaste se pomocí přihlašovacích údajů, které používáte pro přístup k předplatnému Azure.

1. Pokud chcete začít vytvářet novou aplikaci IoT Central Azure, vyberte na stránce **sestavení** možnost **Nová aplikace** .

1. Vyberte **Retail (maloobchod**). Na stránce prodej se zobrazí několik šablon maloobchodních aplikací.

Vytvoření nové aplikace video Analytics:

1. Vyberte šablonu aplikace pro **video Analytics – objekt a detekce pohybu** . Tato šablona obsahuje šablony zařízení pro zařízení použitá v tomto kurzu. Šablona obsahuje ukázkové řídicí panely, které operátory můžou použít k provádění úloh, jako je monitorování a Správa fotoaparátů.

1. Volitelně můžete zvolit popisný **název aplikace**. Tato aplikace je založená na fiktivním maloobchodním obchodě s názvem Northwind Traders. V tomto kurzu se používá **název aplikace** *Northwind Traders video Analytics*.

    > [!NOTE]
    > Použijete-li popisný **název aplikace**, je stále nutné použít jedinečnou hodnotu pro **adresu URL**aplikace.

1. Pokud máte předplatné Azure, vyberte svůj **adresář**, **předplatné Azure**a **USA** jako **umístění**. Pokud předplatné nemáte, můžete povolit **7 dní bezplatnou zkušební verzi** a dokončit požadované kontaktní údaje. V tomto kurzu se používají tři zařízení – dva kamery a IoT Edge zařízení. Pokud nepoužijete bezplatnou zkušební verzi, bude se vám účtovat využití.

    Další informace o adresářích, předplatných a umístěních najdete v tématu [rychlý Start k vytvoření aplikace](../core/quick-deploy-iot-central.md).

1. Vyberte **Vytvořit**.

    :::image type="content" source="./media/tutorial-video-analytics-create-app/new-application.png" alt-text="Stránka aplikace Azure IoT Central":::

### <a name="retrieve-the-configuration-data"></a>Načíst konfigurační data

Později v tomto kurzu při konfiguraci IoT Edge brány potřebujete některá konfigurační data z aplikace IoT Central. IoT Edge zařízení potřebuje tyto informace k registraci v nástroji a k aplikaci se připojí.

V části **Správa** vyberte **svou aplikaci** a poznamenejte si **adresu URL aplikace** a **ID aplikace** v souboru *scratchpad.txt* :

:::image type="content" source="./media/tutorial-video-analytics-create-app/administration.png" alt-text="Snímek obrazovky se zobrazí v podokně Správa stránky video Analytics se zvýrazněnou možností aplikace U R L a aplikace I D.":::

Vyberte **tokeny API** a vygenerujte nový token s názvem **LVAEdgeToken** pro roli **operátora** :

:::image type="content" source="./media/tutorial-video-analytics-create-app/token.png" alt-text="Generovat token":::

Poznamenejte si token v souboru *scratchpad.txt* pro pozdější provedení. Po zavření dialogového okna nelze token znovu zobrazit.

V části **Správa** vyberte **připojení zařízení**a pak vyberte **SAS-IoT-Devices**.

Poznamenejte si **primární klíč** pro zařízení v souboru *scratchpad.txt* . Pomocí tohoto *tokenu sdíleného přístupového podpisu primární skupiny* můžete později nakonfigurovat IoT Edge zařízení.

## <a name="edit-the-deployment-manifest"></a>Úprava manifestu nasazení

Nasadíte IoT Edge modul pomocí manifestu nasazení. V IoT Central můžete manifest importovat jako šablonu zařízení a potom nechat IoT Central spravovat nasazení modulu.

Příprava manifestu nasazení:

1. Otevřete *deployment.amd64.jsv* souboru, který jste uložili do složky *lva-Configuration* pomocí textového editoru.

1. Vyhledejte `LvaEdgeGatewayModule` nastavení a změňte název bitové kopie, jak je znázorněno v následujícím fragmentu kódu:

    ```json
    "LvaEdgeGatewayModule": {
        "settings": {
            "image": "mcr.microsoft.com/lva-utilities/lva-edge-iotc-gateway:1.0-amd64",
    ```

1. Přidejte název účtu Media Services do `env` uzlu v `LvaEdgeGatewayModule` části. Tento název účtu jste si poznamenali v souboru *scratchpad.txt* :

    ```json
    "env": {
        "lvaEdgeModuleId": {
            "value": "lvaEdge"
        },
        "amsAccountName": {
            "value": "<YOUR_AZURE_MEDIA_ACCOUNT_NAME>"
        }
    }
    ```

1. Šablona nezveřejňuje tyto vlastnosti v IoT Central, takže je nutné přidat hodnoty konfigurace Media Services do manifestu nasazení. Vyhledejte `lvaEdge` modul a nahraďte zástupné symboly hodnotami, které jste si poznamenali v souboru *scratchpad.txt* při vytváření účtu Media Services.

    `azureMediaServicesArmId`Je **ID prostředku** , které jste si poznamenali v souboru *scratchpad.txt* při vytváření účtu Media Services.

    Poznamenali jste `aadTenantId` , `aadServicePrincipalAppId` a `aadServicePrincipalSecret` v souboru *scratchpad.txt* , když jste vytvořili instanční objekt pro účet Media Services:

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

Volitelně můžete modul Yolov3 nahradit [ &trade; modulem rozšíření serverově optimalizovaného OpenVINOho modelu – Edge AI](https://github.com/openvinotoolkit/model_server/tree/ams/extras/ams_wrapper) od společnosti Intel. Můžete si stáhnout vzorový manifest nasazení [deployment.openvino.amd64.jsv](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/deployment.openvino.amd64.json). Tento manifest nahrazuje konfigurační nastavení pro `lvaYolov3` modul s následující konfigurací:

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

## <a name="create-the-azure-iot-edge-gateway-device"></a>Vytvoření zařízení Azure IoT Edge brány

Aplikace video Analytics – objekt a detekce pohybu obsahují šablonu zařízení **lva Edge** a zařízení **detekce pohybů lva Edge** . V této části vytvoříte šablonu zařízení brány pomocí manifestu nasazení a přidáte zařízení brány do vaší aplikace IoT Central.

### <a name="create-a-device-template-for-the-lva-edge-gateway"></a>Vytvoření šablony zařízení pro bránu LVA Edge

Import manifestu nasazení a vytvoření šablony zařízení **lva Edge Gateway** :

1. V aplikaci IoT Central přejděte na **šablony zařízení**a vyberte **+ Nový**.

1. Na stránce **Vybrat typ šablony** vyberte dlaždici **Azure IoT Edge** . Pak vyberte **Další: přizpůsobit**.

1. Na stránce **nahrát manifest nasazení Azure IoT Edge** jako název šablony zadejte *lva Edge Gateway* a v **zařízení brány se zařízením pro příjem dat**.

    Zatím nevybírejte manifest nasazení. Pokud tak učiníte, Průvodce nasazením očekává rozhraní pro každý modul, ale je nutné vystavit rozhraní pro **LvaEdgeGatewayModule**. Manifest nahrajete v pozdějším kroku.

    :::image type="content" source="./media/tutorial-video-analytics-create-app/upload-deployment-manifest.png" alt-text="Nenahrávat manifest nasazení":::

    Vyberte **Další: Kontrola**.

1. Na stránce **Kontrola** vyberte **vytvořit**.

### <a name="import-the-device-capability-model"></a>Import modelu schopností zařízení

Šablona zařízení musí zahrnovat model schopností zařízení. Na stránce **Brána lva Edge** vyberte dlaždici **importovat model schopností** . Přejděte do složky *lva-Configuration* , kterou jste předtím vytvořili, a vyberte *LvaEdgeGatewayDcm.jsv* souboru.

Šablona zařízení **brány lva Edge** teď obsahuje **modul brány lva Edge** spolu se třemi rozhraními: **informace o zařízení**, **nastavení brány lva Edge**a **rozhraní lva Edge Gateway**.

### <a name="replace-the-manifest"></a>Nahradit manifest

Na stránce **brány lva Edge** vyberte **+ nahradit manifest**.

:::image type="content" source="./media/tutorial-video-analytics-create-app/replace-manifest.png" alt-text="Nahradit manifest":::

Přejděte do složky *lva-Configuration* a vyberte *deployment.amd64.jsv* souboru manifestu, který jste upravovali dříve. Vyberte **Nahrát**. Po dokončení ověření vyberte **nahradit**.

### <a name="add-relationships"></a>Přidat relace

V šabloně zařízení **brány lva Edge** v části **moduly/lva Edge Gateway**vyberte **relace**. Vyberte **+ Přidat relaci** a přidejte následující dvě relace:

|Zobrazovaný název               |Name          |Cíl |
|-------------------------- |------------- |------ |
|Detektor pohybu LVA Edge   |Použijte výchozí.   |Zařízení detektoru pohybu LVA Edge |
|Detektor objektu LVA Edge   |Použijte výchozí.   |Zařízení LVA Edge pro detektor objektů |

Potom vyberte **Uložit**.

:::image type="content" source="media/tutorial-video-analytics-create-app/relationships.png" alt-text="Přidat relace":::

### <a name="add-views"></a>Přidání zobrazení

Šablona zařízení **brány lva Edge** neobsahuje žádné definice zobrazení.

Přidání zobrazení do šablony zařízení:

1. V šabloně zařízení **brány lva Edge** přejděte do **zobrazení** a vyberte **vizualizaci dlaždice zařízení** .

1. Jako název zobrazení zadejte *zařízení brány lva Edge* .

1. Přidejte do zobrazení následující dlaždice:

    * Dlaždice s vlastnostmi **informací o zařízení** : **model zařízení**, **výrobce**, **operační systém**, **Architektura procesoru**, **verze softwaru**, **Celková paměť**a **Celková velikost úložiště**.
    * Dlaždice spojnicového grafu s **volnou pamětí** a hodnotami telemetrie **prezenčního signálu systému**
    * Dlaždice historie událostí s následujícími událostmi: **vytvořit kameru**, **Odstranit kameru**, **restartovat modul**, **modul spuštěný**, **modul se zastavil**.
    * Dlaždice 2x1 Poslední známá hodnota znázorňující IoT Central telemetrie **stavu klienta** .
    * Dlaždice Poslední známá hodnota 2x1 zobrazující telemetrii **stavu modulu** .
    * Dlaždice Poslední známá hodnota 1x1 zobrazující telemetrii **prezenčního signálu systému** .
    * Dlaždice Poslední známá hodnota 1x1 zobrazující telemetrii **připojených fotoaparátů** .

    :::image type="content" source="media/tutorial-video-analytics-create-app/gateway-dashboard.png" alt-text="Řídicí panel":::

1. Vyberte **Uložit**.

### <a name="publish-the-device-template"></a>Publikování šablony zařízení

Předtím, než budete moci do aplikace přidat zařízení, je nutné publikovat šablonu zařízení:

1. V šabloně zařízení **brány lva Edge** vyberte **publikovat**.

1. Na stránce **Publikovat tuto šablonu zařízení na stránku aplikace** vyberte **publikovat**.

**Brána lva Edge** je teď k dispozici jako typ zařízení, který se má použít na stránce **zařízení** v aplikaci.

## <a name="add-a-gateway-device"></a>Přidat zařízení brány

Přidání zařízení **brány lva Edge** do aplikace:

1. Přejděte na stránku **zařízení** a vyberte šablonu zařízení **lva Edge Gateway** .

1. Vyberte **+ Nový**.

1. V dialogovém okně **vytvořit nové zařízení** změňte název zařízení na *lva Gateway 001*a změňte ID zařízení na *lva-Gateway-001*.

    > [!NOTE]
    > ID zařízení musí být v aplikaci jedinečné.

1. Vyberte **Vytvořit**.

Stav zařízení je **zaregistrovaný**.

### <a name="get-the-device-credentials"></a>Získání přihlašovacích údajů zařízení

Budete potřebovat přihlašovací údaje, které umožní zařízení připojit se k vaší IoT Central aplikaci. Přihlašovací údaje pro získání zařízení:

1. Na stránce **zařízení** vyberte zařízení **lva-Gateway-001** , které jste vytvořili.

1. Vyberte **Připojit**.

1. Na stránce **připojení zařízení** si poznamenejte v souboru *scratchpad.txt* **rozsahu ID**, **ID zařízení**a **primární klíč**zařízení. Tyto hodnoty použijete později.

1. Ujistěte se, že je metoda připojení nastavená na **sdílený přístupový podpis**.

1. Vyberte **Zavřít**.

## <a name="next-steps"></a>Další kroky

Nyní jste vytvořili aplikaci IoT Central pomocí šablony aplikace **video Analytics – objekt a detekce pohybu** , vytvořili jste šablonu zařízení pro zařízení brány a do aplikace jste přidali zařízení brány.

Pokud chcete vyzkoušet video Analytics – objekt a detekci pohybu pomocí IoT Edge modulů, na kterých běží cloudový virtuální počítač s simulovanými video streamy:

> [!div class="nextstepaction"]
> [Vytvoření instance IoT Edge pro video Analytics (virtuální počítač se systémem Linux)](./tutorial-video-analytics-iot-edge-vm.md)

Pokud si chcete vyzkoušet video Analytics – objekt a detekci pohybu pomocí IoT Edgech modulů, na kterých běží reálné zařízení s reálným **ONVIF** fotoaparátem:

> [!div class="nextstepaction"]
> [Vytvoření instance IoT Edge pro video Analytics (Intel NUC)](./tutorial-video-analytics-iot-edge-nuc.md)
