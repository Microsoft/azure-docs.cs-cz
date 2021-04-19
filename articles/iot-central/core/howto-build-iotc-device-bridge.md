---
title: Nasazení mostu zařízení Azure IoT Central | Microsoft Docs
description: Nasaďte most IoT Central zařízení pro propojení dalších cloudů IoT s aplikací IoT Central. Mezi další cloudy IoT patří Sigfox, Cloud pro zařízení ve částicích a síť.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 04/19/2021
ms.topic: how-to
ms.openlocfilehash: 6b535ecb80fae9f55eb6ab11751c26e0c6d0e9e5
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2021
ms.locfileid: "107713703"
---
# <a name="use-the-iot-central-device-bridge-to-connect-other-iot-clouds-to-iot-central"></a>Použití mostu IoT Central zařízení k připojení dalších cloudů IoT k IoT Central

*Tento článek se týká správců.*

## <a name="azure-iot-central-device-bridge"></a>Most zařízení Azure IoT Central

Most zařízení IoT Central je open source řešení, které spojuje další cloudy IoT s vaší aplikací IoT Central. Mezi příklady dalších cloudů IoT patří [Sigfox](https://www.sigfox.com/), [Cloud pro zařízení ve částicích](https://www.particle.io/)a [síť věcí](https://www.thethingsnetwork.org/). Most zařízení funguje pomocí předávání dat ze zařízení připojených k jiným cloudům IoT do vaší aplikace IoT Central. Most zařízení předává data pouze do IoT Central, neodesílá příkazy ani aktualizace vlastností z IoT Central zpět na zařízení.

Most zařízení vám umožňuje kombinovat sílu IoT Central se zařízeními, jako jsou zařízení pro sledování prostředků připojená k síti WAN s nízkou spotřebou Sigfox, zařízení pro monitorování kvality ovzduší v cloudu zařízení částic nebo v zařízeních pro monitorování vlhkosti půdy v síti. Můžete použít IoT Central funkce aplikace, jako jsou pravidla a analýza dat, vytvářet pracovní postupy v Power automatu a Azure Logic Apps nebo data exportovat.

Řešení pro most zařízení zřídí několik prostředků Azure do předplatného Azure, které společně usnadňuje transformaci a přeposílání zpráv zařízení do IoT Central.

## <a name="prerequisites"></a>Požadavky

K dokončení kroků v tomto průvodci, potřebujete aktivní předplatné Azure.

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Dokončete průvodce [vytvořením aplikace Azure IoT Central](./quick-deploy-iot-central.md) pro vytvoření IoT Central aplikace pomocí vlastní šablony **aplikace > vlastní** .

## <a name="overview"></a>Přehled

Most zařízení IoT Central je open source řešení na GitHubu. Používá vlastní šablonu Azure Resource Manager nasazení několika prostředků do předplatného Azure, včetně aplikace Azure Functions.

Aplikace Function App je základní částí mostu zařízení. Přijímá požadavky HTTP POST od jiných platforem IoT prostřednictvím jednoduchého Webhooku. Úložiště [mostů pro zařízení Azure IoT Central](https://github.com/Azure/iotc-device-bridge) obsahuje příklady, které ukazují, jak propojit Sigfox, částic a síťové cloudy. Toto řešení můžete roztáhnout, abyste se připojili k vlastnímu cloudu IoT, pokud vaše platforma může odesílat žádosti HTTP POST do vaší aplikace Function App.

Aplikace Function App transformuje data do formátu přijatého IoT Central a předá je pomocí rozhraní API služby Device Provisioning a klienta zařízení:

:::image type="content" source="media/howto-build-iotc-device-bridge/azure-function.png" alt-text="Snímek obrazovky s snímekem Azure Functions":::

Pokud vaše aplikace IoT Central rozpozná ID zařízení v předané zprávě, telemetrie ze zařízení se zobrazí v IoT Central. Pokud vaše aplikace IoT Central nerozpoznala ID zařízení, aplikace Function App se pokusí zaregistrovat nové zařízení s ID zařízení. Nové zařízení se zobrazí jako **nepřidružené zařízení** na stránce **zařízení** ve vaší aplikaci IoT Central. Na stránce **zařízení** můžete přidružit nové zařízení k šabloně zařízení a pak zobrazit telemetrii.

## <a name="deploy-the-device-bridge"></a>Nasazení mostu zařízení

Nasazení mostu zařízení do předplatného:

1. V aplikaci IoT Central přejděte na stránku **správa > připojení zařízení** .

    1. Poznamenejte si **Rozsah ID**. Tuto hodnotu použijete při nasazení mostu zařízení.

    1. Na stejné stránce otevřete skupinu pro zápis **SAS-IoT-Device** . Na stránce skupina **SAS-IoT-Devices** zkopírujte **primární klíč**. Tuto hodnotu použijete při nasazení mostu zařízení.

1. Pomocí tlačítka **nasadit do Azure** níže otevřete vlastní šablonu správce prostředků, která nasadí aplikaci Function App do svého předplatného. Použijte **Rozsah ID** a **primární klíč** z předchozího kroku:

    [![Nasazení do Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fiotc-device-bridge%2Fmaster%2Fazuredeploy.json)

Po dokončení nasazení budete muset nainstalovat balíčky NPM, které funkce vyžaduje:

1. V Azure Portal otevřete aplikaci Function App, která byla nasazena do vašeho předplatného. Pak přejděte do **Nástroje pro vývoj > konzole**. V konzole nástroje spusťte následující příkazy pro instalaci balíčků:

    ```bash
    cd IoTCIntegration
    npm install
    ```

    Spuštění těchto příkazů může trvat několik minut. Můžete bezpečně ignorovat všechny varovné zprávy.

1. Po dokončení instalace balíčku vyberte na stránce **Přehled** aplikace Function App možnost **restartovat** :

    :::image type="content" source="media/howto-build-iotc-device-bridge/restart.png" alt-text="Snímek obrazovky s restartováním":::

1. Funkce je nyní připravena k použití. Externí systémy můžou použít požadavky HTTP POST k odesílání dat zařízení přes most zařízení do aplikace IoT Central. Adresu URL funkce získáte tak, že přejdete na **funkce > IoTCIntegration > Code + Test > získat adresu URL funkce**:

    :::image type="content" source="media/howto-build-iotc-device-bridge/get-function-url.png" alt-text="Snímek obrazovky s adresou URL funkce Get":::

Texty zpráv odeslané do mostu zařízení musí mít následující formát:

```json
"device": {
  "deviceId": "my-cloud-device"
},
"measurements": {
  "temp": 20.31,
  "pressure": 50,
  "humidity": 8.5,
  "ledColor": "blue"
}
```

Každý klíč v `measurements` objektu se musí shodovat s názvem typu telemetrie v šabloně zařízení v aplikaci IoT Central. Toto řešení nepodporuje zadání ID rozhraní v těle zprávy. Takže pokud mají dvě různá rozhraní typ telemetrie se stejným názvem, měření se zobrazí v datových proudech telemetrie v aplikaci IoT Central.

Do `timestamp` pole text můžete zadat datum a čas UTC zprávy. Toto pole musí být ve formátu ISO 8601. Například, `2020-06-08T20:16:54.602Z`. Pokud nezadáte časové razítko, použije se aktuální datum a čas.

Do textu můžete přidat `modelId` pole. Pomocí tohoto pole můžete zařízení během zřizování přidružit k šabloně zařízení. Tato funkce je podporována pouze [aplikacemi V3](howto-get-app-info.md).

`deviceId`Musí se jednat o alfanumerické a malá písmena a můžou obsahovat spojovníky.

Pokud pole nezahrnete `modelId` nebo pokud IoT Central nerozpozná ID modelu, zpráva s nerozpoznaným nástrojem `deviceId` vytvoří nové _nepřidružené zařízení_ v IoT Central. Operátor může ručně migrovat zařízení do správné šablony zařízení. Další informace najdete v tématu [Správa zařízení ve vaší aplikaci Azure IoT Central > migrace zařízení do šablony](howto-manage-devices.md).

V [aplikacích v2](howto-get-app-info.md)se nové zařízení zobrazí na stránce **Device Explorer > nepřidružená zařízení** . Vyberte možnost **přidružit** a zvolte šablonu zařízení, ve které chcete začít přijímat příchozí telemetrie ze zařízení.

> [!NOTE]
> Dokud nebude zařízení přidruženo k šabloně, vrátí všechna volání HTTP do této funkce chybový stav 403.

Pokud chcete přepnout na protokolování aplikace Function App pomocí Application Insights, přejděte v Azure Portal na **monitorování > protokolů** ve vaší aplikaci Function App. Vyberte **zapnout Application Insights**.

## <a name="provisioned-resources"></a>Zřízené prostředky

Šablona Správce prostředků v předplatném Azure zřídí následující zdroje:

* Function App
* Plán služby App Service
* Účet úložiště
* Trezor klíčů

Trezor klíčů uchovává klíč skupiny SAS pro vaši aplikaci IoT Central.

Function App běží na [plánu spotřeby](https://azure.microsoft.com/pricing/details/functions/). I když tato možnost nenabízí vyhrazené výpočetní prostředky, umožňuje mostu zařízení zpracovávat stovky zpráv zařízení za minutu, které jsou vhodné pro menší loďstva zařízení nebo zařízení, která zprávy odesílají méně často. Pokud vaše aplikace závisí na streamování velkého počtu zpráv zařízení, nahraďte plán spotřeby vyhrazeným [plánem služby App Service](https://azure.microsoft.com/pricing/details/app-service/windows/). Tento plán nabízí vyhrazené výpočetní prostředky, které poskytují rychlejší odezvy serveru. S využitím standardního plánu App Service byl maximální zjištěný výkon funkce Azure Functions v tomto úložišti okolo 1 500 zpráv zařízení za minutu. Další informace najdete v tématu [možnosti hostování funkcí Azure](../../azure-functions/functions-scale.md).

Pokud chcete místo plánu spotřeby použít vyhrazený plán App Service, před nasazením upravte vlastní šablonu. Vyberte  **Upravit šablonu**.

:::image type="content" source="media/howto-build-iotc-device-bridge/edit-template.png" alt-text="Snímek obrazovky s úpravou šablony":::

Nahraďte následující segment:

```json
{
  "type": "Microsoft.Web/serverfarms",
  "apiVersion": "2015-04-01",
  "name": "[variables('planName')]",
  "location": "[resourceGroup().location]",
  "properties": {
    "name": "[variables('planName')]",
    "computeMode": "Dynamic",
    "sku": "Dynamic"
  }
},
```

with

```json
{
  "type": "Microsoft.Web/serverfarms",
  "sku": {
      "name": "S1",
      "tier": "Standard",
      "size": "S1",
      "family": "S",
      "capacity": 1
  },
  "kind": "app",
  "name": "[variables('planName')]",
  "apiVersion": "2016-09-01",
  "location": "[resourceGroup().location]",
  "tags": {
      "iotCentral": "device-bridge",
      "iotCentralDeviceBridge": "app-service-plan"
  },
  "properties": {
      "name": "[variables('planName')]"
  }
},
```

V dalším kroku upravte šablonu, která se má zahrnout `"alwaysOn": true` do konfigurace `functionapp` prostředku v `"properties": {"SiteConfig": {...}}` rámci [Konfigurace alwaysOn](https://github.com/Azure/Azure-Functions/wiki/Enable-Always-On-when-running-on-dedicated-App-Service-Plan) zajišťuje, že aplikace Function App bude vždycky spuštěná.

## <a name="examples"></a>Příklady

Následující příklady popisují, jak nakonfigurovat most zařízení pro různé cloudy IoT:

### <a name="example-1-connecting-particle-devices-through-the-device-bridge"></a>Příklad 1: propojení zařízení částic prostřednictvím mostu zařízení

Pokud chcete připojit zařízení částic prostřednictvím mostu zařízení, aby se IoT Central, přečtěte si konzolu částic a vytvořte novou integraci Webhooku. Nastavte **Formát požadavku** na **JSON**.  V části **Rozšířená nastavení** použijte následující vlastní formát textu:

```json
{
  "device": {
    "deviceId": "{{{PARTICLE_DEVICE_ID}}}"
  },
  "measurements": {
    "{{{PARTICLE_EVENT_NAME}}}": {{{PARTICLE_EVENT_VALUE}}}
  }
}
```

Vložte **adresu URL funkce** z aplikace Azure functions a uvidíte, že se zařízení v IoT Central zobrazí jako nepřidružená zařízení. Další informace najdete v tomto příspěvku v tématu [postup integrace projektů založených na částicích pomocí](https://blog.particle.io/2019/09/26/integrate-particle-with-azure-iot-central/) příspěvku na blogu Azure IoT Central.

### <a name="example-2-connecting-sigfox-devices-through-the-device-bridge"></a>Příklad 2: připojení zařízení Sigfox přes most zařízení

Některé platformy nemusí umožňovat zadání formátu zpráv zařízení odesílaných pomocí Webhooku. U takových systémů je potřeba převést datovou část zprávy do očekávaného formátu těla, než je přemostění zařízení zpracuje. Převod můžete provést ve stejné funkci Azure, která spouští most zařízení.

V této části se dozvíte, jak převést datovou část integrace Webhooku Sigfox do formátu těla očekávaného mostem zařízení. Sigfox Cloud odesílá data zařízení v šestnáctkovém formátu řetězce. Pro usnadnění práce most zařízení zahrnuje funkci převodu pro tento formát, která přijímá podmnožinu možných typů polí v datové části zařízení Sigfox: `int` a `uint` 8, 16, 32 nebo 64 bitů, `float` z 32 bitů nebo 64 bitů, Little endian a big endian. Chcete-li zpracovat zprávy z integrace Webhooku Sigfox, proveďte následující změny v souboru _IoTCIntegration/index.js_ ve Function App.

Chcete-li převést datovou část zprávy, přidejte následující kód před volání `handleMessage` na řádku 21, nahraďte `payloadDefinition` svou definicí datové části Sigfox:

```javascript
const payloadDefinition = 'gforce::uint:8 lat::uint:8 lon::uint:16'; // Replace this with your payload definition

req.body = {
    device: {
        deviceId: req.body.device
    },
    measurements: require('./converters/sigfox')(payloadDefinition, req.body.data)
};
```

Zařízení Sigfox očekávají `204` kód odpovědi. Po volání do řádku 21 přidejte následující kód `handleMessage` :

```javascript
context.res = {
    status: 204
};
```

### <a name="example-3-connecting-devices-from-the-things-network-through-the-device-bridge"></a>Příklad 3: připojení zařízení ze sítě věcí přes most zařízení

Postup připojení síťových zařízení k IoT Central:

* Přidejte do své aplikace novou integraci pomocí protokolu HTTP: **aplikace > integrace > přidat integraci > http Integration**.
* Ujistěte se, že vaše aplikace obsahuje funkci dekodéru, která automaticky převede datovou část zpráv zařízení do formátu JSON předtím, než se pošle do funkce Azure Function: **aplikace > funkce datové části > dekodéru**.

Následující příklad ukazuje funkci dekodéru JavaScriptu, kterou můžete použít k dekódování běžných číselných typů z binárních dat:

```javascript
function Decoder(bytes, port) {
  function bytesToFloat(bytes, decimalPlaces) {
    var bits = (bytes[3] << 24) | (bytes[2] << 16) | (bytes[1] << 8) | bytes[0];
    var sign = (bits >>> 31 === 0) ? 1.0 : -1.0;
    var e = bits >>> 23 & 0xff;
    var m = (e === 0) ? (bits & 0x7fffff) << 1 : (bits & 0x7fffff) | 0x800000;
    var f = Math.round((sign * m * Math.pow(2, e - 150)) * Math.pow(10, decimalPlaces)) / Math.pow(10, decimalPlaces);
    return f;
  }

  function bytesToInt32(bytes, signed) {
    var bits = bytes[0] | (bytes[1] << 8) | (bytes[2] << 16) | (bytes[3] << 24);
    var sign = 1;

    if (signed && bits >>> 31 === 1) {
      sign = -1;
      bits = bits & 0x7FFFFFFF;
    }

    return bits * sign;
  }

  function bytesToShort(bytes, signed) {
    var bits = bytes[0] | (bytes[1] << 8);
    var sign = 1;

    if (signed && bits >>> 15 === 1) {
      sign = -1;
      bits = bits & 0x7FFF;
    }

    return bits * sign;
  }

  return {
    temperature: bytesToFloat(bytes.slice(0, 4), 2),
    presscounter: bytesToInt32(bytes.slice(4, 8), true),
    blueLux: bytesToShort(bytes.slice(8, 10), false)
  };
}
```

Po definování integrace přidejte následující kód před volání do na `handleMessage` řádku 21 souboru *IoTCIntegration nebo index.js* vaší aplikace funkce Azure Functions. Tento kód přeloží tělo integrace protokolu HTTP do očekávaného formátu.

```javascript
device: {
    deviceId: req.body.hardware_serial.toLowerCase()
},
measurements: req.body.payload_fields
};
```

## <a name="limitations"></a>Omezení

Most zařízení přesměruje zprávy pouze na IoT Central a neodesílá zprávy zpět do zařízení. To je důvod, proč vlastnosti a příkazy nefungují u zařízení, která se připojují k IoT Central prostřednictvím tohoto mostu zařízení. Vzhledem k tomu, že se nepodporují nepodporované operace zařízení, není možné aktualizovat vlastnosti zařízení prostřednictvím mostu zařízení. Aby bylo možné používat tyto funkce, zařízení se musí přímo připojit k IoT Central pomocí jedné ze [sad SDK pro zařízení Azure IoT](../../iot-hub/iot-hub-devguide-sdks.md).

## <a name="next-steps"></a>Další kroky
Teď, když jste se naučili, jak nasadit most IoT Central zařízení, je tady doporučený další krok:

> [!div class="nextstepaction"]
> [správu zařízení](howto-manage-devices.md)
