---
title: Nastavit šablonu zařízení v aplikaci Azure IoT Central | Dokumentace Microsoftu
description: Zjistěte, jak nastavit šablonu zařízení měření, nastavení, vlastnostmi, pravidla a řídicí panel.
author: viv-liu
ms.author: viviali
ms.date: 01/30/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: ad506e81f6fe9cdb4604aa9bfc7870ce0bafb294
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2019
ms.locfileid: "55667119"
---
# <a name="set-up-a-device-template-new-ui-design"></a>Nastavit šablonu zařízení (návrh nové uživatelské rozhraní)

Šablona zařízení je podrobný plán, který definuje charakteristiky a chování zařízení, která se připojuje k aplikaci Azure IoT Central.

Tvůrce můžete například vytvořit šablonu zařízení pro ventilátor připojené IoT, obsahující a:

- Telemetrická data měření teploty

- Ventilátor motor chybová událost měření

- Ventilátor provozní stav měření

- Ventilátor rychlosti

- Vlastnost Location

- Pravidla, které odesílají oznámení

- Řídicí panel, který poskytuje celkový přehled o zařízení

Z této šablony zařízení můžete vytvořit a připojit skutečné ventilátor zařízení s názvy, jako operátor **ventilátor 1** a **ventilátor 2**. Všechny tyto fanoušky mají měření, nastavení, vlastnosti, pravidla a řídicí panel, který uživatelé vaší aplikace můžete monitorovat a spravovat.

> [!NOTE]
> Pouze tvůrce a správce může vytvořit, upravit a odstranit zařízení šablony. Každý uživatel může vytvořit zařízení na **Device Explorer** stránky ze stávajících šablon zařízení.

[!INCLUDE [iot-central-experimental-note](../../includes/iot-central-experimental-note.md)]

## <a name="create-a-device-template"></a>Vytvoření šablony zařízení

1. Přejděte **šablon** stránky.

2. Chcete-li vytvořit prázdnou šablonu, klikněte na tlačítko **+** a potom zadejte název, třeba **lednice** nové šablony zařízení. Potom vyberte **vytvořit**:

   ![Stránce s podrobnostmi o zařízení s "Lednice" jako název šablony](./media/howto-set-up-template-experimental/devicedetailspage.png)

4. Nyní jste na **podrobnosti o zařízení** stránky pro novou šablonu zařízení. IoT Central automaticky vytvoří simulované zařízení, když vytvoříte šablonu zařízení. Simulované zařízení umožňuje testovat chování aplikace před připojit skutečné zařízení.

Následující části popisují každou z karet na **šablona zařízení** stránky.

## <a name="measurements"></a>Měření

Měření jsou data, která pochází z vašeho zařízení. Více měření můžete přidat do šablony zařízení tak, aby odpovídaly možnosti vašeho zařízení.

- **Telemetrie** měření jsou číselné datových bodů, které postupně shromažďuje zařízení. Představovaly jste jako nepřetržitý datový proud. Příkladem je teploty.
- **Událost** měření jsou data bodu v čase, která reprezentuje něco významu na zařízení. Úroveň závažnosti představuje důležitost události. Příkladem je chyba ventilátor motor.
- **Stav** měření představující stav zařízení nebo její součásti po určitou dobu. Ventilátor režimu lze například definovat tak, že má **operační** a **Zastaveno** jako dva možné stavy.

### <a name="create-a-telemetry-measurement"></a>Vytvoření měření telemetrie

Přidat novou míru telemetrická data, klikněte na **a nové měření**, zvolte **Telemetrie** jako měření typ a zadejte podrobnosti ve formuláři.

> [!NOTE]
> Názvy polí v šabloně zařízení musí odpovídat názvům vlastností v odpovídajícím kódu zařízení v pořadí pro měření telemetrická data zobrazit v aplikaci při připojení skutečné zařízení. Totéž při konfiguraci nastavení vlastnosti zařízení a příkazy i další definice šablony zařízení v následujících částech.

Například můžete přidat nové měření teploty telemetrická data:

| Zobrazovaný název        | Název pole    |  Jednotky    | Minimum   |Maximum|
| --------------------| ------------- |-----------|-------|---|
| Teplota         | temp          |  degC     |  0    |100|

!["Vytvořit Telemetrie" formulář s podrobnostmi o měření teploty](./media/howto-set-up-template-experimental/measurementsform.png)

Po kliknutí na **Uložit**, **teploty** měření se zobrazí v seznamu měření. Nějakou dobu vidíte vizualizaci data o teplotě ze simulovaného zařízení.

> [!NOTE]
> Datový typ telemetrických dat měření je plovoucí číslo bodu.

### <a name="create-an-event-measurement"></a>Vytvoření měření událostí

