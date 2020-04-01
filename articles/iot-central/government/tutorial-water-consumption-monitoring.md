---
title: 'Kurz: Vytvoření aplikace pro monitorování spotřeby vody s Azure IoT Central'
description: 'Kurz: Naučte se vytvořit aplikaci pro monitorování spotřeby vody pomocí šablon aplikací Azure IoT Central.'
author: miriambrus
ms.author: miriamb
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 7f00947504e5c6355379ce1e400fd2325016e05a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77122057"
---
# <a name="tutorial-create-a-water-consumption-monitoring-application-with-azure-iot-central"></a>Kurz: Vytvoření aplikace pro monitorování spotřeby vody pomocí Azure IoT Central

Tento kurz ukazuje, jak vytvořit aplikaci pro monitorování spotřeby vody Azure IoT Central pomocí šablony aplikace monitorování spotřeby vody Azure IoT Central.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Pomocí šablony monitorování spotřeby vody Azure IoT Central vytvořte aplikaci pro monitorování spotřeby vody.
> * Prozkoumejte a přizpůsobte řídicí panel operátora.
> * Prozkoumejte šablony zařízení.
> * Prozkoumejte simulovaná zařízení.
> * Prozkoumejte a nakonfigurujte pravidla.
> * Konfigurace úloh.
> * Přizpůsobte si značku aplikace pomocí bílého značení.

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

