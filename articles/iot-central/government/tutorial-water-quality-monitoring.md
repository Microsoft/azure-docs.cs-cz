---
title: 'Kurz: Vytvoření aplikace pro monitorování kvality vody pomocí Azure IoT Central'
description: 'Kurz: Naučte se vytvářet aplikace monitorování kvality vody pomocí šablon aplikací Azure IoT Central.'
author: miriambrus
ms.author: miriamb
ms.date: 12/11/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 3e55970b0603da9be9bf28a50d4c474e34a1cad3
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2021
ms.locfileid: "103017235"
---
# <a name="tutorial-create-a-water-quality-monitoring-application-in-azure-iot-central"></a>Kurz: Vytvoření aplikace pro monitorování kvality vody v Azure IoT Central

Tento kurz vás provede vytvořením aplikace monitorování kvality vody v Azure IoT Central. Aplikaci vytvoříte z šablony aplikace **monitorování kvality** služby Azure IoT Central voda.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Použijte šablonu **monitorování kvality vody** k vytvoření aplikace pro monitorování kvality vody.
> * Prozkoumejte a přizpůsobte řídicí panel operátora.
> * Prozkoumejte šablonu zařízení pro monitorování kvality vody.
> * Prozkoumejte simulovaná zařízení.
> * Prozkoumejte a nakonfigurujte pravidla.
> * Nakonfigurujte úlohy.
> * Přizpůsobte branding aplikace pomocí označení bílé.

## <a name="prerequisites"></a>Požadavky

