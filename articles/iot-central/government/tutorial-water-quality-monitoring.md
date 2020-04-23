---
title: 'Kurz: Vytvoření aplikace pro monitorování kvality vody s Azure IoT Central'
description: 'Kurz: Naučte se, jak vytvořit aplikaci pro monitorování kvality vody pomocí šablon aplikací Azure IoT Central.'
author: miriambrus
ms.author: miriamb
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 77ec999d63175f63c1de6e31fdb3f72c963d228c
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "82024468"
---
# <a name="tutorial-create-a-water-quality-monitoring-application-in-azure-iot-central"></a>Kurz: Vytvoření aplikace pro monitorování kvality vody v Azure IoT Central



Tento kurz vás provede vytvořením aplikace pro monitorování kvality vody v Azure IoT Central. Aplikaci vytvoříte ze šablony aplikace **pro monitorování kvality virtuálního prostředí** Azure IoT Central Water.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Pomocí šablony **monitorování kvality vody** vytvořte aplikaci pro monitorování kvality vody.
> * Prozkoumejte a přizpůsobte řídicí panel operátora.
> * Prozkoumejte šablonu zařízení pro sledování kvality vody.
> * Prozkoumejte simulovaná zařízení.
> * Prozkoumejte a nakonfigurujte pravidla.
> * Konfigurace úloh.
> * Přizpůsobte branding aplikací pomocí bílého značení.

## <a name="prerequisites"></a>Požadované součásti

