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
ms.openlocfilehash: bda056a75ae9d696dab389b85fe1bfb2935ee1a8
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35261980"
---
# <a name="set-up-a-device-template"></a>Nastavit šablonu zařízení

Šablonu zařízení je to plán, který definuje charakteristiky a chování typu zařízení, která se připojuje k Microsoft Azure IoT centrální aplikaci.

Tvůrce můžete například vytvořit šablonu zařízení pro připojené ventilátor IoT, který má:

- Teplotní telemetrie měření

- Ventilátor měření událostí motor chyb

- Ventilátor operační měření stavu

- Ventilátor rychlost nastavení

- Vlastnost umístění

- Pravidla, která odesílání výstrah

- Řídicí panel, který vám dává všechna zobrazení o zařízení

Z této šablony zařízení můžete vytvořit a připojit zařízení skutečné ventilátor s názvy, jako operátor **ventilátor-1** a **ventilátor 2**. Všechny tyto ventilátory mít pravidla měření, nastavení a vlastnosti a řídicí panel, který uživatelé vaší aplikace můžete sledovat a spravovat.

> [!NOTE]
Pouze počítačů a správce můžete vytvořit, upravit a odstranit zařízení šablony. Kterýkoli uživatel může vytvářet zařízení **Explorer zařízení** stránky ze stávajících šablon zařízení.

## <a name="create-a-new-device-template"></a>Vytvoření nové šablony zařízení

1. Přejděte na **Tvůrce aplikací** stránky.

1. Chcete-li vytvořit prázdnou šablonou, zvolte **vytvořit šablonu zařízení**a potom zvolte **vlastní**.

1. Zadejte název pro novou šablonu zařízení a zvolte **vytvořit**.

    ![Stránka Podrobnosti zařízení](./media/howto-set-up-template/devicedetailspage.png)

1. Nyní jste na **podrobnosti o zařízení** stránky nového simulovaného zařízení. Simulované zařízení se automaticky vytvoří za vás, když vytvoříte novou šablonu zařízení. To sestavy dat a lze řídit stejně jako skutečné zařízení.

Nyní podívejte se na každé kartě **podrobnosti o zařízení** stránky.

## <a name="measurements"></a>Měření

Měření jsou dat pocházejících z vašeho zařízení. Můžete přidat více měření do šablony zařízení tak, aby odpovídaly možnosti zařízení. V současné době telemetrie a událostí jsou typy podporované měření.

- **Telemetrie** měření jsou číselné datové body zařízení postupně shromažďuje a jsou reprezentována nepřetržitý proud. Například teploty.
- **Událost** měření jsou data v okamžiku, která představují něco násobek na zařízení. Události mají závažnost s nimi spojených, představující význam události. Například ventilátor motor chyby
- **Stav** měření reprezentovat stav zařízení nebo jeho součástí přes v časovém intervalu. Ventilátor například režim, který může mít operační a zastavena jako dva možné stavy.

### <a name="create-a-telemetry-measurement"></a>Vytvořit měření Telemetrie
Chcete-li přidat novou míru telemetrická data, klikněte na **+ nového měření** tlačítko, které se otevře formulář s možnostmi a vyberte typ měrné jednotky. Vyberte **Telemetrie** a zadejte podrobnosti **vytvořit Telemetrie** formuláře.

> [!NOTE]
> Když je skutečné zařízení připojené, platíte pozornost název měření hlásí zařízení. Název musí přesně odpovídat **název pole** měření.

Například můžete přidat novou míru teplotní telemetrie:

![Měření formuláře](./media/howto-set-up-template/measurementsform.png)

Když vyberete **Uložit**, **teploty** měření se zobrazí v seznamu měření a operátor uvidí vizualizaci dat teploty je shromažďování zařízení.

![Měření grafu](./media/howto-set-up-template/measurementsgraph.png)

### <a name="create-an-event-measurement"></a>Vytvořit měření událostí
Chcete-li přidat nového měření událostí, klikněte na **+ nového měření** tlačítko, které se otevře formulář s možnostmi a vyberte typ měrné jednotky. Vyberte **událostí** a zadejte podrobnosti **vytvořit událost** formuláře.

