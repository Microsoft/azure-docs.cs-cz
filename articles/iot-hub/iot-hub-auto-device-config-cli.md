---
title: Konfigurace a monitorování zařízení IoT ve velkém měřítku s Azure IoT Hub (CLI) | Microsoft Docs
description: Na konfigurací automatické zařízení Azure IoT Hub můžete přiřadit konfigurace na více zařízení
author: ChrisGMsft
manager: bruz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/13/2018
ms.author: chrisgre
ms.openlocfilehash: c12a07aabdecb070cfa99f8851f907499599a1fc
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37035986"
---
# <a name="configure-and-monitor-iot-devices-at-scale-using-the-azure-cli"></a>Konfigurace a monitorování zařízení IoT škálované pomocí rozhraní příkazového řádku Azure

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-hub-auto-device-config-selector.md)]

Správa automatického zařízení v Azure IoT Hub automatizuje řadu opakovaných a složitější úlohy správy loďstev velké zařízení přes celého jejich životních cyklů. Se správou automatického zařízení můžete cílí na skupiny zařízení, na základě jejich vlastností, zadejte požadované konfigurace a nechat IoT Hub, aktualizujte si zařízení vždy, když se do oboru.  To se provádí pomocí konfigurace automatického zařízení, která vám také umožní shrnout doplňování a dodržování předpisů, slučování popisovač a je v konfliktu a zavedení konfigurace v rámci fázového přístupu.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Konfigurace pracovních zařízení automatické aktualizace sadu dvojčata zařízení pomocí požadované vlastnosti a vytváření sestav Souhrn podle dvojče zařízení hlášené vlastnosti.  Zavádí nové třídy a dokument JSON s názvem _konfigurace_ jehož tří částí:

* **Cíle podmínku** definuje rozsah dvojčata zařízení aktualizovat. Cílovou podmínku je zadán jako dotaz na zařízení twin značky nebo hlášené vlastnosti.

* **Směrovat obsah** definuje požadované vlastnosti, které chcete přidat nebo aktualizovat v dvojčata cílové zařízení. Obsah obsahuje cestu k části požadované vlastnosti chcete změnit.

* **Metriky** definovat souhrnné počty různé stavy konfigurace, jako **úspěch**, **probíhá**, a **chyba**. Vlastní metriky v dotazech na zařízení zadány twin hlášené vlastnosti.  Metriky systému jsou výchozích metrik, které měří stav aktualizace twin, například počet dvojčata zařízení, které jsou cíleny a počet dvojčata, které byly úspěšně aktualizovány. 

## <a name="cli-prerequisites"></a>Požadavky rozhraní příkazového řádku

