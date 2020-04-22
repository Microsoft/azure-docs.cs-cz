---
title: Automatická správa zařízení ve velkém měřítku pomocí služby Azure IoT Hub (CLI) | Dokumenty společnosti Microsoft
description: Automatické konfigurace Služby Azure IoT Hub slouží ke správě více zařízení nebo modulů IoT
author: Philmea
manager: bruz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: philmea
ms.openlocfilehash: 76728b99f1e9309294079d4df88e563054e780e7
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81767568"
---
# <a name="automatic-iot-device-and-module-management-using-the-azure-cli"></a>Automatická správa zařízení a modulů IoT pomocí rozhraní příkazového příkazu Azure

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-hub-auto-device-config-selector.md)]

Automatická správa zařízení v Azure IoT Hub automatizuje mnoho opakujících se a složitých úloh správy velkých vozových parků zařízení. Díky automatické správě zařízení můžete cílit na sadu zařízení na základě jejich vlastností, definovat požadovanou konfiguraci a pak nechat IoT Hub aktualizovat zařízení, když se dostanou do oboru. Tato aktualizace se provádí pomocí _automatické konfigurace zařízení_ nebo konfigurace _automatického modulu_, která umožňuje shrnout dokončení a dodržování předpisů, zpracování slučování a konfliktů a zavádění konfigurací v postupném přístupu.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Automatická správa zařízení funguje tak, že aktualizuje sadu dvojčat zařízení nebo dvojčata modulu s požadovanými vlastnostmi a hlásí souhrn, který je založen na vlastnostech ohlášených dvojčat.  Zavádí novou třídu a dokument JSON s názvem *Konfigurace,* která má tři části:

* **Cílová podmínka** definuje obor dvojčata zařízení nebo dvojčata modulu, které mají být aktualizovány. Cílová podmínka je určena jako dotaz na značky dvojčete zařízení nebo oznamovány vlastnosti.

* **Cílový obsah** definuje požadované vlastnosti, které mají být přidány nebo aktualizovány v cílových dvojčatzařízení nebo dvojčata modulu. Obsah obsahuje cestu k části požadované vlastnosti, které mají být změněny.

* **Metriky** definují souhrnný počet různých stavů konfigurace, jako je **úspěch**, **probíhá**a **chyba**. Vlastní metriky jsou určeny jako dotazy na dvojče hlášené vlastnosti.  Systémové metriky jsou výchozí metriky, které měří stav aktualizace dvojčete, jako je například počet dvojčat, která jsou cílená a počet dvojčat, která byla úspěšně aktualizována.

Automatické konfigurace spustit poprvé krátce po vytvoření konfigurace a pak v pětiminutových intervalech. Metriky dotazy spustit při každém spuštění automatické konfigurace.

## <a name="cli-prerequisites"></a>Požadavky cli

* Centrum [IoT v](../iot-hub/iot-hub-create-using-cli.md) předplacenéazure. 

* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) ve vašem prostředí. Minimálně vaše verze Azure CLI musí být 2.0.70 nebo vyšší. Ke kontrole použijte příkaz `az –-version`. Tato verze podporuje příkazy rozšíření az a zavádí příkazové rozhraní Knack. 

