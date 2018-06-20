---
title: Synchronizace stavu zařízení ze služby Azure IoT Hub | Microsoft Docs
description: Stav mezi zařízeními a centrem IoT můžete synchronizovat pomocí dvojčat zařízení
services: iot-hub
documentationcenter: ''
author: dominicbetts
manager: timlt
ms.assetid: ''
ms.service: iot-hub
ms.devlang: dotnet
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/14/2018
ms.author: dobett
ms.custom: mvc
ms.openlocfilehash: b1e7fa441dc52a647828c7d3785f09533af06fd0
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34651317"
---
<!-- **TODO** Update publish config with repo paths before publishing! -->

# <a name="tutorial-configure-your-devices-from-a-back-end-service"></a>Kurz: Konfigurace zařízení z back-endové služby

Vedle příjmu telemetrických dat ze zařízení můžete potřebovat zařízení nakonfigurovat z back-endové služby. Když do zařízení odesíláte požadovanou konfiguraci, můžete z nich někdy chtít zároveň přijmout aktualizace stavu a souladu s předpisy. Můžete tak například pro zařízení nastavit cílový rozsah provozní teploty nebo z nich shromáždit informace o verzi firmwaru.

K synchronizaci informací o stavu mezi zařízením a centrem IoT slouží _dvojčata zařízení_. [Dvojče zařízení](iot-hub-devguide-device-twins.md) je dokument JSON přidružený ke konkrétnímu zařízení a uložený službou IoT Hub v cloudu, kam na něj můžete odeslat [dotaz](iot-hub-devguide-query-language.md). Dvojče zařízení obsahuje _požadované vlastnosti_, _ohlášené vlastnosti_ a _značky_. Požadované vlastnosti nastavuje back-endová aplikace a zařízení je přečte. Ohlášené vlastnosti nastavuje zařízení a čte je back-endová aplikace. Značku nastavuje back-endová aplikace a do zařízení ji neodesílá. Značky slouží k uspořádání zařízení. V tomto kurzu se dozvíte, jak používat požadované a ohlášené vlastnosti k synchronizaci informací o stavu:

![Přehled informací o dvojčatech](media/tutorial-device-twins/DeviceTwins.png)

V tomto kurzu provedete následující úlohy:

> [!div class="checklist"]
> * Vytvoření centra IoT a přidání testovacího zařízení do registru identit
> * Použití požadovaných vlastností k odeslání informací o stavu do simulovaného zařízení.
> * Použití ohlášených vlastností k přijetí informací o stavu ze simulovaného zařízení

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

Pokud zatím ve svém předplatném nemáte centrum IoT nastavené, můžete jej nastavit pomocí následujícího skriptu rozhraní příkazového řádku. Skript používá pro centrum IoT název **tutorial-iot-hub**. Při spuštění ho nahraďte vlastním jedinečným názvem. Skript vytvoří skupinu prostředků a centrum v oblasti **USA – střed**, kterou můžete změnit na bližší zeměpisnou oblast. Skript načte připojovací řetězec služby IoT Hub, který použijete v ukázkové back-endové aplikaci k připojení do centra IoT:

```azurecli-interactive
hubname=tutorial-iot-hub
location=centralus

# Install the IoT extension if it's not already installed:
az extension add --name azure-cli-iot-ext

# Create a resource group:
az group create --name tutorial-iot-hub-rg --location $location

# Create your free-tier IoT Hub. You can only have one free IoT Hub per subscription:
az iot hub create --name $hubname --location $location --resource-group tutorial-iot-hub-rg --sku S1

# Make a note of the service connection string, you need it later:
az iot hub show-connection-string --hub-name $hub-name -o table

```

V tomto kurzu se používá simulované zařízení s názvem **MyTwinDevice**. Následující skript přidá toto zařízení do registru identit a načte jeho připojovací řetězec:

```azurecli-interactive
# Set the name of your IoT hub:
hubname=tutorial-iot-hub

# Create the device in the identity registry:
az iot hub device-identity create --device-id MyTwinDevice --hub-name $hubname --resource-group tutorial-iot-hub-rg

# Retrieve the device connection string, you need this later:
az iot hub device-identity show-connection-string --device-id MyTwinDevice --hub-name $hubname --resource-group tutorial-iot-hub-rg -o table

```