* [Služby IoT hub](../iot-hub/iot-hub-create-using-cli.md) ve vašem předplatném Azure. 
* [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) ve vašem prostředí. Vaše verze Azure CLI 2.0 musí být minimálně 2.0.24 nebo novější. Ke kontrole použijte příkaz `az –-version`. Tato verze podporuje příkazy rozšíření az a zavádí příkazové rozhraní Knack. 
* [IoT rozšíření pro Azure CLI 2.0](https://github.com/Azure/azure-iot-cli-extension).

## <a name="implement-device-twins-to-configure-devices"></a>Implementace dvojčata zařízení konfigurace zařízení

Konfigurace automatického zařízení vyžadují použití dvojčata zařízení synchronizovat stav mezi cloudu a zařízení.  Odkazovat na [Rady pro pochopení a použití dvojčata zařízení IoT hub] [ lnk-device-twin] pokyny k použití dvojčata zařízení.

## <a name="identify-devices-using-tags"></a>Identifikovat zařízení pomocí značek

Před vytvořením konfigurace, je nutné zadat zařízení, která chcete ovlivnit. Azure IoT Hub identifikovat zařízení pomocí značek v dvojče zařízení. Každé zařízení může mít více značek a jejich můžete definovat způsobem, který dává smysl pro vaše řešení. Například pokud budete spravovat zařízení v různých umístěních, můžete přidat těmito značkami k dvojče zařízení:

```json
"tags": {
    "location": {
        "state": "Washington",
        "city": "Tacoma"
    }
},
```
## <a name="define-the-target-content-and-metrics"></a>Zadejte cíl obsah a metriky

Cíl obsah a metrika dotazy jsou definované jako dokumenty JSON, které popisují zařízení twin požadované vlastnosti jako sada a hlášené vlastnosti měření.  Vytvoření konfigurace aplikace automatické zařízení pomocí Azure CLI 2.0, uložte obsah cíl a metriky místně jako soubory s příponou .txt. Když spustíte příkaz pro použití v konfiguraci zařízení, použije cesty k souborům v novější další části. 

Zde je ukázka základní cíl obsahu:

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

Zde jsou příklady metriky dotazů:

```json
{
  "queries": {
    "Compliant": "select deviceId from devices where configurations.[[chillersettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='current'",
    "Error": "select deviceId from devices where configurations.[[chillersettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='error'",
    "Pending": "select deviceId from devices where configurations.[[chillersettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='pending'"
  }
}
```

## <a name="create-a-configuration"></a>Vytvoření konfigurace

Cílová zařízení nakonfigurujete tak, že vytvoříte konfigurace, která se skládá z obsahu cíl a metriky. 

Použijte následující příkaz pro vytvoření konfigurace:

   ```cli
   az iot hub configuration create --config-id [configuration id] --labels [labels] --content [file path] --hub-name [hub name] --target-condition [target query] --priority [int] --metrics [metric queries]
   ```

* **id – konfigurace** -název konfigurace, která bude vytvořena ve službě IoT hub. Zadejte konfiguraci jedinečný název, který je až 128 malá písmena. Vyhněte se mezery a následující neplatné znaky: `& ^ [ ] { } \ | " < > /`.
* **--popisky** -přidat popisky, abyste mohli snadněji sledovat konfiguraci. Popisky jsou název, hodnotu páry, které popisují vaše nasazení. Například `HostPlatform, Linux` nebo `Version, 3.0.1`
* **--obsah** -JSON vložené nebo cesta k obsahu cíl nastavit jako twin požadovaných vlastností. 
* **– název centra** -název centra IoT, ve kterém se vytvoří konfigurace. Rozbočovače musí být v aktuálním předplatném. Přepnout na požadované předplatné pomocí příkazu `az account set -s [subscription name]`
* **--cílovou podmínku** -zadejte cílovou podmínku k určení zařízení, která budou cílem s touto konfigurací. Je založena na zařízení twin značky nebo dvojče zařízení potřeby vlastnosti a musí odpovídat formátu výraz. Například `tags.environment='test'` nebo `properties.desired.devicemodel='4000x'`. 
* **--priority** -kladné celé číslo. V případě, že dvě nebo více konfigurací se budou zaměřovat na stejném zařízení, uplatní se konfigurace s nejvyšší číselnou hodnotou priority.
* **--metriky** -Filepath metriky dotazy. Metriky poskytují souhrnné počty různé stavy, které zařízení může hlásit zpět v důsledku použití obsah konfigurace. Můžete například vytvořit metriky pro čekající změny nastavení metriky pro chyby a metriky pro změny nastavení úspěšné. 

## <a name="monitor-a-configuration"></a>Monitorování konfigurace

Použijte následující příkaz k zobrazení obsahu konfigurace:

   ```cli
az iot hub configuration show --config-id [configuration id] --hub-name [hub name]
   ```
* **id – konfigurace** -název konfigurace, které existuje ve službě IoT hub.
* **– název centra** -název centra IoT, ve kterém existuje v konfiguraci. Rozbočovače musí být v aktuálním předplatném. Přepnout na požadované předplatné pomocí příkazu `az account set -s [subscription name]`

Zkontrolujte konfiguraci v příkazovém okně. **Metriky** počet pro jednotlivé metriky, který je vyhodnocován každého rozbočovače seznamů vlastností:
* **targetedCount** -metrika systému, která určuje počet dvojčata zařízení IoT hub, které splňují podmínku cílení.
* **appliedCount** -metrika systému určuje počet zařízení, která má cíl obsahu použita.
* **Vlastní metriku** -všechny metriky, které jste definovali bude považovat za metrik uživatele.

Pro každou z metriky můžete zobrazit seznam ID zařízení nebo objekty pomocí následujícího příkazu:

   ```cli
az iot hub configuration show-metric --config-id [configuration id] --metric-id [metric id] --hub-name [hub name] --metric-type [type] 
   ```

* **id – konfigurace** -název nasazení, které existuje ve službě IoT hub.
* **– id metrika** – název metriky, pro který chcete zobrazit seznam zařízení ID, například `appliedCount`
* **– název centra** -název centra IoT, ve kterém nasazení existuje. Rozbočovače musí být v aktuálním předplatném. Přepnout na požadované předplatné pomocí příkazu `az account set -s [subscription name]`
* **– Typ metriky** -může být typ metriky `system` nebo `user`.  Metriky systému `targetedCount` a `appliedCount`. Všechny další metriky jsou metrik uživatele.

## <a name="modify-a-configuration"></a>Upravte konfiguraci

Pokud upravíte konfiguraci, změny okamžitě replikovat do všech cílových zařízení. 

Pokud aktualizujete cílovou podmínku, provedou se následující aktualizace:
* Pokud dvojče zařízení nesplnilo původní cílovou podmínku, ale splňuje novou cílovou podmínku a tato konfigurace je nejvyšší prioritou pro tento dvojče zařízení, tato konfigurace se použijí pro dvojče zařízení. 
* Pokud dvojče zařízení už splňuje cílovou podmínku, budou odstraněna nastavení z konfigurace a dvojče zařízení se změní další konfigurace nejvyšší prioritou. 
* Pokud dvojče zařízení aktuálně spuštěných této konfigurace už splňuje cílovou podmínku a nesplňuje cíl podmínku všechny ostatní konfigurace, nastavení z konfigurace se odeberou a na twin budou provedeny žádné další změny. 

Použijte následující příkaz k aktualizaci konfigurace:

   ```cli
az iot hub configuration update --config-id [configuration id] --hub-name [hub name] --set [property1.property2='value']
   ```
* **id – konfigurace** -název konfigurace, které existuje ve službě IoT hub.
* **– název centra** -název centra IoT, ve kterém existuje v konfiguraci. Rozbočovače musí být v aktuálním předplatném. Přepnout na požadované předplatné pomocí příkazu `az account set -s [subscription name]`
* **– Nastavte** -aktualizovat vlastnosti v konfiguraci. Můžete aktualizovat následující vlastnosti:
    * targetCondition – např. `targetCondition=tags.location.state='Oregon'`
    * popisky 
    * priorita

## <a name="delete-a-configuration"></a>Odstranění konfigurace

Při odstranění konfigurace žádné dvojčata zařízení trvat na jejich další konfigurace nejvyšší prioritou. Pokud dvojčata zařízení nesplňují podmínku cíl jakoukoli jinou konfiguraci, se neuplatní žádné další nastavení. 

Pokud chcete odstranit konfiguraci použijte následující příkaz:

   ```cli
az iot hub configuration delete --config-id [configuration id] --hub-name [hub name] 
   ```
* **id – konfigurace** -název konfigurace, které existuje ve službě IoT hub.
* **– název centra** -název centra IoT, ve kterém existuje v konfiguraci. Rozbočovače musí být v aktuálním předplatném. Přepnout na požadované předplatné pomocí příkazu `az account set -s [subscription name]`

## <a name="next-steps"></a>Další postup
V tomto článku jste se dozvěděli, jak konfigurovat a monitorovat zařízení IoT ve velkém měřítku. Další informace o správě Azure IoT Hub na následujících odkazech:

* [Spravovat vaše identit zařízení IoT Hub hromadně][lnk-bulkIDs]
* [Metriky služby IoT Hub][lnk-metrics]
* [Monitorování operací][lnk-monitor]

Pokud chcete prozkoumat další možnosti IoT Hub, najdete v části:

* [Příručka vývojáře pro službu IoT Hub][lnk-devguide]
* [Nasazení AI do hraničních zařízení s použitím Azure IoT Edge][lnk-iotedge]

Prozkoumat pomocí službu zřizování zařízení IoT Hub povolit zajišťování nula touch, za běhu, najdete v článku: 

* [Zařízení Azure IoT Hub zřizování služby][lnk-dps]

[lnk-device-twin]: iot-hub-devguide-device-twins.md
[lnk-bulkIDs]: iot-hub-bulk-identity-mgmt.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-dps]: https://azure.microsoft.com/documentation/services/iot-dps
[lnk-portal]: https://portal.azure.com
