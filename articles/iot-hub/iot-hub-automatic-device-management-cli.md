---
title: Automatická správa zařízení ve velkém měřítku s využitím Azure IoT Hub (CLI) | Microsoft Docs
description: Použití automatických konfigurací Azure IoT Hub ke správě více zařízení nebo modulů IoT
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: robinsh
ms.openlocfilehash: 21d8f513ea0f749f0318b9bc5926a746f840505b
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2020
ms.locfileid: "92147835"
---
# <a name="automatic-iot-device-and-module-management-using-the-azure-cli"></a>Automatická správa zařízení a modulů IoT pomocí rozhraní Azure CLI

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-hub-auto-device-config-selector.md)]

Automatická správa zařízení v Azure IoT Hub automatizuje mnoho opakujících se a složitých úloh při správě rozsáhlých loďstva zařízení. Díky automatické správě zařízení můžete cílit na sadu zařízení na základě jejich vlastností, definovat požadovanou konfiguraci a potom nechat zařízení IoT Hub aktualizovat, když vstoupí do rozsahu. Tato aktualizace se provádí pomocí _automatické konfigurace zařízení_ nebo _automatické konfigurace modulu_, která umožňuje shrnout dokončování a dodržování předpisů, zpracovávat sloučení a konflikty a nastavovat konfigurace v rámci postupného přístupu.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Automatická správa zařízení funguje tak, že aktualizuje sadu dvojitých vláken zařízení nebo nevláken modulu s požadovanými vlastnostmi a ohlásí souhrn, který vychází z nefunkčních hlášených vlastností.  Zavádí nový dokument třídy a JSON s názvem *Konfigurace* , která má tři části:

* **Podmínka cíle** definuje rozsah vláken zařízení nebo nepodmíněných vláken modulu, které se mají aktualizovat. Cílová podmínka je zadána jako dotaz na značky a/nebo hlášené vlastnosti zařízení.

* **Cílový obsah** definuje požadované vlastnosti, které se mají přidat nebo aktualizovat v cílovém zařízení vlákna nebo vlákna v modulu. Obsah zahrnuje cestu k oddílu požadovaných vlastností, které mají být změněny.

* **Metriky** definují Souhrnné počty různých stavů **konfigurace, například** **úspěch**, probíhá a **Chyba**. Vlastní metriky jsou zadány jako dotazy na nedokončené hlášené vlastnosti.  Systémové metriky jsou výchozí metriky, které měří stav s dvojitou aktualizací, jako je třeba počet nezpracovaných vláken a počet úspěšně aktualizovaných vláken.

Automatické konfigurace se spouští poprvé po vytvoření konfigurace a pak v intervalu pěti minut. Dotazy na metriky se spouštějí při každém spuštění automatické konfigurace.

## <a name="cli-prerequisites"></a>Předpoklady rozhraní příkazového řádku

* [IoT Hub](../iot-hub/iot-hub-create-using-cli.md) ve vašem předplatném Azure. 

* Rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli) ve vašem prostředí. Minimální verze rozhraní příkazového řádku Azure CLI musí být 2.0.70 nebo vyšší. Ke kontrole použijte příkaz `az –-version`. Tato verze podporuje příkazy rozšíření az a zavádí příkazové rozhraní Knack. 

