---
title: Aktualizace firmwaru zařízení přes službu Azure IoT Hub | Microsoft Docs
description: Implementace procesu aktualizace firmwaru zařízení s využitím úloh a dvojčat zařízení.
services: iot-hub
author: dominicbetts
manager: timlt
ms.service: iot-hub
ms.devlang: dotnet
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/21/2018
ms.author: dobett
ms.custom: mvc
ms.openlocfilehash: 515c9917add27663e8d145fee3e1effc89291bc0
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37033683"
---
<!-- **TODO** Update publish config with repo paths before publishing! -->

# <a name="tutorial-implement-a-device-firmware-update-process"></a>Kurz: Implementace procesu aktualizace firmwaru zařízení

U zařízení připojených k centru IoT možná budete potřebovat aktualizovat firmware. Například můžete do firmwaru chtít přidat nové funkce nebo implementovat opravy zabezpečení. V řadě scénářů IoT je nepraktické být fyzicky u zařízení a pak na ně ručně instalovat aktualizace firmwaru. Tento kurz ukazuje, jak můžete začít a vzdáleně monitorovat proces aktualizace firmwaru prostřednictvím back-endové aplikace připojené k centru.

Za účelem vytvoření a monitorování procesu aktualizace firmwaru back-endová aplikace v tomto kurzu vytvoří _konfiguraci_ ve vašem centru IoT. Automatická správa zařízení ve službě IoT Hub pomocí této konfigurace aktualizuje sadu _požadovaných vlastností dvojčete zařízení_ na všech vašich chladících zařízeních. Požadované vlastnosti určují podrobnosti o nutné aktualizaci firmwaru. Chladící zařízení během procesu aktualizace firmwaru hlásí svůj stav do back-endové aplikace pomocí _ohlášených vlastností dvojčete zařízení_. Back-endová aplikace může pomocí konfigurace monitorovat ohlášené vlastnosti odesílané ze zařízení a sledovat proces aktualizace firmwaru až do konce:

![Proces aktualizace firmwaru](media/tutorial-firmware-update/Process.png)

V tomto kurzu provedete následující úlohy:

> [!div class="checklist"]
> * Vytvoření centra IoT a přidání testovacího zařízení do registru identit zařízení
> * Vytvoření konfigurace definující aktualizaci firmwaru
> * Simulace procesu aktualizace firmwaru na zařízení
> * Příjem aktualizací stavu ze zařízení v průběhu aktualizace firmwaru

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Dvě ukázkové aplikace, které spustíte v tomto rychlém startu, jsou napsány pomocí Node.js. Na počítači používaném pro vývoj potřebujete mít Node.js v4.x.x nebo novější.

