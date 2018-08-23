---
title: Konfigurovat a monitorovat zařízení IoT ve velkém měřítku s využitím Azure IoT Hub (CLI) | Dokumentace Microsoftu
description: Konfigurace automatického zařízení Azure IoT Hub můžete přiřadit konfiguraci pro různá zařízení
author: ChrisGMsft
manager: bruz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/13/2018
ms.author: chrisgre
ms.openlocfilehash: f81ef3c231874f314d6fe023ba247a0bcff61e90
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2018
ms.locfileid: "42054010"
---
# <a name="configure-and-monitor-iot-devices-at-scale-using-the-azure-cli"></a>Konfigurovat a monitorovat zařízení IoT ve velkém měřítku pomocí Azure CLI

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-hub-auto-device-config-selector.md)]

Správa automatického zařízení ve službě Azure IoT Hub automatizuje mnoho opakovaných a složité úlohy správy flotily nebo velké zařízení přes celého jejich životního cyklu. Se správou automatického zařízení můžete cílit na sadu zařízení na základě jejich vlastností, definují požadovanou konfiguraci a nechat pokaždé, když se do rozsahu sem přišli aktualizaci zařízení služby IoT Hub.  To se provádí pomocí automatické konfigurace, které vám také umožní shrnout dokončení a dodržování předpisů, popisovač sloučení a konflikty a zavedení konfigurace v rámci fázového přístupu.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Konfigurace pracovních automatické aktualizace sadu dvojčata zařízení pomocí požadovaných vlastností a vytváření sestav Souhrn podle dvojče zařízení ohlášené vlastnosti.  Přináší nová třída a dokument JSON s názvem *konfigurace* která má tři části:

* **Cílová podmínka** definuje rozsah dvojčata zařízení aktualizovat. Cílová podmínka je zadán jako dotaz na značky dvojčat zařízení a/nebo ohlášené vlastnosti.

* **Směrovat obsah** definuje požadované vlastnosti, které chcete přidat ani aktualizovat v dvojčat cílové zařízení. Obsah obsahuje cestu k oddílu požadované vlastnosti změnit.

* **Metriky** definovat souhrnný počet různých stavů konfigurace, jako **úspěch**, **probíhá**, a **chyba**. Vlastní metriky jsou zadané jako dotazy na zařízení ohlášené vlastnosti dvojčete.  Systémové metriky jsou výchozí metrik pro měření stavu aktualizace dvojčete, jako je počet dvojčata zařízení, na kterou cílí a počet dvojčat, které se úspěšně aktualizovaly. 

## <a name="cli-prerequisites"></a>Požadavky na rozhraní příkazového řádku

