---
title: Kontrola připojení zařízení k Azure IoT Hubu
description: Nástroje služby IoT Hub slouží k řešení potíží s připojením zařízení do vašeho centra IoT během vývoje.
services: iot-hub
author: dominicbetts
manager: timlt
ms.author: dobett
ms.custom: mvc
ms.date: 05/29/2018
ms.topic: tutorial
ms.service: iot-hub
ms.openlocfilehash: bb9bcfcc5f78ee82f187d331055e8f2fd2ed9e64
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2019
ms.locfileid: "55745805"
---
# <a name="tutorial-use-a-simulated-device-to-test-connectivity-with-your-iot-hub"></a>Kurz: Použití simulovaného zařízení k otestování připojení k službě IoT hub

V tomto kurzu použijete nástroje portálu Azure IoT Hub a rozhraní příkazového řádku Azure k otestování připojení zařízení. Tento kurz také využívá jednoduchý simulátor zařízení, který spustíte na svém stolním počítači.

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Zkontrolovat ověřování zařízení
> * Zkontrolovat připojení zařízení ke cloudu
> * Zkontrolovat připojení cloudu k zařízení
> * Zkontrolovat synchronizaci dvojčat zařízení

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Požadavky

Skripty rozhraní příkazového řádku spouštěné v tomto kurzu využívají [rozšíření Microsoft Azure IoT pro Azure CLI](https://github.com/Azure/azure-iot-cli-extension/blob/master/README.md). Toto rozšíření nainstalujete zadáním následujícího příkazu do příkazového řádku:

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Aplikace simulátoru zařízení, kterou budete v tomto kurzu spouštět, je napsána v jazyce Node.js. Na počítači používaném pro vývoj potřebujete mít Node.js v4.x.x nebo novější.

Node.js pro různé platformy si můžete stáhnout z webu [nodejs.org](https://nodejs.org).

Aktuální verzi Node.js na počítači používaném pro vývoj můžete ověřit pomocí následujícího příkazu:

```cmd/sh
node --version
```

Stáhněte si ukázkový projekt simulátoru zařízení v Node.js z https://github.com/Azure-Samples/azure-iot-samples-node/archive/master.zip a extrahujte archiv ZIP.

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

Pokud jste v některém dřívějším kurzu nebo příručce Rychlý start už vytvořili centrum IoT na úrovni Free nebo Standard, můžete tento krok přeskočit.

[!INCLUDE [iot-hub-tutorials-create-free-hub](../../includes/iot-hub-tutorials-create-free-hub.md)]

## <a name="check-device-authentication"></a>Kontrola ověření zařízení

Před jakoukoli výměnou dat je třeba zařízení u centra ověřit. Ke správě zařízení a kontrole ověřovacích klíčů, které používají, můžete na portálu využít nástroj **Zařízení IoT** v části **Správa zařízení**. V této části kurzu přidáte nové testovací zařízení, načtete jeho klíč a zkontrolujete, jestli se testovací zařízení připojí k centru. Později resetujete ověřovací klíč a budete zkoumat, co se stane, když se zařízení pokusí použít zastaralý klíč. V této části kurzu se používá portál Azure Portal k vytvoření, správě a sledování zařízení a ukázkový simulátor zařízení napsaný v Node.js.

Přihlaste se na portál a přejděte do svého centra IoT. Pak přejděte k nástroji **Zařízení IoT**:

![Nástroj Zařízení IoT](media/tutorial-connectivity/iot-devices-tool.png)

Nové zařízení zaregistrujete tak, že kliknete na **+ Přidat**, nastavíte **ID zařízení** na **MyTestDevice**a kliknete na **Uložit**:

![Přidání nového zařízení](media/tutorial-connectivity/add-device.png)

Získejte připojovací řetězec zařízení **MyTestDevice** tak, že na něj kliknete v seznamu zařízení a pak zkopírujete hodnotu **Připojovací řetězec – primární klíč**. Připojovací řetězec obsahuje *klíč pro sdílený přístup* k zařízení.

![Načtení připojovacího řetězce zařízení](media/tutorial-connectivity/copy-connection-string.png)

Pokud chcete simulovat, že zařízení **MyTestDevice** odesílá telemetrii do centra IoT, spusťte aplikaci simulovaného zařízení v Node.js, kterou jste si stáhli v předchozí části.

V okně terminálu na počítači pro vývoj přejděte do kořenové složky ukázkového projektu Node.js, který máte stažený. Potom přejděte **iot hub\Tutorials\ConnectivityTests** složky.

V okně terminálu pomocí následujících příkazů nainstalujte požadované knihovny a spusťte aplikaci simulovaného zařízení. Použijte připojovací řetězec zařízení jste si poznamenali při přidání zařízení na portálu.

```cmd/sh
npm install
node SimulatedDevice-1.js "{your device connection string}"
```

V okně terminálu se během pokusu o připojení do centra IoT budou zobrazovat informace:

![Připojení simulovaného zařízení](media/tutorial-connectivity/sim-1-connected.png)

Teď jste se ze zařízení úspěšně ověřili pomocí klíče zařízení vygenerovaného centrem IoT.

### <a name="reset-keys"></a>Resetování klíčů

V této části resetujete klíč zařízení a uvidíte chybu, ke které dojde, pokud se simulované zařízení pokusí o připojení.

Pokud chcete resetovat primární klíč zařízení pro **MyTestDevice**, spusťte následující příkazy:

```azurecli-interactive
# Generate a new Base64 encoded key using the current date
read key < <(date +%s | sha256sum | base64 | head -c 32)

# Requires the IoT Extension for Azure CLI
# az extension add --name azure-cli-iot-ext

# Reset the primary device key for MyTestDevice
az iot hub device-identity update --device-id MyTestDevice --set authentication.symmetricKey.primaryKey=$key --hub-name {YourIoTHubName}
```

V okně terminálu na počítači pro vývoj znovu spusťte aplikaci simulovaného zařízení:

```cmd/sh
npm install
node SimulatedDevice-1.js "{your device connection string}"
```

Tentokrát se poté, co se aplikace pokusí o připojení, zobrazí chyba ověřování:

![Chyba připojení](media/tutorial-connectivity/sim-1-fail.png)

### <a name="generate-shared-access-signature-sas-token"></a>Vygenerování tokenu sdíleného přístupového podpisu (SAS)

Pokud zařízení používá některou ze sad SDK pro zařízení centra IoT Hub, vygeneruje kód knihovny SDK token SAS potřebný k ověření u centra. Token SAS se generuje z názvu centra, názvu zařízení a klíče zařízení.

V některých scénářích, například v cloudové bráně protokolu nebo v rámci schématu vlastního ověřování, může být nutné, abyste token SAS vygenerovali sami. Při řešení problémů s generováním kódu SAS je užitečné, když jste schopni vytvořit známý funkční token SAS k použití při testování.

> [!NOTE]
> Ukázka SimulatedDevice-2.js zahrnuje příklady generování tokenu SAS se sadou SDK i bez ní.

Známý funkční token SAS vygenerujete v rozhraní příkazového řádku následujícím příkazem:

```azurecli-interactive
az iot hub generate-sas-token --device-id MyTestDevice --hub-name {YourIoTHubName}
```

Poznamenejte si úplný text vygenerovaného tokenu SAS. Token SAS bude vypadat přibližně takto: `'SharedAccessSignature sr=tutorials-iot-hub.azure-devices.net%2Fdevices%2FMyTestDevice&sig=....&se=1524155307'`

V okně terminálu na počítači pro vývoj přejděte do kořenové složky ukázkového projektu Node.js, který máte stažený. Pak přejděte do složky **iot-hub\Tutorials\ConnectivityTests\simulated-device**.

V okně terminálu spusťte následující příkazy pro instalaci požadovaných knihoven a spuštění aplikace simulovaného zařízení:

```cmd/sh
npm install
node SimulatedDevice-2.js "{Your SAS token}"
```

V okně terminálu se během pokusu o připojení do centra IoT pomocí tokenu SAS budou zobrazovat informace:

![Připojení simulovaného zařízení pomocí tokenu](media/tutorial-connectivity/sim-2-connected.png)

Teď jste se ze zařízení úspěšně ověřili pomocí tokenu SAS vygenerovaného příkazem z příkazového řádku. Soubor **SimulatedDevice-2.js** obsahuje ukázkový kód pro postup, jak vygenerovat token SAS v kódu.

### <a name="protocols"></a>Protokoly

Zařízení může k připojení do centra IoT použít kterýkoli z následujících protokolů:

| Protocol (Protokol) | Odchozí port |
| --- | --- |
| MQTT |8883 |
| MQTT přes WebSockets |443 |
| AMQP |5671 |
| AMQP přes WebSockets |443 |
| HTTPS |443 |

Pokud odchozí port blokuje brána firewall, zařízení se nemůže připojit:

![Zablokovaný port](media/tutorial-connectivity/port-blocked.png)

## <a name="check-device-to-cloud-connectivity"></a>Kontrola připojení zařízení ke cloudu

Jakmile se zařízení připojí, obvykle se pokusí začít odesílat telemetrii do centra IoT. V této části se dozvíte, jak můžete ověřit, že telemetrie odesílaná ze zařízení dojde do centra.

Nejdřív pomocí následujícího příkazu do simulovaného zařízení načtěte aktuální připojovací řetězec:

```azurecli-interactive
az iot hub device-identity show-connection-string --device-id MyTestDevice --output table --hub-name {YourIoTHubName}
```

Simulované zařízení, které odesílá zprávy, spustíte tak, že ve staženém kódu přejdete do složky **iot-hub\Tutorials\ConnectivityTests\simulated-device**.

V okně terminálu spusťte následující příkazy pro instalaci požadovaných knihoven a spuštění aplikace simulovaného zařízení:

```cmd/sh
npm install
node SimulatedDevice-3.js "{your device connection string}"
```

V okně terminálu se během odesílání telemetrie do centra IoT budou zobrazovat informace:

![Simulované zařízení odesílá zprávy](media/tutorial-connectivity/sim-3-sending.png)

V části **Metrics** (Metriky) na portálu můžete ověřit, zda telemetrické zprávy přicházejí do centra IoT:

![Přechod k metrikám služby IoT Hub](media/tutorial-connectivity/metrics-portal.png)

Vyberte centrum IoT v rozevíracím seznamu **Resource** (Prostředek), vyberte metriku **Telemetry messages sent** (Odeslané telemetrické zprávy) a časové rozmezí nastavte na **Past hour** (Poslední hodina). Graf zobrazuje agregovaný počet zpráv odeslaných simulovaným zařízením:

![Zobrazení metrik služby IoT Hub](media/tutorial-connectivity/metrics-active.png)

Po spuštění simulovaného zařízení několik minut trvá, než jsou metriky k dispozici.

## <a name="check-cloud-to-device-connectivity"></a>Kontrola připojení cloudu k zařízení

V této části se vysvětluje, jak provést testovací volání zařízení přímou metodou a zkontrolovat tak jeho připojení ke cloudu. Simulované zařízení spustíte na svém počítači pro vývoj a budete naslouchat voláním přímou metodou z centra.

V okně terminálu následujícím příkazem spusťte aplikaci simulovaného zařízení:

```cmd/sh
node SimulatedDevice-3.js "{your device connection string}"
```

Volání zařízení přímou metodou pomocí příkazu z příkazového řádku:

```azurecli-interactive
az iot hub invoke-device-method --device-id MyTestDevice --method-name TestMethod --timeout 10 --method-payload '{"key":"value"}' --hub-name {YourIoTHubName}
```

Jakmile simulované zařízení přijme volání přímou metodou, vytiskne zprávu na konzole:

![Simulované zařízení přijímá volání přímou metodou](media/tutorial-connectivity/receive-method-call.png)

Když simulované zařízení úspěšně přijme volání přímou metodou, odešle potvrzení zpět do centra:

![Přijetí potvrzení přímé metody](media/tutorial-connectivity/method-acknowledgement.png)

## <a name="check-twin-synchronization"></a>Kontrola synchronizace dvojčat zařízení

Dvojčata zařízením slouží k synchronizaci stavu mezi zařízením a centrem. V této části se naučíte pomocí rozhraní příkazového řádku odeslat do zařízení _požadované vlastnosti_ a přečíst _ohlášené vlastnosti_ odeslané zařízením.

Simulované zařízení, s kterým v této části pracujete, odesílá ohlášené vlastnosti do centra při každém svém spuštění a při přijetí požadovaných vlastností je vždy vytiskne na konzolu.

V okně terminálu následujícím příkazem spusťte aplikaci simulovaného zařízení:

```cmd/sh
node SimulatedDevice-3.js "{your device connection string}"
```

Pokud chcete ověřit, že centrum přijalo od zařízení ohlášené vlastnosti, použijte následující příkaz rozhraní příkazového řádku:

```azurecli-interactive
az iot hub device-twin show --device-id MyTestDevice --hub-name {YourIoTHubName}
```

Ve výstupu z příkazu vidíte v části s ohlášenými vlastnostmi položku **devicelaststarted**. Tato vlastnost obsahuje datum a čas posledního spuštění simulovaného zařízení.

![Zobrazení ohlášených vlastností](media/tutorial-connectivity/reported-properties.png)

Pokud chcete ověřit, že centrum odesílá do zařízení hodnoty požadovaných vlastností, použijte následující příkaz rozhraní příkazového řádku:

```azurecli-interactive
az iot hub device-twin update --set properties.desired='{"mydesiredproperty":"propertyvalue"}' --device-id MyTestDevice --hub-name {YourIoTHubName}
```

Simulované zařízení při přijetí aktualizace požadovaných vlastností z centra vytiskne zprávu:

![Příjem požadovaných vlastností](media/tutorial-connectivity/desired-properties.png)

Vedle průběžného přijímání změn v požadovaných vlastnostech je simulované zařízení také automaticky zkontroluje při svém spuštění.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už centrum IoT nepotřebujete, odstraňte ho společně se skupinou prostředků na portálu. Provedete to výběrem skupiny prostředků **tutorials-iot-hub-rg**, která obsahuje vaše centrum IoT, a kliknutím na **Odstranit**.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se dozvěděli, jak zkontrolovat klíče zařízení, připojení zařízení ke cloudu a cloudu k zařízení a synchronizaci dvojčat zařízení. Další informace o tom, jak sledovat centrum IoT, najdete v článku s postupy monitorování služby IoT Hub.

> [!div class="nextstepaction"]
> [Monitorování s diagnostikou](iot-hub-monitor-resource-health.md)
