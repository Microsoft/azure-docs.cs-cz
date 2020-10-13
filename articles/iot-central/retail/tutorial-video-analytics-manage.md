---
title: Kurz – monitorování videa pomocí šablony aplikace Azure IoT Central video Analytics – detekce objektů a pohybů
description: V tomto kurzu se dozvíte, jak pomocí řídicích panelů v šabloně video Analytics – objekt a detekce pohybu spravovat kamery a monitorovat video.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.author: nandab
author: KishorIoT
ms.date: 07/31/2020
ms.openlocfilehash: 1c1ddeb8cafd8aa7584da48a715139c5d12eeb10
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91874794"
---
# <a name="tutorial-monitor-and-manage-a-video-analytics---object-and-motion-detection-application"></a>Kurz: monitorování a Správa aplikace video Analytics – objekt a detekce pohybu

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Přidejte kamery detekce objektu a pohybu do aplikace IoT Central.
> * Umožňuje spravovat streamy videa a přehrávat je, když se zjistí zajímavé události.

## <a name="prerequisites"></a>Požadavky

Než začnete, měli byste dokončit:

* Jedna z předchozích [vytvoření živé aplikace video Analytics v azure IoT Central](./tutorial-video-analytics-create-app-yolo-v3.md) nebo [Vytvoření analýzy videí v Azure IoT Central (OpenVINO &trade; )](tutorial-video-analytics-create-app-openvino.md) kurzy.
* Jedna z předchozích [vytvoření instance IoT Edge pro Live video Analytics (virtuální počítač se systémem Linux)](tutorial-video-analytics-iot-edge-vm.md) nebo [vytvoření instance IoT Edge pro kurzy NUC (Live video Analytics)](tutorial-video-analytics-iot-edge-nuc.md) .

