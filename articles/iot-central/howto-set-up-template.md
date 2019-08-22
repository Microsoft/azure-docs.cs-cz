---
title: Nastavení šablony zařízení v aplikaci Azure IoT Central | Microsoft Docs
description: Naučte se, jak nastavit šablonu zařízení pomocí měření, nastavení, vlastností, pravidel a řídicího panelu.
author: viv-liu
ms.author: viviali
ms.date: 06/19/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 63c0a04a6d18d6af850b1492d2efa9df9aa65219
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877369"
---
# <a name="set-up-a-device-template"></a>Nastavení šablony zařízení

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

Šablona zařízení je podrobný plán, který definuje charakteristiky a chování typu zařízení, které se připojuje k aplikaci Azure IoT Central.

Tvůrce může například vytvořit šablonu zařízení pro připojený ventilátor, která má následující vlastnosti:

- Měření telemetrie teploty
- Měření polohy
- Měření událostí chyb motoru ventilátoru
- Měření provozního stavu ventilátoru
- Nastavení rychlosti ventilátoru
- Pravidla odesílající výstrahy
- Řídicí panel, který vám poskytne celkový přehled o zařízení

Pomocí této šablony zařízení může operátor vytvořit a propojit reálné ventilátory s názvy, jako jsou **ventilátory 1** a **ventilátor-2**. Všechny tyto ventilátory mají měření, nastavení, vlastnosti, pravidla a řídicí panel, který mohou uživatelé vaší aplikace monitorovat a spravovat.

> [!NOTE]
> Šablony zařízení můžou vytvářet, upravovat a odstraňovat jenom tvůrci a správci. Každý uživatel může na stránce **Device Explorer** vytvořit zařízení z existujících šablon zařízení.

## <a name="create-a-device-template"></a>Vytvoření šablony zařízení

1. Přejděte na stránku **šablony zařízení** .

2. Pokud chcete vytvořit šablonu, začněte tím, že vyberete **+ Nová**.

3. Pokud chcete rychle začít, vyberte si z existujících předem připravených šablon. V opačném případě vyberte možnost **vlastní**, zadejte název a kliknutím na tlačítko **vytvořit** Sestavte vlastní šablonu od začátku.

   ![Knihovna šablon zařízení](./media/howto-set-up-template/newtemplate.png)

4. Když vytvoříte vlastní šablonu, zobrazí se stránka s podrobnostmi o **zařízení** pro novou šablonu zařízení. Při vytvoření šablony zařízení IoT Central automaticky vytvoří simulované zařízení. Simulované zařízení umožňuje testovat chování aplikace před připojením reálného zařízení.

V následujících částech jsou popsány jednotlivé karty na stránce **šablony zařízení** .

## <a name="measurements"></a>Měření

Měření jsou data, která pocházejí z vašeho zařízení. Do šablony zařízení můžete přidat několik měření, která budou odpovídat možnostem vašeho zařízení.

- Měření **telemetrie** jsou Numerické datové body, které zařízení shromažďuje v průběhu času. Jsou reprezentovány jako průběžný datový proud. Příkladem je teplota.
- Měření **událostí** jsou data v časovém okamžiku, která v zařízení představují něco z významnosti. Úroveň závažnosti představuje důležitost události. Příkladem může být chyba ventilátoru.
- Měření **stavu** představuje stav zařízení nebo jeho součástí v časovém intervalu. Například režim ventilátoru lze definovat jako **operační** a zastavené jako dva možné stavy.
- Měření **polohy** jsou souřadnice zeměpisné délky a šířky zařízení v časovém intervalu. Například ventilátor lze přesunout z jednoho umístění do druhého.

### <a name="create-a-telemetry-measurement"></a>Vytvoření měření telemetrie

Pokud chcete přidat nové měření telemetrie, vyberte **+ nové měření**, jako typ měření zvolte **telemetrie** a zadejte podrobnosti formuláře.

> [!NOTE]
> Názvy polí v šabloně zařízení musí odpovídat názvům vlastností v odpovídajícím kódu zařízení, aby se měření telemetrie zobrazovalo v aplikaci při připojení reálného zařízení. Stejný postup proveďte při konfiguraci nastavení, vlastností zařízení a příkazů během definování šablony zařízení v následujících oddílech.

Můžete například přidat nové měření telemetrie na teplotu:

| Zobrazovaný název        | Název pole    |  Jednotky    | Minimum   |Maximum|
| --------------------| ------------- |-----------|-------|---|
| Teplota         | názvem          |  degC     |  0    |100|