Doporučujeme, abyste měli předplatné Azure k dokončení tohoto kurzu. Pokud nemáte předplatné Azure, můžete ho vytvořit na [stránce registrace Azure](https://aka.ms/createazuresubscription).

## <a name="create-a-water-quality-monitoring-application-in-azure-iot-central"></a>Vytvoření aplikace pro monitorování kvality vody v Azure IoT Central

V této části použijete šablonu **monitorování kvality azure** IoT Central Water k vytvoření aplikace pro monitorování kvality vody.

1. Přejděte na [domovskou stránku Azure IoT Central](https://aka.ms/iotcentral).

    Pokud máte předplatné Azure, přihlaste se pomocí přihlašovacích údajů, které používáte k přístupu. V opačném případě se přihlaste pomocí účtu Microsoft:

    ![Přihlášení k účtu organizace](./media/tutorial-waterqualitymonitoring/sign-in.png)

1. Vyberte **Sestavte** v levém podokně Azure IoT Central a vyberte kartu **Vláda.** Podokno vlády zobrazuje několik šablon vládních aplikací.

    ![Šablony vládních aplikací](./media/tutorial-waterqualitymonitoring/iotcentral-government-tab-overview1.png)

1. Vyberte šablonu aplikace **monitorování kvality vody.** Tato šablona aplikace obsahuje šablonu zařízení kvality vody, simulovaná zařízení, řídicí panel operátora a předem nakonfigurovaná pravidla monitorování.

1. Vyberte **Vytvořit aplikaci**. Otevře se podokno **Nová aplikace** a zobrazí následující prvky:

    * **Název aplikace**: Ve výchozím nastavení je název aplikace **Monitorování kvality vody** následované jedinečným řetězcem ID, který generuje Azure IoT Central. Pokud chcete, můžete zadat zobrazovaný název nebo změnit název aplikace později.
    * **ADRESA URL**: Můžete zadat libovolnou adresu URL nebo hodnotu adresy URL později změnit.
    * Pokud máte předplatné Azure, zadejte hodnoty pro **Adresář**, **předplatné Azure**a **Oblast**. Pokud nemáte předplatné, můžete zapnout **7denní bezplatnou zkušební verzi** a vyplnit požadované kontaktní informace.

    Další informace o adresářích a předplatných najdete v [tématu Vytvoření aplikace](../core/quick-deploy-iot-central.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) rychlý start.

1. V levé dolní části stránky vyberte tlačítko **Vytvořit.**

    ![Stránka nové aplikace Azure IoT Central](./media/tutorial-waterqualitymonitoring/new-application-waterqualitymonitoring1.png)

    ![Informace o fakturaci nové aplikace Azure IoT Central](./media/tutorial-waterqualitymonitoring/new-application-waterqualitymonitoring1-billinginfo.png)

Teď jste vytvořili aplikaci pro monitorování kvality vody pomocí šablony monitorování kvality azure IoT Central **Water.**

Nová aplikace je dodávána s těmito předkonfigurovanými součástmi:

* Řídicí panely operátorů
* Šablony zařízení pro monitorování kvality vody
* Simulovaná zařízení pro sledování kvality vody
* Pravidla a pracovní místa
* Branding, který používá bílé značení

Aplikaci můžete kdykoli upravit.

Dále prozkoumejte aplikaci a proveďte některé úpravy.

## <a name="explore-and-customize-the-operator-dashboard"></a>Prozkoumání a přizpůsobení řídicího panelu operátora

Po vytvoření aplikace se otevře **podokno řídicího panelu kvality vody Wide World.**

   ![Přístrojová deska pro monitorování kvality vody](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-dashboard1.png)

Jako tvůrce můžete vytvářet a přizpůsobovat zobrazení na řídicím panelu pro použití operátory. Než se však pokusíte přizpůsobit, nejprve prozkoumejte řídicí panel.

Všechna data zobrazená na řídicím panelu jsou založena na simulovaných datech zařízení, která jsou popsána v další části.

Řídicí panel obsahuje následující typy dlaždic:

* **Wide World water utility obrázek dlaždice**: První dlaždice v levém horním rohu přístrojové desky je obrázek, který ukazuje fiktivní nástroj s názvem Wide World. Dlaždici můžete přizpůsobit tak, aby používala vlastní obrázek, nebo můžete dlaždici odebrat.

* **Průměrné dlaždice výkonu výkonu pH**: Dlaždice klíčových ukazatelů výkonu, jako **je Průměrné pH za posledních 30 minut,** jsou v horní části panelu řídicího panelu. Dlaždice klíčových ukazatelů výkonu můžete přizpůsobit a nastavit na jiný typ a časový rozsah.

* **Mapa oblasti monitorování vody**: Azure IoT Central používá Azure Maps, které můžete přímo nastavit ve vaší aplikaci k zobrazení umístění zařízení. Můžete také mapovat informace o poloze z vaší aplikace do zařízení a pak pomocí Služby Azure Maps zobrazit informace na mapě. Najeďte přes mapu a vyzkoušejte ovládací prvky.

* **Graf průměrné horečné mapy rozložení pH**: Můžete vybrat různé vizualizační grafy, které zobrazí telemetrii zařízení způsobem, který je nejvhodnější pro vaši aplikaci.

* **Spojnicový graf kritických ukazatelů kvality**: Telemetrii zařízení můžete vizualizovat jako spojnicový graf v časovém rozsahu.  

* **Koncentrace pruhového grafu chemických látek**: Telemetrii zařízení můžete vizualizovat v pruhovém grafu.

* **Tlačítko Akce**: Řídicí panel obsahuje dlaždici pro akce, které může operátor iniciovat přímo z řídicího panelu monitorování. Příkladem takových akcí je obnovení vlastností zařízení.

* **Dlaždice seznamu vlastností**: Řídicí panel obsahuje více dlaždic vlastností, které představují informace o prahové hodnotě, informace o stavu zařízení a informace o údržbě.

### <a name="customize-the-dashboard"></a>Přizpůsobit řídicí panel

Jako tvůrce můžete přizpůsobit zobrazení na řídicím panelu pro použití operátory.

1. Vyberte **Upravit,** chcete-li přizpůsobit **podokno řídicího panelu kvality vody wide world.** Řídicí panel můžete přizpůsobit výběrem příkazů v nabídce **Úpravy.** Po zobrazení řídicího panelu v režimu úprav můžete přidat nové dlaždice nebo nakonfigurovat existující soubory.

    ![Úprava řídicího panelu](./media/tutorial-waterqualitymonitoring/edit-dashboard.png)

1. Výběrem **možnosti + Nový** vytvořte nový řídicí panel, který můžete konfigurovat. Můžete mít více řídicích panelů a můžete mezi nimi procházet z nabídky řídicího panelu.

## <a name="explore-a-water-quality-monitoring-device-template"></a>Prozkoumejte šablonu zařízení pro sledování kvality vody

Šablona zařízení v Azure IoT Central definuje možnosti zařízení. Dostupné možnosti jsou telemetrie, vlastnosti a příkazy. Jako tvůrce můžete definovat šablony zařízení v Azure IoT Central, které představují možnosti připojených zařízení. Můžete také vytvořit simulovaná zařízení pro testování šablony zařízení a aplikace.

Aplikace pro sledování kvality vody, kterou jste vytvořili, je dodávána se šablonou zařízení pro sledování kvality vody.

Zobrazení šablony zařízení:

1. V levém podokně aplikace v Azure IoT Central vyberte **šablony zařízení.**
1. V seznamu šablon zařízení vyberte **Sledování kvality vody**. Tato šablona zařízení se otevře.

    ![Šablona zařízení](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devicetemplate.png)

### <a name="customize-the-device-template"></a>Přizpůsobení šablony zařízení

Procvičte si přizpůsobení následujících nastavení šablony zařízení:

1. V nabídce šablony zařízení vyberte **Přizpůsobit**.
1. Přejděte na typ telemetrie **teploty.**
1. Změňte hodnotu **Zobrazovaného názvu** na **Hlášenou teplotu**.
1. Změňte měrnou jednotku nebo nastavte **hodnotu Min** a **Max**.
1. Vyberte **Uložit**.

#### <a name="add-a-cloud-property"></a>Přidání vlastnosti cloudu

1. V nabídce šablony zařízení vyberte **Vlastnost Cloud**.
1. Chcete-li přidat novou vlastnost cloudu, vyberte **+ Přidat vlastnost cloudu**. Ve službě Azure IoT Central můžete přidat vlastnost, která je relevantní pro zařízení, ale neočekává se, že bude odeslána zařízením. Jedním z příkladů takové vlastnosti je prahová hodnota výstrahy specifická pro oblast instalace, informace o majetku nebo informace o údržbě.
1. Vyberte **Uložit**.

### <a name="explore-views"></a>Prozkoumat zobrazení

Šablona zařízení pro sledování kvality vody je dodávána s předdefinovanými zobrazeními. Zobrazení definují, jak operátoři vidí data zařízení a nastavují vlastnosti cloudu. Prozkoumejte zobrazení a procvičte si provádění změn.

  ![Zobrazení předlohy zařízení](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devicetemplate-views.png)

### <a name="publish-the-device-template"></a>Publikování šablony zařízení

Pokud provedete nějaké změny, nezapomeňte vybrat **Publikovat,** abyste šablonu zařízení publikovali.

### <a name="create-a-new-device-template"></a>Vytvoření nové šablony zařízení

1. Výběrem **možnosti + Nový** vytvořte novou šablonu zařízení a postupujte podle procesu vytváření.
1. Vytvořte si vlastní šablonu zařízení nebo zvolte šablonu zařízení z katalogu zařízení Azure IoT.

## <a name="explore-simulated-devices"></a>Prozkoumejte simulovaná zařízení

Aplikace pro sledování kvality vody, kterou jste vytvořili ze šablony aplikace, má dvě simulovaná zařízení. Tato zařízení mapovat na kvalitu vody monitorovací zařízení šablony.

### <a name="view-the-devices"></a>Zobrazit zařízení

1. V levém podokně aplikace vyberte **Zařízení.**

   ![Zařízení](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devices.png)

1. Vyberte jedno simulované zařízení.

    ![Vybrat zařízení 1](./media/tutorial-waterqualitymonitoring/waterqualitymonitor-device1.png)

1. Na kartě **Vlastnosti cloudu** změňte prahovou hodnotu **acidity (pH)** z **8** na **9**.
1. Prozkoumejte kartu **Vlastnosti zařízení** a kartu **Řídicí panel zařízení.**

> [!NOTE]
> Všechny karty byly nakonfigurovány ze **zobrazení šablony zařízení**.

### <a name="add-new-devices"></a>Přidání nových zařízení

Na kartě **Zařízení** vyberte **+ Nový** a přidejte nové zařízení.

## <a name="explore-and-configure-rules"></a>Prozkoumání a konfigurace pravidel

Ve Službě Azure IoT Central můžete vytvořit pravidla, která automaticky monitorují telemetrii zařízení. Tato pravidla spustí akci, pokud jsou splněny některé z jejich podmínek. Jednou z možných akcí je odesílání e-mailových oznámení. Mezi další možnosti patří akce Microsoft Flow nebo akce webhooku pro odesílání dat do jiných služeb.

Aplikace pro monitorování kvality vody, kterou jste vytvořili, má dvě předkonfigurovaná pravidla.

### <a name="view-rules"></a>Zobrazit pravidla

1. V levém podokně aplikace vyberte **Pravidla.**

   ![Pravidla](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-rules.png)

1. Vyberte **výstrahu vysokého pH**, což je jedno z předkonfigurovaných pravidel v aplikaci.

   ![Pravidlo výstrahy vysokého pH](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-highphalert.png)

   Pravidlo **výstrahy vysokého pH** je nakonfigurováno tak, aby kontrolovalo stav kyselosti (pH) větší než 8.

Dále přidejte k pravidlu akci e-mailu:

1. Vyberte **+ E-mail**.
1. Do pole **Zobrazované jméno** zadejte **výstrahu Vysoké hodnotu pH**.
1. Do pole **Do** zadejte e-mailovou adresu přidruženou k vašemu účtu Azure IoT Central.
1. Volitelně zadejte poznámku, která bude zahrnuta do textu e-mailu.
1. Chcete-li akci dokončit, vyberte **Hotovo.**
1. Chcete-li uložit a aktivovat nové pravidlo, vyberte **Uložit.**

Během několika minut byste měli obdržet e-mail, pokud je splněna nakonfigurovaná podmínka.

> [!NOTE]
> Aplikace odešle e-mail pokaždé, když je splněna podmínka. Chcete-li zastavit příjem automatického e-mailu z tohoto pravidla, vyberte **možnost Zakázat.**
  
Chcete-li vytvořit nové pravidlo, vyberte v levém podokně aplikace možnost **Pravidla** a pak vyberte **možnost +Nový**.

## <a name="configure-jobs"></a>Konfigurace úloh

Díky úlohám Azure IoT Central můžete aktivovat aktualizace zařízení nebo cloudových vlastností na více zařízeních. Úlohy můžete také použít k aktivaci příkazů zařízení na více zařízeních. Azure IoT Central automatizuje pracovní postup za vás.

1. V levém podokně aplikace vyberte **Úlohy.**
1. Vyberte **+Nový** a nakonfigurujte jednu nebo více úloh.

## <a name="customize-your-application"></a>Přizpůsobení aplikace

Jako tvůrce můžete změnit několik nastavení přizpůsobit uživatelské prostředí ve vaší aplikaci.

1. Vyberte **možnost Správa** > **Přizpůsobit aplikaci**.
1. V části **Logo aplikace**vyberte **Změnit,** chcete-li vybrat obrázek, který chcete nahrát jako logo.
1. V části **Ikona Prohlížeče**vyberte **Změnit** a vyberte obrázek, který se zobrazí na kartách prohlížeče.
1. V **části Barvy prohlížeče**můžete nahradit výchozí hodnoty šestnáctkovými barevnými kódy HTML.
1. Výběrem **možnosti Nastavení** změňte hodnotu **motivu**.

   ![Přizpůsobení aplikace](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-customize-your-application1.png)

### <a name="update-the-application-image"></a>Aktualizace bitové kopie aplikace

1. Vyberte**nastavení aplikace** **pro správu** > .

1. Pomocí tlačítka **Vybrat obrázek** vyberte obrázek, který chcete nahrát jako obrázek aplikace.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete aplikaci nadále používat, odstraňte ji pomocí následujících kroků:

1. Otevřete kartu **Správa** v podokně aplikace zcela vlevo.
1. Vyberte **Nastavení aplikace** a vyberte tlačítko **Odstranit.**

    ![Odstranění aplikace](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-application-settings-delete-app1.png)

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o [konceptech monitorování kvality vody](./concepts-waterqualitymonitoring-architecture.md).
