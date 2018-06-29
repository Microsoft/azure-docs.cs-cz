---
title: Nastavit šablonu zařízení v aplikaci Azure IoT centrální | Microsoft Docs
description: Zjistěte, jak nastavit šablonu zařízení s měření, nastavení, vlastnosti, pravidla a řídicí panel.
author: viv-liu
ms.author: viviali
ms.date: 04/16/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: f71d4c7dc94fedfd598ab87c51366ba9fb1f184a
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063031"
---
# <a name="set-up-a-device-template"></a>Nastavit šablonu zařízení

Šablonu zařízení je to plán, který definuje charakteristiky a chování typu zařízení, která se připojuje k Microsoft Azure IoT centrální aplikaci.

Tvůrce můžete například vytvořit šablonu zařízení pro ventilátor IoT připojení, který má:

- Teplotní telemetrie měření

- Ventilátor měření událostí motor chyb

- Ventilátor operační měření stavu

- Ventilátor rychlost nastavení

- Vlastnost umístění

- Pravidla, která odesílání výstrah

- Řídicí panel, který vám dává celkový přehled o zařízení

Z této šablony zařízení můžete vytvořit a připojit zařízení skutečné ventilátor s názvy, jako operátor **ventilátor-1** a **ventilátor 2**. Všechny tyto ventilátory mít měření, nastavení, vlastnosti, pravidla a řídicí panel, který uživatelé vaší aplikace můžete sledovat a spravovat.

> [!NOTE]
> Pouze počítačů a správce můžete vytvořit, upravit a odstranit zařízení šablony. Kterýkoli uživatel může vytvářet zařízení na **Explorer zařízení** stránky ze stávajících šablon zařízení.

## <a name="create-a-device-template"></a>Vytvoření šablony zařízení

1. Přejděte na **Tvůrce aplikací** stránky.

2. Chcete-li vytvořit prázdnou šablonou, vyberte **vytvořit šablonu zařízení**a potom vyberte **vlastní**.

3. Zadejte název pro novou šablonu zařízení a vyberte **vytvořit**.

   ![Stránka Podrobnosti zařízení s "Ledničce" jako název šablony](./media/howto-set-up-template/devicedetailspage.png)

4. Nyní jste na **podrobnosti o zařízení** stránky nového simulovaného zařízení. Simulované zařízení se automaticky vytvoří za vás, když vytvoříte šablonu zařízení. To sestavy dat a lze řídit stejně jako skutečné zařízení.

Nyní podívejte se na každé kartě **podrobnosti o zařízení** stránky.

## <a name="measurements"></a>Měření

Měření jsou data, která pochází z vašeho zařízení. Můžete přidat více měření do šablony zařízení tak, aby odpovídaly možnosti zařízení.

- **Telemetrie** měření jsou číselné datové body, které postupně shromažďuje zařízení. Budou se reprezentován jako nepřetržitý proud. Příkladem je teploty.
- **Událost** měření jsou data v okamžiku, která reprezentuje něco násobek na zařízení. Úroveň závažnosti představuje význam událost. Příklad je chyba ventilátor motor.
- **Stav** měření reprezentovat stav zařízení nebo jeho součástí přes v časovém intervalu. Například lze definovat ventilátor režimu tak, že má **operační** a **Zastaveno** jako dva možné stavy.

### <a name="create-a-telemetry-measurement"></a>Vytvořit měření telemetrie
Chcete-li přidat novou míru telemetrická data, vyberte **+ nového měření** tlačítko. Vyberte **Telemetrie** jako měření zadejte a zadejte podrobnosti **vytvořit Telemetrie** formuláře.

> [!NOTE]
> Když je skutečné zařízení připojené, věnujte pozornost název měření, která zařízení odesílá. Název musí přesně odpovídat **název pole** položka pro měření.

Například můžete přidat novou míru teplotní telemetrie:

!["Vytvořit Telemetrie" formulář s podrobnostmi o měření teploty](./media/howto-set-up-template/measurementsform.png)

Po výběru **Uložit**, **teploty** měření se zobrazí v seznamu měření. Operátor můžete zobrazit vizualizaci z teploty data, která je shromažďování zařízení.

![Měření grafu](./media/howto-set-up-template/measurementsgraph.png)

### <a name="create-an-event-measurement"></a>Vytvořit měření událostí
Chcete-li přidat nového měření událostí, vyberte **+ nového měření** tlačítko. Vyberte **událostí** jako měření zadejte a zadejte podrobnosti **vytvořit událost** formuláře.