## <a name="send-state-information"></a>Odeslání informací o stavu

Požadované vlastnosti slouží k odeslání informací o stavu z back-endové aplikace do zařízení. V této části najdete postup následujících úloh:

* Příjem požadovaných vlastností do zařízení a jejich zpracování
* Odeslání požadovaných vlastností z back-endové aplikace

Ukázkový kód simulovaného zařízení, které přijímá požadované vlastnosti, zobrazíte ve složce **iot-hub/Tutorials/DeviceTwins** v ukázkovém projektu Node.js, který jste si stáhli. Poté otevřete soubor SimulatedDevice.js v libovolném textovém editoru.

Následující části popisují kód, který běží na simulovaném zařízení a odpovídá na změny požadovaných vlastností odeslané z back-endové aplikace:

### <a name="retrieve-the-device-twin-object"></a>Načtení objektu dvojčete zařízení

Následující kód se pomocí připojovacího řetězce zařízení připojí k centru IoT:

[!code-javascript[Create IoT Hub client](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=createhubclient&highlight=2 "Create IoT Hub client")]

Následující kód získá z klientského objektu dvojče:

[!code-javascript[Get twin](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=gettwin&highlight=2 "Get twin")]

### <a name="sample-desired-properties"></a>Ukázka požadovaných vlastností

U požadovaných vlastnosti můžete použít jakoukoli strukturu, která vyhovuje potřebám vaší aplikace. V tomto příkladu používáme jednu vlastnost nejvyšší úrovně s názvem **fanOn** a ostatní vlastnosti seskupené do samostatných **součástí**. Následující fragment kódu JSON ukazuje strukturu požadovaných vlastností použitou v tomto kurzu:

[!code[Sample desired properties](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/desired.json "Sample desired properties")]

### <a name="create-handlers"></a>Vytvoření obslužných rutin

K aktualizaci požadovaných vlastností můžete vytvořit obslužné rutiny, které budou odpovídat na aktualizace na různých úrovních hierarchie JSON. Tato obslužná rutina například sleduje všechny změny požadovaných vlastností odeslané z back-endové aplikace do zařízení. Proměnná **delta** obsahuje požadované vlastnosti odeslané z back-endu řešení:

[!code-javascript[Handle all properties](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=allproperties&highlight=2 "Handle all properties")]

Následující obslužná rutina reaguje pouze na změny provedené v požadované vlastnosti **fanOn**:

[!code-javascript[Handle fan property](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=fanproperty&highlight=2 "Handle fan property")]

### <a name="handlers-for-multiple-properties"></a>Obslužné rutiny pro více vlastností

V předchozím příkladu požadovaných vlastností JSON obsahuje uzel **climate** v části **components** dvě vlastnosti: **minTemperature** a **maxTemperature**.

V místním objektu **dvojčete** zařízení se ukládá kompletní sada požadovaných a ohlášených vlastností. Proměnná **delta** odeslaná z back-endu může aktualizovat jen dílčí sadu požadovaných vlastností. Pokud simulované zařízení obdrží aktualizaci jen jedné z vlastností **minTemperature** a **maxTemperature**, následující fragment kódu zajistí, že se její hodnota použije v místním dvojčeti pro druhou hodnotu ke konfiguraci zařízení:

[!code-javascript[Handle climate component](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=climatecomponent&highlight=2 "Handle climate component")]

V místním objektu **dvojčete** se ukládá kompletní sada požadovaných a ohlášených vlastností. Proměnná **delta** odeslaná z back-endu může aktualizovat jen dílčí sadu požadovaných vlastností.

### <a name="handle-insert-update-and-delete-operations"></a>Zpracování operací vložení, aktualizace a odstranění

Požadované vlastnosti odeslané z back-endu neuvádějí, jaká operace se s konkrétní požadovanou vlastností provádí. Kód musí typ operace vyvodit z aktuální sady požadovaných vlastností uložených místně a ze změn odeslaných z centra.

