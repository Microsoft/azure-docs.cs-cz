---
title: Použití dynamické telemetrie | Dokumentace Microsoftu
description: V tomto kurzu se naučíte použití dynamické telemetrie s předkonfigurovaného řešení vzdáleného monitorování sady Azure IoT Suite.
services: ''
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 562799dc-06ea-4cdd-b822-80d1f70d2f09
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: 60e9ee00fabf15a62e782c70bca251b1a8e617c3
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38723998"
---
# <a name="use-dynamic-telemetry-with-the-remote-monitoring-preconfigured-solution"></a>Použití dynamické telemetrie s předkonfigurovaného řešení vzdáleného monitorování

Dynamické telemetrie umožňuje vizualizovat všechny telemetrická data odesílaná do předkonfigurovaného řešení vzdáleného monitorování. Simulovaná zařízení, které nasazení s předkonfigurovaným řešením, odesílají telemetrii teploty a vlhkosti, který můžete vizualizovat na řídicím panelu. Pokud se přizpůsobení existujících zařízení pracujících s Simulovaná, vytvoření nového simulovaného zařízení nebo připojení fyzických zařízení k předkonfigurovanému řešení můžete odeslat další telemetrická data hodnoty jako externí teplotu, ot. / min nebo rychlost větru. Potom můžete vizualizovat tato další telemetrické údaje na řídicím panelu.

Tento kurz používá jednoduché simulovaného zařízení Node.js, který můžete snadno upravit, můžete experimentovat s dynamické telemetrie.

K dokončení tohoto kurzu, budete potřebovat:

* Aktivní předplatné Azure. Pokud nemáte účet, můžete si během několika minut vytvořit bezplatný účet zkušební. Podrobnosti najdete v článku [Bezplatná zkušební verze Azure][lnk_free_trial].
* [Node.js] [ lnk-node] verze 0.12.x nebo novější.

Tento kurz pro všechny operační systémy, jako je například Windows nebo Linux, kde ji můžete nainstalovat Node.js.

[!INCLUDE [iot-suite-v1-provision-remote-monitoring](../../includes/iot-suite-v1-provision-remote-monitoring.md)]

[!INCLUDE [iot-suite-v1-send-external-temperature](../../includes/iot-suite-v1-send-external-temperature.md)]

## <a name="add-a-telemetry-type"></a>Přidání telemetrické typu

Dalším krokem je nahradit telemetrii generovanou simulované zařízení Node.js s novou sadu hodnot:

1. Zastavit simulovaného zařízení Node.js zadáním **Ctrl + C** v příkazovém řádku nebo prostředí.
2. Základní data hodnoty pro existující teploty, vlhkosti a externí teplotní telemetrie se zobrazí v souboru remote_monitoring.js. Přidejte hodnotu základní data **ot. / min** následujícím způsobem:

    ```nodejs
    // Sensors data
    var temperature = 50;
    var humidity = 50;
    var externalTemperature = 55;
    var rpm = 200;
    ```

3. Na Node.js simulované zařízení používá **generateRandomIncrement** funkce v souboru remote_monitoring.js přidání náhodného přírůstek do základní datové hodnoty. Náhodné **ot. / min** hodnotu přidáním psát kód za existující randomizations následujícím způsobem:

    ```nodejs
    temperature += generateRandomIncrement();
    externalTemperature += generateRandomIncrement();
    humidity += generateRandomIncrement();
    rpm += generateRandomIncrement();
    ```

4. Přidáte novou hodnotu ot. / min na datovou část JSON, které zařízení odesílá do služby IoT Hub:

    ```nodejs
    var data = JSON.stringify({
      'DeviceID': deviceId,
      'Temperature': temperature,
      'Humidity': humidity,
      'ExternalTemperature': externalTemperature,
      'RPM': rpm
    });
    ```

5. Spuštění simulovaného zařízení Node.js pomocí následujícího příkazu:

    `node remote_monitoring.js`

6. Podívejte se na nový typ telemetrie ot. / min, který se zobrazí v grafu na řídicím panelu:

![Přidání ot. / min na řídicí panel][image3]

> [!NOTE]
> Budete muset zakázat a potom povolit zařízení Node.js na **zařízení** stránky na řídicím panelu, aby se změna projevila okamžitě.

## <a name="customize-the-dashboard-display"></a>Přizpůsobení zobrazení řídicího panelu

**Device-Info** zprávy můžou být metadata o zařízení můžete do služby IoT Hub odesílat telemetrická data. Tato metadata můžete určit typy telemetrie, které zařízení odesílá. Upravit **deviceMetaData** hodnota v souboru remote_monitoring.js **Telemetrie** následující definice **příkazy** definice. Následující fragment kódu ukazuje kódu **příkazy** definice (Nezapomeňte přidat `,` po **příkazy** definice):