- Předplatné Azure. Pokud nemáte předplatné Azure, můžete ho vytvořit na [stránce registrace Azure](https://aka.ms/createazuresubscription).

## <a name="create-a-water-consumption-monitoring-app-with-azure-iot-central"></a>Vytvoření aplikace pro monitorování spotřeby vody pomocí Azure IoT Central

V této části použijete šablonu monitorování spotřeby vody Azure IoT Central k vytvoření aplikace pro monitorování spotřeby vody v Azure IoT Central.

Vytvoření nové aplikace pro monitorování spotřeby vody Azure IoT Central:

1. Přejděte na web [domovské stránky Azure IoT Central.](https://aka.ms/iotcentral)

    Pokud máte předplatné Azure, přihlaste se pomocí přihlašovacích údajů, které používáte k přístupu. V opačném případě se přihlaste pomocí účtu Microsoft.

    ![Zadání účtu organizace](media/tutorial-waterconsumptionmonitoring/sign-in.png)

1. V levém podokně vyberte **Sestavit** a vyberte kartu **Vláda.** Na stránce **Vláda** se zobrazí několik šablon vládních aplikací.

   ![Vytváření šablon aplikací pro státní správu](./media/tutorial-waterconsumptionmonitoring/iotcentral-government-tab-overview1.png)

1. Vyberte šablonu aplikace **monitorování spotřeby vody.**
Tato šablona obsahuje šablonu ukázkového zařízení pro spotřebu vody, simulované zařízení, řídicí panel operátora a předem nakonfigurovaná pravidla monitorování.

1. Výběrem **možnosti Vytvořit aplikaci** otevřete formulář Pro vytvoření **nové aplikace** s následujícími poli:
    * **Název aplikace**: Ve výchozím nastavení aplikace používá *monitorování spotřeby vody* následované jedinečným řetězcem ID, který generuje Azure IoT Central. Volitelně zvolte popisný název aplikace. Název aplikace můžete později také změnit.
    * **Adresa URL**: Azure IoT Central automaticky vygeneruje adresu URL na základě názvu aplikace. Adresu URL můžete aktualizovat podle svých představ. Adresu URL můžete také později změnit.
    * Pokud máte předplatné Azure, zadejte informace o **adresáři**, **předplatném Azure**a **umístění.** Pokud nemáte předplatné, můžete vybrat možnost **7denní bezplatné zkušební verze** a vyplnit požadované kontaktní informace.

    Další informace o adresářích a předplatných naleznete [v tématu Vytvoření rychlého spuštění aplikace](../core/quick-deploy-iot-central.md).

1. V dolní části stránky vyberte **Vytvořit.**

    ![Stránka aplikace Azure IoT Central Nová aplikace](./media/tutorial-waterconsumptionmonitoring/new-application-waterconsumptionmonitoring.png)

    ![Stránka s informacemi o centrální fakturaci Azure IoT](./media/tutorial-waterconsumptionmonitoring/new-application-waterconsumptionmonitoring-billinginfo.png)

Teď jste vytvořili aplikaci pro monitorování spotřeby vody pomocí šablony monitorování spotřeby vody Azure IoT Central.

Aplikace pro monitorování spotřeby vody je dodávána s předkonfigurovanou:

* Ukázkové řídicí panely operátorů.
* Ukázka předdefinovaných šablon průtoku vody a ventilů.
* Simulovaný průtok vody a inteligentní ventilová zařízení.
* Pravidla a pracovní místa.
* Ukázka značky pomocí bílého značení.

Je to vaše aplikace, a můžete ji kdykoliv upravit. Nyní pojďme prozkoumat aplikaci a provést některé úpravy.

## <a name="explore-and-customize-the-operator-dashboard"></a>Prozkoumání a přizpůsobení řídicího panelu operátora

Po vytvoření aplikace se otevře ukázkový **řídicí panel spotřeby vody Wide World.**

   ![Přístrojová deska pro monitorování spotřeby vody](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-dashboardfull.png)

Jako tvůrce můžete vytvářet a přizpůsobovat zobrazení na řídicím panelu pro operátory. Než se ho pokusíte přizpůsobit, prozkoumáme řídicí panel.

> [!NOTE]
> Všechna data zobrazená na řídicím panelu jsou založena na simulovaných datech zařízení, která prozkoumáme v další části.
  
Přístrojová deska se skládá z různých druhů dlaždic:

* **Wide World Water Utility obrázek dlaždice:** První dlaždice v přístrojové desce je obraz dlaždice fiktivní vody utility Wide World Water Water. Dlaždici můžete přizpůsobit vložením vlastního obrázku nebo jeho odebráním.
* **Dlaždice klíčového ukazatele výkonu průměrného průtoku vody**: Dlaždice klíčového ukazatele výkonu je nakonfigurována tak, aby jako příklad *zobrazovala průměr za posledních 30 minut*. Dlaždici klíčový ukazatel výkonu můžete přizpůsobit a nastavit na jiný typ a časový rozsah.
* **Dlaždice příkazů zařízení**: Tyto dlaždice zahrnují **dlaždice Zavřít ventil**, Otevřený **ventil**a Nastavit dlaždice **polohy ventilu.** Výběr příkazů vás přenese na stránku příkazů simulovaného zařízení. Ve službě Azure IoT Central je *příkaz* typ *schopnosti zařízení.* Tento koncept prozkoumáme později v části "Šablona zařízení" v tomto kurzu.
* **Mapa distribuční oblasti vody**: Mapa používá Azure Maps, kterou můžete nakonfigurovat přímo v Azure IoT Central. Na dlaždici mapy se zobrazí umístění zařízení. Najeďte přes mapu a vyzkoušejte ovládací prvky nad mapou, třeba *přiblížení*, *přiblížení*nebo *rozbalení*.

    ![Mapa přístrojové desky pro monitorování spotřeby vody](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-dashboard-map.png)

* **Graf průměrného průtoku vody** a **spojnicový graf Stav prostředí**: Můžete vizualizovat jeden nebo více telemoráží zařízení vykreslené jako spojnicový graf v požadovaném časovém rozsahu.
* **Graf heatmapy průměrného tlaku ventilu**: Můžete zvolit typ vizualizace heatmapy dat telemetrie zařízení, která chcete zobrazit rozložená v časovém rozsahu s barevným indexem.
* **Obnovit dlaždice obsahu prahů výstrah**: Můžete zahrnout dlaždice obsahu s výzvou k akci a vložit odkaz na stránku akce. V takovém případě prahová hodnota výstrahy obnovení přejdete do aplikace **Úlohy**, kde můžete spustit aktualizace vlastností zařízení. Tuto možnost prozkoumáme později v části Konfigurace úloh v tomto kurzu.
* **Dlaždice vlastností**: Na řídicím panelu jsou **zobrazeny provozní informace ventilu**, **prahové hodnoty upozornění na průtok**a dlaždice informací o **údržbě.**

### <a name="customize-the-dashboard"></a>Přizpůsobit řídicí panel

Jako tvůrce můžete přizpůsobit zobrazení v řídicím panelu pro operátory.

1. Výběrem **možnosti Upravit** přizpůsobíte **řídicí panel spotřeba vody v celém světě**. Řídicí panel můžete přizpůsobit výběrem nabídky **Úpravy.** Po zobrazení řídicího panelu v režimu **úprav** můžete přidat nové dlaždice nebo je můžete nakonfigurovat.

     ![Upravit řídicí panel](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-edit-dashboard.png)

1. Výběrem **možnosti + Nový** vytvořte nový řídicí panel a nakonfigurujte jej od začátku. Můžete mít více řídicích panelů a můžete se pohybovat mezi řídicími panely v nabídce řídicího panelu.

## <a name="explore-the-device-template"></a>Prozkoumejte šablonu zařízení

Šablona zařízení v Azure IoT Central definuje možnosti zařízení, které může být telemetrie, vlastnost nebo příkaz. Jako tvůrce můžete definovat jednu nebo více šablon zařízení v Azure IoT Central, které představují možnosti zařízení, která se připojíte.

Aplikace pro monitorování spotřeby vody je dodávána se dvěma šablonami referenčních zařízení, které představují *průtokoměr* a *inteligentní ventilové* zařízení.

Zobrazení šablony zařízení:

1. V levém podokně aplikace v Azure IoT Central vyberte **šablony zařízení.** V seznamu **Šablony zařízení** uvidíte dvě šablony zařízení, **inteligentní ventil** a **průtokoměr**.

   ![Šablona zařízení](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate.png)

1. Vyberte šablonu **zařízení průtokoměru** a seznamte se s možnostmi zařízení.

     ![Průtokoměr šablony zařízení](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate-flowmeter.png)

### <a name="customize-the-device-template"></a>Přizpůsobení šablony zařízení

Přizpůsobení šablony zařízení:

1. V nabídce **Šablony zařízení** přejděte na **Přizpůsobit.**
1. Najděte `Temperature` typ telemetrie.
1. Aktualizujte **zobrazovaný název** na `Temperature` . `Reported temperature`
1. Aktualizujte měrnou jednotku nebo nastavte **hodnotu Min** a **Max**.
1. Vyberte **Uložit,** chcete-li uložit všechny změny.

### <a name="add-a-cloud-property"></a>Přidání vlastnosti cloudu

1. Přejděte do nabídky **Vlastnosti cloudu** v nabídce **Šablony zařízení.**
1. Přidejte novou vlastnost cloudu výběrem **+ Přidat vlastnost Cloud**.
    Ve službě Azure IoT Central můžete přidat službu, která je relevantní pro zařízení. Jako příklad může být cloudová vlastnost prahová hodnota upozornění specifická pro oblast instalace, informace o majetku nebo jiné informace o údržbě.
1. Vyberte **Uložit,** chcete-li uložit všechny změny.

### <a name="views"></a>Zobrazení

Šablona zařízení monitoru spotřeby vody je dodávána s předdefinovanými zobrazeními. Prozkoumejte zobrazení a můžete provádět aktualizace. Zobrazení definují, jak operátoři vidí data zařízení, ale také vlastnosti vstupního cloudu.

  ![Zobrazení předlohy zařízení](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate-views.png)

### <a name="publish"></a>Publikování

Pokud jste provedli nějaké změny, nezapomeňte šablonu zařízení **publikovat.**

### <a name="create-a-new-device-template"></a>Vytvoření nové šablony zařízení

Výběrem **možnosti + Nový** vytvořte novou šablonu zařízení a postupujte podle procesu vytváření.
Vlastní šablonu zařízení můžete vytvořit od začátku nebo si můžete vybrat šablonu zařízení z katalogu zařízení Azure.

## <a name="explore-simulated-devices"></a>Prozkoumejte simulovaná zařízení

Ve Službě Azure IoT Central můžete vytvořit simulovaná zařízení pro testování šablony zařízení a aplikace. Aplikace pro monitorování spotřeby vody má dvě simulovaná zařízení mapovaná na šablony **průtokoměru** a **inteligentního ventilu.**

### <a name="view-the-devices"></a>Zobrazit zařízení

1. V levém podokně vyberte **Zařízení** > **Všechna zařízení.**

   ![Podokno Všechna zařízení](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devices.png)

1. Vyberte **inteligentní ventil 1**.

    ![Inteligentní ventil 1](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitor-device1.png)

1. Na kartě **Příkazy** můžete zobrazit tři příkazy zařízení **(Zavřít ventil**, **Otevřený ventil**a **Nastavit polohu ventilu),** které jsou funkcedefinované v šabloně zařízení **Smart Valve.**

1. Prozkoumejte kartu **Vlastnosti zařízení** a kartu **Řídicí panel zařízení.**

> [!NOTE]
> Všimněte si, že všechny karty jsou konfigurovány ze zobrazení šablony zařízení.

### <a name="add-new-devices"></a>Přidání nových zařízení

Přidejte nová zařízení tak, že na kartě **Zařízení** vyberete **+ Nový.**

## <a name="explore-and-configure-rules"></a>Prozkoumání a konfigurace pravidel

Ve službě Azure IoT Central můžete vytvořit pravidla pro automatické sledování telemetrie zařízení a aktivační akce, když je splněna jedna nebo více podmínek. Akce mohou zahrnovat odesílání e-mailových oznámení nebo aktivaci akce Microsoft Power Automate nebo akce webhooku pro odesílání dat do jiných služeb.

Vytvořená aplikace pro monitorování spotřeby vody má tři předkonfigurovaná pravidla.

### <a name="view-rules"></a>Zobrazit pravidla

1. V levém podokně vyberte **Pravidla.**

   ![Podokno Pravidla](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-rules.png)

1. Vyberte **výstrahu vysokého pH**, což je jedno z předkonfigurovaných pravidel v aplikaci.

     ![Výstraha vysokého pH](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-highflowalert.png)

    Pravidlo `High flow alert` je nakonfigurováno pro `Acidity (pH)` `greater than` kontrolu `Max flow threshold`proti stavu je . Maximální prahová hodnota průtoku je vlastnost cloudu definovaná v šabloně zařízení **Smart Valve.** Hodnota `Max flow threshold` je nastavena na instanci zařízení.

Nyní vytvoříme e-mailovou akci.

Přidání akce do pravidla:

1. Vyberte **+ E-mail**.
1. Zadejte **výstrahu Vysoké ho uH** jako popisný **zobrazovaný název** akce.
1. Zadejte e-mailovou adresu přidruženou k účtu Azure IoT Central v **části Do**.
1. Volitelně zadejte poznámku, která bude zahrnuta do textu e-mailu.
1. Chcete-li akci dokončit, vyberte **Hotovo.**
1. Chcete-li uložit a aktivovat nové pravidlo, vyberte **Uložit.**

Během několika minut byste měli po splnění nakonfigurované podmínky obdržet e-mail.

> [!NOTE]
> Aplikace odešle e-mail pokaždé, když je splněna podmínka. Výběrem **možnosti Zakázat** zakážete pravidlo, chcete-li zastavit příjem e-mailů z automatického pravidla.
  
Vytvoření nového pravidla:

* Na kartě **Pravidla** v levém podokně vyberte **možnost + Nový.**

## <a name="configure-jobs"></a>Konfigurace úloh

Ve službě Azure IoT Central umožňují úlohy spouštět aktualizace zařízení nebo cloudových vlastností na více zařízeních. Kromě vlastností můžete také použít úlohy k aktivaci příkazů zařízení na více zařízeních. Azure IoT Central automatizuje pracovní postup za vás.

1. V levém podokně vyberte **Úlohy.**
1. Vyberte **+ Nový**a nakonfigurujte jednu nebo více úloh.

## <a name="customize-your-application"></a>Přizpůsobení aplikace

Jako tvůrce můžete změnit několik nastavení přizpůsobit uživatelské prostředí ve vaší aplikaci.

1. Vyberte **možnost Správa** > **Přizpůsobit aplikaci**.
1. Chcete-li vybrat obrázek, který chcete nahrát jako **logo aplikace**, vyberte tlačítko **Změnit.**
1. Chcete-li vybrat obrázek **ikony prohlížeče,** který se zobrazí na kartách prohlížeče, vyberte tlačítko **Změnit.**
1. Výchozí **barvy prohlížeče** můžete také nahradit přidáním šestnáctkových barevných kódů HTML.

   ![Výběry loga aplikace, ikony prohlížeče a barev prohlížeče](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-customize-your-application.png)

1. Bitové kopie aplikací můžete také změnit výběrem**možnosti Nastavení aplikace**pro **správu** > . Chcete-li vybrat obrázek, který chcete nahrát jako obraz aplikace, vyberte tlačítko **Vybrat obrázek.**
1. Nakonec můžete také změnit **motiv** výběrem ikony **Nastavení** v pravém horním rohu aplikace.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete pokračovat v používání této aplikace, odstraňte ji.

1. V levém podokně aplikace Azure IoT Central vyberte **Správa.**
1. Vyberte **Nastavení aplikace**a v dolní části stránky vyberte **Odstranit.**

## <a name="next-steps"></a>Další kroky

* Další informace o [konceptech monitorování spotřeby vody](./concepts-waterconsumptionmonitoring-architecture.md).