![Formulář pro vytvoření telemetrie s podrobnostmi pro měření teploty](./media/howto-set-up-template/measurementsform.png)

Po výběru **Uložit**se v seznamu měření zobrazí měření **teploty** . V krátké době vidíte vizualizaci dat o teplotě z simulovaného zařízení.

Při zobrazování telemetrie si můžete vybrat z následujících možností agregace: Average, minim, maximum, součet a počet. **Průměr** je vybrán jako výchozí agregace v grafu.

> [!NOTE]
> Datový typ měření telemetrie je číslo s plovoucí desetinnou čárkou.

### <a name="create-an-event-measurement"></a>Vytvoření měření události

Chcete-li přidat novou měření události, vyberte **+ Nová měření** a jako typ měření vyberte **událost** . Do formuláře **vytvořit událost** zadejte podrobnosti.

Zadejte **Zobrazovaný název**, **název pole**a podrobnosti o **závažnosti** události. Můžete si vybrat ze tří dostupných úrovní závažnosti: **Chyba**, **Upozornění**a **informace**.

Můžete například přidat novou událost **chyby motoru ventilátoru** .

| Zobrazovaný název        | Název pole    |  Výchozí závažnost |
| --------------------| ------------- |-----------|
| Chyba motoru ventilátoru     | fanmotorerror |  Chyba    |

![Formulář vytvořit událost s podrobnostmi o události ventilátoru](./media/howto-set-up-template/eventmeasurementsform.png)

Po výběru **Uložit**se v seznamu měření zobrazí měření **chyb motoru ventilátoru** . V krátké době se zobrazí vizualizace dat událostí z simulovaného zařízení.

Chcete-li zobrazit další podrobnosti o události, vyberte ikonu události v grafu:

![Podrobnosti o události "Chyba motoru ventilátoru"](./media/howto-set-up-template/eventmeasurementsdetail.png)

> [!NOTE]
> Datový typ měření události je řetězec.

### <a name="create-a-state-measurement"></a>Vytvoření měření stavu

Chcete-li přidat nové měření stavu, vyberte tlačítko **+ nové měření** a jako typ míry vyberte možnost **stav** . Do formuláře **vytvořit stav** zadejte podrobnosti.

Zadejte podrobnosti pro **zobrazované jméno**, **název pole**a **hodnoty** stavu. Každá hodnota může mít také zobrazovaný název, který se použije, když se hodnota zobrazí v grafech a tabulkách.

Můžete například přidat nový stav **režimu ventilátoru** , který má dvě možné hodnoty, které může zařízení odeslat, provozovat a **zastavit**.

| Zobrazovaný název | Název pole    |  Hodnota 1   | Zobrazovaný název | Hodnota 2    |Zobrazovaný název  |
| -------------| ------------- |----------- | -------------| -----------| -------------|
| Režim ventilátoru     | fanmode       |  1         | Funguje    |     0      | Zastaveno      |

![Formulář "upravit stav" s podrobnostmi pro režim ventilátoru](./media/howto-set-up-template/statemeasurementsform.png)

Po výběru **Uložit**se v seznamu měření zobrazí měření stavu **režimu ventilátoru** . V krátké době vidíte vizualizaci stavových dat ze simulovaného zařízení.

Pokud zařízení odesílá příliš mnoho datových bodů v krátké době, zobrazí se měření stavu s jiným vizuálů. Výběrem grafu zobrazíte všechny datové body v tomto časovém období v chronologickém pořadí. Můžete také zúžit časový rozsah, aby se zobrazila měření zobrazená v grafu.

> [!NOTE]
> Datový typ měření stavu je řetězec.

### <a name="create-a-location-measurement"></a>Vytvořit měření polohy

Chcete-li přidat nové měření umístění, vyberte **+ nové měření**, zvolte **umístění** jako typ měření a zadejte podrobnosti formuláře **vytvořit měření** .

Můžete například přidat nové měření telemetrie umístění:

| Zobrazovaný název        | Název pole    |
| --------------------| ------------- |
| Umístění assetu      |  assetloc     |

![Formulář pro vytvoření umístění s podrobnostmi pro měření polohy](./media/howto-set-up-template/locationmeasurementsform.png)

Po výběru **Uložit**se v seznamu měření zobrazí měření **umístění** . V krátké době se zobrazí vizualizace dat umístění z simulovaného zařízení.

Při zobrazování umístění můžete vybrat z následujících možností: nejnovější umístění a historie umístění. **Historie umístění** se aplikuje jenom na vybraný časový rozsah.