Node.js pro různé platformy si můžete stáhnout z webu [nodejs.org](https://nodejs.org).

Aktuální verzi Node.js na počítači používaném pro vývoj můžete ověřit pomocí následujícího příkazu:

```cmd/sh
node --version
```

Stáhněte si ukázkový projekt Node.js z https://github.com/Azure-Samples/azure-iot-samples-node/archive/master.zip a extrahujte archiv ZIP.

## <a name="set-up-azure-resources"></a>Nastavení prostředků Azure

Abyste mohli dokončit tento kurz, musí vaše předplatné Azure obsahovat centrum IoT se zařízením přidaným do registru identit zařízení. Záznam v registru identit zařízení umožňuje připojení simulovaného zařízení, které v tomto kurzu spustíte, do vašeho centra.

Pokud zatím ve svém předplatném nemáte centrum IoT nastavené, můžete jej nastavit pomocí následujícího skriptu rozhraní příkazového řádku. Skript používá pro centrum IoT název **tutorial-iot-hub**. Při spuštění ho nahraďte vlastním jedinečným názvem. Skript vytvoří skupinu prostředků a centrum v oblasti **USA – střed**, kterou můžete změnit na bližší zeměpisnou oblast. Skript načte připojovací řetězec služby IoT Hub, který použijete v ukázkové back-endové aplikaci pro připojení k centru IoT:

```azurecli-interactive
hubname=tutorial-iot-hub
location=centralus

# Install the IoT extension if it's not already installed
az extension add --name azure-cli-iot-ext

# Create a resource group
az group create --name tutorial-iot-hub-rg --location $location

# Create your free-tier IoT Hub. You can only have one free IoT Hub per subscription
az iot hub create --name $hubname --location $location --resource-group tutorial-iot-hub-rg --sku S1

# Make a note of the service connection string, you need it later
az iot hub show-connection-string --hub-name $hub-name -o table

```

V tomto kurzu se používá simulované zařízení **MyFirmwareUpdateDevice**. Následující skript přidá toto zařízení do registru identit zařízení, nastaví hodnotu značky a načte jeho připojovací řetězec:

```azurecli-interactive
# Set the name of your IoT hub
hubname=tutorial-iot-hub

# Create the device in the identity registry
az iot hub device-identity create --device-id MyFirmwareUpdateDevice --hub-name $hubname --resource-group tutorial-iot-hub-rg

# Add a device type tag
az iot hub device-twin update --device-id MyFirmwareUpdateDevice --hub-name $hubname --set tags='{"devicetype":"chiller"}'

# Retrieve the device connection string, you need this later
az iot hub device-identity show-connection-string --device-id MyFirmwareUpdateDevice --hub-name $hubname --resource-group tutorial-iot-hub-rg -o table

```

Pokud tyto příkazy spouštíte na příkazovém řádku Windows nebo Powershellu, přečtěte si informace o uvozování řetězců JSON na stránce s [tipy pro azure-iot-cli-extension](https://github.com/Azure/azure-iot-cli-extension/wiki/Tips
).

## <a name="start-the-firmware-update"></a>Spuštění aktualizace firmwaru

V back-endové aplikaci vytvoříte konfiguraci automatické správy zařízení, která zahájí proces aktualizace firmwaru na všech zařízeních se značkou **devicetype** s hodnotou chiller (chladič). V této části najdete postup následujících úloh:

* Vytvoření konfigurace z back-endové aplikace
* Monitorování úlohy až do konce

### <a name="use-desired-properties-to-start-the-firmware-upgrade-from-the-back-end-application"></a>Spuštění upgradu firmwaru z back-endové aplikace s využitím požadovaných vlastností

Pokud si chcete prohlédnout kód back-endové aplikace, který vytvoří konfiguraci, v ukázkovém projektu Node.js, který jste si stáhli, přejděte do složky **iot-hub/Tutorials/FirmwareUpdate**. Poté otevřete soubor ServiceClient.js v libovolném textovém editoru.

Back-endová aplikace vytvoří následující konfiguraci:

[!code-javascript[Automatic device management configuration](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/ServiceClient.js?name=configuration "Automatic device management configuration")]

Konfigurace obsahuje následující části:

* `content` určuje požadované vlastnosti firmwaru odesílané do vybraných zařízení.
* `metrics` určuje dotazy, které se mají spustit a které hlásí stav aktualizace firmwaru.
* `targetCondition` vybírá zařízení, která obdrží aktualizaci firmwaru.
* `priorty` nastavuje relativní prioritu této konfigurace vzhledem k ostatním konfiguracím.

Back-endová aplikace k vytvoření konfigurace, která nastaví požadované vlastnosti, používá následující kód:

[!code-javascript[Create configuration](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/ServiceClient.js?name=createConfiguration "Create configuration")]

Po vytvoření konfigurace monitoruje aplikace aktualizaci firmwaru:

[!code-javascript[Monitor firmware update](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/ServiceClient.js?name=monitorConfiguration "Monitor firmware update")]

Konfigurace hlásí dva typy metrik:

* Systémové metriky, které hlásí, kolik zařízení je cílem a na kolika zařízeních je aktualizace nainstalovaná.
* Vlastní metriky generované dotazy, které do konfigurace přidáte. V tomto kurzu dotazy hlásí, kolik zařízení je v jednotlivých fázích procesu aktualizace.

### <a name="respond-to-the-firmware-upgrade-request-on-the-device"></a>Reakce na žádost o upgrade firmwaru na zařízení

Pokud si chcete prohlédnout kód simulovaného zařízení, který zpracovává požadované vlastnosti firmwaru odesílané z back-endové aplikace, v ukázkovém projektu Node.js, který jste si stáhli, přejděte do složky **iot-hub/Tutorials/FirmwareUpdate**. Poté otevřete soubor SimulatedDevice.js v libovolném textovém editoru.

Aplikace simulovaného zařízení vytvoří obslužnou rutinu pro aktualizace požadovaných vlastností **properties.desired.firmware** ve dvojčeti zařízení. V obslužné události se provádí několik základních kontrol požadovaných vlastností před spuštěním procesu aktualizace:

[!code-javascript[Handle desired property update](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/SimulatedDevice.js?name=initiateUpdate "Handle desired property update")]

## <a name="update-the-firmware"></a>Aktualizace firmwaru

Funkce **initiateFirmwareUpdateFlow** spustí aktualizaci. Tato funkce pomocí **vodopádové** funkce spouští postupně jednotlivé fáze procesu aktualizace. V tomto příkladu má aktualizace firmwaru čtyři fáze. V první fázi se stáhne image, v druhé fázi se image ověří pomocí kontrolního součtu, ve třetí fázi se image nainstaluje a v poslední fázi se zařízení restartuje:

[!code-javascript[Firmware update flow](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/SimulatedDevice.js?name=firmwareupdateflow "Firmware update flow")]

Během procesu aktualizace hlásí simulované zařízení její průběh pomocí ohlášených vlastností:

[!code-javascript[Reported properties](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/SimulatedDevice.js?name=reportedProperties "Reported properties")]

Následující fragment kódu ukazuje implementaci fáze stahování. Ve fázi stahování simulované zařízení pomocí ohlášených vlastností odesílá informace o stavu do back-endové aplikace:

[!code-javascript[Download image phase](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/SimulatedDevice.js?name=downloadimagephase "Download image phase")]

## <a name="run-the-sample"></a>Spuštění ukázky

V této části spustíte dvě ukázkové aplikace a budete sledovat, jak back-endová aplikace vytvoří konfiguraci pro správu procesu aktualizace firmwaru na simulovaném zařízení.

Ke spuštění aplikace simulovaného zařízení a back-endové aplikace potřebujete připojovací řetězce zařízení a služby. Tyto řetězce jste si poznamenali, když jste na začátku tohoto kurzu vytvářeli příslušné prostředky.

Spusťte aplikaci simulovaného zařízení tak, že otevřete okno prostředí nebo příkazového řádku a přejdete v projektu Node.js, který jste si stáhli, do složky **iot-hub/Tutorials/FirmwareUpdate**. Potom spusťte následující příkazy:

```cmd/sh
npm install
node SimulatedDevice.js "{your device connection string}"
```

Back-endovou aplikaci spusťte tak, že otevřete další okno prostředí nebo příkazového řádku. Pak přejděte v projektu Node.js, který jste si stáhli, do složky **iot-hub/Tutorials/FirmwareUpdate**. Potom spusťte následující příkazy:

```cmd/sh
npm install
node ServiceClient.js "{your service connection string}"
```

Následující snímek obrazovky ukazuje výstup z aplikace simulovaného zařízení a způsob, jakým reaguje na aktualizaci požadovaných vlastností firmwaru z back-endové aplikace:

![Simulované zařízení](./media/tutorial-firmware-update/SimulatedDevice.png)

Následující snímek obrazovky ukazuje výstup z back-endové aplikace a je na něm zvýrazněno vytvoření konfigurace pro aktualizaci požadovaných vlastností firmwaru:

![Back-endová aplikace](./media/tutorial-firmware-update/BackEnd1.png)

Následující snímek obrazovky ukazuje výstup z back-endové aplikace a je na něm zvýrazněno monitorování metrik aktualizace firmwaru ze simulovaného zařízení:

![Back-endová aplikace](./media/tutorial-firmware-update/BackEnd2.png)

Kvůli latenci v registru identit zařízení IoT Hub se možná nezobrazí všechny aktualizace stavu odeslané do back-endové aplikace. Metriky můžete zobrazit také na portálu v části **Automatická správa zařízení > Konfigurace zařízení IoT** vašeho centra IoT:

![Zobrazení konfigurace na portálu](./media/tutorial-firmware-update/portalview.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud si chcete projít další kurz, zachovejte skupinu prostředků a centrum IoT pro pozdější použití.

Pokud už centrum IoT nepotřebujete, odstraňte ho společně se skupinou prostředků na portálu. Provedete to výběrem skupiny prostředků **tutorial-iot-hub-rg**, která obsahuje vaše centrum IoT, a kliknutím na **Odstranit**.

Můžete použít také rozhraní příkazového řádku:

```azurecli-interactive
# Delete your resource group and its contents
az group delete --name tutorial-iot-hub-rg
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak pro svá připojená zařízení implementovat proces aktualizace firmwaru. V dalším kurzu zjistíte, jak pomocí nástrojů portálu Azure IoT Hub a příkazů Azure CLI otestovat připojení zařízení.

> [!div class="nextstepaction"]
[Použití simulovaného zařízení pro otestování připojení k IoT Hubu](tutorial-connectivity.md)
