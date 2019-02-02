---
title: Nastavit šablonu zařízení v aplikaci Azure IoT Central | Dokumentace Microsoftu
description: Zjistěte, jak nastavit šablonu zařízení měření, nastavení, vlastnostmi, pravidla a řídicí panel.
author: viv-liu
ms.author: viviali
ms.date: 10/26/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: fc18262326a8474cac417b67a37df35d91d75439
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2019
ms.locfileid: "55657735"
---
# <a name="set-up-a-device-template"></a>Nastavení šablony zařízení

Šablona zařízení je podrobný plán, který definuje charakteristiky a chování zařízení, která se připojuje k aplikaci Azure IoT Central.

Tvůrce můžete například vytvořit šablonu zařízení pro ventilátor připojené IoT, obsahující:

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

1. Přejděte **Tvůrce aplikací** stránky.

2. Chcete-li vytvořit prázdnou šablonou, vyberte **vytvořit šablonu zařízení**a pak vyberte **vlastní**.

3. Zadejte název (například lednice-1) nové šablony zařízení a vyberte **vytvořit**.

   ![Stránce s podrobnostmi o zařízení s "Lednice" jako název šablony](./media/howto-set-up-template/devicedetailspage.png)

4. Nyní jste na **podrobnosti o zařízení** stránky nového simulovaného zařízení. Simulované zařízení se automaticky vytvoří za vás, když vytvoříte šablonu zařízení. Sestavy dat a je možné řídit stejně jako skutečné zařízení.

Nyní Pojďme se podívat na jednotlivých kartách **podrobnosti o zařízení** stránky.

## <a name="measurements"></a>Měření

Měření jsou data, která pochází z vašeho zařízení. Více měření můžete přidat do šablony zařízení tak, aby odpovídaly možnosti vašeho zařízení.

- **Telemetrie** měření jsou číselné datových bodů, které postupně shromažďuje zařízení. Představovaly jste jako nepřetržitý datový proud. Příkladem je teploty.
- **Událost** měření jsou data bodu v čase, která reprezentuje něco významu na zařízení. Úroveň závažnosti představuje důležitost události. Příkladem je chyba ventilátor motor.
- **Stav** měření představující stav zařízení nebo její součásti po určitou dobu. Ventilátor režimu lze například definovat tak, že má **operační** a **Zastaveno** jako dva možné stavy.

### <a name="create-a-telemetry-measurement"></a>Vytvoření měření telemetrie
Chcete-li přidat novou míru telemetrická data, **upravit šablonu**a potom klikněte na tlačítko **a nové měření** tlačítko. Vyberte **Telemetrie** jako měření typ a zadejte podrobnosti **vytvořit Telemetrie** formuláře.

> [!NOTE]
> Názvy polí v šabloně zařízení musí odpovídat názvům vlastností v odpovídajícím kódu zařízení v pořadí pro měření telemetrická data zobrazit v aplikaci při připojení skutečné zařízení. Totéž při konfiguraci nastavení vlastnosti zařízení a příkazy i další definice šablony zařízení v následujících částech.

Například můžete přidat nové měření teploty telemetrická data:
| Zobrazovaný název        | Název pole    |  Jednotky    | Minimum   |Maximum|
| --------------------| ------------- |-----------|-------|---|
| Teplota         | temp          |  degC     |  0    |100|

!["Vytvořit Telemetrie" formulář s podrobnostmi o měření teploty](./media/howto-set-up-template/measurementsform.png)

Po výběru **provádí**, **teploty** měření se zobrazí v seznamu měření. V nějakou dobu zobrazí se vizualizace data o teplotě, který je generován simulovaného zařízení. Při vytváření šablony zařízení simulovaného zařízení je vygenerován ze šablony, která umožňuje testovat chování aplikace před reálného fyzických zařízení je připojené.


> [!NOTE]
  Datový typ telemetrických dat měření je plovoucí číslo bodu.

### <a name="create-an-event-measurement"></a>Vytvoření měření událostí
Chcete-li přidat novou událost měření **upravit šablonu**a potom klikněte na tlačítko **a nové měření** tlačítko. Vyberte **události** jako měření typ a zadejte podrobnosti **vytvořit událost** formuláře.