Doporučujeme, abyste měli k dokončení tohoto kurzu předplatné Azure. Pokud nemáte předplatné Azure, můžete ho vytvořit na [stránce registrace do Azure](https://aka.ms/createazuresubscription).

## <a name="create-a-water-quality-monitoring-application-in-azure-iot-central"></a>Vytvoření aplikace monitorování kvality vody v Azure IoT Central

V této části použijete šablonu **monitorování kvality vody** Azure IoT Central k vytvoření aplikace pro monitorování kvality vody.

1. Přejít na [domovskou stránku Azure IoT Central](https://aka.ms/iotcentral).

    Pokud máte předplatné Azure, přihlaste se pomocí přihlašovacích údajů, které používáte pro přístup k němu. V opačném případě se přihlaste pomocí účet Microsoft:

    ![Přihlaste se k účtu vaší organizace.](./media/tutorial-waterqualitymonitoring/sign-in.png)

1. Vyberte možnost **sestavit** v levém podokně Azure IoT Central a vyberte kartu **státní správa** . V podokně Správa se zobrazí několik šablon aplikací pro státní správu.

    ![Šablony aplikací pro státní správu](./media/tutorial-waterqualitymonitoring/iotcentral-government-tab-overview1.png)

1. Vyberte šablonu aplikace **monitorování kvality vody** . Tato šablona aplikace zahrnuje šablonu zařízení s kvalitou voda, simulovaná zařízení, řídicí panel operátora a předem nakonfigurovaná pravidla monitorování.

1. Vyberte **Vytvořit aplikaci**. Otevře se **nové podokno aplikace** a zobrazí se následující prvky:

    * **Název aplikace**: ve výchozím nastavení má název aplikace **monitorování kvality vody** následovaný jedinečným řetězcem ID, který vygeneruje služba Azure IoT Central. Pokud chcete, můžete zadat zobrazované jméno nebo změnit název aplikace později.
    * **Adresa URL**: můžete zadat libovolnou adresu URL, kterou chcete, nebo změnit hodnotu adresy URL později.
    * Pokud máte předplatné Azure, zadejte hodnoty pro **adresář**, **předplatné Azure** a **oblast**. Pokud předplatné nemáte, můžete zapnout **7 dní bezplatnou zkušební verzi** a doplnit požadované kontaktní údaje.

    Další informace o adresářích a předplatných najdete v tématu rychlý Start [k vytvoření aplikace](../core/quick-deploy-iot-central.md) .

1. V levé dolní části stránky vyberte tlačítko **vytvořit** .

    ![Stránka nové aplikace Azure IoT Central](./media/tutorial-waterqualitymonitoring/new-application-waterqualitymonitoring1.png)

    ![Informace o fakturaci IoT Central nové aplikace v Azure](./media/tutorial-waterqualitymonitoring/new-application-waterqualitymonitoring1-billinginfo.png)

Nyní jste vytvořili aplikaci pro monitorování kvality vody pomocí šablony **monitorování kvality** služby Azure IoT Central voda.

Vaše nová aplikace je dodávána s těmito předem konfigurovanými součástmi:

* Řídicí panely operátorů
* Šablony zařízení monitorování kvality vody
* Simulovaná zařízení pro monitorování kvality vody
* Pravidla a úlohy
* Branding, který používá označení bílé

Svou aplikaci můžete kdykoli upravit.

Dále Prozkoumejte aplikaci a udělejte si vlastní nastavení.

## <a name="explore-and-customize-the-operator-dashboard"></a>Prozkoumejte a přizpůsobte řídicí panel operátora.

Po vytvoření aplikace se otevře podokno **řídicí panel celé světové kvality vody** .

   ![Řídicí panel monitorování kvality vody](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-dashboard1.png)

Jako tvůrce můžete vytvářet a přizpůsobovat zobrazení na řídicím panelu pro použití operátory. Před tím, než se pokusíte upravit, nejprve Prozkoumejte řídicí panel.

Všechna data zobrazená na řídicím panelu jsou založena na simulovaných datech zařízení, která jsou popsána v následující části.

Řídicí panel obsahuje následující typy dlaždic:

* **Dlaždice s obrázkem v celém světě**: první dlaždice v levém horním rohu řídicího panelu je obrázek, který zobrazuje fiktivní nástroj pojmenovaný World. Dlaždici můžete přizpůsobit tak, aby používala vlastní obrázek, nebo můžete dlaždici odebrat.

* **Průměrných dlaždic KUV na ukazateli**: v horní části podokna řídicích panelů jsou dlaždice klíčových ukazatelů výkonu, jako **průměrná hodnota pH za posledních 30 minut** . Můžete přizpůsobit dlaždice klíčových ukazatelů výkonu a nastavit každý na jiný typ a časový rozsah.

* **Mapa oblasti monitorování vody**: Azure IoT Central používá Azure Maps, které můžete přímo nastavit v aplikaci, aby se zobrazilo umístění zařízení. Informace o umístění z aplikace můžete také namapovat na vaše zařízení a pak pomocí Azure Maps zobrazit informace na mapě. Najeďte myší na mapu a vyzkoušejte ovládací prvky.

* **Průměrná hodnota pH – graf tepelného mapování**: můžete vybrat různé grafy vizualizace a zobrazit telemetrii zařízení způsobem, který je pro vaši aplikaci nejvhodnější.

* **Indikátory kritické kvality spojnicový graf**: můžete vizualizovat telemetrii zařízení vykreslenou jako spojnicový graf v časovém rozsahu.  

* **Soustředění pruhového grafu chemických agentů**: můžete vizualizovat telemetrii zařízení v pruhovém grafu.

* **Tlačítko akce**: řídicí panel obsahuje dlaždici pro akce, které operátor může iniciovat přímo z řídicího panelu monitorování. Obnovení vlastností zařízení je například příkladem těchto akcí.

* **Dlaždice seznamu vlastností**: řídicí panel obsahuje více dlaždic vlastností, které reprezentují informace o prahové hodnotě, informace o stavu zařízení a informace o údržbě.

### <a name="customize-the-dashboard"></a>Přizpůsobit řídicí panel

Jako tvůrce můžete přizpůsobit zobrazení na řídicím panelu pro použití operátory.

1. Výběrem **Upravit upravíte** podokno **řídicí panel celé světové kvality vody** . Řídicí panel můžete přizpůsobit tak, že vyberete příkazy v nabídce **Upravit** . Jakmile je řídicí panel v režimu úprav, můžete přidat nové dlaždice nebo můžete nakonfigurovat stávající soubory.

    ![Upravit řídicí panel](./media/tutorial-waterqualitymonitoring/edit-dashboard.png)

1. Vyberte **+ Nová** a vytvořte nový řídicí panel, který můžete nakonfigurovat. Můžete mít více řídicích panelů a mezi nimi můžete přecházet z nabídky řídicího panelu.

## <a name="explore-a-water-quality-monitoring-device-template"></a>Prozkoumat šablonu zařízení pro monitorování kvality vody

Šablona zařízení v Azure IoT Central definuje možnosti zařízení. Dostupné možnosti jsou telemetrie, vlastnosti a příkazy. Jako tvůrce můžete definovat šablony zařízení v Azure IoT Central, které reprezentují možnosti připojených zařízení. Můžete také vytvořit simulovaná zařízení, abyste mohli testovat šablonu a aplikaci pro svoje zařízení.

Vytvořená aplikace pro monitorování kvality vody je dodávána se šablonou zařízení pro monitorování kvality vody.

Postup zobrazení šablony zařízení:

1. Vyberte **šablony zařízení** v levém podokně aplikace v Azure IoT Central.
1. V seznamu šablon zařízení vyberte možnost **monitorování kvality vody**. Otevře se šablona zařízení.

    ![Šablona zařízení](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devicetemplate.png)

### <a name="customize-the-device-template"></a>Přizpůsobení šablony zařízení

Postup přizpůsobení následujících nastavení šablony zařízení:

1. V nabídce šablona zařízení vyberte **přizpůsobit**.
1. Přejít na typ telemetrie **teploty** .
1. Změňte hodnotu **zobrazovaného názvu** na **hlášenou teplotu**.
1. Změňte jednotku měření nebo nastavte **minimální hodnotu** a **maximální hodnotu**.
1. Vyberte **Uložit**.

#### <a name="add-a-cloud-property"></a>Přidat vlastnost cloudu

1. V nabídce šablony zařízení vyberte **vlastnost Cloud**.
1. Pokud chcete přidat novou vlastnost cloudu, vyberte **+ Přidat vlastnost cloudu**. V Azure IoT Central můžete přidat vlastnost, která je relevantní pro zařízení, ale neočekává se, že se zařízení pošle. Jedním z příkladů takových vlastností je prahová hodnota pro výstrahu, která je specifická pro oblast instalace, informace o aktivech nebo informace o údržbě.
1. Vyberte **Uložit**.

### <a name="explore-views"></a>Prozkoumat zobrazení

Šablona zařízení monitorování kvality vody je dodávána s předdefinovanými zobrazeními. Zobrazení definují, jak operátory uvidí data zařízení a nastavují vlastnosti cloudu. Prozkoumejte zobrazení a praktická cvičení, jak provádět změny.

  ![Zobrazení šablon zařízení](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devicetemplate-views.png)

### <a name="publish-the-device-template"></a>Publikování šablony zařízení

Pokud provedete nějaké změny, nezapomeňte vybrat **publikovat** a publikovat šablonu zařízení.

### <a name="create-a-new-device-template"></a>Vytvoření nové šablony zařízení

1. Vyberte **+ Nová** a vytvořte novou šablonu zařízení a postupujte podle procesu vytváření.
1. Vytvořte vlastní šablonu zařízení nebo vyberte šablonu zařízení z katalogu zařízení Azure IoT.

## <a name="explore-simulated-devices"></a>Prozkoumat simulovaná zařízení

Aplikace monitorování kvality vody, kterou jste vytvořili v šabloně aplikace, má dvě simulovaná zařízení. Tato zařízení se mapují na šablonu zařízení pro monitorování kvality vody.

### <a name="view-the-devices"></a>Zobrazit zařízení

1. Vyberte **zařízení** v levém podokně aplikace.

   ![Zařízení](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devices.png)

1. Vyberte jedno simulované zařízení.

    ![Vyberte zařízení 1.](./media/tutorial-waterqualitymonitoring/waterqualitymonitor-device1.png)

1. Na kartě **vlastnosti cloudu** změňte **prahovou hodnotu pro kyselost (pH)** z **8** na **9**.
1. Prozkoumejte kartu **vlastnosti zařízení** a kartu **řídicí panel zařízení** .

> [!NOTE]
> Všechny karty byly nakonfigurovány ze **zobrazení šablon zařízení**.

### <a name="add-new-devices"></a>Přidat nová zařízení

Na kartě **zařízení** vyberte **+ Nová** a přidejte nové zařízení.

## <a name="explore-and-configure-rules"></a>Prozkoumat a nakonfigurovat pravidla

V Azure IoT Central můžete vytvořit pravidla, která automaticky monitorují telemetrii zařízení. Tato pravidla aktivují akci, když je splněna kterákoli z podmínek. Jednou z možných akcí je odesílání e-mailových oznámení. Mezi další možnosti patří akce automatizace nebo akce Webhooku, která odesílá data do jiných služeb.

Vytvořená aplikace pro monitorování kvality vody má dvě předem nakonfigurovaná pravidla.

### <a name="view-rules"></a>Zobrazit pravidla

1. V podokně aplikace vyberte **pravidla** v levém podokně.

   ![Pravidla](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-rules.png)

1. Vyberte **vysoké upozornění pH**, což je jedno z předkonfigurovaných pravidel v aplikaci.

   ![Pravidlo upozornění s vysokým pH](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-highphalert.png)

   Pravidlo **upozornění s vysokým pH** je nakonfigurované tak, aby kontrolovalo stav kyselosti (pH) větší než 8.

Potom do pravidla přidejte akci e-mailu:

1. Vyberte **+ e-mail**.
1. Do pole **Zobrazovaný název** zadejte **výstrahu vysoké pH**.
1. Do pole **do** zadejte e-mailovou adresu přidruženou k vašemu účtu Azure IoT Central.
1. Volitelně můžete zadat poznámku, která se má zahrnout do textu e-mailu.
1. Akci dokončete výběrem možnosti **Hotovo** .
1. Kliknutím na **Uložit** uložte a aktivujte nové pravidlo.

Během několika minut byste měli dostávat e-maily, když je splněna nakonfigurovaná podmínka.

> [!NOTE]
> Aplikace pošle e-mail pokaždé, když je splněna podmínka. Vyberte **Zakázat** pro pravidlo, které zastaví příjem automatizovaného e-mailu z tohoto pravidla.
  
Chcete-li vytvořit nové pravidlo, vyberte **pravidla** v levém podokně aplikace a pak vyberte **+ Nový**.

## <a name="configure-jobs"></a>Konfigurace úloh

Pomocí úloh Azure IoT Central můžete na více zařízeních aktivovat aktualizace vlastností zařízení nebo cloudu. Úlohy můžete použít také k aktivaci příkazů zařízení na více zařízeních. Azure IoT Central automatizuje pracovní postup za vás.

1. Vyberte **úlohy** v levém podokně aplikace.
1. Vyberte **+ Nový** a nakonfigurujte jednu nebo víc úloh.

## <a name="customize-your-application"></a>Přizpůsobení aplikace

Jako tvůrce můžete změnit několik nastavení pro přizpůsobení uživatelského prostředí aplikace.

1. Vyberte možnost **Správa**  >  **přizpůsobení aplikace**.
1. V části **logo aplikace** vyberte **změnit** a zvolte obrázek, který se má nahrát jako logo.
1. V části **ikona prohlížeče** vyberte **změnit** a zvolte bitovou kopii, která se zobrazí na kartách prohlížeče.
1. V části **barvy v prohlížeči** můžete nahradit výchozí hodnoty kódy hexadecimálních barev HTML.
1. Vyberte **Nastavení** a změňte hodnotu **Theme**.

   ![Přizpůsobení aplikace](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-customize-your-application1.png)

### <a name="update-the-application-image"></a>Aktualizace image aplikace

1. Vyberte   >  **nastavení aplikace** pro správu.

1. Pomocí tlačítka **Vybrat obrázek** můžete zvolit obrázek, který se má nahrát jako obrázek aplikace.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete nadále používat aplikaci, odstraňte aplikaci pomocí následujících kroků:

1. Otevřete kartu **Správa** v levém podokně aplikace.
1. Vyberte **nastavení aplikace** a klikněte na tlačítko **Odstranit** .

    ![Odstranění aplikace](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-application-settings-delete-app1.png)

## <a name="next-steps"></a>Další kroky

* Další informace: 

> [!div class="nextstepaction"]
> [Koncepty monitorování kvality vody](./concepts-waterqualitymonitoring-architecture.md).
