---
title: Nasazení webové aplikace prostorové analýzy
titleSuffix: Azure Cognitive Services
description: Naučte se používat prostorovou analýzu ve webové aplikaci.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 01/12/2021
ms.author: aahi
ms.openlocfilehash: de011fb0f827ea90efe33e237bbf1c5100dc76a7
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2021
ms.locfileid: "98183468"
---
# <a name="how-to-deploy-a-people-counting-web-application"></a>Postupy: nasazení webové aplikace pro počítání lidí

V tomto článku se dozvíte, jak integrovat prostorovou analýzu do webové aplikace, která rozumí pohybu lidí, a monitoruje počet lidí, kteří zabírají fyzický prostor. 

V tomto kurzu se naučíte, jak:

* Nasazení kontejneru prostorových analýz
* Konfigurace operace a kamery
* Konfigurace připojení IoT Hub ve webové aplikaci
* Nasazení a testování webové aplikace

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/cognitive-services/) .
* Základní porozumění konfiguracím nasazení Azure IoT Edge a [Azure IoT Hub](../../iot-hub/index.yml)
* Nakonfigurovaný [hostitelský počítač](spatial-analysis-container.md).

## <a name="deploy-the-spatial-analysis-container"></a>Nasazení kontejneru prostorových analýz

Vyplňte [aplikaci žádosti](https://aka.ms/csgate) , abyste získali přístup ke spuštění kontejneru. 

Postupujte podle [Nastavení hostitelského počítače](./spatial-analysis-container.md) a nakonfigurujte hostitelský počítač a připojte zařízení IoT Edge k Azure IoT Hub. 

### <a name="deploy-an-azure-iot-hub-service-in-your-subscription"></a>Nasazení služby Azure IoT Hub ve vašem předplatném

Nejdřív vytvořte instanci služby Azure IoT Hub pomocí cenové úrovně Standard (S1) nebo úrovně Free (S0). Podle těchto pokynů vytvořte tuto instanci pomocí Azure CLI.

Vyplňte požadované parametry:
* Předplatné: název nebo ID vašeho předplatného Azure.
* Skupina prostředků: Vytvořte název pro vaši skupinu prostředků.
* Název centra IoT: Vytvořte název pro IoT Hub
* IoTHub název: název IoT Hub, který jste vytvořili. 
* Název hraničního zařízení: vytvoření názvu hraničního zařízení

```azurecli
az login
az account set --subscription <name or ID of Azure Subscription>
az group create --name "<Resource Group Name>" --location "WestUS"

az iot hub create --name "<IoT Hub Name>" --sku S1 --resource-group "test-resource-group"

az iot hub device-identity create --hub-name "<IoT Hub Name>" --device-id "<Edge Device Name>" --edge-enabled
```

### <a name="deploy-the-container-on-azure-iot-edge-on-the-host-computer"></a>Nasazení kontejneru v Azure IoT Edge v hostitelském počítači

Nasaďte kontejner prostorových analýz jako modul IoT na hostitelském počítači pomocí Azure CLI. Proces nasazení vyžaduje soubor manifestu nasazení, který pojednává o požadovaných kontejnerech, proměnných a konfiguracích pro vaše nasazení. Můžete najít ukázkový manifest nasazení [konkrétního Azure Stackho okraje](https://go.microsoft.com/fwlink/?linkid=2142179), [neAzure Stackho](https://go.microsoft.com/fwlink/?linkid=2152189)a [virtuální počítač Azure s použitím MANIFESTU nasazení specifického pro GPU](https://go.microsoft.com/fwlink/?linkid=2152189) na GitHubu, který zahrnuje základní konfiguraci nasazení pro kontejner *prostorových analýz* . 

Alternativně můžete pomocí rozšíření Azure IoT pro Visual Studio Code provádět operace se službou IoT Hub. Další informace najdete [v nasazení Azure IoT Edge moduly z Visual Studio Code](../../iot-edge/how-to-deploy-modules-vscode.md) .

> [!NOTE] 
> Kontejnery *prostorových analýz – telegraf* a *prostorové analýzy pro diagnostiku* jsou volitelné. Můžete se rozhodnout je odebrat z *DeploymentManifest.jsv* souboru. Další informace najdete v článku [telemetrie a řešení problémů](./spatial-analysis-logging.md) . Můžete najít tři ukázkové *DeploymentManifest.js* souborů na GitHubu, pro [Azure Stack hraniční zařízení](https://go.microsoft.com/fwlink/?linkid=2142179), [stolní počítač](https://go.microsoft.com/fwlink/?linkid=2152189)nebo [virtuální počítač Azure s grafickým procesorem](https://go.microsoft.com/fwlink/?linkid=2152189) .

### <a name="set-environment-variables"></a>Nastavení proměnných prostředí

Většina **proměnných prostředí** pro modul IoT Edge je již nastavena v ukázce *DeploymentManifest.jsna* souborech propojených výše. V souboru vyhledejte `BILLING_ENDPOINT` `API_KEY` proměnné prostředí a zobrazené níže. Nahraďte hodnoty identifikátorem URI koncového bodu a klíčem rozhraní API, který jste vytvořili dříve. Zajistěte, aby byla hodnota smlouvy EULA nastavena na přijmout. 

```json
"EULA": { 
    "value": "accept"
},

"BILLING_ENDPOINT":{ 
    "value": "<Use a key from your Computer Vision resource>"
},
"API_KEY":{
    "value": "<Use the endpoint from your Computer Vision resource>"
}
```

### <a name="configure-the-operation-parameters"></a>Konfigurovat parametry operace

Teď, když je dokončená počáteční konfigurace kontejneru *prostorové analýzy* , je dalším krokem konfigurace parametrů operací a jejich přidání do nasazení. 

Prvním krokem je aktualizovat odkaz na vzorový manifest nasazení výše a nakonfigurovat operationId pro `cognitiveservices.vision.spatialanalysis-personcount` , jak je znázorněno níže:


```json
"personcount": {
    "operationId": "cognitiveservices.vision.spatialanalysis-personcount",
    "version": 1,
    "enabled": true,
    "parameters": {
        "VIDEO_URL": "<Replace RTSP URL here>",
        "VIDEO_SOURCE_ID": "<Replace with friendly name>",
        "VIDEO_IS_LIVE":true,
        "DETECTOR_NODE_CONFIG": "{ \"gpu_index\": 0 }",
        "SPACEANALYTICS_CONFIG": "{\"zones\":[{\"name\":\"queue\",\"polygon\":[<Replace with your values>], \"events\": [{\"type\":\"count\"}], \"threshold\":<use 0 for no threshold.}]}"
    }
},
```

Po aktualizaci manifestu nasazení postupujte podle pokynů výrobce fotoaparátu a nainstalujte fotoaparát, nakonfigurujte adresu URL kamery a nakonfigurujte uživatelské jméno a heslo. 

Dále nastavte `VIDEO_URL` adresu URL služby RTSP kamery a přihlašovací údaje pro připojení k fotoaparátu.

Pokud má hraniční zařízení více než jeden grafický procesor, vyberte GPU, na které se má spustit Tato operace. Ujistěte se, že vyrovnáváte zatížení operací, u kterých na jednom GPU současně neběží víc než 8 operací.  

Dále nakonfigurujte zónu, ve které chcete spočítat lidi. Chcete-li nakonfigurovat mnohoúhelník zóny, postupujte podle pokynů výrobce a načtěte snímek z fotoaparátu. Chcete-li určit všechny vrcholy mnohoúhelníku, vyberte bod na snímku, nastavte souřadnice x, y bodu vzhledem k levému a hornímu rohu rámce a rozdělte odpovídajícími rozměry snímků. Nastavte výsledky jako souřadnice x, y vrcholu. V poli můžete nastavit konfiguraci mnohoúhelníku zóny `SPACEANALYTICS_CONFIG` .

Toto je ukázkový rámec videa, který ukazuje, jak se počítají souřadnice vrcholu pro rámec o velikosti 1920/1080.
![Ukázka snímku videa](./media/spatial-analysis/sample-video-frame.jpg)

Můžete také vybrat prahovou hodnotu spolehlivosti, pokud se zjištěné osoby počítají a generují se události. Nastavte prahovou hodnotu na 0, pokud chcete, aby byly všechny události výstupní.

### <a name="execute-the-deployment"></a>Spustit nasazení

Teď, když je manifest nasazení dokončený, použijte tento příkaz v rozhraní příkazového řádku Azure CLI a nasaďte kontejner na hostitelském počítači jako modul IoT Edge.

```azurecli
az login
az extension add --name azure-iot
az iot edge set-modules --hub-name "<IoT Hub name>" --device-id "<IoT Edge device name>" --content DeploymentManifest.json -–subscription "<subscriptionId>"
```

Vyplňte požadované parametry:

* IoT Hub název: název Azure IoT Hub
* DeploymentManifest.js: název souboru nasazení
* IoT Edge název zařízení: název IoT Edge zařízení hostitelského počítače.
* Předplatné: ID nebo název předplatného

Tento příkaz spustí nasazení a v Azure Portal můžete zobrazit stav nasazení v instanci služby Azure IoT Hub. Stav může být zobrazený jako *417 – konfigurace nasazení zařízení není nastavená* , dokud zařízení nedokončí stahování imagí kontejneru a začne běžet.

### <a name="validate-that-the-deployment-was-successful"></a>Ověření úspěšného nasazení

Vyhledejte *stav modulu runtime* v nastavení modulu IoT Edge pro modul prostorových analýz ve vaší instanci IoT Hub na Azure Portal. **Požadovaná hodnota** a **hlášené hodnoty** pro *běhový stav* by měly vyslovit `Running` . Níže najdete informace o tom, co se bude na Azure Portal zobrazovat.

![Příklad ověření nasazení](./media/spatial-analysis/deployment-verification.png)

V tomto okamžiku je v kontejneru prostorové analýzy spuštěná operace. Vygeneruje pro ni přehledy AI `cognitiveservices.vision.spatialanalysis-personcount` a směruje tyto poznatky jako telemetrii do instance Azure IoT Hub. Chcete-li konfigurovat další fotoaparáty, můžete aktualizovat soubor manifestu nasazení a znovu spustit nasazení.

## <a name="person-counting-web-application"></a>Webová aplikace pro počítání osob

Tato osoba, která počítá webovou aplikaci, vám umožní rychle nakonfigurovat ukázkovou webovou aplikaci a hostovat ji v prostředí Azure.

### <a name="get-the-person-counting-app-container"></a>Získání kontejneru aplikace pro počítání osob

Formulář kontejneru této aplikace je k dispozici na Azure Container Registry. Ke stažení použijte následující příkaz docker pull. Kontaktujte Microsoft na projectarchon@microsoft.com pro přístup k přístupovému tokenu.

```bash
docker login rtvsofficial.azurecr.io -u <token name> -p <password>
docker pull rtvsofficial.azurecr.io/acceleratorapp.personcount:1.0
```

Nahrajte kontejner do Azure Container Registry (ACR).

```bash
az acr login --name <your ACR name>

docker tag rtvsofficial.azurecr.io/acceleratorapp.personcount:1.0 [desired local image name]

docker push [desired local image name]
```

Pokud chcete nainstalovat kontejner, vytvořte novou Web App for Containers Azure a vyplňte požadované parametry. Pak přejdete na kartu **Docker** a vyberte **jeden kontejner** a pak **Azure Container Registry**. Použijte svou instanci Azure Container Registry, na kterou jste vložili obrázek výše.

![Zadat podrobnosti obrázku](./media/spatial-analysis/solution-app-create-screen.png)

Po zadání výše uvedených parametrů klikněte na **zkontrolovat + vytvořit** a vytvořte aplikaci.

### <a name="configure-the-app"></a>Konfigurace aplikace 

Počkejte, až se instalace dokončí, a přejděte k prostředku v Azure Portal. Přejít do **konfiguračního** oddílu a přidejte následující dvě **nastavení aplikace**.

* `EventHubConsumerGroup` – Název řetězce skupiny příjemců z IoT Hub Azure, můžete v IoT Hub vytvořit novou skupinu uživatelů nebo použít výchozí skupinu. 
* `IotHubConnectionString`– Připojovací řetězec k vašemu IoT Hub Azure, můžete ho načíst z části klíče v tématu Konfigurace parametrů prostředků Azure IoT Hub ![](./media/spatial-analysis/solution-app-config-page.png)

Po přidání těchto dvou nastavení klikněte na **Uložit**. Pak v levé navigační nabídce klikněte na **ověřování/autorizace** a aktualizujte je s požadovanou úrovní ověřování. Doporučujeme Azure Active Directory (Azure AD) Express. 

### <a name="test-the-app"></a>Otestování aplikace

Přejdete do webové aplikace Azure a ověříte, že nasazení bylo úspěšné a webová aplikace je spuštěná. Přejděte na konfigurovanou adresu URL: `<yourapp>.azurewebsites.net` zobrazíte spuštěnou aplikaci.

![Otestování nasazení](./media/spatial-analysis/solution-app-output.png)

## <a name="get-the-personcount-source-code"></a>Získání zdrojového kódu PersonCount
Pokud chcete zobrazit nebo upravit zdrojový kód pro tuto aplikaci, můžete ji najít [na GitHubu](https://github.com/Azure-Samples/cognitive-services-spatial-analysis).

## <a name="next-steps"></a>Další kroky

* [Konfigurace operací prostorových analýz](./spatial-analysis-operations.md)
* [Protokolování a řešení potíží](spatial-analysis-logging.md)
* [Průvodce umístěním kamery](spatial-analysis-camera-placement.md)
* [Průvodce umístěním zóny a linky](spatial-analysis-zone-line-placement.md)