Zadejte **zobrazovaný název**, **název pole**, a **závažnost** podrobnosti o události. Můžete vybrat ze tří dostupných úrovní závažnosti: **Chyba**, **upozornění**, a **informace**.  

Například můžete přidat nový **Motor chyba ventilátor** událostí.

| Zobrazovaný název        | Název pole    |  Výchozí závažnost | 
| --------------------| ------------- |-----------|
| Chyba motoru ventilátoru     | fanmotorerror |  Chyba    | 

!["Vytvořit událost" formulář s podrobnostmi ventilátor motor události](./media/howto-set-up-template/eventmeasurementsform.png)

Po výběru **provádí**, **Motor chyba ventilátor** měření se zobrazí v seznamu měření. Operátor můžete zobrazit vizualizaci dat události, které zařízení odesílá.

![Měření grafu události](./media/howto-set-up-template/eventmeasurementschart.png)

Chcete-li zobrazit další podrobnosti o události, vyberte ikonu události v grafu.

![Podrobnosti o události "Ventilátor Motor chyba"](./media/howto-set-up-template/eventmeasurementsdetail.png)

> [!NOTE]
  Datový typ měření událostí je řetězec.

### <a name="create-a-state-measurement"></a>Vytvoření měření stavu
Chcete-li přidat nový měření stavu **upravit šablonu**a potom klikněte na tlačítko **a nové měření** tlačítko. Vyberte **stavu** jako měření typ a zadejte podrobnosti **vytvořit stavu** formuláře.

Zadejte podrobnosti pro **zobrazovaný název**, **název pole**, a **hodnoty** stavu. Každá hodnota může mít také zobrazovaný název, který se použije, když hodnota se zobrazí v grafech a tabulky.

Například můžete přidat nový **ventilátor režimu** stavu, který má dva možné hodnoty, které zařízení může odesílat, **operační** a **Zastaveno**.


| Zobrazovaný název | Název pole    |  Hodnota 1   | Zobrazovaný název | Hodnota 2    |Zobrazovaný název  | 
| -------------| ------------- |----------- | -------------| -----------| -------------|
| Režim ventilátoru     | fanmode       |  1         | Funguje    |     0      | Zastaveno      |

!["Upravit stav" formulář s podrobnostmi o ventilátor režimu](./media/howto-set-up-template/statemeasurementsform.png)

Po výběru **provádí**, **ventilátor režimu** měření stavu se zobrazí v seznamu měření. Operátor, který můžete zobrazit vizualizaci, která zařízení odesílá data o stavu.

![Měření stavu grafu](./media/howto-set-up-template/statemeasurementschart.png)

Pokud zařízení pošle příliš mnoho datových bodů v malých dobu trvání, zobrazí se měření stavu s jiný vizuál, jak je znázorněno na následujícím snímku obrazovky. Pokud kliknete na graf, všechny datové body v rámci tohoto časového období se zobrazují v chronologickém pořadí. Také můžete zúžit rozsah času zobrazíte měření vykreslit v grafu.

> [!NOTE]
  Datový typ měření stavu je řetězec.

## <a name="settings"></a>Nastavení

Nastavení řízení zařízení. Umožňují operátory aplikace zadejte vstupy pro zařízení. Můžete přidat více nastavení do šablony zařízení, která se zobrazí jako dlaždice na **nastavení** kartu pro operátory používat. Lze přidat mnoho typů nastavení: číslo, text, datum, přepínací tlačítko, rozevíracího seznamu a popisek oddíl. 

Nastavení může být v jednom ze tří stavů. Zařízení odesílá tyto stavy.

- **Synchronizované**: Zařízení se změnila na hodnotu nastavení.

- **Čekající**: Zařízení se momentálně mění hodnotu nastavení.

- **Chyba**: Zařízení vrátilo chybu.

Například můžete přidat nové nastavení rychlost ventilátor tak, že vyberete **upravit šablonu** a zadáte na novém **číslo** nastavení:

| Zobrazovaný název  | Název pole    |  Jednotky  | Desetinná místa |Počáteční|
| --------------| ------------- |---------| ---------|---- |
| Ventilátor rychlost     | fanSpeed      | OT. / MIN     | 2        | 0   |

!["Konfigurace číslo" formulář s podrobnostmi o nastavení rychlosti](./media/howto-set-up-template/settingsform.png)

Po výběru **Uložit**, **ventilátor rychlost** nastavení se zobrazí jako dlaždice a je připravená k použití Změna rychlosti ventilátor zařízení.

Po vytvoření dlaždice, vyberte **provádí** v pravé horní části obrazovky. Skutečné zařízení po připojení k aplikaci, hodnota nastavení se změní na synchronizované.

![Karta "Nastavení" s "Režimu návrhu" přepínač pro dlaždici](./media/howto-set-up-template/settingstile.png)

## <a name="properties"></a>Vlastnosti

Vlastnosti jsou metadata zařízení, který je spojen se zařízením, jako je například umístění zařízení a sériové číslo. Můžete přidat více vlastností do šablony zařízení, která se zobrazí jako dlaždice na **vlastnosti** kartu. Lze přidat mnoho typů vlastností: číslo, text, datum, přepínací tlačítko, vlastnosti zařízení, popisek a umístění. Operátor můžete zadat hodnoty vlastností při jejich vytvoření zařízení, a mohli upravit tyto hodnoty v každém okamžiku. Ale vlastnosti zařízení jsou jen pro čtení se odesílají ze zařízení do aplikace a nelze změnit pomocí operátoru. Při připojení skutečné zařízení, na dlaždici vlastnosti zařízení budou aktualizovány v aplikaci. 

Vlastnosti se dělí do dvou kategorií:

- **Zařízení** vlastnosti, které zařízení odesílá do aplikace IoT Central. Tyto jsou jen pro čtení hodnoty oznámí zařízení a budou aktualizovány v aplikaci při připojení skutečné zařízení. 
- **Aplikace** vlastnosti, které jsou uloženy výhradně v aplikaci a operátor může upravit. Zařízení nedokáže rozpoznat vlastnosti aplikace.

Umístění zařízení můžete například přidat jako nový **Text** vlastností (vlastnost aplikací) tak, že vyberete **upravit šablonu** a zadávání v nové vlastnosti:

| Zobrazovaný název  | Název pole | Oříznout mezery na začátku  | Oříznout mezery na konci  | Rozlišování velikosti písmen| Minimální délka | Maximální délka |
| --------------| -----------|---------| ---------|---- |----|----|
| Umístění      | loc        | Vypnuto     |  Vypnuto     | Smíšené  | 0 | 100|

!["Konfigurace Text" formuláři na kartě "Properties"](./media/howto-set-up-template/propertiesform.png)

Po výběru **Uložit**, umístění zařízení se zobrazí jako dlaždice:

![Umístění dlaždice](./media/howto-set-up-template/propertiestile.png)

Po vytvoření dlaždice můžete změnit hodnotu vlastnosti aplikace. Nejdřív vyberte **provádí** v pravé horní části obrazovky.

### <a name="create-a-location-property-through-azure-maps"></a>Vytvořit vlastnost umístění prostřednictvím Azure Maps
Můžete předat geografický kontext dat o poloze v Azure IoT Central a mapovat všechny souřadnice zeměpisné šířky a délky adresy. Nebo můžete jednoduše zeměpisné šířky a délky souřadnicích mapy. Azure Maps umožňuje tato funkce v IoT Central.

Můžete přidat dva typy vlastností umístění:
- **Umístění jako vlastnost aplikace**, která je uložena výhradně v aplikaci. Zařízení nedokáže rozpoznat vlastnosti aplikace.
- **Umístění jako vlastnost zařízení**, která zařízení odesílá do aplikace.

#### <a name="add-location-as-an-application-property"></a>Přidat umístění jako vlastnost aplikace 
Můžete vytvořit umístění vlastnost jako vlastnost aplikace s využitím map Azure ve vaší aplikaci IoT Central. Například můžete přidat adresu instalace zařízení. 

