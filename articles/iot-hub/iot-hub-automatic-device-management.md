---
title: Automatická správa zařízení ve velkém měřítku pomocí Azure IoT Hub | Dokumenty společnosti Microsoft
description: Automatické konfigurace Služby Azure IoT Hub slouží ke správě více zařízení a modulů IoT
author: ChrisGMsft
manager: bruz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: chrisgre
ms.openlocfilehash: 75c6b7d89e7ae540e7428afde127281aa3f15fc6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271301"
---
# <a name="automatic-iot-device-and-module-management-using-the-azure-portal"></a>Automatická správa zařízení a modulů IoT pomocí portálu Azure

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-hub-auto-device-config-selector.md)]

Automatická správa zařízení v Azure IoT Hub automatizuje mnoho opakujících se a složitých úloh správy velkých vozových parků zařízení. Díky automatické správě zařízení můžete cílit na sadu zařízení na základě jejich vlastností, definovat požadovanou konfiguraci a pak nechat IoT Hub aktualizovat zařízení, když se dostanou do oboru. Tato aktualizace se provádí pomocí _automatické konfigurace zařízení_ nebo konfigurace _automatického modulu_, která umožňuje shrnout dokončení a dodržování předpisů, zpracování slučování a konfliktů a zavádění konfigurací v postupném přístupu.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Automatická správa zařízení funguje tak, že aktualizuje sadu dvojčat zařízení nebo dvojčata modulu s požadovanými vlastnostmi a hlásí souhrn, který je založen na vlastnostech ohlášených dvojčat.  Zavádí novou třídu a dokument JSON s názvem *Konfigurace,* která má tři části:

* **Cílová podmínka** definuje obor dvojčata zařízení nebo dvojčata modulu, které mají být aktualizovány. Cílová podmínka je určena jako dotaz na značky dvojčat nebo ohlášené vlastnosti.

* **Cílový obsah** definuje požadované vlastnosti, které mají být přidány nebo aktualizovány v cílových dvojčatzařízení nebo dvojčata modulu. Obsah obsahuje cestu k části požadované vlastnosti, které mají být změněny.

* **Metriky** definují souhrnný počet různých stavů konfigurace, jako je **úspěch**, **probíhá**a **chyba**. Vlastní metriky jsou určeny jako dotazy na dvojče hlášené vlastnosti.  Systémové metriky jsou výchozí metriky, které měří stav aktualizace dvojčete, jako je například počet dvojčat, která jsou cílená a počet dvojčat, která byla úspěšně aktualizována.

Automatické konfigurace spustit poprvé krátce po vytvoření konfigurace a pak v pětiminutových intervalech. Metriky dotazy spustit při každém spuštění automatické konfigurace.

## <a name="implement-twins"></a>Implementovat dvojčata

Automatické konfigurace zařízení vyžadují použití dvojčat zařízení k synchronizaci stavu mezi cloudem a zařízeními.  Další informace najdete v tématu [Principy a použití dvojčat zařízení ve službě IoT Hub](iot-hub-devguide-device-twins.md).

Konfigurace automatických modulů vyžadují použití dvojčat modulu k synchronizaci stavu mezi cloudem a moduly. Další informace najdete [v tématu Principy a použití dvojčat modulů v centru IoT Hub](iot-hub-devguide-module-twins.md).

## <a name="use-tags-to-target-twins"></a>Použití značek k cílení dvojčat

Před vytvořením konfigurace je nutné určit, která zařízení nebo moduly chcete ovlivnit. Azure IoT Hub identifikuje zařízení a pomocí značek v dvojčeti zařízení a identifikuje moduly pomocí značek v dvojčeti modulu. Každé zařízení nebo moduly mohou mít více značek a můžete je definovat jakýmkoli způsobem, který dává smysl pro vaše řešení. Pokud například spravujete zařízení na různých místech, přidejte do dvojčete zařízení následující značky:

