---
title: Automatická správa zařízení ve velkém měřítku s využitím Azure IoT Hub | Microsoft Docs
description: Použití automatických konfigurací Azure IoT Hub ke správě více zařízení a modulů IoT
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: robinsh
ms.custom:
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: e30daa3f81ed5dcae1323e721bf85cfed8fa9614
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2020
ms.locfileid: "92147806"
---
# <a name="automatic-iot-device-and-module-management-using-the-azure-portal"></a>Automatická správa zařízení a modulů IoT pomocí webu Azure Portal

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-hub-auto-device-config-selector.md)]

Automatická správa zařízení v Azure IoT Hub automatizuje mnoho opakujících se a složitých úloh při správě rozsáhlých loďstva zařízení. Díky automatické správě zařízení můžete cílit na sadu zařízení na základě jejich vlastností, definovat požadovanou konfiguraci a potom nechat zařízení IoT Hub aktualizovat, když vstoupí do rozsahu. Tato aktualizace se provádí pomocí _automatické konfigurace zařízení_ nebo _automatické konfigurace modulu_, která umožňuje shrnout dokončování a dodržování předpisů, zpracovávat sloučení a konflikty a nastavovat konfigurace v rámci postupného přístupu.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Automatická správa zařízení funguje tak, že aktualizuje sadu dvojitých vláken zařízení nebo nevláken modulu s požadovanými vlastnostmi a ohlásí souhrn, který vychází z nefunkčních hlášených vlastností.  Zavádí nový dokument třídy a JSON s názvem *Konfigurace* , která má tři části:

* **Podmínka cíle** definuje rozsah vláken zařízení nebo nepodmíněných vláken modulu, které se mají aktualizovat. Cílová podmínka je určena jako dotaz na zdvojených značkách nebo hlášených vlastnostech.

* **Cílový obsah** definuje požadované vlastnosti, které se mají přidat nebo aktualizovat v cílovém zařízení vlákna nebo vlákna v modulu. Obsah zahrnuje cestu k oddílu požadovaných vlastností, které mají být změněny.

* **Metriky** definují Souhrnné počty různých stavů **konfigurace, například** **úspěch**, probíhá a **Chyba**. Vlastní metriky jsou zadány jako dotazy na nedokončené hlášené vlastnosti.  Systémové metriky jsou výchozí metriky, které měří stav s dvojitou aktualizací, jako je třeba počet nezpracovaných vláken a počet úspěšně aktualizovaných vláken.

Automatické konfigurace se spouští poprvé po vytvoření konfigurace a pak v intervalu pěti minut. Dotazy na metriky se spouštějí při každém spuštění automatické konfigurace.

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

## <a name="create-a-configuration"></a>Vytvořit konfiguraci