Následující fragment kódu ukazuje, jak simulované zařízení zpracovává operace vložení, aktualizace a odstranění u seznamu **components** v požadovaných vlastnostech. Zjistíte v něm, jak použít hodnoty **null** k označení, že se určitá součást má odstranit:

[!code-javascript[Handle components](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=components&highlight=2,6,13 "Handle components")]

### <a name="send-desired-properties-to-a-device-from-the-back-end"></a>Odeslání požadovaných vlastností z back-endu do zařízení

Už víte, jak zařízení implementuje obslužné rutiny pro příjem aktualizací požadovaných vlastností. V této části se dozvíte, jak do zařízení z back-endové aplikace odeslat změny požadovaných vlastností.

Ukázkový kód simulovaného zařízení, které přijímá požadované vlastnosti, zobrazíte ve složce **iot-hub/Tutorials/DeviceTwins** v ukázkovém projektu Node.js, který jste si stáhli. Poté otevřete soubor ServiceClient.js v libovolném textovém editoru.

Následující fragment kódu ukazuje, jak se připojit k registru identit zařízení a získat přístup k dvojčeti konkrétního zařízení:

[!code-javascript[Create registry and get twin](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/ServiceClient.js?name=getregistrytwin&highlight=2,6 "Create registry and get twin")]

Následující fragment obsahuje *opravy* různých požadovaných vlastností, které back-endová aplikace odesílá do zařízení:

[!code-javascript[Patches sent to device](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/ServiceClient.js?name=patches&highlight=2,12,26,41,56 "Patches sent to device")]

Následující fragment ukazuje, jak back-endová aplikace odesílá do zařízení aktualizaci požadované vlastnosti:

[!code-javascript[Send desired properties](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/ServiceClient.js?name=senddesiredproperties&highlight=2 "Send desired properties")]

### <a name="run-the-applications"></a>Spuštění aplikací

V této části spustíte dvě ukázkové aplikace a budete sledovat, jak back-endová aplikace odesílá aktualizace požadované vlastnosti do aplikace simulovaného zařízení.

Ke spuštění aplikace simulovaného zařízení a back-endové aplikace potřebujete připojovací řetězce zařízení a služby. Tyto řetězce jste si poznamenali, když jste na začátku tohoto kurzu vytvářeli příslušné prostředky.

Spusťte aplikaci simulovaného zařízení tak, že otevřete okno prostředí nebo příkazového řádku a přejdete v projektu Node.js, který jste si stáhli, do složky **iot-hub/Tutorials/DeviceTwins**. Potom spusťte následující příkazy:

```cmd/sh
npm install
node SimulatedDevice.js "{your device connection string}"
```

Back-endovou aplikaci spusťte tak, že otevřete další okno prostředí nebo příkazového řádku. Pak přejděte v projektu Node.js, který jste si stáhli, do složky **iot-hub/Tutorials/DeviceTwins**. Potom spusťte následující příkazy:

```cmd/sh
npm install
node ServiceClient.js "{your service connection string}"
```

Následující snímek obrazovky ukazuje výstup z aplikace simulovaného zařízení, je na něm zvýrazněno zpracování aktualizace požadované vlastnosti **maxTemperature**. Vidíte zde, jak se spouští obslužná rutina nejvyšší úrovně a obslužná rutina součásti climate:

![Simulované zařízení](./media/tutorial-device-twins/SimulatedDevice1.png)

Následující snímek obrazovky ukazuje výstup z back-endové aplikace, je na něm zvýrazněno odeslání aktualizace požadované vlastnosti **maxTemperature**:

![Back-endová aplikace](./media/tutorial-device-twins/BackEnd1.png)

## <a name="receive-state-information"></a>Příjem informací o stavu

Back-endová aplikace přijme ze zařízení informace o stavu jako ohlášené vlastnosti. Zařízení nastaví ohlášené vlastnosti a odešle je do centra IoT. Back-endová aplikace může číst aktuální hodnoty ohlášených vlastností z dvojčete zařízení, které je uloženo v centru.

### <a name="send-reported-properties-from-a-device"></a>Odeslání ohlášených vlastností ze zařízení

