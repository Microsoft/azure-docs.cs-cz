---
title: Kurz – ověření připojení zařízení k Azure IoT Hub
description: Kurz – použití IoT Hub nástrojů k řešení potíží během vývoje, ve službě IoT Hub problémy s připojením zařízení.
services: iot-hub
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.custom:
- mvc
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
- devx-track-js
- devx-track-azurecli
ms.date: 02/22/2019
ms.topic: tutorial
ms.service: iot-hub
ms.openlocfilehash: 253ec23a421415c11e4b47670dca870ebc463256
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99538706"
---
# <a name="tutorial-use-a-simulated-device-to-test-connectivity-with-your-iot-hub"></a>Kurz: Použití simulovaného zařízení k otestování připojení k IoT Hubu

V tomto kurzu použijete nástroje portálu Azure IoT Hub a rozhraní příkazového řádku Azure k otestování připojení zařízení. Tento kurz také využívá jednoduchý simulátor zařízení, který spustíte na svém stolním počítači.

Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Zkontrolovat ověřování zařízení
> * Zkontrolovat připojení zařízení ke cloudu
> * Zkontrolovat připojení cloudu k zařízení
> * Zkontrolovat synchronizaci dvojčat zařízení

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

Aplikace simulátoru zařízení, kterou budete v tomto kurzu spouštět, je napsána v jazyce Node.js. Ve vývojovém počítači potřebujete Node.js v10 za účelem. x. x nebo novější.