1. V [Azure Portal](https://portal.azure.com)přejdete do služby IoT Hub. 

2. Vyberte **Konfigurace zařízení IoT**.

3. Vyberte **Přidat konfiguraci zařízení** nebo **Přidat konfiguraci modulu**.

   ![Přidat konfiguraci zařízení nebo konfiguraci modulu](./media/iot-hub-automatic-device-management/create-automatic-configuration.png)

Existuje pět kroků pro vytvoření konfigurace. V následujících částech si projdete každé z nich. 

### <a name="name-and-label"></a>Název a popisek

1. Poskytněte konfiguraci jedinečný název, který bude obsahovat až 128 malých písmen. Vyhněte se mezerám a následujícími neplatnými znaky: `& ^ [ ] { } \ | " < > /` .

2. Přidejte popisky, které vám pomůžou sledovat vaše konfigurace. Popisky jsou **názvy**, páry **hodnot** , které popisují vaši konfiguraci. Příkladem je `HostPlatform, Linux` nebo `Version, 3.0.1`.

3. Kliknutím na tlačítko **Další** přejděte k dalšímu kroku. 

### <a name="specify-settings"></a>Zadat nastavení

Tato část definuje obsah, který se má nastavit v nezpracovaných vlákenách zařízení nebo modulu. Pro každou sadu nastavení existují dva vstupy. První je dvojitá cesta, což je cesta k oddílu JSON v rámci vláken požadovaných vlastností, které budou nastaveny.  Druhá je obsah JSON, který se má vložit do této části. 

Můžete například nastavit dvojitou cestu k `properties.desired.chiller-water` a pak zadat následující obsah JSON: 

```json
{
  "temperature": 66,
  "pressure": 28
}
```

![Nastavení zdvojené cesty a obsahu](./media/iot-hub-automatic-device-management/module-config-twin-settings.png)


Jednotlivá nastavení můžete nastavit také zadáním celé cesty a zadáním hodnoty bez závorek. Například s nazdvojenou cestou `properties.desired.chiller-water.temperature` nastavte obsah na `66` . Pak vytvořte nové nastavení s dvojím nastavením pro vlastnost tlak. 

Pokud dvě nebo více konfigurací cílí na stejnou cestu, bude platit obsah z konfigurace s nejvyšší prioritou (Priorita je definována v kroku 4).

Chcete-li odebrat existující vlastnost, zadejte hodnotu vlastnosti `null` .

Další nastavení můžete přidat tak, že vyberete možnost **Přidat zdvojené zařízení** nebo **Přidat nastavení s dvojitým modulem**.

### <a name="specify-metrics-optional"></a>Zadat metriky (volitelné)

Metriky poskytují souhrnné počty různých stavů, které může zařízení nebo modul nahlásit zpět po použití konfiguračního obsahu. Můžete například vytvořit metriku pro změny nastavení čekání, metriky pro chyby a metriky pro úspěšné změny nastavení.

Každá konfigurace může mít až pět vlastních metrik. 

1. Zadejte název **metriky**.

2. Zadejte dotaz na **kritéria metriky**.  Dotaz vychází z nezaznamenaných vlastností zařízení.  Metrika představuje počet řádků vrácených dotazem.

Například:

```sql
SELECT deviceId FROM devices 
  WHERE properties.reported.chillerWaterSettings.status='pending'
```

Můžete zahrnout klauzuli, kterou konfigurace použila, například: 

```sql
/* Include the double brackets. */
SELECT deviceId FROM devices 
  WHERE configurations.[[yourconfigname]].status='Applied'
```

Pokud vytváříte metriku pro vytváření sestav v konfigurovaných modulech, vyberte `moduleId` z `devices.modules` . Například:

```sql
SELECT deviceId, moduleId FROM devices.modules
  WHERE properties.reported.lastDesiredStatus.code = 200
```

### <a name="target-devices"></a>Cílová zařízení

Pomocí vlastnosti značky z vašich vláken můžete cílit na konkrétní zařízení nebo moduly, které by měly tuto konfiguraci přijmout. Můžete také cílit na nedokončené hlášené vlastnosti.

Automatické konfigurace zařízení můžou směrovat jenom nepřesné značky zařízení a konfigurace automatických modulů můžou být jenom cílové moduly s dvojitými značkami. 

Vzhledem k tomu, že více konfigurací může cílit na stejné zařízení nebo modul, každá konfigurace potřebuje číslo priority. Pokud dojde ke konfliktu, konfigurace s nejvyšší prioritou je služba WINS. 

1. Jako **prioritu**konfigurace zadejte kladné celé číslo. Nejvyšší číselná hodnota je považována za nejvyšší prioritu. Pokud mají dvě konfigurace stejné číslo priority, ten, který byl vytvořen naposledy službou WINS. 

2. Zadejte **cílovou podmínku** pro určení, která zařízení nebo moduly budou cílem této konfigurace. Podmínka je založena na nepodmíněných značkách nebo ve dvojitě hlášených vlastnostech a měla by odpovídat formátu výrazu. 

   Pro automatickou konfiguraci zařízení můžete určit, že chcete cílit jenom na značku, nebo na vlastnost hlášeno. Příkladem je `tags.environment='test'` nebo `properties.reported.chillerProperties.model='4000x'`. Můžete nastavit `*` cílení na všechna zařízení. 
   
   Pro automatickou konfiguraci modulu použijte dotaz k zadání značek nebo hlášených vlastností z modulů registrovaných ve službě IoT Hub. Příkladem je `from devices.modules where tags.environment='test'` nebo `from devices.modules where properties.reported.chillerProperties.model='4000x'`. Zástupný znak nelze použít pro cílení na všechny moduly. 

3. Kliknutím na tlačítko **Další** přejdete k poslednímu kroku.

### <a name="review-configuration"></a>Kontrola konfigurace

Zkontrolujte informace o konfiguraci a pak vyberte **Odeslat**.

## <a name="monitor-a-configuration"></a>Monitorování konfigurace

Pokud chcete zobrazit podrobnosti o konfiguraci a monitorovat zařízení, která ho používají, použijte následující postup:

1. V [Azure Portal](https://portal.azure.com)přejdete do služby IoT Hub. 

2. Vyberte **Konfigurace zařízení IoT**.

3. Zkontrolujte seznam konfigurací. Pro každou konfiguraci můžete zobrazit následující podrobnosti:

   * **ID** – název konfigurace.

   * **Podmínka cíle** – dotaz, který slouží k definování cílových zařízení nebo modulů.

   * **Priorita** – číslo priority přiřazené ke konfiguraci.

   * **Čas vytvoření** – časové razítko z doby, kdy byla konfigurace vytvořena. Toto časové razítko se používá k přerušení vztahů, pokud mají dvě konfigurace stejnou prioritu. 

   * **Systémové metriky** – metriky, které jsou vypočítány IoT Hub a nemohou být upravovány vývojáři. Cíl Určuje počet vláken zařízení, která odpovídají cílové podmínce. Použije zadaný počet nevláken zařízení, která byla upravena konfigurací, což může zahrnovat částečné změny v případě, že se změnila samostatná konfigurace s vyšší prioritou. 

   * **Vlastní metriky** – metriky, které vývojář zadal jako dotazy pro nedokončené hlášené vlastnosti.  Pro každou konfiguraci lze definovat až pět vlastních metrik. 
   
4. Vyberte konfiguraci, kterou chcete monitorovat.  

5. Zkontrolujte podrobnosti o konfiguraci. Pomocí karet můžete zobrazit konkrétní podrobnosti o zařízeních, která konfiguraci přijala.

   * **Podmínka cíle** – zařízení nebo moduly, které se shodují s cílovou podmínkou. 

   * **Metriky** – seznam systémových metrik a vlastní metriky.  Seznam zařízení nebo modulů, které se počítají pro jednotlivé metriky, můžete zobrazit tak, že vyberete metriku v rozevíracím seznamu a pak vyberete **Zobrazit zařízení** nebo **Zobrazit moduly**.

   * Nastavení s dvojitou podmnožinou **zařízení** nebo **modulu** – nastavení s dvojitou čárkou – nastavení, které je nastavené konfigurací. 

   * **Popisky konfigurace** – páry klíč-hodnota používané k popisu konfigurace.  Popisky nemají žádný vliv na funkčnost. 

## <a name="modify-a-configuration"></a>Úprava konfigurace

Když upravíte konfiguraci, změny se okamžitě replikují na všechna cílová zařízení nebo moduly. 

Pokud aktualizujete cílovou podmínku, dojde k následujícím aktualizacím:

* Pokud se nepodmíněný objekt neshoduje se starou cílovou podmínkou, ale splňuje novou cílovou podmínku a tato konfigurace je nejvyšší prioritou pro tuto akci, pak se tato konfigurace použije. 

* Pokud aktuálně spuštěná Tato konfigurace už nesplňuje cílovou podmínku, nastavení z konfigurace se odeberou a tato akce se upraví podle další konfigurace s nejvyšší prioritou. 

* Pokud aktuálně spuštěná Tato konfigurace již nesplňuje cílovou podmínku a nesplňuje cílovou podmínku žádné jiné konfigurace, bude nastavení z konfigurace odebráno a žádné další změny nebudou provedeny na tomto vlákna. 

Chcete-li upravit konfiguraci, použijte následující postup: 

1. V [Azure Portal](https://portal.azure.com)přejdete do služby IoT Hub. 

2. Vyberte **Konfigurace zařízení IoT**. 

3. Vyberte konfiguraci, kterou chcete upravit. 

4. Proveďte aktualizace následujících polí: 

   * Cílová podmínka 
   * Popisky 
   * Priorita 
   * Metriky

4. Vyberte **Uložit**.

5. Pokud chcete sledovat změny, postupujte podle kroků v části [monitorování konfigurace](#monitor-a-configuration) . 

## <a name="delete-a-configuration"></a>Odstraní konfiguraci.

Když konfiguraci odstraníte, všechna vlákna zařízení převezmou svou další konfiguraci s nejvyšší prioritou. Pokud zařízení nesplňuje cílovou podmínku jakékoli jiné konfigurace, nebudou použita žádná další nastavení. 

1. V [Azure Portal](https://portal.azure.com)přejdete do služby IoT Hub. 

2. Vyberte **Konfigurace zařízení IoT**. 

3. Pomocí zaškrtávacího políčka vyberte konfiguraci, kterou chcete odstranit. 

4. Vyberte **Odstranit**.

5. Výzva vás vyzve k potvrzení.

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