Přidat novou míru události, klikněte na **a nové měření** a vyberte **události** jako typ měrné jednotky. Zadejte podrobnosti **vytvořit událost** formuláře.

Zadejte **zobrazovaný název**, **název pole**, a **závažnost** podrobnosti o události. Můžete vybrat ze tří dostupných úrovní závažnosti: **Chyba**, **upozornění**, a **informace**.

Například můžete přidat nový **Motor chyba ventilátor** událostí.

| Zobrazovaný název        | Název pole    |  Výchozí závažnost |
| --------------------| ------------- |-----------|
| Chyba motoru ventilátoru     | fanmotorerror |  Chyba    |

!["Vytvořit událost" formulář s podrobnostmi ventilátor motor události](./media/howto-set-up-template-experimental/eventmeasurementsform.png)

Po kliknutí na **Uložit**, **Motor chyba ventilátor** měření se zobrazí v seznamu měření. Nějakou dobu vidíte vizualizaci data události ze simulovaného zařízení.

Chcete-li zobrazit další podrobnosti o události, klikněte na ikonu události v grafu:

![Podrobnosti o události "Ventilátor Motor chyba"](./media/howto-set-up-template-experimental/eventmeasurementsdetail.png)

> [!NOTE]
> Datový typ měření událostí je řetězec.

### <a name="create-a-state-measurement"></a>Vytvoření měření stavu

Chcete-li přidat nového měření stavu, klikněte na tlačítko **a nové měření** tlačítko a vyberte **stavu** jako typ měrné jednotky. Zadejte podrobnosti **vytvořit stavu** formuláře.

Zadejte podrobnosti pro **zobrazovaný název**, **název pole**, a **hodnoty** stavu. Každá hodnota může mít také zobrazovaný název, který se použije, když hodnota se zobrazí v grafech a tabulky.

Například můžete přidat nový **ventilátor režimu** stavu, který má dva možné hodnoty, které zařízení může odesílat, **operační** a **Zastaveno**.

| Zobrazovaný název | Název pole    |  Hodnota 1   | Zobrazovaný název | Hodnota 2    |Zobrazovaný název  | 
| -------------| ------------- |----------- | -------------| -----------| -------------|
| Režim ventilátoru     | fanmode       |  1         | Funguje    |     0      | Zastaveno      |

!["Upravit stav" formulář s podrobnostmi o ventilátor režimu](./media/howto-set-up-template-experimental/statemeasurementsform.png)

Po kliknutí na **Uložit**, **ventilátor režimu** měření stavu se zobrazí v seznamu měření. Stručně řečeno, i když naleznete v tématu vizualizace dat o stavu ze simulovaného zařízení.

Pokud zařízení pošle příliš mnoho datových bodů v malých dobu trvání, se zobrazí s jiný vizuál měření stavu. Klikněte na graf zobrazíte všechny datové body v rámci tohoto časového období v chronologickém pořadí. Také můžete zúžit rozsah času zobrazíte měření vykreslit v grafu.

> [!NOTE]
> Datový typ měření stavu je řetězec.

## <a name="settings"></a>Nastavení

Nastavení řízení zařízení. Umožňují, aby operátoři mohli zadejte vstupy pro zařízení. Můžete přidat více nastavení do šablony zařízení, která se zobrazí jako dlaždice na **nastavení** kartu pro operátory používat. Lze přidat mnoho typů nastavení: číslo, text, datum, přepínací tlačítko, rozevíracího seznamu a popisek oddíl.

Nastavení může být v jednom ze tří stavů. Zařízení odesílá tyto stavy.

- **Synchronizované**: Zařízení se změnila na hodnotu nastavení.

- **Čekající**: Zařízení se momentálně mění hodnotu nastavení.

- **Chyba**: Zařízení vrátilo chybu.

Například můžete přidat nové nastavení rychlost ventilátor kliknutím **nastavení** a zadáte na novém **číslo** nastavení:

| Zobrazovaný název  | Název pole    |  Jednotky  | Desetinná místa |Počáteční|
| --------------| ------------- |---------| ---------|---- |
| Ventilátor rychlost     | fanSpeed      | OT. / MIN     | 2        | 0   |

!["Konfigurace číslo" formulář s podrobnostmi o nastavení rychlosti](./media/howto-set-up-template-experimental/settingsform.png)

Po výběru **Uložit**, **ventilátor rychlost** nastavení se zobrazí jako dlaždice. Operátor můžete použít ve **Device Explorer** stránky Změna rychlosti ventilátor zařízení.

## <a name="properties"></a>Vlastnosti

