---
title: Konfigurace a monitorování zařízení IoT ve velkém měřítku službou Azure IoT Hub | Microsoft Docs
description: Na konfigurací automatické zařízení Azure IoT Hub můžete přiřadit konfigurace na více zařízení
author: ChrisGMsft
manager: bruz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/13/2018
ms.author: chrisgre
ms.openlocfilehash: 29a56e212f842e8f4243eca7fc865175fd275a39
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37030763"
---
# <a name="configure-and-monitor-iot-devices-at-scale-using-the-azure-portal"></a>Konfigurace a monitorování zařízení IoT škálované pomocí portálu Azure

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-hub-auto-device-config-selector.md)]

Správa automatického zařízení v Azure IoT Hub automatizuje řadu opakovaných a složitější úlohy správy loďstev velké zařízení přes celého jejich životních cyklů. Se správou automatického zařízení můžete cílí na skupiny zařízení, na základě jejich vlastností, zadejte požadované konfigurace a nechat IoT Hub, aktualizujte si zařízení vždy, když se do oboru.  To se provádí pomocí konfigurace automatického zařízení, která vám také umožní shrnout doplňování a dodržování předpisů, slučování popisovač a je v konfliktu a zavedení konfigurace v rámci fázového přístupu.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Konfigurace pracovních zařízení automatické aktualizace sadu dvojčata zařízení pomocí požadované vlastnosti a vytváření sestav Souhrn podle dvojče zařízení hlášené vlastnosti.  Zavádí nové třídy a dokument JSON s názvem _konfigurace_ jehož tří částí:

* **Cíle podmínku** definuje rozsah dvojčata zařízení aktualizovat. Cílovou podmínku je zadán jako dotaz na zařízení twin značky nebo hlášené vlastnosti.

* **Směrovat obsah** definuje požadované vlastnosti, které chcete přidat nebo aktualizovat v dvojčata cílové zařízení. Obsah obsahuje cestu k části požadované vlastnosti chcete změnit.

* **Metriky** definovat souhrnné počty různé stavy konfigurace, jako **úspěch**, **probíhá**, a **chyba**. Vlastní metriky v dotazech na zařízení zadány twin hlášené vlastnosti.  Metriky systému jsou výchozích metrik, které měří stav aktualizace twin, například počet dvojčata zařízení, které jsou cíleny a počet dvojčata, které byly úspěšně aktualizovány. 

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

## <a name="create-a-configuration"></a>Vytvoření konfigurace

1. V [portál Azure][lnk-portal], přejděte do služby IoT hub. 
1. Vyberte **konfigurace zařízení IoT**.
1. Vyberte **Přidat konfiguraci**.

Existují pět kroky pro vytvoření konfigurace. V následujících částech provede každé z nich. 

### <a name="step-1-name-and-label"></a>Krok 1: Název a popisku

1. Zadejte konfiguraci jedinečný název, který je až 128 malá písmena. Vyhněte se mezery a následující neplatné znaky: `& ^ [ ] { } \ | " < > /`.
1. Přidejte popisky, abyste mohli snadněji sledovat vaše konfigurace. Popisky jsou **název**, **hodnotu** páry, které popisují konfiguraci. Například `HostPlatform, Linux` nebo `Version, 3.0.1`.
1. Vyberte **Další** přesunout do kroku 2. 

### <a name="step-2-specify-settings"></a>Krok 2: Zadejte nastavení

V této části určuje cíl obsah ve dvojčata cílové zařízení. Existují dva vstupy pro každou sadu nastavení. První je cesta twin zařízení, která je cesta k části JSON v rámci twin požadovaných vlastností, které budou nastaveny.  Druhá je obsah JSON, který má být vložen v této části. Například nastavte cestu Twin zařízení a obsah takto:

![Nastavte cestu Twin zařízení a obsahu](./media/iot-hub-auto-device-config/create-configuration-full-browser.png)

Můžete také nastavit jednotlivá nastavení zadáním celou cestu v zařízení Twin cesta a hodnota obsah do bez závorek. Například nastavit cestu Twin zařízení `properties.desired.chiller-water.temperature` a nastavte obsah na: `66`

Pokud dva nebo více konfigurací cíle se stejnou cestou Twin zařízení, uplatní se obsah z nejvyšší prioritou konfigurace (s prioritou je definovaný v kroku 4).

Pokud chcete odebrat vlastnost, zadejte hodnotu vlastnosti, která `null`.

Můžete přidat další nastavení výběrem **přidat dvojici nastavení zařízení**

### <a name="step-3-specify-metrics-optional"></a>Krok 3: Zadejte metriky (volitelné)

Metriky poskytují souhrnné počty různé stavy, které zařízení může hlásit zpět v důsledku použití obsah konfigurace. Můžete například vytvořit metriky pro čekající změny nastavení metriky pro chyby a metriky pro změny nastavení úspěšné.

1. Zadejte název **název metriky**
1. Zadejte dotaz na **metrika kritéria**.  Dotaz je založen na zařízení twin hlášené vlastnosti.  Metrika představuje počet řádků vrácených dotazem.

Příklad: `SELECT deviceId FROM devices WHERE properties.reported.chillerWaterSettings.status='pending'`

Můžete zahrnout klauzuli, že konfigurace byla použita, například: `SELECT deviceId FROM devices WHERE configurations.[[yourconfigname]].status='Applied'` včetně hranatých závorek.


### <a name="step-4-target-devices"></a>Krok 4: Cílová zařízení

Použijte vlastnost značky z vaší dvojčata zařízení pro konkrétní zařízení, které přijmou tato konfigurace.  Můžete také vybrat zařízení podle zařízení twin hlášené vlastnosti.