* [Služby IoT hub](../iot-hub/iot-hub-create-using-cli.md) ve vašem předplatném Azure. 
* [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) ve vašem prostředí. Vaše verze Azure CLI 2.0 musí být minimálně 2.0.24 nebo novější. Ke kontrole použijte příkaz `az –-version`. Tato verze podporuje příkazy rozšíření az a zavádí příkazové rozhraní Knack. 
* [Rozšíření IoT pro Azure CLI 2.0](https://github.com/Azure/azure-iot-cli-extension).

## <a name="implement-device-twins-to-configure-devices"></a>Implementace dvojčata zařízení ke konfiguraci zařízení

Konfigurace automatického zařízení vyžadují použití dvojčat zařízení k synchronizaci stavu mezi cloudem a zařízení.  Odkazovat na [principy a použití dvojčat zařízení ve službě IoT Hub](iot-hub-devguide-device-twins.md) pokyny k použití dvojčat zařízení.

## <a name="identify-devices-using-tags"></a>Identifikace zařízení pomocí značek

Než budete moct vytvořit konfiguraci, je nutné zadat zařízení, která chcete ovlivnit. Azure IoT Hub identifikuje zařízení pomocí značek ve dvojčeti zařízení. Každé zařízení může mít více značek a můžete je definovat způsobem, který dává smysl pro vaše řešení. Pokud budete spravovat zařízení v různých umístěních, můžete například přidat následující značky do dvojčete zařízení:

```json
"tags": {
    "location": {
        "state": "Washington",
        "city": "Tacoma"
    }
},
```

## <a name="define-the-target-content-and-metrics"></a>Zadejte cíl obsahu a metriky

Cíl obsahu a metriku dotazy jsou definované jako dokumenty JSON, které popisují zařízení dvojčete požadovaných vlastností tak, aby sada a ohlášené vlastnosti k měření.  Automatické konfiguraci pomocí Azure CLI 2.0 vytvoříte uložte cíl obsahu a metriky místně jako soubory s příponou .txt. Při spuštění příkazu použijte konfiguraci zařízení použijete v další části Další cesty k souborům. 

Tady je ukázka základní cíl obsahu:

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

Tady jsou příklady dotazy na metriky:

```json
{
  "queries": {
    "Compliant": "select deviceId from devices where configurations.[[chillersettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='current'",
    "Error": "select deviceId from devices where configurations.[[chillersettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='error'",
    "Pending": "select deviceId from devices where configurations.[[chillersettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='pending'"
  }
}
```

## <a name="create-a-configuration"></a>Vytvořit konfiguraci

Cílová zařízení nakonfigurujete tak, že vytvoříte konfiguraci, která se skládá z obsahu cíl a metriky. 

Použijte následující příkaz pro vytvoření konfigurace:

```cli
   az iot hub configuration create --config-id [configuration id] \
     --labels [labels] --content [file path] --hub-name [hub name] \
     --target-condition [target query] --priority [int] \
     --metrics [metric queries]
```

* --**id konfigurace** – název konfigurace, která bude vytvořena ve službě IoT hub. Zadejte konfiguraci jedinečný název, který je až 128 malá písmena. Vyhněte se mezery a následující neplatné znaky: `& ^ [ ] { } \ | " < > /`.

* --**popisky** -přidat popisky pro sledování vaší konfigurace. Popisky jsou název, páry hodnota, která popisují vaše nasazení. Například `HostPlatform, Linux` nebo `Version, 3.0.1`

* --**obsah** -vložené JSON nebo cesta k cílové obsah nastavit jako dvojčete požadované vlastnosti. 

* --**název centra** – název služby IoT hub, ve kterém se vytvoří konfigurace. Centrum musí být v rámci aktuálního předplatného. Přepnutí na požadované předplatné příkazem `az account set -s [subscription name]`

* --**Cílová podmínka** – zadejte cílovou podmínku k určení zařízení, která budou cílem s touto konfigurací. Podmínka je založen na značky dvojčat zařízení nebo požadované vlastnosti dvojčete zařízení a by měl odpovídat formátu výrazu. Například `tags.environment='test'` nebo `properties.desired.devicemodel='4000x'`. 

* --**Priorita** -kladné celé číslo. V případě, že dva nebo více konfigurací cílí na stejném zařízení, budou platit konfigurace s nejvyšší číselnou hodnotou priority.

* --**metriky** -Filepath metriky dotazů. Metriky poskytují souhrnný počet různé stavy, které zařízení může nahlásit v důsledku použití obsah konfigurace. Například můžete vytvořit metriku pro čekající změny nastavení, metriky pro chyby a metriky pro úspěšné nastavení změny. 

## <a name="monitor-a-configuration"></a>Monitorování konfigurace

Použijte následující příkaz k zobrazení obsahu, konfigurace:

```cli
az iot hub configuration show --config-id [configuration id] \
  --hub-name [hub name]
```

* --**id konfigurace** – název konfigurace, který existuje ve službě IoT hub.

* --**název centra** – název služby IoT hub, ve které existuje v konfiguraci. Centrum musí být v rámci aktuálního předplatného. Přepnutí na požadované předplatné příkazem `az account set -s [subscription name]`

Zkontrolujte konfiguraci role v příkazovém okně. **Metriky** počet pro každou metriku, která se vyhodnocuje na základě každé centrum seznamů vlastností:

* **targetedCount** – systém metriku, která určuje počet dvojčat zařízení ve službě IoT Hub, která splňují cílovou podmínku.

* **appliedCount** -metrika systému určuje počet zařízení, která jste využili cíl obsahu použita.

* **Vlastní metrika** – všechny metriky, které jste definovali se budou považovat za metrik uživatele.

Pomocí následujícího příkazu můžete zobrazit seznam ID zařízení nebo objekty pro každý z metrik:

```cli
az iot hub configuration show-metric --config-id [configuration id] \
   --metric-id [metric id] --hub-name [hub name] --metric-type [type] 
```

* --**id konfigurace** – název nasazení, který existuje ve službě IoT hub.

* --**Metrika id** – název metriky, pro kterou chcete zobrazit seznam zařízení ID, například `appliedCount`.

* --**název centra** – název služby IoT hub, ve které existuje nasazení. Centrum musí být v rámci aktuálního předplatného. Přepnutí na požadované předplatné příkazem `az account set -s [subscription name]`.

* --**Typ metriky** -metriky typ může být `system` nebo `user`.  Systémové metriky jsou `targetedCount` a `appliedCount`. Veškerá jiná metrika jsou metrik uživatele.

## <a name="modify-a-configuration"></a>Upravit konfigurace

Pokud upravíte konfiguraci, replikaci změn okamžitě na všechna cílová zařízení. 

Pokud aktualizujete cílovou podmínku, dojde k následující aktualizace:

* Pokud dvojčete zařízení nesplnilo původní cílovou podmínku, ale splňuje novou cílovou podmínku a tato konfigurace je nejvyšší prioritu tohoto dvojčete zařízení, tato konfigurace se použije k dvojčeti zařízení. 

* Pokud dvojčete zařízení již splňuje cílovou podmínku, nastavení z konfigurace se odeberou a dvojčeti zařízení budou upraveny konfigurací další nejvyšší prioritu. 

* Pokud aktuálně spuštěné této konfigurace už dvojčete zařízení splňuje cílovou podmínku a nesplňuje cílová podmínka všechny ostatní konfigurace, nastavení z konfigurace se odeberou a provedeny žádné další změny v dvojčeti. 

Použijte následující příkaz k aktualizaci konfigurace:

```cli
az iot hub configuration update --config-id [configuration id] \
   --hub-name [hub name] --set [property1.property2='value']
```

* --**id konfigurace** – název konfigurace, který existuje ve službě IoT hub.

* --**název centra** – název služby IoT hub, ve které existuje v konfiguraci. Centrum musí být v rámci aktuálního předplatného. Přepnutí na požadované předplatné příkazem `az account set -s [subscription name]`.

* --**Nastavte** – umožňuje aktualizovat vlastnost v konfiguraci. Můžete aktualizovat následujícími vlastnostmi:

    * targetCondition – například `targetCondition=tags.location.state='Oregon'`

    * popisky 

    * priorita

## <a name="delete-a-configuration"></a>Odstranění konfigurace

Při konfiguraci odstranit, všechny dvojčata zařízení převezmou konfigurace jejich další nejvyšší prioritu. Dvojčata zařízení nesplňují cílovou podmínku ostatní konfigurace, se použijí žádná nastavení. 

Pomocí následujícího příkazu odstraňte konfigurace:

```cli
az iot hub configuration delete --config-id [configuration id] \
   --hub-name [hub name] 
```
* --**id konfigurace** – název konfigurace, který existuje ve službě IoT hub.

* --**název centra** – název služby IoT hub, ve které existuje v konfiguraci. Centrum musí být v rámci aktuálního předplatného. Přepnutí na požadované předplatné příkazem `az account set -s [subscription name]`.

## <a name="next-steps"></a>Další postup

V tomto článku jste zjistili, jak konfigurovat a monitorovat zařízení IoT ve velkém měřítku. Další informace o správě služby Azure IoT Hub na následujících odkazech:

* [Správa identit zařízení služby IoT Hub hromadné](iot-hub-bulk-identity-mgmt.md)
* [Metriky služby IoT Hub](iot-hub-metrics.md)
* [Monitorování operací](iot-hub-operations-monitoring.md)

Podrobněji prozkoumat možnosti služby IoT Hub, najdete v tématech:

* [Příručka vývojáře pro IoT Hub](iot-hub-devguide.md)
* [Nasazení AI do hraničních zařízení pomocí služby Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)

Prozkoumat pomocí IoT Hub Device Provisioning Service umožňuje plně automatizované, just-in-time zřizování, najdete v tématech: 

* [Služba Azure IoT Hub Device Provisioning](/azure/iot-dps)