Měli byste mít na svém místním počítači nainstalovaný [Docker](https://www.docker.com/products/docker-desktop) pro spuštění aplikace Prohlížeč videa.

## <a name="add-an-object-detection-camera"></a>Přidat kameru detekce objektu

V IoT Central aplikaci přejděte na zařízení s **bránou lva 001** , které jste vytvořili dříve. Pak vyberte kartu **příkazy** .

Použijte hodnoty v následující tabulce jako hodnoty parametrů příkazu **Přidat žádost o fotoaparát** . Hodnoty uvedené v tabulce předpokládají, že používáte simulovanou kameru na virtuálním počítači Azure. Pokud používáte skutečnou kameru, upravte hodnoty odpovídajícím způsobem:

| Pole| Description| Ukázková hodnota|
|---------|---------|---------|
| ID kamery      | ID zařízení pro zřizování | kamera – 003 |
| Název kamery    | Popisný název           | Kamera detekce objektu |
| Adresa URL pro RTSP       | Adresa streamu   | RTSP://10.0.0.4:554/media/camera-300s.mkv|
| Uživatelské jméno RTSP  |                         | uživatel    |
| Heslo RTSP  |                         | heslo    |
| Typ detekce | Rozevírací seznam                | Detekce objektů       |

Vyberte **Spustit** pro přidání zařízení fotoaparátu:

:::image type="content" source="media/tutorial-video-analytics-manage/add-camera.png" alt-text="Přidat kameru":::

> [!NOTE]
> Šablona zařízení **lva Edge Object detektor** již v aplikaci existuje.

## <a name="add-a-motion-detection-camera-optional"></a>Přidat kameru detekce pohybu (volitelné)

Pokud máte k zařízení IoT Edge brány připojené dva fotoaparáty, opakujte předchozí kroky a přidejte do aplikace kameru detekce pohybu. Použijte jiné hodnoty pro **ID kamery**, **Název kamery**a parametry **adresy URL pro RTSP** .

## <a name="view-the-downstream-devices"></a>Zobrazit zařízení pro příjem dat

Vyberte kartu **zařízení pro příjem dat** pro zařízení s **bránou lva 001** , aby se zobrazila zařízení kamera, která jste právě přidali:

:::image type="content" source="media/tutorial-video-analytics-manage/inspect-downstream.png" alt-text="Přidat kameru":::

Zařízení fotoaparátu se zobrazí také v seznamu na stránce **zařízení** v aplikaci.

## <a name="configure-and-manage-the-camera"></a>Konfigurace a Správa kamery

Přejděte na zařízení **kamera-003** a vyberte kartu **Spravovat** .

Použijte výchozí hodnoty, nebo změňte, pokud potřebujete přizpůsobit vlastnosti zařízení:

**Nastavení kamery**

| Vlastnost | Popis | Navrhovaná hodnota |
|-|-|-|
| Hostitel přehrávání videa | Hostitel pro prohlížeč Azure Media Player | http://localhost:8094 |

**LVA operace a diagnostika**

| Vlastnost | Popis | Navrhovaná hodnota |
|-|-|-|
| Automatické spuštění | Po restartování brány LVA spustit detekci objektu | Zaškrtnuto |
| Ladit telemetrii | Trasování událostí | Volitelné |
|Časový limit odvození (s)| Množství času, které se použilo k určení, které odvozování se zastavilo | 20 |

**Rozpoznávání objektů AI**

| Vlastnost | Popis | Navrhovaná hodnota |
|-|-|-|
| Maximální doba zachycení odvození (s) | Maximální doba zachytávání | 15 |
| Třídy detekce | Řetězce, oddělené čárkami a značkami detekce. Další informace najdete v [seznamu podporovaných značek](https://github.com/Azure/live-video-analytics/blob/master/utilities/video-analysis/yolov3-onnx/tags.txt) . | nákladní vůz, sběrnice |
| Prahová hodnota spolehlivosti | Procento kvalifikace k určení, jestli je detekce objektu platná | 70 |
| Vzorkovací frekvence snímků s odvozením (FPS) | [Popis tady] | 2 |

Vyberte **Uložit**.

Po několika sekundách se zobrazí **přijatá** potvrzovací zpráva pro každé nastavení:

:::image type="content" source="media/tutorial-video-analytics-manage/object-detection.png" alt-text="Přidat kameru":::

## <a name="start-lva-processing"></a>Spustit zpracování LVA

Přejděte na zařízení **kamera-003** a vyberte kartu **příkazy** .

Spusťte příkaz **Start lva Processing** .

Po dokončení příkazu si prohlédněte historii příkazů, abyste se ujistili, že nejsou k dispozici žádné chyby:

:::image type="content" source="media/tutorial-video-analytics-manage/start-processing.png" alt-text="Přidat kameru":::

## <a name="monitor-the-cameras"></a>Monitorování fotoaparátů

Přejděte na zařízení **kamera-003** a vyberte kartu **řídicí panel** :

:::image type="content" source="media/tutorial-video-analytics-manage/camera-dashboard.png" alt-text="Přidat kameru":::

Na dlaždici **počet detekcí** se zobrazuje průměrný počet zjištění pro všechny vybrané objekty třídy detekce během intervalu detekce s jednou sekundou.

Výsečový graf **tříd detekce** zobrazuje procento detekce pro každý typ třídy.

**Video o odvození události** je seznam odkazů na prostředky v Azure Media Services, které obsahují zjišťování. Odkaz používá hostitelský přehrávač, který je popsaný v následující části.

## <a name="start-the-streaming-endpoint"></a>Spuštění koncového bodu streamování

Spusťte Media Services koncový bod, aby klientská Media Player aplikace mohla streamovat zaznamenané video:

* V Azure Portal přejděte do skupiny prostředků **lva-RG** .
* Otevřete prostředek **koncového bodu streamování** .
* Na stránce **Podrobnosti koncového bodu streamování** vyberte **Spustit**. Zobrazí se upozornění, že fakturace začne při spuštění koncového bodu.

## <a name="view-stored-video"></a>Zobrazit uložené video

Počet dní, po které se budou sledovat kamery a jejich reakce na podezřelé image. Pomocí automatického označování událostí a přímých odkazů na uložené video s odvozeným zjišťováním můžou operátoři zabezpečení najít v seznamu události, které vás zajímají, a pak pomocí odkazu zobrazit video.

K zobrazení videa uloženého ve vašem Azure Media Services účtu můžete použít [přehrávač videa amp](https://github.com/Azure/live-video-analytics/tree/master/utilities/amp-viewer) .

Aplikace IoT Central ukládá video v Azure Media Services, kde ho můžete streamovat. K přehrání videa uloženého v Azure Media Services potřebujete přehrávač videa.

Ujistěte se, že je na vašem místním počítači spuštěný **Docker** .

Otevřete příkazový řádek a pomocí následujícího příkazu spusťte přehrávač videa v kontejneru Docker na místním počítači. Zástupné symboly v příkazu nahraďte hodnotami, které jste si poznamenali dříve v souboru *scratchpad.txt* . `amsAadClientId` `amsAadSecret` `amsAadTenantId` `amsSubscriptionId` `amsAccountName` Při vytváření instančního objektu pro účet Media Services jste si poznamenali,,, a.

```bash
docker run -it --rm -e amsAadClientId="<FROM_AZURE_PORTAL>" -e amsAadSecret="<FROM_AZURE_PORTAL>" -e amsAadTenantId="<FROM_AZURE_PORTAL>" -e amsArmAadAudience="https://management.core.windows.net" -e amsArmEndpoint="https://management.azure.com" -e amsAadEndpoint="https://login.microsoftonline.com" -e amsSubscriptionId="<FROM_AZURE_PORTAL>" -e amsResourceGroup="<FROM_AZURE_PORTAL>" -e amsAccountName="<FROM_AZURE_PORTAL>" -p 8094:8094 mcr.microsoft.com/lva-utilities/amp-viewer:1.0-amd64
```

|Parametr Docker | Přístup k rozhraní API AMS (JSON)|
|-|-|
|amsAadClientId| AadClientId|
|amsAadSecret| AadSecret |
|amsAadTenantId| AadTenantId |
|amsSubscriptionId| SubscriptionId|
|amsResourceGroup| ResourceGroup |
|amsAccountName| AccountName|

Přejděte na zařízení **kamera-003** a vyberte kartu **řídicí panel** . Pak klikněte na jeden z hypertextových odkazů detekce zachyceného objektu na dlaždici **video pro odvození události** . Video se zobrazí na stránce zobrazené v místním přehrávači videa:

:::image type="content" source="media/tutorial-video-analytics-manage/video-snippet.png" alt-text="Přidat kameru":::

## <a name="change-the-simulated-devices-in-application-dashboards"></a>Změna simulovaných zařízení v řídicích panelech aplikací

Řídicí panely aplikace se původně naplní telemetrie a vlastnostmi generovanými z IoT Central simulovaných zařízení. Pokud chcete nakonfigurovat dlaždice na telemetrii z reálných kamer nebo simulátoru Live555, postupujte takto:

1. Přejděte na řídicí panel aplikace **(ukázka) Real Camera Monitor** .
1. Vyberte **Upravit**.
1. Vyberte dlaždici **poznámek** a odstraňte ji.
1. Změňte název řídicího panelu na *monitor reálné kamery*.
1. Na dlaždici **počet odvození** vyberte ikonu konfigurace.
1. V části **Konfigurace grafu** vyberte jeden nebo více reálných kamer ve skupině zařízení **lva Edge Object detektor** .
1. Vyberte `AI Inference Interface/Inference Count` pole telemetrie.
1. Vyberte **Aktualizovat**.

1. Opakujte postup pro následující dlaždice:
    1. **Zjišťování** výsečového grafu používá `AI Inference Interface/Inference/entity/tag/value` typ telemetrie.
    1. **Odvození** používá `AI Inference Interface/Inference/entity/tag/value` poslední známou hodnotu.
    1. **Důvěra%** používá `AI Inference Interface/Inference/entity/tag/confidence` poslední známou hodnotu.
    1. **Snímek** používá `AI Inference Interface/Inference Image` zobrazení jako obrázek.
    1. **Odvození videa** o události `AI Inference Interface/Inference Event Video` se používá jako odkaz.
1. Vyberte **Uložit**.

Řídicí panel **Real Camera Monitor** nyní zobrazuje hodnoty ze zařízení reálné kamery:

:::image type="content" source="media/tutorial-video-analytics-manage/update-real-cameras.png" alt-text="Přidat kameru":::

## <a name="pause-processing"></a>Pozastavit zpracování

Můžete pozastavit živé zpracování analýzy videí v aplikaci:

1. V aplikaci přejděte na stránku **příkazy** pro kameru detekce objektu. Pak spusťte příkaz **stop lva Processing** .

1. Zastavte koncový bod streamování pro váš účet Media Services:
    * V Azure Portal přejděte do skupiny prostředků **lva-RG** .
    * Klikněte na prostředek **koncového bodu streamování** .
    * Na stránce **Podrobnosti koncového bodu streamování** vyberte **zastavit**.

## <a name="tidy-up"></a>Uklizený nahoru

Pokud jste s aplikací hotoví, můžete odebrat všechny prostředky, které jste vytvořili následujícím způsobem:

1. V aplikaci IoT Central přejděte na stránku **aplikace** v části **Správa** . Vyberte **Odstranit**.
1. V Azure Portal odstraňte skupinu prostředků **lva-RG** .
1. V místním počítači zastavte kontejner Docker pro **amp Viewer** .

## <a name="next-steps"></a>Další kroky

Nyní jste se naučili, jak přidat kamery do aplikace IoT Central a nakonfigurovat je pro rozpoznávání objektů a pohybů.

Informace o tom, jak přizpůsobit zdrojový kód pro IoT Edge moduly:

> [!div class="nextstepaction"]
> [Úprava a sestavení modulů pro bránu Live video Analytics](./tutorial-video-analytics-build-module.md)