* [Rozšíření IoT pro Azure CLI](https://github.com/Azure/azure-cli).

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

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

## <a name="define-the-target-content-and-metrics"></a>Definování cílového obsahu a metrik

Cílový obsah a dotazy na metriky jsou určeny jako dokumenty JSON, které popisují požadované vlastnosti dvojčete nebo dvojčete modulu zařízení, které mají být nastaveny a oznamovány vlastnosti k měření.  Chcete-li vytvořit automatickou konfiguraci pomocí azure cli, uložte cílový obsah a metriky místně jako soubory TXT. Cesty k souborům v pozdější části se používají při spuštění příkazu k použití konfigurace v zařízení.

Tady je základní ukázka cílového obsahu pro automatickou konfiguraci zařízení:

```json
{
  "content": {
    "deviceContent": {
      "properties.desired.chillerWaterSettings": {
        "temperature": 38,
        "pressure": 78
      }
    }
}
```

Konfigurace automatických modulů se chovají velmi `moduleContent` podobně, ale cílíte místo `deviceContent`.

```json
{
  "content": {
    "moduleContent": {
      "properties.desired.chillerWaterSettings": {
        "temperature": 38,
        "pressure": 78
      }
    }
}
```

Tady jsou příklady dotazů na metriky:

```json
{
  "queries": {
    "Compliant": "select deviceId from devices where configurations.[[chillerdevicesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='current'",
    "Error": "select deviceId from devices where configurations.[[chillerdevicesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='error'",
    "Pending": "select deviceId from devices where configurations.[[chillerdevicesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='pending'"
  }
}
```

Metriky pro moduly jsou také podobné dotazy pro zařízení, ale vyberete pro `moduleId` . `devices.modules` Příklad: 

```json
{
  "queries": {
    "Compliant": "select deviceId, moduleId from devices.module where configurations.[[chillermodulesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='current'"
  }
}
```

## <a name="create-a-configuration"></a>Vytvoření konfigurace

Cílová zařízení nakonfigurujete vytvořením konfigurace, která se skládá z cílového obsahu a metrik. 

K vytvoření konfigurace použijte následující příkaz:

```azurecli
   az iot hub configuration create --config-id [configuration id] \
     --labels [labels] --content [file path] --hub-name [hub name] \
     --target-condition [target query] --priority [int] \
     --metrics [metric queries]
```

* --**config-id** - Název konfigurace, která bude vytvořena v centru IoT. Pojmenujte konfiguraci jedinečným názvem, který má až 128 malých písmen. Vyhněte se mezerám `& ^ [ ] { } \ | " < > /`a následujícím neplatným znakům: .

* --**popisky** – Přidejte štítky, které vám pomohou sledovat konfiguraci. Popisky jsou Název, Dvojice hodnot, které popisují vaše nasazení. Příklad: `HostPlatform, Linux` nebo `Version, 3.0.1`

* --**content** - Inline JSON nebo cesta k souboru k cílovému obsahu, který má být nastaven jako twin požadované vlastnosti. 

* --**název centra** – název centra IoT, ve kterém bude konfigurace vytvořena. Centrum musí být v aktuálním předplatném. Přepněte na požadované předplatné pomocí příkazu`az account set -s [subscription name]`

* --**cílový stav** – Zadejte cílovou podmínku, která určí, která zařízení nebo moduly budou touto konfigurací zaměřeny.Pro automatickou konfiguraci zařízení je podmínka založena na značkách dvojčete zařízení nebo požadovaných vlastnostech dvojčete zařízení a měla by odpovídat formátu výrazu.Příkladem je `tags.environment='test'` nebo `properties.desired.devicemodel='4000x'`.Pro automatickou konfiguraci modulu je podmínka založena na dvouznačkách modulu nebo požadovaných vlastnostech dvojčete modulu.. Příkladem je `from devices.modules where tags.environment='test'` nebo `from devices.modules where properties.reported.chillerProperties.model='4000x'`.

* --**priorita** - Kladné celé číslo. V případě, že dvě nebo více konfigurací jsou zaměřeny na stejné zařízení nebo modul, bude platit konfigurace s nejvyšší číselnou hodnotou priority.

* --**metriky** – Filepath k dotazům na metriky. Metriky poskytují souhrnpočty různých stavů, které zařízení nebo modul může hlásit zpět po použití obsahu konfigurace. Můžete například vytvořit metriku pro čekající změny nastavení, metriku chyb a metriku pro úspěšné změny nastavení. 

## <a name="monitor-a-configuration"></a>Sledování konfigurace

K zobrazení obsahu konfigurace použijte následující příkaz:

```azurecli
az iot hub configuration show --config-id [configuration id] \
  --hub-name [hub name]
```

* --**config-id** - Název konfigurace, která existuje v centru IoT.

* --**název centra** – název centra IoT hub, ve kterém konfigurace existuje. Centrum musí být v aktuálním předplatném. Přepněte na požadované předplatné pomocí příkazu`az account set -s [subscription name]`

Zkontrolujte konfiguraci v příkazovém okně.Vlastnost **metriky** uvádí počet pro každou metriku, která je vyhodnocována každým centrem:

* **targetedCount** - systémová metrika, která určuje počet dvojčat zařízení nebo dvojčata modulů v centru IoT Hub, které odpovídají podmínce cílení.

* **appliedCount** - Systémová metrika určuje počet zařízení nebo modulů, u kterých byl použit cílový obsah.

* **Vaše vlastní metrika** – všechny metriky, které jste definovali, jsou metriky pro uživatele.

Seznam ID zařízení, ID modulu nebo objektů pro každou z metrik můžete zobrazit pomocí následujícího příkazu:

```azurecli
az iot hub configuration show-metric --config-id [configuration id] \
   --metric-id [metric id] --hub-name [hub name] --metric-type [type] 
```

* --**config-id** - Název nasazení, který existuje v centru IoT.

* --**metric-ID** – název metriky, pro kterou chcete zobrazit seznam ID zařízení nebo ID modulu, například `appliedCount`.

* --**název centra** – název služby IoT hub, ve kterém existuje nasazení. Centrum musí být v aktuálním předplatném. Přepněte na požadované předplatné `az account set -s [subscription name]`pomocí příkazu .

* --**typ metric-** Typ metriky může být `system` nebo `user`.  Systémové metriky `targetedCount` `appliedCount`jsou a . Všechny ostatní metriky jsou metriky uživatele.

## <a name="modify-a-configuration"></a>Změna konfigurace

Když změníte konfiguraci, změny se okamžitě replikují do všech cílených zařízení. 

Pokud aktualizujete cílovou podmínku, dojde k následujícím aktualizacím:

* Pokud dvojče nesplnilo starou cílovou podmínku, ale splňuje novou cílovou podmínku a tato konfigurace je pro toto dvojče nejvyšší prioritou, použije se tato konfigurace. 

* Pokud dvojče aktuálně spuštěné v této konfiguraci již nesplňuje cílovou podmínku, nastavení z konfigurace bude odebráno a dvojče bude změněno další konfigurací s nejvyšší prioritou. 

* Pokud dvojče aktuálně spuštěné v této konfiguraci již nesplňuje cílovou podmínku a nesplňuje cílovou podmínku jiných konfigurací, budou nastavení z konfigurace odebrána a nebudou provedeny žádné další změny na dvojčeti. 

K aktualizaci konfigurace použijte následující příkaz:

```azurecli
az iot hub configuration update --config-id [configuration id] \
   --hub-name [hub name] --set [property1.property2='value']
```

* --**config-id** - Název konfigurace, která existuje v centru IoT.

* --**název centra** – název centra IoT hub, ve kterém konfigurace existuje. Centrum musí být v aktuálním předplatném. Přepněte na požadované předplatné `az account set -s [subscription name]`pomocí příkazu .

* --**set** - Aktualizace vlastnosti v konfiguraci. Můžete aktualizovat následující vlastnosti:

    * targetCondition - například`targetCondition=tags.location.state='Oregon'`

    * Popisky 

    * Prioritou

## <a name="delete-a-configuration"></a>Odstranění konfigurace

Když odstraníte konfiguraci, všechna dvojčata zařízení nebo dvojčata modulu převezmou svou další konfiguraci s nejvyšší prioritou. Pokud dvojčata nesplňují cílovou podmínku jiné konfigurace, pak se nepoužijí žádná další nastavení. 

K odstranění konfigurace použijte následující příkaz:

```azurecli
az iot hub configuration delete --config-id [configuration id] \
   --hub-name [hub name] 
```

* --**config-id** - Název konfigurace, která existuje v centru IoT.

* --**název centra** – název centra IoT hub, ve kterém konfigurace existuje. Centrum musí být v aktuálním předplatném. Přepněte na požadované předplatné `az account set -s [subscription name]`pomocí příkazu .

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