```nodejs
'Commands': [{
  'Name': 'SetTemperature',
  'Parameters': [{
    'Name': 'Temperature',
    'Type': 'double'
  }]
},
{
  'Name': 'SetHumidity',
  'Parameters': [{
    'Name': 'Humidity',
    'Type': 'double'
  }]
}],
'Telemetry': [{
  'Name': 'Temperature',
  'Type': 'double'
},
{
  'Name': 'Humidity',
  'Type': 'double'
},
{
  'Name': 'ExternalTemperature',
  'Type': 'double'
}]
```

> [!NOTE]
> Řešení vzdáleného monitorování pro porovnání definice metadat s daty v datovém proudu telemetrických dat používá nerozlišují.


Přidávání **Telemetrie** definice, jak je znázorněno v předchozím fragmentu kódu nezmění chování řídicího panelu. Ale může také obsahovat metadata **DisplayName** atributů pro přizpůsobení zobrazení v řídicím panelu. Aktualizace **Telemetrie** definice metadat, jak je znázorněno v následujícím fragmentu kódu:

```nodejs
'Telemetry': [
{
  'Name': 'Temperature',
  'Type': 'double',
  'DisplayName': 'Temperature (C*)'
},
{
  'Name': 'Humidity',
  'Type': 'double',
  'DisplayName': 'Humidity (relative)'
},
{
  'Name': 'ExternalTemperature',
  'Type': 'double',
  'DisplayName': 'Outdoor Temperature (C*)'
}
]
```

Následující snímek obrazovky ukazuje, jak tato změna upraví legendu grafu na řídicím panelu:

![Přizpůsobit legendu grafu][image4]

> [!NOTE]
> Budete muset zakázat a potom povolit zařízení Node.js na **zařízení** stránky na řídicím panelu, aby se změna projevila okamžitě.

## <a name="filter-the-telemetry-types"></a>Filtrovat typy telemetrie

Ve výchozím nastavení zobrazuje graf na řídicí panel v datovém proudu telemetrických dat každé datové řady. Můžete použít **Device-Info** metadat potlačit zobrazení telemetrie konkrétní typy v grafu. 

Chcete-li vytvořit graf zobrazit telemetrii pouze teploty a vlhkosti, vynechejte **ExternalTemperature** z **Device-Info** **Telemetrie** metadat následujícím způsobem:

```nodejs
'Telemetry': [
{
  'Name': 'Temperature',
  'Type': 'double',
  'DisplayName': 'Temperature (C*)'
},
{
  'Name': 'Humidity',
  'Type': 'double',
  'DisplayName': 'Humidity (relative)'
},
//{
//  'Name': 'ExternalTemperature',
//  'Type': 'double',
//  'DisplayName': 'Outdoor Temperature (C*)'
//}
]
```

**Venkovní teplota** už nebude zobrazovat v grafu:

![Filtrování telemetrických dat na řídicím panelu][image5]

Tato změna ovlivní pouze zobrazení grafu. **ExternalTemperature** datové hodnoty jsou stále uloženy a k dispozici pro zpracování back-end.

> [!NOTE]
> Budete muset zakázat a potom povolit zařízení Node.js na **zařízení** stránky na řídicím panelu, aby se změna projevila okamžitě.

## <a name="handle-errors"></a>Zpracování chyb

Pro datový proud, chcete-li zobrazit v grafu jeho **typ** v **Device-Info** metadata musí odpovídat datovému typu hodnoty telemetrie. Například, pokud metadata, která určuje **typ** vlhkosti data jsou **int** a **double** se nachází v datovém proudu telemetrických dat, pak telemetrie vlhkosti nezobrazí. v grafu. Ale **vlhkosti** hodnoty jsou stále uloženy a k dispozici pro zpracování back-end.

## <a name="next-steps"></a>Další postup

Teď, když už víte, jak používat dynamické telemetrie, další informace o tom, jak předkonfigurovaná řešení využívají informace o zařízení: [předkonfigurované řešení vzdáleného monitorování informace metadat zařízení] [ lnk-devinfo].

[lnk-devinfo]: iot-suite-v1-remote-monitoring-device-info.md

[image3]: media/iot-suite-v1-dynamic-telemetry/image3.png
[image4]: media/iot-suite-v1-dynamic-telemetry/image4.png
[image5]: media/iot-suite-v1-dynamic-telemetry/image5.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-node]: http://nodejs.org