Node.js pro různé platformy si můžete stáhnout z webu [nodejs.org](https://nodejs.org).

Aktuální verzi Node.js na počítači používaném pro vývoj můžete ověřit pomocí následujícího příkazu:

```cmd/sh
node --version
```

Stáhněte si ukázkový projekt simulátoru zařízení v Node.js z https://github.com/Azure-Samples/azure-iot-samples-node/archive/master.zip a extrahujte archiv ZIP.

Ujistěte se, že je v bráně firewall otevřený port 8883. Ukázka zařízení v tomto kurzu používá protokol MQTT, který komunikuje přes port 8883. Tento port může být blokovaný v některých podnikových a vzdělávacích prostředích sítě. Další informace a způsoby, jak tento problém obejít, najdete v tématu [připojení k IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

Pokud jste v některém dřívějším kurzu nebo příručce Rychlý start už vytvořili centrum IoT na úrovni Free nebo Standard, můžete tento krok přeskočit.

[!INCLUDE [iot-hub-tutorials-create-free-hub](../../includes/iot-hub-tutorials-create-free-hub.md)]

## <a name="check-device-authentication"></a>Kontrola ověření zařízení

Před jakoukoli výměnou dat je třeba zařízení u centra ověřit. Ke správě zařízení a kontrole ověřovacích klíčů, které používají, můžete na portálu využít nástroj **Zařízení IoT** v části **Správa zařízení**. V této části kurzu přidáte nové testovací zařízení, načtete jeho klíč a zkontrolujete, jestli se testovací zařízení připojí k centru. Později resetujete ověřovací klíč a budete zkoumat, co se stane, když se zařízení pokusí použít zastaralý klíč. V této části kurzu se používá portál Azure Portal k vytvoření, správě a sledování zařízení a ukázkový simulátor zařízení napsaný v Node.js.

Přihlaste se na portál a přejděte do svého centra IoT. Pak přejděte k nástroji **Zařízení IoT**:

:::image type="content" source="media/tutorial-connectivity/iot-devices-tool.png" alt-text="Nástroj Zařízení IoT":::

Pokud chcete zaregistrovat nové zařízení, klikněte na **+ Nový**, nastavte **ID zařízení** na **MyTestDevice** a klikněte na **Uložit**.

:::image type="content" source="media/tutorial-connectivity/add-device.png" alt-text="Přidání nového zařízení":::

Pokud chcete načíst připojovací řetězec pro **MyTestDevice**, klikněte na něj v seznamu zařízení a potom zkopírujte hodnotu **primárního připojovacího řetězce** . Připojovací řetězec obsahuje *klíč pro sdílený přístup* k zařízení.

:::image type="content" source="media/tutorial-connectivity/copy-connection-string.png" alt-text="Načíst připojovací řetězec zařízení}":::

Pokud chcete simulovat, že zařízení **MyTestDevice** odesílá telemetrii do centra IoT, spusťte aplikaci simulovaného zařízení v Node.js, kterou jste si stáhli v předchozí části.

V okně terminálu na počítači pro vývoj přejděte do kořenové složky ukázkového projektu Node.js, který máte stažený. Pak přejděte do složky **IoT-hub\Tutorials\ConnectivityTests** .

V okně terminálu pomocí následujících příkazů nainstalujte požadované knihovny a spusťte aplikaci simulovaného zařízení. Použijte připojovací řetězec zařízení, na které jste si poznamenali, když jste zařízení přidali na portálu.

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
# az extension add --name azure-iot

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

V některých scénářích, například v cloudové bráně protokolu nebo v rámci schématu vlastního ověřování, může být nutné, abyste token SAS vygenerovali sami. Chcete-li řešit problémy s vaším kódem pro generování SAS, je vhodné vygenerovat token SAS považovaný za známý k použití při testování.

> [!NOTE]
> Ukázka SimulatedDevice-2.js zahrnuje příklady generování tokenu SAS se sadou SDK i bez ní.

Známý funkční token SAS vygenerujete v rozhraní příkazového řádku následujícím příkazem:

```azurecli-interactive
az iot hub generate-sas-token --device-id MyTestDevice --hub-name {YourIoTHubName}
```

Poznamenejte si úplný text vygenerovaného tokenu SAS. Token SAS bude vypadat přibližně takto: `SharedAccessSignature sr=tutorials-iot-hub.azure-devices.net%2Fdevices%2FMyTestDevice&sig=....&se=1524155307`

V okně terminálu na počítači pro vývoj přejděte do kořenové složky ukázkového projektu Node.js, který máte stažený. Pak přejděte do složky **IoT-hub\Tutorials\ConnectivityTests** .

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

| Protokol | Odchozí port |
| --- | --- |
| MQTT |8883 |
| MQTT přes WebSockets |443 |
| AMQP |5671 |
| AMQP přes WebSockets |443 |
| HTTPS |443 |

Pokud odchozí port blokuje brána firewall, zařízení se nemůže připojit:

![Zablokovaný port](media/tutorial-connectivity/port-blocked.png)

## <a name="check-device-to-cloud-connectivity"></a>Zkontrolovat připojení zařízení ke cloudu

Jakmile se zařízení připojí, obvykle se pokusí začít odesílat telemetrii do centra IoT. V této části se dozvíte, jak můžete ověřit, že telemetrie odesílaná ze zařízení dojde do centra.

Nejdřív pomocí následujícího příkazu do simulovaného zařízení načtěte aktuální připojovací řetězec:

```azurecli-interactive
az iot hub device-identity connection-string show --device-id MyTestDevice --output table --hub-name {YourIoTHubName}
```

Chcete-li spustit simulované zařízení, které odesílá zprávy, přejděte do složky **IoT-hub\Tutorials\ConnectivityTests** v kódu, který jste stáhli.

V okně terminálu spusťte následující příkazy pro instalaci požadovaných knihoven a spuštění aplikace simulovaného zařízení:

```cmd/sh
npm install
node SimulatedDevice-3.js "{your device connection string}"
```

V okně terminálu se během odesílání telemetrie do centra IoT budou zobrazovat informace:

![Simulované zařízení odesílá zprávy](media/tutorial-connectivity/sim-3-sending.png)

**Metriky** můžete na portálu použít k ověření, že zprávy telemetrie dosáhnou služby IoT Hub. Vyberte centrum IoT v rozevíracím seznamu **Resource** (Prostředek), vyberte metriku **Telemetry messages sent** (Odeslané telemetrické zprávy) a časové rozmezí nastavte na **Past hour** (Poslední hodina). Graf zobrazuje agregovaný počet zpráv odeslaných simulovaným zařízením:

![Zobrazení metrik služby IoT Hub](media/tutorial-connectivity/metrics-portal.png)

Po spuštění simulovaného zařízení několik minut trvá, než jsou metriky k dispozici.

## <a name="check-cloud-to-device-connectivity"></a>Zkontrolovat připojení cloudu k zařízení

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

Když simulované zařízení úspěšně přijme přímé volání metody, pošle potvrzení zpět do centra:

![Přijmout potvrzení přímé metody](media/tutorial-connectivity/method-acknowledgement.png)

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

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli, jak zkontrolovat klíče zařízení, připojení zařízení ke cloudu a cloudu k zařízení a synchronizaci dvojčat zařízení. Další informace o tom, jak sledovat centrum IoT, najdete v článku s postupy monitorování služby IoT Hub.

> [!div class="nextstepaction"]
> [Monitorování IoT Hubu](monitor-iot-hub.md)