Na tomto formuláři zadejte **zobrazovaný název**, **název pole**a **závažnost** události. Můžete zvolit ze tří dostupných úrovní závažnosti - **chyba**, **upozornění**, a **informace**.  

Například můžete přidat novou událost "Ventilátor Motor chyba".

![Formulář měření událostí](./media/howto-set-up-template/eventmeasurementsform.png)

Když vyberete **Uložit**, **Motor chyba ventilátor** měření se zobrazí v seznamu měření a operátor uvidí vizualizaci dat událostí odesílá zařízení.

![Graf měření událostí](./media/howto-set-up-template/eventmeasurementschart.png)

Chcete-li zobrazit další podrobnosti o události, klikněte na ikonu událostí na graf:

![Podrobnosti události měření](./media/howto-set-up-template/eventmeasurementsdetail.png)


### <a name="create-a-state-measurement"></a>Vytvořit měření stavu
Chcete-li přidat nového měření stavu, klikněte na **+ nového měření** tlačítko, které se otevře formulář s možnostmi a vyberte typ měrné jednotky. Vyberte **stavu** a zadejte podrobnosti **vytvořit stavu** formuláře.

Na tomto formuláři zadejte **zobrazovaný název**, **název pole**a možné **hodnoty** stavu. Každý **hodnota** může mít zobrazovaný název, který se použije při zobrazuje hodnota na grafů a tabulek.

Například můžete přidat nové stavu "Ventilátor režim", který má dvě možné hodnoty, které mohou odesílat zařízení, **operační** a **Zastaveno**.

![Stav měření formuláře](./media/howto-set-up-template/statemeasurementsform.png)

Když vyberete **Uložit**, **ventilátor režimu** měření stavu se zobrazí v seznamu měření a operátor uvidí vizualizaci dat o stavu zařízení odesílá.

![Tabulka stavů měření](./media/howto-set-up-template/statemeasurementschart.png)

V případě, že zařízení v krátkém čase pošle příliš mnoho datových bodů, měření stavu se zobrazí s jiným vizuálem, jak je uvedeno níže. Pokud kliknete na graf, zobrazí se v chronologickém pořadí všechny datové body v daném časovém období. Můžete také zúžit časový rozsah a zobrazte měření na graf vykreslen.

![Podrobnosti o stavu měření](./media/howto-set-up-template/statemeasurementsdetail.png)


## <a name="settings"></a>Nastavení

Nastavení řídí zařízení. Umožňují operátory vaší aplikace zadejte vstupy pro zařízení. Můžete přidat několik nastavení do šablony zařízení, která se zobrazují jako dlaždice na **nastavení** kartě operátorů sloužící k použití. Existuje šest typů nastavení, můžete přidat: číslo, text, datum, přepnutí, rozevíracího seznamu a popisku oddílu.

> [!NOTE]
> Když je skutečné zařízení připojené, věnovat pozornost název nastavení sestav zařízení. Název musí přesně odpovídat **název pole** nastavení.

Nastavení může být v jednom ze tří stavů. Tyto stavy oznamuje zařízení.

- **Synchronizovat**: zařízení změnila, aby odrážela hodnotu nastavení.

- **Čekající**: zařízení je momentálně změna na hodnotu nastavení.

- **Chyba**: zařízení vrátilo chybu.

Například můžete přidat nové nastavení rychlosti ventilátor:

![Nastavení formuláře](./media/howto-set-up-template/settingsform.png)

Po výběru **Uložit**, **ventilátor rychlost** nastavení se zobrazí jako dlaždici a je připravená pro a použít ke změně rychlosti ventilátor zařízení.

> [!NOTE]
> Po vytvoření nová dlaždice můžete vyzkoušet nové nastavení. Nejprve vypnout režimu návrhu v horní pravé části obrazovky:

![Nastavení dlaždice](./media/howto-set-up-template/settingstile.png)

## <a name="properties"></a>Vlastnosti