1. Na **vlastnosti** kartu, vyberte možnost **upravit šablonu**.

   ![Karta "Properties" se zapnutý režim návrhu](./media/howto-set-up-template/locationcloudproperty1.png)

2. V knihovně, vyberte **umístění**.
3. Konfigurace **zobrazovaný název**, **název pole**a (volitelně) **počáteční hodnota** pro umístění. 

    | Zobrazovaný název  | Název pole | Počáteční hodnota |
    | --------------| -----------|---------| 
    | Adresa instalace | insta_address | Microsoft, 1 Microsoft Way, Redmond, WA 98052   |

   !["Konfigurace umístění" formulář s podrobnostmi o umístění](./media/howto-set-up-template/locationcloudproperty2.png)

   Existují dvě podporované formáty pro přidání do umístění:
   - **Umístění jako adresa**
   - **Umístění jako souřadnice** 

4. Vyberte **Uložit** a **provádí**. Operátor teď můžete aktualizovat hodnotu umístění ve formě pole umístění. 

#### <a name="add-location-as-a-device-property"></a>Přidat umístění jako vlastnosti zařízení 

Můžete vytvořit umístění vlastnost jako vlastnost zařízení, která zařízení odesílá. Například, pokud chcete sledovat polohu zařízení:

1. Na **vlastnosti** kartu, vyberte možnost **upravit šablonu**.

   ![Karta "Properties" se zapnutý režim návrhu](./media/howto-set-up-template/locationdeviceproperty1.png)

2. Vyberte **vlastnosti zařízení** z knihovny.
3. Nakonfigurujte zobrazovaný název a názvu pole a vyberte **umístění** jako datový typ. 

    | Zobrazovaný název  | Název pole | Typ dat |
    | --------------| -----------|-----------| 
    | Device location (Umístění zařízení) | deviceLoc| location  |

   > [!NOTE]
   > Názvy polí musí odpovídat názvům vlastností v odpovídajícím kódu zařízení

   !["Konfigurace vlastnosti zařízení" formulář s podrobnostmi o umístění](./media/howto-set-up-template/locationdeviceproperty2.png)