Zadejte **zobrazovaný název**, **název pole**, a **závažnost** podrobnosti o události. Můžete zvolit ze tří dostupných úrovní závažnosti: **chyba**, **upozornění**, a **informace**.  

Například můžete přidat nové **Motor chyba ventilátor** událostí.

!["Vytvoření události" formulář s podrobnostmi ventilátor motor události](./media/howto-set-up-template/eventmeasurementsform.png)

Po výběru **Uložit**, **Motor chyba ventilátor** měření se zobrazí v seznamu měření. Operátor můžete zobrazit vizualizaci dat události, které zařízení odesílá.

![Graf měření událostí](./media/howto-set-up-template/eventmeasurementschart.png)

Chcete-li zobrazit další podrobnosti o události, vyberte ikonu událostí na graf:

![Podrobnosti události "Error ventilátor Motor.](./media/howto-set-up-template/eventmeasurementsdetail.png)


### <a name="create-a-state-measurement"></a>Vytvořit měření stavu
Chcete-li přidat nového měření stavu, vyberte **+ nového měření** tlačítko. Vyberte **stavu** jako měření zadejte a zadejte podrobnosti **vytvořit stavu** formuláře.

Zadejte podrobnosti pro **zobrazovaný název**, **název pole**, a **hodnoty** stavu. Každá hodnota může mít zobrazovaný název, který se použije, když se zobrazí hodnota v grafů a tabulek.

Například můžete přidat nové **ventilátor režimu** stavu, který má dvě možné hodnoty, které zařízení může odesílat, **operační** a **Zastaveno**.

!["Upravit stav" formulář s podrobnostmi o ventilátor režimu](./media/howto-set-up-template/statemeasurementsform.png)

Po výběru **Uložit**, **ventilátor režimu** měření stavu se zobrazí v seznamu měření. Operátor můžete zobrazit vizualizaci dat o stavu, který odesílá zařízení.

![Měření stavu grafu](./media/howto-set-up-template/statemeasurementschart.png)

Pokud zařízení odesílá příliš mnoho datových bodů v malých doba trvání, zobrazí se měření stavu s jinou visual, jak je znázorněno na následujícím snímku obrazovky. Pokud kliknete na graf, všechny datové body v rámci toto časové období se zobrazují v chronologickém pořadí. Můžete také zúžit časový rozsah a zobrazte měření na graf vykreslen.

![Podrobnosti pro měření stavu "Statické ventilátor režim"](./media/howto-set-up-template/statemeasurementsdetail.png)


## <a name="settings"></a>Nastavení

Nastavení řídí zařízení. Umožňují operátory vaší aplikace zadejte vstupy pro zařízení. Můžete přidat několik nastavení do šablony zařízení, která se zobrazují jako dlaždice na **nastavení** kartě operátorů sloužící k použití. Můžete přidat šesti typy nastavení: číslo, text, datum, přepnutí, rozevíracího seznamu a popisku oddílu.

> [!NOTE]
> Když je skutečné zařízení připojené, věnujte pozornost název nastavení, která zařízení odesílá. Název musí přesně odpovídat **název pole** položka pro třídu setting.

Nastavení může být v jednom ze tří stavů. Zařízení se hlásí tyto stavy.

- **Synchronizovat**: zařízení změnila, aby odrážela hodnotu nastavení.

- **Čekající**: zařízení je momentálně změna na hodnotu nastavení.

- **Chyba**: zařízení vrátilo chybu.

Například můžete přidat nové nastavení rychlosti ventilátor:

!["Konfigurace číslo" formulář s podrobnostmi o nastavení rychlosti](./media/howto-set-up-template/settingsform.png)

Po výběru **Uložit**, **ventilátor rychlost** nastavení se zobrazí jako dlaždici a je připravený k použít ke změně rychlosti ventilátor zařízení.

Po vytvoření dlaždici, můžete vyzkoušet nové nastavení. Nejprve vypněte režimu návrhu v pravé horní části obrazovky.

![Karta "Nastavení" s "Režimu návrhu" přepínač pro dlaždici](./media/howto-set-up-template/settingstile.png)

## <a name="properties"></a>Vlastnosti

Vlastnosti jsou metadata zařízení, která má přidružené k zařízení, jako je například umístění zařízení a sériové číslo. Můžete přidat do šablony zařízení, která se zobrazují jako dlaždice na víc vlastností **vlastnosti** kartě. Operátor můžete zadat hodnoty pro vlastnosti, při jejich vytvoření zařízení a mohli upravit tyto hodnoty kdykoli. Můžete přidat šesti typy vlastností: číslo, text, datum, přepnutí, vlastnosti zařízení a popisku.

Existují dvě kategorie vlastností:

- **Zařízení** vlastnosti, které zařízení sestavy.
- **Aplikace** vlastnosti, které jsou uložena výhradně v aplikaci. Zařízení nerozpoznal vlastnosti aplikace.

> [!NOTE]
> Pro vlastnosti zařízení když je skutečné zařízení připojené, věnujte pozornost název vlastnosti, která zařízení odesílá. Název se musí přesně shodovat **název pole** položku Vlastnosti. Pro vlastnosti aplikace názvu pole může být nic, co chcete, tak dlouho, dokud se v šabloně zařízení jedinečný název.

Umístění zařízení můžete například přidat jako novou vlastnost:

!["Konfigurace Text" formuláři na kartě "Vlastnosti"](./media/howto-set-up-template/propertiesform.png)

Po výběru **Uložit**, zobrazí se umístění zařízení jako dlaždici:

![Dlaždice umístění](./media/howto-set-up-template/propertiestile.png)

Po vytvoření dlaždici, můžete změnit hodnotu vlastnosti. Nejprve vypněte režimu návrhu v pravé horní části obrazovky.

### <a name="create-a-location-property-through-azure-maps"></a>Vytvořit vlastnost umístění prostřednictvím Azure mapy
Může poskytnout kontext geografické umístění data z Azure IoT centrální a mapovat všechny zeměpisnou šířku a délku souřadnice adresu. Nebo můžete jednoduše zeměpisnou šířku a délku souřadnicích mapy. Azure mapy umožňuje tato funkce v centrální IoT.

Můžete přidat dva typy vlastností umístění:
- **Umístění jako vlastnost aplikací**, která je uložena výhradně v aplikaci. Zařízení nerozpoznal vlastnosti aplikace.
- **Umístění jako vlastnost zařízení**, která hlásí zařízení.

#### <a name="add-location-as-an-application-property"></a>Přidat umístění jako vlastnost aplikací 
Umístění vlastnost jako vlastnost aplikací můžete vytvořit pomocí Azure mapy ve vaší aplikaci střed IoT. Například můžete přidat adresu instalace zařízení. 

1. Na **vlastnosti** ověřte, že **režimu návrhu** je **na**.

   ![Karta "Vlastnosti" režim návrhu zapnut](./media/howto-set-up-template/locationcloudproperty1.png)

2. V knihovně, vyberte **umístění**.
3. Konfigurace **zobrazovaný název**, **název pole**a (volitelně) **počáteční hodnota** pro umístění. 

   !["Konfigurovat umístění" formulář s podrobnostmi o umístění](./media/howto-set-up-template/locationcloudproperty2.png)

   Existují dvě podporované formáty přidat umístění:
   - **Umístění jako adresa**
   - **Umístění jako souřadnice** 

4. Vyberte **Uložit**. 

   ![Vlastnost umístění s adresou instalace přidán](./media/howto-set-up-template/locationcloudproperty3.png)

Operátor teď můžete aktualizovat hodnotu umístění ve formě pole umístění. 

#### <a name="add-location-as-a-device-property"></a>Přidat umístění jako vlastnosti zařízení 

Vytvořit vlastnost umístění jako vlastnosti zařízení, která zařízení odesílá. Pokud například chcete sledovat umístění zařízení:

1. Na **vlastnosti** ověřte, že **režimu návrhu** je **na**.

   ![Karta "Vlastnosti" režim návrhu zapnut](./media/howto-set-up-template/locationdeviceproperty1.png)

2. Vyberte **vlastnosti zařízení** z knihovny.
3. Nakonfigurujte zobrazovaný název a názvu pole a vyberte **umístění** jako datový typ. 

   > [!NOTE]
   > Název pole musí přesně shodovat název vlastnosti, která zařízení odesílá. 

   !["Konfigurovat vlastnosti zařízení" formulář s podrobnostmi o umístění](./media/howto-set-up-template/locationdeviceproperty2.png)

Nyní, když jste nakonfigurovali vaší vlastnost umístění, můžete [přidat mapu, která bude vizualizovat umístění v řídicím panelu zařízení](#add-an-azure-maps-location-in-the-dashboard).

## <a name="commands"></a>Příkazy

Příkazy se používají k vzdáleně spravovat zařízení. Umožňují operátory aplikace okamžitě spouštět příkazy na zařízení. Můžete přidat více příkazů do šablony zařízení, která se zobrazují jako dlaždice na **příkazy** kartě operátorů sloužící k použití. Jako tvůrce zařízení máte možnost definovat příkazy podle svých požadavků.

Jak se liší od nastavení příkaz? 

* **Nastavení**: nastavení konfigurace, který chcete použít pro zařízení a chtějí zařízení uchová tuto konfiguraci, dokud ho změnit. Například chcete nastavit teplota mrazničce a chcete, aby nastavení i v případě, že mrazírenských restartuje. 

* **Příkaz**: můžete použít příkazy pro okamžité spuštění příkazu na zařízení vzdáleně z centrální IoT. Pokud zařízení není připojený, příkaz vyprší časový limit a nezdaří. Například můžete chtít restartování zařízení.  

Když spustíte příkaz, může být v jednom ze tří stavů, v závislosti na tom, jestli zařízení přijat příkaz. 

Například můžete přidat nové **Echo** příkaz:

!["Konfigurovat příkaz" formulář s podrobnostmi o odezvu](./media/howto-set-up-template/commandsecho.png)

Po výběru **Uložit**, **Echo** příkazu se zobrazí jako dlaždici a je připravený k použití na odezvu zařízení.

Po vytvoření dlaždici, můžete vyzkoušet nový příkaz.

## <a name="rules"></a>Pravidla

Pravidla umožňují operátory monitorování zařízení skoro v reálném čase. Pravidla automaticky vyvolání akce, například odeslání e-mailem při aktivaci pravidla. Dnes je k dispozici jeden typ pravidla:

- **Pravidlo telemetrie**, které se aktivuje, když vybraná zařízení telemetrie protne zadanou prahovou hodnotu. [Další informace o pravidlech telemetrie](howto-create-telemetry-rules.md).

## <a name="dashboard"></a>Řídicí panel

Řídicí panel je, kde můžete operátor moct zobrazit informace o zařízení. Jako tvůrce můžete přidat dlaždice na této stránce pomůžou operátory pochopit, jak se zařízení chovají. Více dlaždice řídicího panelu můžete přidat do šablony zařízení. Můžete přidat šest typů dlaždice řídicího panelu: bitovou kopii, řádek grafu, pruhový graf, klíčového ukazatele výkonu, nastavení a vlastností a popisku.

Například můžete přidat **nastavení a vlastností** dlaždice zobrazíte výběr aktuálními hodnotami vlastností a nastavení:

!["Konfigurace podrobnosti o zařízení" formulář s podrobnostmi o nastavení a vlastností](./media/howto-set-up-template/dashboardsettingsandpropertiesform.png)

Nyní když operátor zobrazení řídicího panelu, uvidí tuto dlaždici, která zobrazuje vlastnosti a nastavení zařízení:

![Karta "Řídicího panelu" s zobrazené nastavení a vlastností pro dlaždici](./media/howto-set-up-template/dashboardtile.png)

### <a name="add-an-azure-maps-location-in-the-dashboard"></a>Přidat umístění služby Azure Maps v řídicím panelu

Pokud jste nakonfigurovali umístění vlastnost dříve v [vytvořit vlastnost umístění prostřednictvím Azure mapy](#create-a-location-property-through-azure-maps), můžete vizualizovat umístění pomocí mapování v řídicím panelu zařízení.

1. Na **řídicí panel** ověřte, že **režimu návrhu** je **na**.

   ![Karta "Řídicího panelu" režim návrhu zapnut](./media/howto-set-up-template/locationcloudproperty4map.png)

2. Na řídicím panelu zařízení, vyberte **mapy** z knihovny. 
3. Zadejte název a zvolte vlastnost umístění, který jste dříve nakonfigurovali jako součást vaší vlastnosti zařízení.

   !["Konfigurace mapy" formulář s podrobnostmi o název a vlastnosti](./media/howto-set-up-template/locationcloudproperty5map.png)

4. Vyberte **Uložit**. Dlaždice map teď zobrazuje umístění, které jste vybrali. 

   ![Dlaždice map s vybraném umístění.](./media/howto-set-up-template/locationcloudproperty6map.png) 

Požadovaná velikost můžete nastavit velikost mapy.

Nyní když operátor zobrazení řídicího panelu, uvidí všechny dlaždice řídicího panelu, které jste nakonfigurovali, včetně Mapa umístění.

![Dlaždice na řídicím panelu](./media/howto-set-up-template/locationcloudproperty7map.png) 

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili jak nastavit šablonu zařízení v Azure IoT centrální aplikaci, můžete:

> [!div class="nextstepaction"]
> [Vytvořit novou verzi šablony zařízení](howto-version-devicetemplate.md)