Datový typ měření umístění je objekt, který obsahuje zeměpisnou délku, zeměpisnou šířku a volitelnou nadmořskou výšku. Následující fragment kódu ukazuje strukturu JavaScriptu:

```javascript
assetloc: {
  lon: floating point number,
  lat: floating point number,
  alt?: floating point number
}
```

Po připojení reálného zařízení se umístění, které jste přidali jako měření, aktualizuje s hodnotou odeslanou zařízením. Po nakonfigurování měření polohy můžete [Přidat mapu, která bude vizualizovat umístění na řídicím panelu zařízení](#add-a-location-measurement-in-the-dashboard).

## <a name="settings"></a>Nastavení

Nastavení řídí zařízení. Umožňují operátorům poskytovat vstup do zařízení. K šabloně zařízení, která se zobrazí jako dlaždice na kartě **Nastavení** , můžete přidat více nastavení, která se použijí pro operátory, které se mají použít. Můžete přidat mnoho typů nastavení: číslo, text, datum, přepínač a popisek oddílu.

Nastavení může být v jednom ze tří stavů. Zařízení hlásí tyto stavy.

- **Synchronizovaný**: Zařízení se změnilo tak, aby odráželo hodnotu nastavení.

- **Čeká na vyřízení**: Zařízení se v současné době mění na hodnotu nastavení.

- **Chyba:** Zařízení vrátilo chybu.

Můžete například přidat nové nastavení rychlosti ventilátoru výběrem **Nastavení** a zadáním v nastavení nové **číslo** :

| Zobrazovaný název  | Název pole    |  Jednotky  | Desetinných míst |Počáteční|
| --------------| ------------- |---------| ---------|---- |
| Rychlost ventilátoru     | fanSpeed      | /MIN     | 2        | 0   |

![Formulář "konfigurovat číslo" s podrobnostmi pro nastavení rychlosti](./media/howto-set-up-template/settingsform.png)

Po výběru **Uložit**se nastavení **rychlosti ventilátoru** zobrazí jako dlaždice. Operátor může použít nastavení na stránce **Device Explorer** ke změně rychlosti ventilátoru zařízení.

## <a name="properties"></a>Vlastnosti

Vlastnosti jsou metadata, která jsou přidružená k zařízení, jako je pevné umístění zařízení a sériové číslo. Přidejte do šablony zařízení více vlastností, které se zobrazí jako dlaždice na kartě **vlastnosti** . Vlastnost má typ, jako je číslo, text, datum, přepínač, vlastnost zařízení, popisek nebo pevné umístění. Operátor určuje hodnoty vlastností při vytváření zařízení a může kdykoli upravovat tyto hodnoty. Vlastnosti zařízení jsou jen pro čtení a odesílají se ze zařízení do aplikace. Operátor nemůže změnit vlastnosti zařízení. Po připojení reálného zařízení se v aplikaci aktualizují dlaždice vlastností zařízení.

Vlastnosti se dělí do dvou kategorií:

- _Vlastnosti zařízení_ , které zařízení hlásí do aplikace IoT Central. Vlastnosti zařízení jsou hodnoty jen pro čtení hlášené zařízením a v aplikaci se aktualizují při připojení reálného zařízení.
- _Vlastnosti aplikace_ , které jsou uloženy v aplikaci a mohou být upravovány operátorem. Vlastnosti aplikace jsou uloženy pouze v aplikaci a nikdy se nezobrazuje v zařízení.

Například můžete přidat poslední datum služby pro zařízení jako vlastnost nového **data** (vlastnost aplikace) na kartě **vlastnosti** :

| Zobrazovaný název  | Název pole | Počáteční hodnota   |
| --------------| -----------|-----------------|
| Last serviced (Poslední údržba)      | lastServiced        | 01/29/2019     |

![Formulář konfigurace poslední služby na kartě Vlastnosti](./media/howto-set-up-template/propertiesform.png)

Po výběru **Uložit**se jako dlaždice zobrazí poslední datum a čas doručení zařízení.

Po vytvoření dlaždice můžete změnit hodnotu vlastnosti aplikace v **Device Explorer**.

### <a name="create-a-location-property"></a>Vytvoření vlastnosti umístění

Data o poloze v Azure můžete poskytnout geografickým kontextům IoT Central a namapovat libovolné souřadnice zeměpisné šířky a délky nebo ulici. Azure Maps tuto funkci povoluje v IoT Central.

Můžete přidat dva typy vlastností umístění:

- **Umístění jako vlastnost aplikace**, která je uložena v aplikaci. Vlastnosti aplikace jsou uloženy pouze v aplikaci a nikdy se nezobrazuje v zařízení.
- **Umístění jako vlastnost zařízení**, kterou zařízení hlásí do aplikace. Tento typ vlastnosti se nejlépe používá pro statické umístění.

> [!NOTE]
> Umístění jako vlastnost nezaznamenává historii. Pokud je žádoucí historie, použijte měření umístění.

#### <a name="add-location-as-an-application-property"></a>Přidat umístění jako vlastnost aplikace

Vlastnost Location můžete vytvořit jako vlastnost aplikace pomocí Azure Maps ve vaší aplikaci IoT Central. Můžete například přidat adresu instalace zařízení:

1. Přejděte na kartu **vlastnosti** .

2. V knihovně vyberte **umístění**.

3. Nakonfigurujte **zobrazované jméno**, **název pole**a (volitelně) **počáteční hodnotu** pro umístění.

    | Zobrazovaný název  | Název pole | Počáteční hodnota |
    | --------------| -----------|---------|
    | Adresa instalace | installAddress | Microsoft, 1 Microsoft Way, Redmond, WA 98052   |

   ![Formulář Konfigurace umístění s podrobnostmi pro umístění](./media/howto-set-up-template/locationcloudproperty2.png)

   Existují dva podporované formáty pro přidání umístění:
   - **Umístění jako adresa**
   - **Umístění jako souřadnice**

4. Vyberte **Uložit**. Operátor může aktualizovat hodnotu umístění v **Device Explorer**.

#### <a name="add-location-as-a-device-property"></a>Přidat umístění jako vlastnost zařízení

Vlastnost Location můžete vytvořit jako vlastnost zařízení, kterou zařízení hlásí. Například pokud chcete sledovat umístění zařízení:

1. Přejděte na kartu **vlastnosti** .

2. V knihovně vyberte **vlastnost zařízení** .

3. Nakonfigurujte zobrazované jméno a název pole a jako datový typ vyberte **umístění** :

    | Zobrazovaný název  | Název pole | Typ dat |
    | --------------| -----------|-----------|
    | Device location (Umístění zařízení) | deviceLocation | location  |

   > [!NOTE]
   > Názvy polí musí odpovídat názvům vlastností v odpovídajícím kódu zařízení.

   ![Formulář "Konfigurace vlastností zařízení" s podrobnostmi pro umístění](./media/howto-set-up-template/locationdeviceproperty2.png)

Po připojení reálného zařízení se umístění, které jste přidali jako vlastnost zařízení, aktualizuje s hodnotou odeslanou zařízením. Po nakonfigurování vlastnosti Location můžete [Přidat mapu, která bude vizualizovat umístění na řídicím panelu zařízení](#add-a-location-property-in-the-dashboard).

## <a name="commands"></a>Příkazy

Příkazy slouží ke vzdálené správě zařízení. Umožňují operátorům spouštět na zařízení příkazy. K šabloně zařízení, která se zobrazí jako dlaždice na kartě **příkazy** , můžete přidat více příkazů, které se použijí pro operátory, které se mají použít. Jako tvůrce zařízení máte flexibilní možnost definovat příkazy podle vašich požadavků.

Jak se příkaz liší od nastavení?

- **Nastavení**: Nastavení je konfigurace, kterou chcete použít pro zařízení. Chcete, aby zařízení zachovalo tuto konfiguraci, dokud je nezměníte. Například chcete nastavit teplotu mrazicího programu a chcete toto nastavení i v případě, že se mraznička restartuje.

- **Příkaz**: Příkazy můžete použít k okamžitému spuštění příkazu na zařízení vzdáleně z IoT Central. Pokud zařízení není připojené, vyprší příkaz a dojde k chybě. Například chcete restartovat zařízení.

Nový příkaz **echo** můžete například přidat tak, že vyberete kartu **příkazy** , vyberete **+ Nový příkaz**a zadáte podrobnosti nového příkazu:

| Zobrazovaný název  | Název pole | Výchozí časový limit | Typ dat |
| --------------| -----------|---------------- | --------- |
| Příkaz pro zobrazení výsledků  | echo       |  30             | text      |

![Formulář "konfigurace příkazu" s podrobnostmi pro echo](./media/howto-set-up-template/commandsecho1.png)

Po výběru **Uložit**se příkaz **echo** zobrazí jako dlaždice a je připravený k použití z **Device Explorer** při připojení reálného zařízení. Aby příkazy úspěšně běžely, musí názvy polí příkazu odpovídat názvům vlastností v odpovídajícím kódu zařízení.

[Tady je odkaz na ukázkový kód zařízení jazyka C.](https://github.com/Azure/iot-central-firmware/blob/ad40358906aeb8f2040a822ba5292df866692c16/MXCHIP/mxchip_advanced/src/AzureIOTClient.cpp#L34)

## <a name="rules"></a>Pravidla

Pravidla umožňují operátorům monitorovat zařízení téměř v reálném čase. Pravidla automaticky vyvolávají akce, jako je odeslání e-mailu, když se pravidlo aktivuje. V současné době je k dispozici jeden typ pravidla:

- **Pravidlo telemetrie**, které se aktivuje, když vybraná telemetrie zařízení přebírá určenou prahovou hodnotu. [Přečtěte si další informace o pravidlech telemetrie](howto-create-telemetry-rules.md).

## <a name="dashboard"></a>Řídicí panel

Řídicí panel je místo, kde operátor přejde k zobrazení informací o zařízení. Jako tvůrce přidáte na tuto stránku dlaždice, které pomůžou operátorům pochopit, jak se zařízení chová. Můžete přidat mnoho typů dlaždic řídicích panelů, jako je například obrázek, spojnicový graf, pruhový graf, klíčový ukazatel výkonu (KPI), nastavení a vlastnosti a popisek.

Můžete například přidat dlaždici **nastavení a vlastnosti** pro zobrazení výběru aktuální hodnoty nastavení a vlastností výběrem karty **řídicí panel** a dlaždice z knihovny:

![Formulář konfigurace podrobností o zařízení s podrobnostmi o nastavení a vlastnostech](./media/howto-set-up-template/dashboardsettingsandpropertiesform1.png)

Když teď operátor zobrazí řídicí panel v **Device Explorer**, uvidí dlaždici.

### <a name="add-a-location-measurement-in-the-dashboard"></a>Přidání měření polohy na řídicím panelu

Pokud jste nakonfigurovali měření umístění, můžete umístění vizualizovat pomocí mapy na řídicím panelu zařízení. Pro měření polohy máte možnost vykreslovat historii umístění.

1. Přejděte na kartu **řídicí panel** .

1. Na řídicím panelu zařízení vyberte **Mapa** z knihovny.

1. Dejte mapě název. Následující příklad má název **zařízení aktuální umístění**. Pak zvolte měření umístění, které jste předtím nakonfigurovali na kartě **měření** . V následujícím příkladu je vybraná možnost měření **umístění assetu** :

   ![Formulář konfigurace mapy s podrobnostmi pro název a vlastnosti](./media/howto-set-up-template/locationcloudproperty5map.png)

1. Vyberte **Uložit**. Dlaždice mapa nyní zobrazuje umístění, které jste vybrali.

Můžete změnit velikost dlaždice mapy. Když operátor zobrazí řídicí panel v **Device Explorer**, zobrazí se všechny dlaždice řídicího panelu, které jste nakonfigurovali, včetně mapy umístění.

### <a name="add-a-location-property-in-the-dashboard"></a>Přidání vlastnosti Location na řídicím panelu

Pokud jste nakonfigurovali vlastnost Location (umístění), můžete vizualizovat umístění pomocí mapy na řídicím panelu zařízení.

1. Přejděte na kartu **řídicí panel** .

1. Na řídicím panelu zařízení vyberte **Mapa** z knihovny.

1. Dejte mapě název. Následující příklad má název **zařízení aktuální umístění**. Pak zvolte vlastnost umístění, kterou jste předtím nakonfigurovali na kartě **vlastnosti** . V následujícím příkladu je vybráno měření **umístění zařízení** :

   ![Konfigurovat formulář mapy s podrobnostmi pro název a vlastnosti](./media/howto-set-up-template/locationcloudproperty6map.png)

1. Vyberte **Uložit**. Dlaždice mapa nyní zobrazuje umístění, které jste vybrali.

Můžete změnit velikost dlaždice mapy. Když operátor zobrazí řídicí panel v **Device Explorer**, zobrazí se všechny dlaždice řídicího panelu, které jste nakonfigurovali, včetně mapy umístění.

Další informace o tom, jak používat dlaždice v Azure IoT Central, najdete v tématu [použití dlaždic řídicího panelu](howto-use-tiles.md).

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili, jak v aplikaci Azure IoT Central nastavit šablonu zařízení, můžete:

- [Vytvořit novou verzi šablony zařízení](howto-version-device-template.md)
- [Připojení zařízení IoT DevKit MXChip k aplikaci Azure IoT Central](howto-connect-devkit.md)
- [Připojení Obecné klientské aplikace k aplikaci Azure IoT Central (Node. js)](howto-connect-nodejs.md)