Po připojení se skutečné zařízení, umístění, které jste přidali jako vlastnost zařízení aktualizuje hodnotu odeslané ze zařízení. Instalační adresa, umístění, které jste přidali jako vlastnost aplikací, je upravovat dlaždici. Teď, když jste nakonfigurovali váš vlastnost umístění, můžete [přidat mapu, která bude vizualizovat umístění na řídicím panelu zařízení](#add-an-azure-maps-location-in-the-dashboard).

## <a name="commands"></a>Příkazy

Příkazy se používají ke vzdálené správě zařízení. Umožňují operátory aplikace okamžitě spouštět příkazy na zařízení. Můžete přidat více příkazů do zařízení, která se zobrazí jako dlaždice na šablony **příkazy** kartu pro operátory používat. Jako tvůrce zařízení máte možnost definovat příkazy podle vašich požadavků.

Čím se liší od nastavení příkazu 

* **Nastavení**: Nastavení konfigurace, která má být použita k zařízení, a chcete zařízení budou zachovány tuto konfiguraci, dokud ho změnit. Například můžete chtít nastavit teploty mrazničce a chcete, aby toto nastavení i v případě, že mrazírenských restartuje. 

* **Příkaz**: Pomocí příkazů okamžité spuštění příkazu na zařízení vzdáleně z IoT Central. Pokud zařízení není připojené, vyprší časový limit příkazu a selže. Například chcete restartovat zařízení.  


Například můžete přidat nový **Echo** příkaz tak, že vyberete **úpravy šablony**, pak levým na **+ nový příkaz**a zadáním do nového příkazu:

| Zobrazovaný název  | Název pole | Výchozí časový limit | Typ dat |
| --------------| -----------|---------------- | --------- | 
| Příkaz pro zobrazení výsledků  | echo       |  30             | text      |

!["Příkaz konfigurace" formulář s podrobnostmi o programu echo](./media/howto-set-up-template/commandsecho.png)

Po výběru **Uložit** a **provádí**, **Echo** příkazu se zobrazí jako dlaždice a je připravená k použití vypisovat zařízení po připojení vaší skutečné zařízení. Názvy polí ve svých rukou musí odpovídat názvům vlastností v odpovídajícím kódu zařízení, aby příkazy úspěšně spustit.


## <a name="rules"></a>Pravidla

Pravidla povolení operátory k monitorování zařízení téměř v reálném čase. Pravidla automaticky vyvolat akce, jako je odeslání e-mailu, když se aktivuje pravidlo. Jeden typ pravidla je v současnosti dostupné:

- **Pravidlo telemetrie**, která se aktivuje, když telemetrické vybrané zařízení překročí zadanou prahovou hodnotu. [Další informace o pravidlech telemetrie](howto-create-telemetry-rules.md).

## <a name="dashboard"></a>Řídicí panel

Řídicí panel je, kde může použít operátor zobrazit informace o zařízení. Jako tvůrce můžete přidat dlaždice na tuto stránku a operátoři pochopit, jak se zařízení chová. Víc dlaždic řídicího panelu lze přidat do šablony zařízení. Lze přidat mnoho typů dlaždic řídicích panelů, jako je například image, spojnicový graf, pruhový graf, klíčový ukazatel výkonu (KPI), nastavení a vlastnosti a popisek.

Například můžete přidat **nastavením a vlastnostem** dlaždici zobrazíte výběr aktuální hodnoty vlastností a nastavení tak, že vyberete **upravit šablonu** a na dlaždici z knihovny:

!["Konfigurace podrobnosti o zařízení" formulář s podrobnostmi o nastavení a vlastnosti](./media/howto-set-up-template/dashboardsettingsandpropertiesform.png)

Nyní když operátor zobrazení řídicího panelu, uvidí tuto dlaždici, která zobrazuje vlastnosti a nastavení zařízení:

![Karta "Řídicí panel" se zobrazené nastavení a vlastnosti pro dlaždici](./media/howto-set-up-template/dashboardtile.png)

### <a name="add-an-azure-maps-location-in-the-dashboard"></a>Přidat umístění služby Azure Maps na řídicím panelu

Pokud jste nakonfigurovali umístění vlastnost dříve v [vytvoření vlastnosti umístění prostřednictvím Azure Maps](#create-a-location-property-through-azure-maps), umístění můžete vizualizovat pomocí mapování na řídicím panelu zařízení.

1. Na **řídicí panel** kartu, vyberte možnost **upravit šablonu**.

   ![Karta "Řídicí panel" se zapnutý režim návrhu](./media/howto-set-up-template/locationcloudproperty4map.png)

2. Na řídicím panelu zařízení vyberte **mapy** z knihovny. 
3. Pojmenujte jej. V příkladu níže má název umístění instalace, klikněte na tlačítko Vlastnosti umístění, které jste dříve nakonfigurovali na kartě Vlastnosti. V následujícím příkladu **Adresa instalace** zaškrtnuto.

   !["Konfigurace mapování" formulář s podrobnostmi o název a vlastnosti](./media/howto-set-up-template/locationcloudproperty5map.png)

4. Vyberte **Uložit**. Mapovou dlaždici teď zobrazuje umístění, které jste vybrali. 

   ![Mapovou dlaždici s vybrané umístění](./media/howto-set-up-template/locationcloudproperty6map.png) 

Na mapě můžete změnit velikost na požadovanou velikost. Nyní když operátor zobrazení řídicího panelu, uvidí všechny dlaždice řídicího panelu, které jste nakonfigurovali, včetně Mapa umístění.

## <a name="next-steps"></a>Další postup

Teď, když jsme zjistili, jak nastavit šablonu zařízení v aplikaci Azure IoT Central, můžete:

> [!div class="nextstepaction"]
> [Vytvoření nové šablony verze zařízení](howto-version-devicetemplate.md)