Vlastnosti jsou metadata, která má přidružený k zařízení, jako je například umístění zařízení a sériové číslo. Přidat do šablony zařízení, která se zobrazí jako dlaždice na více vlastností **vlastnosti** kartu. Vlastnost může mít typ, jako je číslo, text, datum, přepínací tlačítko, vlastnosti zařízení, popisku nebo umístění. Operátor můžete zadat hodnoty vlastností při jejich vytvoření zařízení, a mohli upravit tyto hodnoty v každém okamžiku. Vlastnosti zařízení jsou jen pro čtení a odesílány ze zařízení do aplikace. Operátor nelze změnit vlastnosti zařízení. Po připojení skutečných zařízení, na dlaždici vlastnosti zařízení je aktualizace v aplikaci.

Vlastnosti se dělí do dvou kategorií:

- _Vlastnosti zařízení_ , která zařízení odesílá do aplikace IoT Central. Vlastnosti zařízení jsou jen pro čtení hodnoty oznámí zařízení a aktualizují v aplikaci po připojení skutečné zařízení.
- _Vlastnosti aplikace_ , které jsou uloženy v aplikaci a operátor může upravit. Zařízení nedokáže rozpoznat vlastnosti aplikace.

Například můžete přidat data poslední obsluhované zařízení jako nový **datum** vlastnosti (vlastnosti aplikace) **vlastnosti** kartu:

| Zobrazovaný název  | Název pole | Počáteční hodnota   |
| --------------| -----------|-----------------|
| Last serviced (Poslední údržba)      | lastServiced        | 01/29/2019     |

!["Konfigurace poslední Serviced" formuláři na kartě "Properties"](./media/howto-set-up-template-experimental/propertiesform.png)

Po výběru **Uložit**, poslední Údržba data pro zařízení se zobrazí jako dlaždice.

Po vytvoření dlaždice můžete změnit hodnoty vlastnosti aplikace **Device Explorer**.

### <a name="create-a-location-property-through-azure-maps"></a>Vytvořit vlastnost umístění prostřednictvím Azure Maps

Můžete předat geografický kontext dat o poloze v Azure IoT Central a mapovat všechny souřadnice zeměpisné šířky a délky adresy. Nebo můžete namapovat zeměpisné šířky a délky souřadnice. Azure Maps umožňuje tato funkce v IoT Central.

Můžete přidat dva typy vlastností umístění:

- **Umístění jako vlastnost aplikace**, které je uložený v aplikaci. Zařízení nedokáže rozpoznat vlastnosti aplikace.
- **Umístění jako vlastnost zařízení**, která zařízení odesílá do aplikace.

#### <a name="add-location-as-an-application-property"></a>Přidat umístění jako vlastnost aplikace

Můžete vytvořit umístění vlastnost jako vlastnost aplikace s využitím map Azure ve vaší aplikaci IoT Central. Například můžete přidat adresu instalace zařízení:

1. Přejděte **vlastnosti** kartu.

2. V knihovně, vyberte **umístění**.

3. Konfigurace **zobrazovaný název**, **název pole**a (volitelně) **počáteční hodnota** pro umístění.

    | Zobrazovaný název  | Název pole | Počáteční hodnota |
    | --------------| -----------|---------| 
    | Adresa instalace | installAddress | Microsoft, 1 Microsoft Way, Redmond, WA 98052   |

   !["Konfigurace umístění" formulář s podrobnostmi o umístění](./media/howto-set-up-template-experimental/locationcloudproperty2.png)

   Existují dvě podporované formáty pro přidání do umístění:
   - **Umístění jako adresa**
   - **Umístění jako souřadnice**

4. Klikněte na **Uložit**. Operátor můžete aktualizovat hodnotu umístění v **Device Explorer**.

#### <a name="add-location-as-a-device-property"></a>Přidat umístění jako vlastnosti zařízení

Můžete vytvořit umístění vlastnost jako vlastnost zařízení, která zařízení odesílá. Například, pokud chcete sledovat polohu zařízení:

1. Přejděte **vlastnosti** kartu.

2. Vyberte **vlastnosti zařízení** z knihovny.

3. Nakonfigurujte zobrazovaný název a názvu pole a vyberte **umístění** jako datový typ:

    | Zobrazovaný název  | Název pole | Typ dat |
    | --------------| -----------|-----------|
    | Device location (Umístění zařízení) | deviceLocation | location  |

   > [!NOTE]
   > Názvy polí musí odpovídat názvům vlastností v odpovídajícím kódu zařízení

   !["Konfigurace vlastnosti zařízení" formulář s podrobnostmi o umístění](./media/howto-set-up-template-experimental/locationdeviceproperty2.png)

