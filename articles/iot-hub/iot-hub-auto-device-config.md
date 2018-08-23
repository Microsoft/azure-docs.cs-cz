---
title: Konfigurovat a monitorovat zařízení IoT ve velkém měřítku pomocí služby Azure IoT Hub | Dokumentace Microsoftu
description: Konfigurace automatického zařízení Azure IoT Hub můžete přiřadit konfiguraci pro různá zařízení
author: ChrisGMsft
manager: bruz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/13/2018
ms.author: chrisgre
ms.openlocfilehash: 2edde122b109779794bb86752d69a5318edb9235
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2018
ms.locfileid: "42057478"
---
# <a name="configure-and-monitor-iot-devices-at-scale-using-the-azure-portal"></a>Konfigurovat a monitorovat zařízení IoT ve velkém měřítku pomocí webu Azure portal

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-hub-auto-device-config-selector.md)]

Správa automatického zařízení ve službě Azure IoT Hub automatizuje mnoho opakovaných a složité úlohy správy flotily nebo velké zařízení přes celého jejich životního cyklu. Se správou automatického zařízení můžete cílit na sadu zařízení na základě jejich vlastností, definují požadovanou konfiguraci a nechat pokaždé, když se do rozsahu sem přišli aktualizaci zařízení služby IoT Hub.  To se provádí pomocí automatické konfigurace, které vám také umožní shrnout dokončení a dodržování předpisů, popisovač sloučení a konflikty a zavedení konfigurace v rámci fázového přístupu.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Konfigurace pracovních automatické aktualizace sadu dvojčata zařízení pomocí požadovaných vlastností a vytváření sestav Souhrn podle dvojče zařízení ohlášené vlastnosti.  Přináší nová třída a dokument JSON s názvem *konfigurace* která má tři části:

* **Cílová podmínka** definuje rozsah dvojčata zařízení aktualizovat. Cílová podmínka je zadán jako dotaz na značky dvojčat zařízení a/nebo ohlášené vlastnosti.

* **Směrovat obsah** definuje požadované vlastnosti, které chcete přidat ani aktualizovat v dvojčat cílové zařízení. Obsah obsahuje cestu k oddílu požadované vlastnosti změnit.

* **Metriky** definovat souhrnný počet různých stavů konfigurace, jako **úspěch**, **probíhá**, a **chyba**. Vlastní metriky jsou zadané jako dotazy na zařízení ohlášené vlastnosti dvojčete.  Systémové metriky jsou výchozí metrik pro měření stavu aktualizace dvojčete, jako je počet dvojčata zařízení, na kterou cílí a počet dvojčat, které se úspěšně aktualizovaly. 

## <a name="implement-device-twins-to-configure-devices"></a>Implementace dvojčata zařízení ke konfiguraci zařízení

Konfigurace automatického zařízení vyžadují použití dvojčat zařízení k synchronizaci stavu mezi cloudem a zařízení. Odkazovat na [principy a použití dvojčat zařízení ve službě IoT Hub](iot-hub-devguide-device-twins.md) pokyny k použití dvojčat zařízení.

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

## <a name="create-a-configuration"></a>Vytvořit konfiguraci