```json
"tags": {
    "location": {
        "state": "Washington",
        "city": "Tacoma"
    }
},
```

## <a name="create-a-configuration"></a>Vytvoření konfigurace

1. Na [webu Azure Portal](https://portal.azure.com)přejděte do centra IoT hub. 

2. Vyberte **konfiguraci zařízení IoT**.

3. Vyberte **Přidat konfiguraci zařízení** nebo **Přidat konfiguraci modulu**.

   ![Přidání konfigurace zařízení nebo modulu](./media/iot-hub-automatic-device-management/create-automatic-configuration.png)

Existuje pět kroků k vytvoření konfigurace. Následující části procházejí každou z nich. 

### <a name="name-and-label"></a>Název a popisek

1. Pojmenujte konfiguraci jedinečným názvem, který má až 128 malých písmen. Vyhněte se mezerám `& ^ [ ] { } \ | " < > /`a následujícím neplatným znakům: .

2. Přidejte štítky, které vám pomohou sledovat konfigurace. Popisky jsou **název** **, dvojice hodnot,** které popisují konfiguraci. Příkladem je `HostPlatform, Linux` nebo `Version, 3.0.1`.

3. Výběrem **možnosti Další** přejděte k dalšímu kroku. 

### <a name="specify-settings"></a>Zadat nastavení

Tato část definuje obsah, který má být nastaven v cílových dvojčatech zařízení nebo modulu. Pro každou sadu nastavení existují dva vstupy. První je dvojče cesty, což je cesta k oddílu JSON v rámci twin požadované vlastnosti, které budou nastaveny.  Druhý je obsah JSON, které mají být vloženy v této části. 

Můžete například nastavit cestu dvojčete k `properties.desired.chiller-water` následujícímu obsahu JSON a zadat jej: 

```json
{
  "temperature": 66,
  "pressure": 28
}
```

![Nastavení cesty dvojčete a obsahu](./media/iot-hub-automatic-device-management/module-config-twin-settings.png)


Můžete také nastavit jednotlivá nastavení zadáním celé cesty dvojčete a poskytnutím hodnoty bez závorek. Například s dvojitou `properties.desired.chiller-water.temperature`cestou nastavte `66`obsah na . Pak vytvořte nové nastavení dvojčete pro vlastnost tlaku. 

Pokud dvě nebo více konfigurací cílí na stejnou cestu dvojčete, použije se obsah z konfigurace s nejvyšší prioritou (priorita je definována v kroku 4).

Chcete-li odebrat existující vlastnost, zadejte `null`hodnotu vlastnosti .

Další nastavení můžete přidat výběrem možnosti **Přidat nastavení dvojčete zařízení** nebo Přidat nastavení **dvojčete modulu**.

### <a name="specify-metrics-optional"></a>Určit metriky (volitelné)

Metriky poskytují souhrnpočty různých stavů, které zařízení nebo modul může hlásit zpět po použití obsahu konfigurace. Můžete například vytvořit metriku pro čekající změny nastavení, metriku chyb a metriku pro úspěšné změny nastavení.

Každá konfigurace může mít až pět vlastních metrik. 

1. Zadejte název pro **Název metriky**.

2. Zadejte dotaz na **kritéria metriky**.  Dotaz je založen na vlastnostech ohlášených dvojčete zařízení.  Metrika představuje počet řádků vrácených dotazem.

Například:

```sql
SELECT deviceId FROM devices 
  WHERE properties.reported.chillerWaterSettings.status='pending'
```

Můžete zahrnout klauzuli, že konfigurace byla použita, například: 

```sql
/* Include the double brackets. */
SELECT deviceId FROM devices 
  WHERE configurations.[[yourconfigname]].status='Applied'
```

Pokud vytváříte metriku pro vytváření přehledů `moduleId` na `devices.modules`konfigurovaných modulech, vyberte z . Například:

```sql
SELECT deviceId, moduleId FROM devices.modules
  WHERE properties.reported.lastDesiredStatus.code = 200
```

### <a name="target-devices"></a>Cílová zařízení

Pomocí vlastnosti značky z dvojčat cílí na konkrétní zařízení nebo moduly, které by měly přijímat tuto konfiguraci. Můžete také cílit na vlastnosti ohlášené dvojčetem.

Automatické konfigurace zařízení mohou cílit pouze na značky dvojčete zařízení a konfigurace automatických modulů mohou cílit pouze na značky dvojčat modulu. 

Vzhledem k tomu, že více konfigurací může cílit na stejné zařízení nebo modul, každá konfigurace potřebuje číslo priority. Pokud dojde ke konfliktu, konfigurace s nejvyšší prioritou vyhrává. 

1. Zadejte kladné celé číslo pro **prioritu**konfigurace . Nejvyšší číselná hodnota je považována za nejvyšší prioritu. Pokud dvě konfigurace mají stejné číslo priority, vyhrává ta, která byla vytvořena naposledy. 

2. Zadejte **cílovou podmínku,** která určí, která zařízení nebo moduly budou s touto konfigurací zacílena. Podmínka je založena na značkách dvojčat nebo na vlastnostech oznámených dvojčat a měla by odpovídat formátu výrazu. 

   Pro automatickou konfiguraci zařízení můžete zadat pouze značku nebo ohlášenou vlastnost, na kterou chcete cílit. Příkladem je `tags.environment='test'` nebo `properties.reported.chillerProperties.model='4000x'`. Můžete určit `*` cílit na všechna zařízení. 
   
   Pro automatickou konfiguraci modulu použijte dotaz k určení značek nebo ohlášených vlastností z modulů registrovaných v centru IoT. Příkladem je `from devices.modules where tags.environment='test'` nebo `from devices.modules where properties.reported.chillerProperties.model='4000x'`. Zástupný znak nelze použít k cílení na všechny moduly. 

3. Výběrem **možnosti Další** přejděte k poslednímu kroku.

### <a name="review-configuration"></a>Zkontrolovat konfiguraci

Zkontrolujte informace o konfiguraci a vyberte **Odeslat**.

## <a name="monitor-a-configuration"></a>Sledování konfigurace

Chcete-li zobrazit podrobnosti o konfiguraci a sledovat zařízení, která ji spouštějí, postupujte takto:

1. Na [webu Azure Portal](https://portal.azure.com)přejděte do centra IoT hub. 

2. Vyberte **konfiguraci zařízení IoT**.

3. Zkontrolujte konfigurační seznam. Pro každou konfiguraci můžete zobrazit následující podrobnosti:

   * **ID** - název konfigurace.

   * **Cílová podmínka** - dotaz použitý k definování cílových zařízení nebo modulů.

   * **Priorita** - číslo priority přiřazené konfiguraci.

   * **Čas vytvoření** - časové razítko od okamžiku, kdy byla konfigurace vytvořena. Toto časové razítko se používá k přerušení vazeb, pokud dvě konfigurace mají stejnou prioritu. 

   * **Systémové metriky** – metriky, které jsou vypočteny službou IoT Hub a vývojáři je nemohou přizpůsobit. Cílová určuje počet dvojčat zařízení, které odpovídají cílové podmínce. Platí zadaný počet dvojčat zařízení, které byly změněny konfigurací, které mohou zahrnovat částečné změny v případě, že samostatná konfigurace s vyšší prioritou také provedla změny. 

   * **Vlastní metriky** – metriky, které byly vývojářem určeny jako dotazy na vlastnosti nahlášené dvojčetem.  Pro konfiguraci lze definovat až pět vlastních metrik. 
   
4. Vyberte konfiguraci, kterou chcete sledovat.  

5. Zkontrolujte podrobnosti konfigurace. Pomocí karet můžete zobrazit konkrétní podrobnosti o zařízeních, která obdržela konfiguraci.

   * **Cílová podmínka** - zařízení nebo moduly, které odpovídají cílové podmínce. 

   * **Metriky** – seznam systémových metrik a vlastních metrik.  Seznam zařízení nebo modulů, které se počítají pro každou metriku, můžete zobrazit tak, že vyberete metriku v rozevíracím seznamu a pak vyberete **zobrazit zařízení** nebo **zobrazit moduly**.

   * **Nastavení dvojčete zařízení** nebo **nastavení dvojčete modulu** - nastavení dvojčat, která jsou nastavena konfigurací. 

   * **Popisky konfigurace** - dvojice klíč-hodnota slouží k popisu konfigurace.  Popisky nemají žádný vliv na funkčnost. 

## <a name="modify-a-configuration"></a>Změna konfigurace

Při úpravě konfigurace se změny okamžitě replikují do všech cílených zařízení nebo modulů. 

Pokud aktualizujete cílovou podmínku, dojde k následujícím aktualizacím:

* Pokud dvojče nesplnilo starou cílovou podmínku, ale splňuje novou cílovou podmínku a tato konfigurace je pro toto dvojče nejvyšší prioritou, použije se tato konfigurace. 

* Pokud dvojče aktuálně spuštěné v této konfiguraci již nesplňuje cílovou podmínku, nastavení z konfigurace bude odebráno a dvojče bude změněno další konfigurací s nejvyšší prioritou. 

* Pokud dvojče aktuálně spuštěné v této konfiguraci již nesplňuje cílovou podmínku a nesplňuje cílovou podmínku jiných konfigurací, budou nastavení z konfigurace odebrána a nebudou provedeny žádné další změny na dvojčeti. 

Chcete-li upravit konfiguraci, postupujte takto: 

1. Na [webu Azure Portal](https://portal.azure.com)přejděte do centra IoT hub. 

2. Vyberte **konfiguraci zařízení IoT**. 

3. Vyberte konfiguraci, kterou chcete upravit. 

4. Proveďte aktualizace následujících polí: 

   * Cílová podmínka 
   * Popisky 
   * Priorita 
   * Metriky

4. Vyberte **Uložit**.

5. Postupujte podle pokynů v [monitoru konfigurace](#monitor-a-configuration) sledovat změny zavádět. 

## <a name="delete-a-configuration"></a>Odstranění konfigurace

Když odstraníte konfiguraci, všechna dvojčata zařízení převezmou další konfiguraci s nejvyšší prioritou. Pokud dvojčata zařízení nesplňují cílovou podmínku jiné konfigurace, pak se nepoužijí žádná další nastavení. 

1. Na [webu Azure Portal](https://portal.azure.com)přejděte do centra IoT hub. 

2. Vyberte **konfiguraci zařízení IoT**. 

3. Pomocí zaškrtávacího políčka vyberte konfiguraci, kterou chcete odstranit. 

4. Vyberte **Odstranit**.

5. Výzva vás vyzve k potvrzení.

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli, jak konfigurovat a monitorovat zařízení IoT ve velkém měřítku. Další informace o správě služby Azure IoT Hub najdete na těchto odkazech:

* [Hromadná správa identit zařízení služby IoT Hub](iot-hub-bulk-identity-mgmt.md)
* [Metriky IoT Hubu](iot-hub-metrics.md)
* [Monitorování operací](iot-hub-operations-monitoring.md)

Další informace o možnostech IoT Hubu najdete v následujících tématech:

* [Průvodce vývojáři ioT Hubu](iot-hub-devguide.md)
* [Nasazení AI do hraničních zařízení s použitím Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)

Chcete-li prozkoumat pomocí služby zřizování zařízení služby IoT Hub a povolit zřizování s nulovým dotykem a just-in-time, přečtěte si následující: 

* [Služba Azure IoT Hub Device Provisioning](/azure/iot-dps)
