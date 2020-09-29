---
title: Monitorovat vlákna modulu – Azure IoT Edge
description: Postup interpretace vláken zařízení a vláken modulu, aby bylo možné zjistit dostupnost a stav
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 05/29/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 1bf2e3f07d9e5576f62ef9badd9c8a46ac92fad0
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91450158"
---
# <a name="monitor-module-twins"></a>Monitorování dvojčat modulu

Moduly se v Azure neplní, IoT Hub umožňují monitorování dostupnosti a stavu nasazení IoT Edge. Moduly s dvojitou platností ukládají užitečné informace ve službě IoT Hub o výkonu spuštěných modulů. [Agenti IoT Edge](iot-edge-runtime.md#iot-edge-agent) a moduly runtime [centra IoT Edge](iot-edge-runtime.md#iot-edge-hub) v nich udržují své moduly v chodu `$edgeAgent` a v `$edgeHub` uvedeném pořadí:

* `$edgeAgent` obsahuje data o stavu a připojení týkající se IoT Edgeho agenta a modulů runtime centra IoT Edge a vašich vlastních modulů. Agent IoT Edge zodpovídá za nasazení modulů, jejich monitorování a stav připojení sestav do služby Azure IoT Hub.
* `$edgeHub` obsahuje data o komunikaci mezi IoT Edgem rozbočovačem běžícím na zařízení a ve službě Azure IoT Hub. To zahrnuje zpracování příchozích zpráv ze zařízení pro příjem dat. Centrum IoT Edge zodpovídá za zpracování komunikace mezi IoT Hub Azure a IoT Edgemi zařízeními a moduly.

Data jsou uspořádána do metadat, značek, spolu s požadovanými a nahlášenými vlastnostmi v modulech, které jsou ve strukturách JSON. Požadované vlastnosti, které jste zadali v deployment.jssouboru, se zkopírují do nevláken modulu. Agent IoT Edge a centrum IoT Edge každé aktualizuje hlášené vlastnosti pro jejich moduly.

Podobně požadované vlastnosti zadané pro vlastní moduly v deployment.jsv souboru se zkopírují do jeho nedokončeného modulu, ale vaše řešení zodpovídá za poskytování jeho hlášených hodnot vlastností.

Tento článek popisuje, jak zkontrolovat nevlákenná vlákna v modulu Azure Portal, Azure CLI a v Visual Studio Code. Informace o sledování, jak vaše zařízení dostanou nasazení, najdete v tématu [monitorování nasazení IoT Edge](how-to-monitor-iot-edge-deployments.md). Přehled konceptu nevlákenných modulů naleznete [v tématu pochopení a použití vláken modulu v IoT Hub](../iot-hub/iot-hub-devguide-module-twins.md).

> [!TIP]
> Hlášené vlastnosti běhového modulu můžou být zastaralé, pokud se zařízení IoT Edge odpojí od služby IoT Hub. [ping](how-to-edgeagent-direct-method.md#ping) `$edgeAgent` Pokud chcete zjistit, jestli se připojení ztratilo, můžete na něm použít test z tohoto modulu.

## <a name="monitor-runtime-module-twins"></a>Sledovat vlákna modulu runtime

Pokud chcete řešit problémy s připojením nasazení, přečtěte si téma IoT Edge agenta a modul runtime IoT Edge centra a pak přejděte k podrobnostem o dalších modulech.

### <a name="monitor-iot-edge-agent-module-twin"></a>Nevlákenovaná IoT Edge modulu agenta monitorování

Následující JSON zobrazuje `$edgeAgent` nevlákenný modul v Visual Studio Code s většinou sbalenými oddíly JSON.

```json
{
  "deviceId": "Windows109",
  "moduleId": "$edgeAgent",
  "etag": "AAAAAAAAAAU=",
  "deviceEtag": "NzgwNjA1MDUz",
  "status": "enabled",
  "statusUpdateTime": "0001-01-01T00:00:00Z",
  "connectionState": "Disconnected",
  "lastActivityTime": "0001-01-01T00:00:00Z",
  "cloudToDeviceMessageCount": 0,
  "authenticationType": "sas",
  "x509Thumbprint": {
    "primaryThumbprint": null,
    "secondaryThumbprint": null
  },
  "version": 53,
  "properties": {
    "desired": { "···" },
    "reported": {
      "schemaVersion": "1.0",
      "version": { "···" },
      "lastDesiredStatus": { "···" },
      "runtime": { "···" },
      "systemModules": {
        "edgeAgent": { "···" },
        "edgeHub": { "···" }
      },
      "lastDesiredVersion": 5,
      "modules": {
        "SimulatedTemperatureSensor": { "···" }
      },
      "$metadata": { "···" },
      "$version": 48
    }
  }
}
```

KÓD JSON může být popsaný v následujících částech od začátku:

* Metadata – obsahuje data o připojení. V prodlení je stav připojení agenta IoT Edge vždy v odpojeném stavu: `"connectionState": "Disconnected"` . Důvodem, proč se stav připojení týká zpráv D2C (zařízení-Cloud) a Agent IoT Edge neodesílají zprávy D2C.
* Vlastnosti – obsahuje dílčí `desired` `reported` sekce a.
* Vlastnosti. požadovaný – (zobrazený sbalený) očekávané hodnoty vlastností nastavené operátorem v deployment.jsv souboru.
* Properties. hlášené – nejnovější hodnoty vlastností hlášené agentem IoT Edge.

Jak `properties.desired` oddíly a `properties.reported` mají podobnou strukturu a obsahují další metadata pro schéma, verzi a informace o modulu runtime. K dispozici je také `modules` oddíl pro všechny vlastní moduly (například `SimulatedTemperatureSensor` ), a `systemModules` část pro `$edgeAgent` `$edgeHub` moduly runtime a.

Porovnáním hlášených hodnot vlastností s požadovanými hodnotami můžete určit rozpory a identifikovat odpojení, která vám můžou pomoct s řešením problémů. Při provádění těchto porovnávání ověřte `$lastUpdated` hlášené hodnoty v `metadata` části pro vlastnost, kterou zkoumáte.

Následující vlastnosti jsou důležité pro kontrolu řešení potíží:

* **ExitCode** – jakákoliv jiná hodnota než nula znamená, že se modul zastavil s chybou. Kódy chyb 137 nebo 143 se ale použijí, pokud byl modul záměrně nastavený na stav zastaveno.

* **lastStartTimeUtc** – zobrazí **Datum a čas** posledního spuštění kontejneru. Tato hodnota je 0001-01-01T00:00:00Z, pokud se kontejner nespustil.

* **lastExitTimeUtc** – zobrazuje **hodnotu DateTime** , kterou byl kontejner naposledy dokončen. Tato hodnota je 0001-01-01T00:00:00Z, pokud je kontejner spuštěný a nikdy se nezastavil.

* **runtimeStatus** – může to být jedna z následujících hodnot:

    | Hodnota | Popis |
    | --- | --- |
    | Neznámý | Výchozí stav, dokud se nevytvoří nasazení. |
    | omezení rychlosti | Spuštění modulu je naplánováno, ale aktuálně není spuštěno. Tato hodnota je užitečná pro modul, který provádí změny stavu při restartování. V případě, že se neúspěšný modul čeká na restartování během doby chladnutí, modul bude ve stavu omezení rychlosti. |
    | instalovanou | Indikuje, že modul je aktuálně spuštěný. |
    | není v pořádku | Indikuje, že kontrola stavu testu selhala nebo vypršel časový limit. |
    | ukončen | Indikuje, že modul byl úspěšně ukončen (s nulovým ukončovacím kódem). |
    | failed | Indikuje, že se modul ukončil s ukončovacím kódem selhání (nenulový). Modul se může převést zpátky na omezení rychlosti z tohoto stavu v závislosti na tom, jaké zásady restartování platí. Tento stav může znamenat, že došlo k neodstranitelné chybě v modulu. K chybě dojde, když Microsoft Monitoring Agent (MMA) už nemůže resuscitate modul a vyžaduje nové nasazení. |

Podrobnosti najdete v tématu [EdgeAgent ohlásil vlastnosti](module-edgeagent-edgehub.md#edgeagent-reported-properties) .

### <a name="monitor-iot-edge-hub-module-twin"></a>Nevlákenující modul monitorování IoT Edgeho centra

Následující JSON zobrazuje `$edgeHub` nevlákenný modul v Visual Studio Code s většinou sbalenými oddíly JSON.

```json
{
  "deviceId": "Windows109",
  "moduleId": "$edgeHub",
  "etag": "AAAAAAAAAAU=",
  "deviceEtag": "NzgwNjA1MDU2",
  "status": "enabled",
  "statusUpdateTime": "0001-01-01T00:00:00Z",
  "connectionState": "Connected",
  "lastActivityTime": "0001-01-01T00:00:00Z",
  "cloudToDeviceMessageCount": 0,
  "authenticationType": "sas",
  "x509Thumbprint": {
    "primaryThumbprint": null,
    "secondaryThumbprint": null
  },
  "version": 102,
    "properties": {
      "desired": { "···" },
      "reported": {
        "schemaVersion": "1.0",
        "version": { "···" },
      "lastDesiredVersion": 5,
      "lastDesiredStatus": { "···" },
      "clients": {
        "Windows109/SimulatedTemperatureSensor": {
          "status": "Disconnected",
          "lastConnectedTimeUtc": "2020-04-08T21:42:42.1743956Z",
          "lastDisconnectedTimeUtc": "2020-04-09T07:02:42.1398325Z"
        }
      },
      "$metadata": { "···" },
      "$version": 97
    }
  }
}

```

KÓD JSON může být popsaný v následujících částech od začátku:

* Metadata – obsahuje data o připojení.

* Vlastnosti – obsahuje dílčí `desired` `reported` sekce a.
* Vlastnosti. požadovaný – (zobrazený sbalený) očekávané hodnoty vlastností nastavené operátorem v deployment.jsv souboru.
* Properties. hlášené – nejnovější hodnoty vlastností hlášené IoT Edge hub.

Pokud máte problémy se zařízeními pro příjem dat, doporučujeme, abyste si prozkoumali tato data, abyste mohli začít.

## <a name="monitor-custom-module-twins"></a>Monitorovat vlákna vlastního modulu

Informace o připojení vašich vlastních modulů jsou zachovány v modulu IoT Edgeho agenta s dvojitou platností. Modul, který je pro váš vlastní modul, se využije hlavně k údržbě dat pro vaše řešení. Požadované vlastnosti, které jste definovali v deployment.jssouboru, se projeví v modulu s dvojitou platností a váš modul může podle potřeby aktualizovat hlášené hodnoty vlastností.

Pomocí sady [SDK pro zařízení Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks#azure-iot-hub-device-sdks) můžete použít preferovaný programovací jazyk k aktualizaci hlášených hodnot vlastností v modulu, a to na základě kódu aplikace vašeho modulu. Následující postup používá k tomu sadu Azure SDK pro .NET, která používá kód z modulu [SimulatedTemperatureSensor](https://github.com/Azure/iotedge/blob/dd5be125df165783e4e1800f393be18e6a8275a3/edge-modules/SimulatedTemperatureSensor/src/Program.cs) :

1. Vytvořte instanci třídy [ModuleClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient) pomocí metody [CreateFromEnvironmentAysnc](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient.createfromenvironmentasync) .

1. Získejte kolekci vlastností vlákna modulu s metodou [GetTwinAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient.gettwinasync?view=azure-dotnet) .

1. Vytvoření naslouchacího procesu (předání zpětného volání) pro zachycení změn požadovaných vlastností pomocí metody [SetDesiredPropertyUpdateCallbackAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.setdesiredpropertyupdatecallbackasync?view=azure-dotnet)

1. V metodě zpětného volání aktualizujte hlášené vlastnosti v modulu s dvojitou metodou [UpdateReportedPropertiesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient) a předejte do [ní objekt s hodnotou vlastnosti](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.shared.twincollection) , kterou chcete nastavit.

## <a name="access-the-module-twins"></a>Přístup k nevlákenám modulu

V Azure IoT Hub, v Visual Studio Code a pomocí rozhraní příkazového řádku Azure, si můžete prohlédnout JSON pro vlákna modulu.

### <a name="monitor-in-azure-iot-hub"></a>Monitorování v Azure IoT Hub

Chcete-li zobrazit JSON pro modul s dvojitou podobou:

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a přejděte do služby IoT Hub.
1. V nabídce levého podokna vyberte **IoT Edge** .
1. Na kartě **zařízení IoT Edge** vyberte **ID zařízení** s moduly, které chcete monitorovat.
1. Na kartě **moduly** vyberte název modulu a potom v horním řádku nabídek vyberte možnost **Identita modulu** .

  ![Vyberte nevlákenný modul, který chcete zobrazit v Azure Portal](./media/how-to-monitor-module-twins/select-module-twin.png)

Pokud se zobrazí zpráva "pro tento modul neexistuje identita modulu", tato chyba indikuje, že back-end řešení již není k dispozici, které původně vytvořilo identitu.

### <a name="monitor-module-twins-in-visual-studio-code"></a>Monitorovat vlákna v modulu Visual Studio Code

Chcete-li zkontrolovat a upravit modul s dvojitou podmnožinou:

1. Pokud ještě není nainstalovaná, nainstalujte [rozšíření Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) pro Visual Studio Code.
1. V **Průzkumníkovi**rozbalte **IoT Hub Azure**a potom rozbalte zařízení s modulem, který chcete monitorovat.
1. Klikněte pravým tlačítkem na modul a vyberte **Upravit modul s dvojitou**čárkou. Dočasný soubor s nevlákenou modulu se stáhne do vašeho počítače a zobrazí se v Visual Studio Code.

  ![Získání nevlákenných modulů pro úpravu v Visual Studio Code](./media/how-to-monitor-module-twins/edit-module-twin-vscode.png)

Pokud provedete změny, vyberte možnost **aktualizovat modul s dvojitým** kódem v editoru a uložte změny do služby IoT Hub.

  ![Aktualizace vlákna v modulu v Visual Studio Code](./media/how-to-monitor-module-twins/update-module-twin-vscode.png)

### <a name="monitor-module-twins-in-azure-cli"></a>Monitorování vláken v modulu Azure CLI

Pokud chcete zjistit, jestli je IoT Edge spuštěná, použijte příkaz [AZ IoT Hub Invoke-Module-Method](how-to-edgeagent-direct-method.md#ping) k otestování agenta IoT Edge.

[Modul AZ IoT Hub Module-zdvojená](/cli/azure/ext/azure-cli-iot-ext/iot/hub/module-twin) struktura poskytuje tyto příkazy:

* **AZ IoT Hub Module-zdvojené zobrazení** -zobrazení definice vlákna v modulu
* **AZ IoT Hub Module-zdvojená aktualizace** – aktualizuje definici s dvojím načtením.
* **AZ IoT Hub Module-zdvojené nahrazení** -nahraďte nestejnou definici modulu s cílovým JSON.

## <a name="next-steps"></a>Další kroky

Naučte [se komunikovat s EdgeAgent pomocí integrovaných přímých metod](how-to-edgeagent-direct-method.md).