Po připojení se skutečné zařízení, umístění, které jste přidali jako vlastnost zařízení se aktualizuje s hodnotou odeslané ze zařízení. Teď, když jste nakonfigurovali váš vlastnost umístění, můžete [přidat mapu, která bude vizualizovat umístění na řídicím panelu zařízení](#add-an-azure-maps-location-in-the-dashboard).

## <a name="commands"></a>Příkazy

Příkazy se používají ke vzdálené správě zařízení. Umožňují, operátory pro spuštění příkazů v zařízení. Můžete přidat více příkazů do zařízení, která se zobrazí jako dlaždice na šablony **příkazy** kartu pro operátory používat. Jako tvůrce zařízení máte možnost definovat příkazy podle vašich požadavků.

Čím se liší od nastavení příkazu

* **Nastavení**: Nastavení je konfigurace, která má být použita k zařízení. Chcete, aby zařízení budou zachovány tuto konfiguraci, dokud ho změnit. Například můžete chtít nastavit teploty mrazničce a chcete, aby toto nastavení i v případě, že mrazírenských restartuje.

* **Příkaz**: Pomocí příkazů okamžité spuštění příkazu na zařízení vzdáleně z IoT Central. Pokud zařízení není připojené, vyprší časový limit příkazu a selže. Například chcete restartovat zařízení.

Například můžete přidat nový **Echo** příkaz tak, že vyberete **příkazy** kartu, pak levým na **+ nový příkaz**a zadat podrobnosti nového příkazu:

| Zobrazovaný název  | Název pole | Výchozí časový limit | Typ dat |
| --------------| -----------|---------------- | --------- |
| Příkaz pro zobrazení výsledků  | echo       |  30             | text      |

!["Příkaz konfigurace" formulář s podrobnostmi o programu echo](./media/howto-set-up-template-experimental/commandsecho.png)

Po výběru **Uložit**, **Echo** příkazu se zobrazí jako dlaždice a je připravený k použití v **Device Explorer** když skutečné zařízení připojí. Názvy polí ve svých rukou musí odpovídat názvům vlastností v odpovídajícím kódu zařízení, aby příkazy úspěšně spustit.

## <a name="rules"></a>Pravidla

Pravidla povolení operátory k monitorování zařízení téměř v reálném čase. Pravidla automaticky vyvolat akce, jako je odeslání e-mailu, když se aktivuje pravidlo. Jeden typ pravidla je v současnosti dostupné:

- **Pravidlo telemetrie**, která se aktivuje, když telemetrické vybrané zařízení překročí zadanou prahovou hodnotu. [Další informace o pravidlech telemetrie](howto-create-telemetry-rules.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).

## <a name="dashboard"></a>Řídicí panel

Řídicí panel je, kde může použít operátor zobrazit informace o zařízení. Jako tvůrce můžete přidat dlaždice na tuto stránku a operátoři pochopit, jak se zařízení chová. Víc dlaždic řídicího panelu lze přidat do šablony zařízení. Lze přidat mnoho typů dlaždic řídicích panelů, jako je například image, spojnicový graf, pruhový graf, klíčový ukazatel výkonu (KPI), nastavení a vlastnosti a popisek.

Například můžete přidat **nastavením a vlastnostem** zobrazovat výběr aktuálních hodnot nastavení a vlastnosti tak, že vyberete dlaždici **řídicí panel** kartu a na dlaždici z knihovny:

!["Konfigurace podrobnosti o zařízení" formulář s podrobnostmi o nastavení a vlastnosti](./media/howto-set-up-template-experimental/dashboardsettingsandpropertiesform.png)

Nyní když operátor zobrazení řídicího panelu **Device Explorer**, zobrazí se dlaždice.

### <a name="add-an-azure-maps-location-in-the-dashboard"></a>Přidat umístění služby Azure Maps na řídicím panelu

Pokud jste nakonfigurovali vlastnost umístění, můžete vizualizovat umístění pomocí mapování na řídicím panelu zařízení.

1. Přejděte **řídicí panel** kartu.

1. Na řídicím panelu zařízení vyberte **mapy** z knihovny.

1. Na mapě pojmenujte. V následujícím příkladu má název **umístění instalace**. Klikněte na tlačítko Vlastnosti umístění, které jste dříve nakonfigurovali na **vlastnosti** kartu. V následujícím příkladu **Adresa instalace** zaškrtnuto.

   !["Konfigurace mapování" formulář s podrobnostmi o název a vlastnosti](./media/howto-set-up-template-experimental/locationcloudproperty5map.png)

4. Vyberte **Uložit**. Mapovou dlaždici teď zobrazuje umístění, které jste vybrali.

Na mapě můžete změnit velikost na požadovanou velikost. Nyní když operátor zobrazení řídicího panelu **Device Explorer**, dlaždice řídicího panelu, že jste nakonfigurovali, včetně Mapa umístění jsou viditelné.

## <a name="next-steps"></a>Další postup

Teď, když jsme zjistili, jak nastavit šablonu zařízení v aplikaci Azure IoT Central, můžete:

> [!div class="nextstepaction"]
> [Vytvoření nové šablony verze zařízení](howto-version-devicetemplate.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