Vlastnosti jsou přidružené k zařízení, jako je například umístění zařízení a sériové číslo metadat zařízení. Můžete přidat do šablony zařízení, která se zobrazují jako dlaždice na více vlastností **vlastnosti** kartě. Operátor můžete zadat hodnoty pro vlastnosti, když uživatel vytvořit nové zařízení a mohli upravit tyto hodnoty kdykoli. Existuje šest typů vlastností můžete přidat: číslo, text, datum, přepnutí, vlastnosti zařízení a popisku.

Existují dva typy vlastností:

- **Vlastnosti zařízení** jsou hlášené vlastnosti zařízení.
- **Vlastnosti aplikace** jsou vlastnosti uložena výhradně v aplikaci. Zařízení nemá žádné informace o vlastnosti aplikace.

> [!NOTE]
> Pro vlastnosti zařízení když je skutečné zařízení připojené, platíte pozornost název vlastnosti zařízení sestavy. Název musí přesně odpovídat **název pole** vlastnosti. Pro vlastnosti aplikace názvu pole může být nic, co chcete, tak dlouho, dokud se v šabloně zařízení jedinečný název.

Umístění zařízení můžete například přidat jako novou vlastnost:

![Vlastnosti formuláře](./media/howto-set-up-template/propertiesform.png)

Po výběru **Uložit**, zobrazí se umístění zařízení jako dlaždici:

![Dlaždice vlastnosti](./media/howto-set-up-template/propertiestile.png)

> [!NOTE]
> Po vytvoření nová dlaždice, můžete změnit hodnotu vlastnosti. Nejprve vypnout režimu návrhu v horní pravé části obrazovky.

### <a name="create-a-location-property-powered-by-azure-maps"></a>Vytvořit vlastnost umístění používá technologii Azure mapy
Může poskytnout kontext geografické umístění data z Azure IoT centrální a mapovat všechny zeměpisnou šířku a zeměpisnou délku souřadnice adresu nebo jednoduše souřadnice zeměpisné šířky a délky. Tato funkce v Azure IoT centrální používá technologii Azure mapy.

Existují dva typy vlastností umístění, které lze přidat:
- **Umístění jako vlastnost aplikací** kterém bude uložena výhradně v aplikaci. Zařízení nemá žádné informace o vlastnosti aplikace.
- **Umístění jako vlastnost zařízení** které se ohlásí zařízení.

####<a name="adding-location-as-an-application-property"></a>Přidání umístění jako vlastnost aplikací 
Můžete vytvořit umístění vlastnost jako vlastnost aplikací pomocí Azure mapuje v Azure IoT centrální aplikaci. Například můžete přidat adresu instalace zařízení. 

1. Přejděte na kartu vlastností zařízení; Zkontrolujte, zda že je zapnutý režim návrhu.

![Vlastnost umístění](./media/howto-set-up-template/locationcloudproperty1.png)

2. Na kartě vlastností klikněte na umístění.
3. Volitelně můžete nakonfigurujte zobrazované jméno, název pole a počáteční hodnota umístění. 

![Umístění vlastnosti formuláře](./media/howto-set-up-template/locationcloudproperty2.png)

Existují dvě podporované formáty přidat umístění:
- **Umístění jako adresa**
- **Umístění jako souřadnice** 

4. Klikněte na tlačítko Uložit. 

![Pole vlastnosti umístění](./media/howto-set-up-template/locationcloudproperty3.png)

Operátor teď můžete aktualizovat hodnotu umístění ve formě pole umístění. 

####<a name="adding-location-as-a-device-property"></a>Přidání umístění jako vlastnost zařízení 

Vytvořit vlastnost umístění jako vlastnosti zařízení, který je hlášen zařízení.
Například chcete sledovat umístění zařízení.

1.  Přejděte na kartu vlastností zařízení; Zkontrolujte, zda že je zapnutý režim návrhu.
2.  Klikněte na tlačítko Vlastnosti zařízení z knihovny.

![Pole vlastnosti umístění](./media/howto-set-up-template/locationdeviceproperty1.png)