Aktualizace hodnot ohlášených vlastností můžete odesílat jako opravy. Následující fragment kódu obsahuje šablonu opravy, kterou simulované zařízení odesílá. Před odesláním do centra aktualizuje simulované zařízení pole opravy:

[!code-javascript[Reported properties patches](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=reportedpatch&highlight=2 "Reported properties patches")]

K odeslání opravy, která obsahuje ohlášené vlastnosti, do centra používá simulované zařízení následující funkce:

[!code-javascript[Send reported properties](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=sendreportedproperties&highlight=2 "Send reported properties")]

### <a name="process-reported-properties"></a>Zpracování ohlášených vlastností

Back-endová aplikace má k aktuálním hodnotám ohlášených vlastností zařízení přístup prostřednictvím dvojčete zařízení. Následující fragment kódu ukazuje, jak back-endová aplikace načte hodnoty ohlášených vlastností pro simulované zařízení:

[!code-javascript[Display reported properties](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/ServiceClient.js?name=displayreportedproperties&highlight=2 "Display reported properties")]

### <a name="run-the-applications"></a>Spuštění aplikací

V této části spustíte dvě ukázkové aplikace a budete sledovat, jak aplikace simulovaného zařízení odesílá aktualizace ohlášené vlastnosti do back-endové aplikace.

Spouštět budete tytéž dvě ukázkové aplikace, které jste použili ke zjištění, jak se do zařízení odesílají požadované vlastnosti.

Ke spuštění aplikace simulovaného zařízení a back-endové aplikace potřebujete připojovací řetězce zařízení a služby. Tyto řetězce jste si poznamenali, když jste na začátku tohoto kurzu vytvářeli příslušné prostředky.

Spusťte aplikaci simulovaného zařízení tak, že otevřete okno prostředí nebo příkazového řádku a přejdete v projektu Node.js, který jste si stáhli, do složky **iot-hub/Tutorials/DeviceTwins**. Potom spusťte následující příkazy:

```cmd/sh
npm install
node SimulatedDevice.js "{your device connection string}"
```

Back-endovou aplikaci spusťte tak, že otevřete další okno prostředí nebo příkazového řádku. Pak přejděte v projektu Node.js, který jste si stáhli, do složky **iot-hub/Tutorials/DeviceTwins**. Potom spusťte následující příkazy:

```cmd/sh
npm install
node ServiceClient.js "{your service connection string}"
```

Následující snímek obrazovky ukazuje výstup z aplikace simulovaného zařízení, je na něm zvýrazněno odeslání aktualizace ohlášené vlastnosti do centra IoT:

![Simulované zařízení](./media/tutorial-device-twins/SimulatedDevice2.png)

Následující snímek obrazovky ukazuje výstup z back-endové aplikace, je na něm zvýrazněno přijetí aktualizace ohlášené vlastnosti ze zařízení a její zpracování:

![Back-endová aplikace](./media/tutorial-device-twins/BackEnd2.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud si chcete projít další kurz, zachovejte skupinu prostředků a centrum IoT pro pozdější použití.

Pokud už centrum IoT nepotřebujete, odstraňte ho společně se skupinou prostředků na portálu. Provedete to výběrem skupiny prostředků **tutorial-iot-hub-rg**, která obsahuje vaše centrum IoT, a kliknutím na **Odstranit**.

Můžete použít také rozhraní příkazového řádku:

```azurecli-interactive
# Delete your resource group and its contents
az group delete --name tutorial-iot-hub-rg
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili synchronizovat informace o stavu mezi zařízeními a centrem IoT, a to pomocí následujících úloh:

> [!div class="checklist"]
> * Vytvoření centra IoT a přidání testovacího zařízení do registru identit
> * Použití požadovaných vlastností k odeslání informací o stavu do simulovaného zařízení.
> * Použití ohlášených vlastností k přijetí informací o stavu ze simulovaného zařízení

V dalším kurzu se dozvíte, jak využít dvojčata zařízení k implementaci procesu aktualizace firmwaru.

> [!div class="nextstepaction"]
[Použití simulovaného zařízení pro otestování připojení k IoT Hubu](tutorial-connectivity.md)