1. V [webu Azure portal](https://portal.azure.com), přejděte do služby IoT hub. 

2. Vyberte **konfigurace zařízení IoT**.

3. Vyberte **Přidat konfiguraci**.

Existuje pět kroků pro vytvoření konfigurace. V následujících částech se provedou v rámci každé z nich. 

### <a name="name-and-label"></a>Název a popisek

1. Zadejte konfiguraci jedinečný název, který je až 128 malá písmena. Vyhněte se mezery a následující neplatné znaky: `& ^ [ ] { } \ | " < > /`.

2. Přidejte popisky pro sledování vaší konfigurace. Popisky jsou **název**, **hodnotu** dvojice, které popisují konfiguraci. Například `HostPlatform, Linux` nebo `Version, 3.0.1`.

3. Vyberte **Další** přejděte k dalšímu kroku. 

### <a name="specify-settings"></a>Zadat nastavení

Tato část určuje cíl obsahu v cílové zařízení dvojčat. Existují dva vstupy pro každou sadu nastavení. První je cesta dvojčete zařízení, který určuje cestu k části JSON v rámci požadované vlastnosti dvojčete, které budou nastaveny.  Druhým je obsah JSON, který má být vložen v této části. Například nastavte cestu dvojče zařízení a obsah takto:

![Nastavte cestu dvojče zařízení a obsahu](./media/iot-hub-auto-device-config/create-configuration-full-browser.png)

Individuální nastavení můžete také nastavit tak, že zadáte celou cestu cestu dvojče zařízení a hodnotu v obsahu bez závorek. Například nastavte cestu Dvojčete zařízení `properties.desired.chiller-water.temperature` a nastavení obsahu `66`.

Pokud dva nebo více konfigurací cílit na stejné cestě Dvojčete zařízení, budou platit obsah z konfigurace nejvyšší prioritu (Priorita je definována v kroku 4).

Pokud chcete odebrat vlastnost, zadejte hodnotu vlastnosti `null`.

Můžete přidat další nastavení tak, že vyberete **přidat nastavení Dvojčete zařízení**.

### <a name="specify-metrics-optional"></a>Zadejte metriky (volitelné)

Metriky poskytují souhrnný počet různé stavy, které zařízení může nahlásit v důsledku použití obsah konfigurace. Například můžete vytvořit metriku pro čekající změny nastavení, metriky pro chyby a metriky pro úspěšné nastavení změny.

1. Zadejte název pro **název metriky**.

2. Zadejte dotaz na **metrika kritéria**.  Dotaz je založen na zařízení ohlášené vlastnosti dvojčete.  Metrika představuje počet řádků vrácených dotazem.

Příklad:

```sql
SELECT deviceId FROM devices 
  WHERE properties.reported.chillerWaterSettings.status='pending'
```

Může obsahovat klauzuli, že konfigurace se použije, například: 

```sql
/* Include the double brackets. */
SELECT deviceId FROM devices 
  WHERE configurations.[[yourconfigname]].status='Applied'
```

### <a name="target-devices"></a>Cílová zařízení

Můžete cílit na konkrétní zařízení, které by měl dostanou tuto konfiguraci vlastnost značek z vašich dvojčata zařízení.  Můžete také směrovat zařízení tím, že zařízení ohlášené vlastnosti dvojčete.

Protože více konfigurací mohou být zaměřeny na stejném zařízení, je třeba přiřadit každou konfiguraci priorita. Pokud někdy dojde ke konfliktu, wins konfigurace s nejvyšší prioritou. 

1. Zadejte kladné celé číslo pro konfiguraci **Priority**. Nejvyšší číselnou hodnotu, je považován za nejvyšší prioritou. Pokud dvě konfigurace stejné číslo priority, ten, který byl vytvořen většina nedávno wins. 

2. Zadejte **cílová podmínka** k určení zařízení, která budou cílem s touto konfigurací. Podmínka je založen na značky dvojčat zařízení nebo ohlášené vlastnosti dvojčete zařízení a by měl odpovídat formátu výrazu. Například `tags.environment='test'` nebo `properties.reported.chillerProperties.model='4000x'`. Můžete zadat `*` cílit na všech zařízeních.

3. Vyberte **Další** přejít k poslednímu kroku.

### <a name="review-configuration"></a>Kontrola konfigurace

Zkontrolujte informace o konfiguraci a potom vyberte **odeslat**.

## <a name="monitor-a-configuration"></a>Monitorování konfigurace

Chcete-li zobrazit podrobnosti o konfiguraci a monitorování zařízení, na kterých je spuštěná, pomocí následujícího postupu:

1. V [webu Azure portal](https://portal.azure.com), přejděte do služby IoT hub. 

2. Vyberte **konfigurace zařízení IoT**.

3. Kontrola seznamu konfigurace. Pro každou konfiguraci se zobrazí následující podrobnosti:

   * **ID** – název konfigurace.

   * **Cílová podmínka** -dotazu používá k definování cílových zařízení.

   * **Priorita** – Priorita přiřazen do konfigurace.

   * **Čas vytvoření** – časové razítko od kdy byla vytvořena konfigurace. Tímto časovým razítkem se používá s přerušením ties dvě konfigurace mají stejnou prioritu. 

   * **Systémové metriky** -metriky, které se počítají ve službě IoT Hub a nelze je upravovat vývojáři. Cílem určuje počet dvojčata zařízení, které splňují cílovou podmínku. Použije zadaný počet dvojčata zařízení, které byly změněny konfigurace, která může zahrnovat částečné změny v případě, že konfigurace samostatné s vyšší prioritou také provedené změny. 

   * **Vlastní metriky** -metriky, které nebyly zadány vývojář jako dotazy dvojčete zařízení ohlášené vlastnosti.  Až pět vlastních metrik je možné definovat konfiguraci. 
   
4. Vyberte konfiguraci, kterou chcete monitorovat.  

5. Zkontrolujte podrobnosti o konfiguraci. Chcete-li zobrazit konkrétní podrobnosti o zařízení, které přijaly konfigurace můžete použít karty.

   * **Cílová podmínka** – zařízení, které splňují cílovou podmínku. 

   * **Metriky** – seznam systémové metriky a vlastní metriky.  Seznam zařízení, které se počítají pro jednotlivé metriky můžete zobrazit výběrem metriky v rozevíracím seznamu a následným výběrem **zobrazit zařízení**.

   * **Nastavení Dvojčete zařízení** – nastavení dvojčete zařízení, které jsou nastaveny podle konfigurace. 

   * **Konfigurace popisků** -páry klíč hodnota se používá k popisu konfiguraci.  Popisky mít žádný vliv na funkčnost. 

## <a name="modify-a-configuration"></a>Upravit konfigurace

Pokud upravíte konfiguraci, replikaci změn okamžitě na všechna cílová zařízení. 

Pokud aktualizujete cílovou podmínku, dojde k následující aktualizace:

* Pokud dvojčete zařízení nesplnilo původní cílovou podmínku, ale splňuje novou cílovou podmínku a tato konfigurace je nejvyšší prioritu tohoto dvojčete zařízení, tato konfigurace se použije k dvojčeti zařízení. 

* Pokud dvojčete zařízení již splňuje cílovou podmínku, nastavení z konfigurace se odeberou a dvojčeti zařízení budou upraveny konfigurací další nejvyšší prioritu. 

* Pokud aktuálně spuštěné této konfigurace už dvojčete zařízení splňuje cílovou podmínku a nesplňuje cílová podmínka všechny ostatní konfigurace, nastavení z konfigurace se odeberou a provedeny žádné další změny v dvojčeti. 

Pokud chcete upravit konfiguraci, postupujte následovně: 

1. V [webu Azure portal](https://portal.azure.com), přejděte do služby IoT hub. 

2. Vyberte **konfigurace zařízení IoT**. 

3. Vyberte konfiguraci, kterou chcete upravit. 

4. Proveďte aktualizace na následující pole: 

   * Cílová podmínka 
   * Popisky 
   * Priorita 
   * Metriky

4. Vyberte **Uložit**.

5. Postupujte podle kroků v [monitorování konfigurace](#monitor-a-configuration) sledovat změny zavádět. 

## <a name="delete-a-configuration"></a>Odstranění konfigurace

Při konfiguraci odstranit, všechny dvojčata zařízení převezmou konfigurace jejich další nejvyšší prioritu. Dvojčata zařízení nesplňují cílovou podmínku ostatní konfigurace, se použijí žádná nastavení. 

1. V [webu Azure portal](https://portal.azure.com) přejděte do služby IoT hub. 

2. Vyberte **konfigurace zařízení IoT**. 

3. Vyberte konfiguraci, kterou chcete odstranit pomocí zaškrtávacího políčka. 

4. Vyberte **Odstranit**.

5. Výzva zobrazí výzvu k potvrzení.

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