3.  Nakonfigurujte zobrazovaného jména, názvu pole a zvolte "umístění" jako datový typ. 

> [!NOTE]
Název pole musí přesně shodovat na název vlastnosti zařízení sestavy. 

![Pole vlastnosti umístění](./media/howto-set-up-template/locationdeviceproperty2.png)

![Zobrazení operátora vlastnost umístění](./media/howto-set-up-template/locationdeviceproperty2.png)

Teď, když jste nakonfigurovali vaší vlastnost umístění, budete moci přidat mapu, která bude vizualizovat umístění v řídicím panelu zařízení. V tématu Jak [přidat umístění Azure mapy na řídicím panelu](howto-set-up-template.md).




## <a name="rules"></a>Pravidla

Pravidla umožňují operátory monitorování zařízení skoro v reálném čase. Pravidla automaticky vyvolání **akce** například odeslání e-mailu, když se pravidlo spustí. Je jeden typ pravidla k dispozici dnes:

- **Pravidlo telemetrie:** telemetrie pravidlo spustí, když se telemetrie vybrané zařízení protne zadanou prahovou hodnotu. Další informace o [telemetrie pravidla](howto-create-telemetry-rules.md).

## <a name="dashboard"></a>Řídicí panel

Řídicí panel je, kde můžete operátor moct zobrazit informace o zařízení. Jako tvůrce můžete přidat dlaždice k této stránce, který pomůže operátory pochopit, jak se zařízení chovají. Více dlaždice řídicího panelu můžete přidat do šablony zařízení. Existuje šest typů dlaždice řídicího panelu můžete přidat: bitovou kopii, řádek grafu, pruhový graf, klíčového ukazatele výkonu, nastavení a vlastností a popisku.

Například můžete přidat **nastavení a vlastností** dlaždice zobrazíte výběr aktuálními hodnotami vlastností a nastavení:

![Řídicí panel zařízení podrobnosti formuláře](./media/howto-set-up-template/dashboardsettingsandpropertiesform.png)

Nyní když operátor zobrazení řídicího panelu, uvidí tuto dlaždici, která zobrazuje vlastnosti a nastavení zařízení:

![Dlaždice řídicího panelu](./media/howto-set-up-template/dashboardtile.png)

### <a name="add-location-azure-map-in-dashboard"></a>Přidat umístění Azure mapy ve řídicí panel

Pokud jste nakonfigurovali vlastnost umístění jako kroky [vytvoření vlastnosti umístění používá technologii Azure Maps]((howto-set-up-template.md), bude možné vizualizovat umístění pomocí mapy přímo na řídicím panelu zařízení.

1.  Přejděte na kartu řídicí panel zařízení; Zkontrolujte, zda že je zapnutý režim návrhu.
2.  Na řídicím panelu zařízení vyberte mapování z knihovny. 

![Vyberte řídicí panel Azure Mapa umístění](./media/howto-set-up-template/locationcloudproperty4map.png)

3.  Zadejte název a zvolte vlastnost umístění, které jste dříve nakonfigurovali jako součást vlastností vaše zařízení.

![Konfigurace Azure Mapa umístění řídicího panelu](./media/howto-set-up-template/locationcloudproperty5map.png)

4.  Uložit a zobrazí mapy dlaždici zobrazení umístění, které jste vybrali. 

![Řídicí panel umístění vizualizovat Azure mapy](./media/howto-set-up-template/locationcloudproperty6map.png) 

Bude moct změnit velikost mapy požadovaná velikost.

Nyní když operátor zobrazení řídicího panelu, uvidí tento všechny řídicí panel dlaždice jste nakonfigurovali, včetně umístění mapy!

![Řídicí panel Azure Mapa umístění řídicí panel](./media/howto-set-up-template/locationcloudproperty7map.png) 



## <a name="next-steps"></a>Další postup

Teď, když jste se naučili jak nastavit šablonu zařízení v aplikaci Azure IoT centrální, zde je navržené další krok:

> [!div class="nextstepaction"]
> [Vytvořit novou verzi šablony zařízení](howto-version-devicetemplate.md)