* [Rozšíření IoT pro Azure CLI](https://github.com/Azure/azure-cli)

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="implement-twins"></a>Implementovat vlákna

Automatická konfigurace zařízení vyžaduje, aby se při synchronizaci stavu mezi cloudem a zařízeními použily vlákna zařízení.  Další informace najdete v tématu [Principy a použití dvojčat zařízení ve službě IoT Hub](iot-hub-devguide-device-twins.md).

Automatická konfigurace modulů vyžaduje použití vláken modulu k synchronizaci stavu mezi cloudem a moduly. Další informace najdete v tématu [pochopení a použití vláken v modulu v IoT Hub](iot-hub-devguide-module-twins.md).

## <a name="use-tags-to-target-twins"></a>Použití značek k cílení na vlákna

Před vytvořením konfigurace musíte určit, která zařízení nebo moduly mají mít vliv. Azure IoT Hub identifikuje zařízení a používá značky v zařízení, které jsou v práci, a identifikuje moduly pomocí značek v modulu s dvojitou signalizací. Každé zařízení nebo moduly může mít více značek a můžete je definovat jakýmkoli způsobem, který dává smysl pro vaše řešení. Pokud například spravujete zařízení v různých umístěních, přidejte do vlákna zařízení následující značky:

```json
"tags": {
    "location": {
        "state": "Washington",
        "city": "Tacoma"
    }
},
```

## <a name="define-the-target-content-and-metrics"></a>Definování cílového obsahu a metrik

Dotazy cílového obsahu a metriky jsou zadány jako dokumenty JSON, které popisují nedokončené a modulované vlastnosti vláken v zařízení, aby bylo možné nastavit a nahlásit vlastnosti pro měření.  Pokud chcete vytvořit automatickou konfiguraci pomocí Azure CLI, uložte cílový obsah a metriky místně jako soubory. txt. Cesty k souborům použijete v pozdější části, když spustíte příkaz, který aplikuje konfiguraci na vaše zařízení.

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

Automatické konfigurace modulů se chovají velmi podobně, ale místo toho cílíte na `moduleContent` místo `deviceContent` .

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

Dotazy na metriky pro moduly jsou také podobné dotazům na zařízení, ale můžete je vybrat `moduleId` z `devices.modules` . Například: 

```json
{
  "queries": {
    "Compliant": "select deviceId, moduleId from devices.module where configurations.[[chillermodulesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='current'"
  }
}
```

## <a name="create-a-configuration"></a>Vytvořit konfiguraci

Cílová zařízení můžete nakonfigurovat vytvořením konfigurace, která se skládá z cílového obsahu a metrik. 

Pomocí následujícího příkazu vytvořte konfiguraci:

```azurecli
   az iot hub configuration create --config-id [configuration id] \
     --labels [labels] --content [file path] --hub-name [hub name] \
     --target-condition [target query] --priority [int] \
     --metrics [metric queries]
```

* --**config-ID** – název konfigurace, která se vytvoří ve službě IoT Hub. Poskytněte konfiguraci jedinečný název, který bude obsahovat až 128 malých písmen. Vyhněte se mezerám a následujícími neplatnými znaky: `& ^ [ ] { } \ | " < > /` .

* --**Labels** – přidejte popisky, které vám pomůžou sledovat vaši konfiguraci. Popisky jsou názvy, páry hodnot, které popisují vaše nasazení. Příkladem je `HostPlatform, Linux` nebo `Version, 3.0.1`.

* --**obsah** – vložená JSON nebo cesta k souboru cílovému obsahu, který se má nastavit jako nevlákenná požadovaná vlastnost. 

* --**název-centra** – název centra IoT, ve kterém se konfigurace vytvoří. Centrum musí být v aktuálním předplatném. Přepněte na požadované předplatné pomocí příkazu. `az account set -s [subscription name]`

* --**cíl-podmínka** – zadejte cílovou podmínku pro určení, která zařízení nebo moduly budou cílem této konfigurace.Pro automatickou konfiguraci zařízení je podmínka založená na nevyhovujících značkách zařízení nebo na vyplňování požadovaných vlastností zařízení a měla by odpovídat formátu výrazu.Příkladem je `tags.environment='test'` nebo `properties.desired.devicemodel='4000x'`.Pro automatickou konfiguraci modulu je podmínka založená na nepodmíněných značkách modulu nebo v modulu, který je pro něj požadovaný.. Příkladem je `from devices.modules where tags.environment='test'` nebo `from devices.modules where properties.reported.chillerProperties.model='4000x'`.

* --**Priorita** – kladné celé číslo. V případě, že dvě nebo více konfigurací cílí na stejné zařízení nebo modul, bude platit konfigurace s nejvyšší číselnou hodnotou priority.

* --**metriky** – FilePath do dotazů na metriky. Metriky poskytují souhrnné počty různých stavů, které může zařízení nebo modul nahlásit zpět po použití konfiguračního obsahu. Můžete například vytvořit metriku pro změny nastavení čekání, metriky pro chyby a metriky pro úspěšné změny nastavení. 

## <a name="monitor-a-configuration"></a>Monitorování konfigurace

K zobrazení obsahu konfigurace použijte následující příkaz:

```azurecli
az iot hub configuration show --config-id [configuration id] \
  --hub-name [hub name]
```

* --**config-ID** – název konfigurace, která existuje ve službě IoT Hub.

* --**název-centra** – název centra IoT, ve kterém konfigurace existuje. Centrum musí být v aktuálním předplatném. Přepněte na požadované předplatné pomocí příkazu. `az account set -s [subscription name]`

Zkontrolujte konfiguraci v příkazovém okně.Vlastnost **metriky** uvádí počet pro každou metriku, která je vyhodnocována jednotlivými rozbočovači:

* **targetedCount** – systémová metrika, která určuje počet vláken zařízení nebo nevláken modulu v IoT Hub, který odpovídá podmínce cílení.

* **appliedCount** – systémová metrika určuje počet zařízení nebo modulů, u kterých byl použit cílový obsah.

* **Vaše vlastní metrika** – jakékoli metriky, které jste definovali, jsou metriky uživatelů.

Pomocí následujícího příkazu můžete zobrazit seznam ID zařízení, identifikátorů modulů nebo objektů pro každou z těchto metrik:

```azurecli
az iot hub configuration show-metric --config-id [configuration id] \
   --metric-id [metric id] --hub-name [hub name] --metric-type [type] 
```

* --**config-ID** – název nasazení, které existuje ve službě IoT Hub.

* --**metrika-ID** – název metriky, pro kterou chcete zobrazit seznam ID zařízení nebo ID modulů, například `appliedCount` .

* --**název-centra** – název centra IoT, ve kterém existuje nasazení. Centrum musí být v aktuálním předplatném. Přepněte na požadované předplatné pomocí příkazu `az account set -s [subscription name]` .

* --typ **metriky-typ** metriky může být `system` nebo `user` .  Systémové metriky jsou `targetedCount` a `appliedCount` . Všechny ostatní metriky jsou metrikami uživatele.

## <a name="modify-a-configuration"></a>Úprava konfigurace

Když upravíte konfiguraci, změny se okamžitě replikují na všechna cílová zařízení. 

Pokud aktualizujete cílovou podmínku, dojde k následujícím aktualizacím:

* Pokud se nepodmíněný objekt neshoduje se starou cílovou podmínkou, ale splňuje novou cílovou podmínku a tato konfigurace je nejvyšší prioritou pro tuto akci, pak se tato konfigurace použije. 

* Pokud aktuálně spuštěná Tato konfigurace už nesplňuje cílovou podmínku, nastavení z konfigurace se odeberou a tato akce se upraví podle další konfigurace s nejvyšší prioritou. 

* Pokud aktuálně spuštěná Tato konfigurace již nesplňuje cílovou podmínku a nesplňuje cílovou podmínku žádné jiné konfigurace, bude nastavení z konfigurace odebráno a žádné další změny nebudou provedeny na tomto vlákna. 

Pomocí následujícího příkazu aktualizujte konfiguraci:

```azurecli
az iot hub configuration update --config-id [configuration id] \
   --hub-name [hub name] --set [property1.property2='value']
```

* --**config-ID** – název konfigurace, která existuje ve službě IoT Hub.

* --**název-centra** – název centra IoT, ve kterém konfigurace existuje. Centrum musí být v aktuálním předplatném. Přepněte na požadované předplatné pomocí příkazu `az account set -s [subscription name]` .

* --**set** -aktualizuje vlastnost v konfiguraci. Můžete aktualizovat tyto vlastnosti:

    * targetCondition – například `targetCondition=tags.location.state='Oregon'`

    * popisky 

    * upřednostněn

## <a name="delete-a-configuration"></a>Odstraní konfiguraci.

Při odstranění konfigurace se všechna vlákna zařízení nebo vlákna modulu převezmou v další konfiguraci s nejvyšší prioritou. Pokud vlákna nesplňují cílovou podmínku jakékoli jiné konfigurace, nebudou použita žádná další nastavení. 

Pomocí následujícího příkazu odstraňte konfiguraci:

```azurecli
az iot hub configuration delete --config-id [configuration id] \
   --hub-name [hub name] 
```

* --**config-ID** – název konfigurace, která existuje ve službě IoT Hub.

* --**název-centra** – název centra IoT, ve kterém konfigurace existuje. Centrum musí být v aktuálním předplatném. Přepněte na požadované předplatné pomocí příkazu `az account set -s [subscription name]` .

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak nakonfigurovat a monitorovat zařízení IoT ve velkém měřítku. Pokud chcete získat další informace o správě IoT Hub Azure, postupujte podle těchto odkazů:

* [Hromadná správa identit zařízení služby IoT Hub](iot-hub-bulk-identity-mgmt.md)
* [IoT Hub metriky](iot-hub-metrics.md)
* [Monitorování operací](iot-hub-operations-monitoring.md)

Chcete-li dále prozkoumat možnosti IoT Hub, přečtěte si:

* [IoT Hub příručka pro vývojáře](iot-hub-devguide.md)
* [Nasazení AI do hraničních zařízení s použitím Azure IoT Edge](../iot-edge/quickstart-linux.md)

Pokud chcete prozkoumat použití IoT Hub Device Provisioning Service k povolení nulového dotykového zřizování za běhu, přečtěte si téma: 

* [Služba Azure IoT Hub Device Provisioning](../iot-dps/index.yml)