Vzhledem k tomu, že do stejného zařízení mohou být zaměřeny na různé konfigurace, je třeba přiřadit každé konfiguraci číslem priority. Pokud někdy dojde ke konfliktu, služby wins konfigurace s nejvyšší prioritou. 

1. Zadejte kladné celé číslo pro konfiguraci **s prioritou**. Nejvyšší číselnou hodnotu považuje za nejvyšší prioritou. Pokud dvě konfigurace stejné číslo priority, ten, který byl vytvořen většina nedávno wins. 
1. Zadejte **cíle podmínku** k určení zařízení, která budou cílem s touto konfigurací. Je založena na zařízení twin značky nebo dvojče zařízení hlášené vlastnosti a musí odpovídat formátu výraz. Například `tags.environment='test'` nebo `properties.reported.chillerProperties.model='4000x'`. Můžete zadat `*` pro všechna zařízení.
1. Vyberte **Další** přechod poslední krok.

### <a name="step-5-review-configuration"></a>Krok 5: Kontrola konfigurace

Zkontrolujte informace o konfiguraci a pak vyberte **odeslání**.

## <a name="monitor-a-configuration"></a>Monitorování konfigurace

Chcete-li zobrazit podrobnosti o konfiguraci a monitorování zařízení se systémem, použijte následující postup:

1. V [portál Azure][lnk-portal], přejděte do služby IoT hub. 
1. Vyberte **konfigurace zařízení IoT**.
2. Zkontrolujte seznam konfigurace. Pro každou konfiguraci můžete zobrazit následující podrobnosti:
   * **ID** -název konfigurace.
   * **Cíl podmínku** -dotaz, použít k definování cílová zařízení.
   * **Priorita** -číslo priority přiřazené ke konfiguraci.
   * **Čas vytvoření** -časové razítko z vytvoření konfigurace. Toto časové razítko se používá k přerušení ties při dvě konfigurace mají stejnou prioritu. 
   * **Metriky systému** -metriky, které jsou vypočítávány službou IoT Hub a nemůže být upravena vývojáři. Cílem určuje počet dvojčata zařízení, které odpovídají cílovou podmínku. Použije zadaný počet dvojčata zařízení, které byly upraveny konfigurace, která může zahrnovat částečné úpravy v případě, že konfigurace samostatné s vyšší prioritou také provedené změny. 
   * **Vlastní metriky** -metriky, které bylo zadáno vývojáře jako dotazy pro dvojče zařízení hlášené vlastnosti.  Pro konfiguraci je možné definovat maximálně pět vlastní metriky. 
   
1. Vyberte konfiguraci, kterou chcete monitorovat.  
1. Zkontrolujte podrobnosti o konfiguraci. Chcete-li zobrazit konkrétní podrobnosti o zařízení, které přijaly konfigurace můžete použít karty: 
   * **Cíl podmínku** – zařízení, které odpovídají cílovou podmínku. 
   * **Metriky** – seznam systému metriky a vlastní metriky.  Seznam zařízení, která se počítají pro jednotlivé metriky můžete zobrazit výběrem metriky v rozevíracím seznamu a potom výběrem **zobrazit zařízení**.
   * **Nastavení zařízení Twin** -twin nastavení zařízení, které jsou nastavené konfigurace. 
   * **Konfigurace popisky** -páry klíč hodnota, které používají k popisu konfigurace.  Popisky nemají žádný vliv na funkčnost. 

## <a name="modify-a-configuration"></a>Upravte konfiguraci

Pokud upravíte konfiguraci, změny okamžitě replikovat do všech cílových zařízení. 

Pokud aktualizujete cílovou podmínku, provedou se následující aktualizace:
* Pokud dvojče zařízení nesplnilo původní cílovou podmínku, ale splňuje novou cílovou podmínku a tato konfigurace je nejvyšší prioritou pro tento dvojče zařízení, tato konfigurace se použijí pro dvojče zařízení. 
* Pokud dvojče zařízení už splňuje cílovou podmínku, budou odstraněna nastavení z konfigurace a dvojče zařízení se změní další konfigurace nejvyšší prioritou. 
* Pokud dvojče zařízení aktuálně spuštěných této konfigurace už splňuje cílovou podmínku a nesplňuje cíl podmínku všechny ostatní konfigurace, nastavení z konfigurace se odeberou a na twin budou provedeny žádné další změny. 

Chcete-li upravit konfiguraci, použijte následující kroky: 

1. V [portál Azure][lnk-portal], přejděte do služby IoT hub. 
1. Vyberte **konfigurace zařízení IoT**. 
2. Vyberte konfiguraci, kterou chcete upravit. 
3. Proveďte aktualizace následující pole: 
   * Cílovou podmínku 
   * Popisky 
   * Priorita 
   * Metriky
4. Vyberte **Uložit**.
5. Postupujte podle kroků v [monitorování konfigurace] [ukotvení monitor] můžete sledovat změny zavedení. 

## <a name="delete-a-configuration"></a>Odstranění konfigurace

Při odstranění konfigurace žádné dvojčata zařízení trvat na jejich další konfigurace nejvyšší prioritou. Pokud dvojčata zařízení nesplňují podmínku cíl jakoukoli jinou konfiguraci, se neuplatní žádné další nastavení. 

1. V [portál Azure][lnk-portal], přejděte do služby IoT hub. 
1. Vyberte **konfigurace zařízení IoT**. 
2. Vyberte konfiguraci, kterou chcete odstranit, použijte zaškrtávací políčko. 
3. Vyberte **Odstranit**.
4. Na řádku požádá o potvrzení.

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
