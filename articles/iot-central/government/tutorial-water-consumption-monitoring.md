---
title: 'Kurz: Vytvoření aplikace pro monitorování spotřeby vody pomocí Azure IoT Central'
description: 'Kurz: Naučte se vytvářet aplikace pro monitorování spotřeby vody pomocí šablon aplikací Azure IoT Central.'
author: miriambrus
ms.author: miriamb
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 400585d3e5908268708d93ceeefd26a4a5efdd49
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90972389"
---
# <a name="tutorial-create-a-water-consumption-monitoring-application-with-azure-iot-central"></a>Kurz: Vytvoření aplikace monitorování spotřeby vody pomocí Azure IoT Central

V tomto kurzu se dozvíte, jak vytvořit aplikaci monitorování spotřeby ve službě Azure IoT Central voda pomocí šablony aplikace monitorování využití vody Azure IoT Central.

V tomto kurzu se naučíte, jak:

> [!div class="checklist"]
> * K vytvoření aplikace pro monitorování spotřeby vody použijte šablonu monitorování využití vody Azure IoT Central.
> * Prozkoumejte a přizpůsobte řídicí panel operátora.
> * Prozkoumejte šablony zařízení.
> * Prozkoumejte simulovaná zařízení.
> * Prozkoumejte a nakonfigurujte pravidla.
> * Nakonfigurujte úlohy.
> * Přizpůsobte branding aplikace pomocí označení bílé.

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

- Předplatné Azure. Pokud nemáte předplatné Azure, můžete ho vytvořit na [stránce registrace do Azure](https://aka.ms/createazuresubscription).

## <a name="create-a-water-consumption-monitoring-app-with-azure-iot-central"></a>Vytvoření aplikace pro monitorování spotřeby vody pomocí Azure IoT Central

V této části použijete šablonu monitorování spotřeby vody Azure IoT Central k vytvoření aplikace pro monitorování spotřeby vody ve službě Azure IoT Central.

Vytvoření nové aplikace monitorování spotřeby v Azure IoT Centrale:

1. Přejít na web [domovské stránky Azure IoT Central](https://aka.ms/iotcentral) .

    Pokud máte předplatné Azure, přihlaste se pomocí přihlašovacích údajů, které používáte pro přístup k němu. V opačném případě se přihlaste pomocí účet Microsoft.

    ![Zadání účtu organizace](media/tutorial-waterconsumptionmonitoring/sign-in.png)

1. V levém podokně vyberte **sestavení** a vyberte kartu **státní správa** . Stránka pro **státní** správu zobrazuje několik šablon aplikací pro státní správu.

   ![Vytváření šablon aplikací pro státní správu](./media/tutorial-waterconsumptionmonitoring/iotcentral-government-tab-overview1.png)

1. Vyberte šablonu aplikace **monitorování spotřeby vody** .
Tato šablona obsahuje ukázkovou šablonu zařízení pro spotřebu vody, simulované zařízení, řídicí panel operátora a předem nakonfigurovaná pravidla monitorování.

1. Výběrem **vytvořit aplikaci** otevřete formulář pro vytvoření **nové aplikace** s následujícími poli:
    * **Název aplikace**: ve výchozím nastavení používá aplikace *monitorování spotřeby vody* následovaný jedinečným řetězcem ID, který vygeneruje služba Azure IoT Central. Volitelně můžete zvolit popisný název aplikace. Později můžete změnit název aplikace.
    * **Adresa URL**: Azure IoT Central automaticky generuje adresu URL na základě názvu aplikace. Můžete zvolit, aby se adresa URL aktualizovala na míru. Adresu URL můžete později změnit.
    * Pokud máte předplatné Azure, zadejte svůj **adresář**, **předplatné Azure**a informace o **umístění** . Pokud předplatné nemáte, můžete vybrat možnost **bezplatné zkušební verze o 7 dní** a doplnit požadované kontaktní údaje.

    Další informace o adresářích a předplatných najdete v tématu [Vytvoření rychlého startu aplikace](../core/quick-deploy-iot-central.md).

1. V dolní části stránky vyberte **vytvořit** .

    ![Stránka nové aplikace Azure IoT Central](./media/tutorial-waterconsumptionmonitoring/new-application-waterconsumptionmonitoring.png)

    ![Stránka informace o fakturaci Azure IoT Central](./media/tutorial-waterconsumptionmonitoring/new-application-waterconsumptionmonitoring-billinginfo.png)

Nyní jste vytvořili aplikaci pro monitorování spotřeby vody pomocí šablony monitorování využití služby Azure IoT Central voda.

Aplikace monitorování spotřeby vody je dodávána s předem nakonfigurovanými:

* Řídicí panely s ukázkovým operátorem
* Ukázka předdefinovaných šablon vodního toku a ventilů zařízení.
* Simulovaná vodní Flow a zařízení s čipovými ventily.
* Pravidla a úlohy.
* Ukázka brandingu pomocí označení bílé.

Je to vaše aplikace a můžete ji kdykoli upravit. Teď aplikaci prozkoumáme a udělejteme si vlastní nastavení.

## <a name="explore-and-customize-the-operator-dashboard"></a>Prozkoumejte a přizpůsobte řídicí panel operátora.

Po vytvoření aplikace se otevře **řídicí panel ukázka celé světové spotřeby** .

   ![Řídicí panel monitorování spotřeby vody](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-dashboardfull.png)

Jako tvůrce můžete na řídicím panelu vytvořit a přizpůsobit zobrazení operátorů. Pojďme řídicí panel prozkoumat předtím, než se pokusíte ho přizpůsobit.

> [!NOTE]
> Všechna data zobrazená na řídicím panelu jsou založená na simulovaných datech zařízení, která prozkoumáme v další části.
  
Řídicí panel se skládá z různých druhů dlaždic:

* **Dlaždice s obrázkem v celém světě**: první dlaždice na řídicím panelu se nachází na dlaždici obrázku fiktivní vody na celém světě. Dlaždici můžete přizpůsobit vložením vlastního obrázku nebo jeho odebráním.
* **Průměrná voda – dlaždice KUV**: dlaždice klíčového ukazatele výkonu je nakonfigurovaná tak, aby se zobrazovala jako příklad *průměru za posledních 30 minut*. Můžete přizpůsobit dlaždici klíčového ukazatele výkonu a nastavit ji na jiný typ a časový rozsah.
* **Dlaždice příkazů zařízení**: tyto dlaždice zahrnují **uzavírací ventil**, **otevřený ventil**a nastaví dlaždice **Pozice ventilu** . Výběrem příkazů přejdete na stránku s příkazem simulovaného zařízení. V Azure IoT Central je *příkaz* typem *schopnosti zařízení* . Tento koncept prozkoumáme později v tomto kurzu v části "Šablona zařízení".
* **Mapa oblasti distribuce vody**: mapa používá Azure Maps, kterou můžete nakonfigurovat přímo v Azure IoT Central. Na dlaždici mapa se zobrazí umístění zařízení. Najeďte myší na mapu a vyzkoušejte ovládací prvky na mapě, jako je *přiblížení*, *zmenšení*nebo *rozbalení*.

    ![Mapa řídicího panelu monitorování spotřeby vody](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-dashboard-map.png)

* Graf **lineárního toku toků** a **Spojnicový graf stavu prostředí**: můžete vizualizovat jedno nebo několik telemetrií zařízení, které se vykreslí jako spojnicový graf v požadovaném časovém rozsahu.
* **Průměrný graf tlakového ventilu heatmapu**: můžete zvolit typ vizualizace heatmapu dat telemetrie zařízení, která chcete zobrazit distribuované přes časový rozsah s barevným indexem.
* **Ikona resetovat obsah prahových hodnot výstrah**: můžete zahrnout dlaždice obsahu volání do akce a vložit odkaz na stránku akce. V takovém případě vám prahová hodnota pro výstrahu resetování převezme **úlohy**aplikace, ve kterých můžete spouštět aktualizace vlastností zařízení. Tuto možnost prozkoumáme později v části konfigurace úloh v tomto kurzu.
* **Dlaždice vlastností**: řídicí panel zobrazuje **informace o provozním ventilu**, **prahové hodnoty výstrah toků**a **informace o** dlaždicích údržby.

### <a name="customize-the-dashboard"></a>Přizpůsobit řídicí panel

Jako tvůrce můžete na řídicím panelu přizpůsobit zobrazení operátorů.

1. Vyberte **Upravit** a přizpůsobte si **řídicí panel celé světové spotřeby**. Řídicí panel můžete přizpůsobit tak, že vyberete nabídku **Upravit** . Jakmile je řídicí panel v režimu **úprav** , můžete přidat nové dlaždice nebo je můžete nakonfigurovat.

     ![Upravit řídicí panel](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-edit-dashboard.png)

1. Vyberte **+ Nová** a vytvořte nový řídicí panel a nakonfigurujte ho od začátku. Můžete mít více řídicích panelů a mezi řídicími panely můžete přesouvat v nabídce řídicího panelu.

## <a name="explore-the-device-template"></a>Prozkoumat šablonu zařízení

Šablona zařízení v Azure IoT Central definuje schopnost zařízení, což může být telemetrie, vlastnost nebo příkaz. Jako tvůrce můžete v Azure IoT Central definovat jednu nebo více šablon zařízení, které reprezentují možnosti zařízení, ke kterým se připojíte.

Aplikace monitorování spotřeby vody se dodává se dvěma referenčními šablonami pro zařízení, které reprezentují *měřič toku* a zařízení s *čipovým ventilem* .

Postup zobrazení šablony zařízení:

1. V levém podokně aplikace v Azure IoT Central vyberte **šablony zařízení** . V seznamu **šablony zařízení** uvidíte dvě šablony zařízení, **Inteligentní ventil** a **měřič toků**.

   ![Šablona zařízení](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate.png)

1. Vyberte šablonu zařízení **měřiče toku** a seznamte se s možnostmi zařízení.

     ![Měřič toku šablony zařízení](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate-flowmeter.png)

### <a name="customize-the-device-template"></a>Přizpůsobení šablony zařízení

Přizpůsobení šablony zařízení:

1. V nabídce **šablony zařízení** přejděte na **přizpůsobit** .
1. Najděte `Temperature` typ telemetrie.
1. Aktualizujte **Zobrazovaný název** `Temperature` na `Reported temperature` .
1. Aktualizujte jednotku měření nebo nastavte **minimální hodnotu** a **maximální hodnotu**.
1. Výběrem **Uložit** uložte změny.

### <a name="add-a-cloud-property"></a>Přidat vlastnost cloudu

1. V nabídce **šablony zařízení** přejděte do **vlastnosti Cloud** .
1. Kliknutím na **+ přidat cloudovou**vlastnost přidejte novou vlastnost cloudu.
    V Azure IoT Central můžete přidat vlastnost, která je pro zařízení relevantní. Cloudovou vlastností může být například prahová hodnota pro výstrahy specifická pro oblast instalace, informace o aktivech nebo jiné informace o údržbě.
1. Výběrem **Uložit** uložte změny.

### <a name="views"></a>Zobrazení

Šablona zařízení monitorování spotřeby vody je dodávána s předdefinovanými zobrazeními. Prozkoumejte zobrazení a můžete provádět aktualizace. Zobrazení definují, jak operátory uvidí data zařízení, ale také vstupní vlastnosti cloudu.

  ![Zobrazení šablon zařízení](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate-views.png)

### <a name="publish"></a>Publikovat

Pokud jste provedli nějaké změny, nezapomeňte **publikovat** šablonu zařízení.

### <a name="create-a-new-device-template"></a>Vytvoření nové šablony zařízení

Vyberte **+ Nová** a vytvořte novou šablonu zařízení a postupujte podle procesu vytváření.
Můžete vytvořit vlastní šablonu zařízení od začátku nebo můžete zvolit šablonu zařízení z katalogu zařízení Azure.

## <a name="explore-simulated-devices"></a>Prozkoumat simulovaná zařízení

V Azure IoT Central můžete vytvořit simulovaná zařízení, abyste mohli testovat šablonu a aplikaci vašich zařízení. Aplikace monitorování spotřeby vody má dvě simulovaná zařízení, která jsou namapovaná na **měřič toku** a šablony zařízení **čipového ventilu** .

### <a name="view-the-devices"></a>Zobrazit zařízení

1. V levém podokně vyberte **zařízení**  >  **všechna zařízení** .

   ![Podokno všechna zařízení](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devices.png)

1. Vyberte **Inteligentní ventil 1**.

    ![Inteligentní ventil 1](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitor-device1.png)

1. Na kartě **příkazy** uvidíte tři příkazy zařízení (**uzavírací ventil**, **otevřený ventil**a **nastavte polohu ventilu**), které jsou funkcemi definovanými v šabloně zařízení **čipového ventilu** .

1. Prozkoumejte kartu **vlastnosti zařízení** a kartu **řídicí panel zařízení** .

> [!NOTE]
> Všimněte si, že všechny karty jsou nakonfigurované ze zobrazení šablon zařízení.

### <a name="add-new-devices"></a>Přidat nová zařízení

Přidejte nová zařízení tak, že na kartě **zařízení** vyberete **+ Nová** .

## <a name="explore-and-configure-rules"></a>Prozkoumat a nakonfigurovat pravidla

V Azure IoT Central můžete vytvořit pravidla, která automaticky monitorují telemetrii zařízení a triggery, když se splní jedna nebo víc podmínek. Akce můžou zahrnovat posílání e-mailových oznámení nebo aktivaci akce automatizace Microsoftu nebo akce Webhooku k odesílání dat do jiných služeb.

Vytvořená aplikace pro monitorování spotřeby vody má tři předem nakonfigurovaná pravidla.

### <a name="view-rules"></a>Zobrazit pravidla

1. V levém podokně vyberte **pravidla** .

   ![Podokno pravidla](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-rules.png)

1. Vyberte **vysoké upozornění pH**, což je jedno z předkonfigurovaných pravidel v aplikaci.

     ![Výstraha s vysokým pH](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-highflowalert.png)

    `High flow alert`Pravidlo je nakonfigurováno pro kontrolu proti této podmínce `Acidity (pH)` je `greater than` `Max flow threshold` . Maximální prahová hodnota toku je cloudová vlastnost definovaná v šabloně zařízení **čipového ventilu** . Hodnota `Max flow threshold` je nastavená na instanci zařízení.

Nyní vytvoříme akci e-mailu.

Postup přidání akce k pravidlu:

1. Vyberte **+ e-mail**.
1. Jako popisný **Zobrazovaný název** pro akci zadejte **vysoké výstrahy pH** .
1. Zadejte e-mailovou adresu přidruženou k vašemu účtu Azure **IoT Central v aplikaci**.
1. Volitelně můžete zadat poznámku, která se má zahrnout do textu e-mailu.
1. Akci dokončete výběrem možnosti **Hotovo** .
1. Kliknutím na **Uložit** uložte a aktivujte nové pravidlo.

Během několika minut byste měli obdržet e-mail po splnění nakonfigurované podmínky.

> [!NOTE]
> Aplikace pošle e-mail pokaždé, když je splněna podmínka. Vyberte **Zakázat** , pokud chcete pravidlo zakázat, aby se zabránilo přijímání e-mailů od automatizovaného pravidla.
  
Vytvoření nového pravidla:

* Na kartě **pravidla** v levém podokně vyberte **+ Nový** .

## <a name="configure-jobs"></a>Konfigurace úloh

V Azure IoT Central úlohy umožňují aktivovat aktualizace zařízení nebo cloudových vlastností na více zařízeních. Kromě vlastností můžete k aktivaci příkazů zařízení na několika zařízeních použít taky úlohy. Azure IoT Central automatizuje pracovní postup za vás.

1. V levém podokně vyberte **úlohy** .
1. Vyberte **+ Nový**a nakonfigurujte jednu nebo víc úloh.

## <a name="customize-your-application"></a>Přizpůsobení aplikace

Jako tvůrce můžete změnit několik nastavení pro přizpůsobení uživatelského prostředí aplikace.

1. Vyberte možnost **Správa**  >  **přizpůsobení aplikace**.
1. Chcete-li zvolit obrázek, který se má nahrát jako **logo aplikace**, vyberte tlačítko **změnit** .
1. Chcete-li zvolit obrázek **ikony prohlížeče** , který se zobrazí na kartách prohlížeče, vyberte tlačítko **změnit** .
1. Můžete také nahradit výchozí barvy v **prohlížeči** přidáním hexadecimálních kódů HTML.

   ![Výběry pro logo aplikace, ikonu prohlížeče a barvy v prohlížeči](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-customize-your-application.png)

1. Můžete také změnit image aplikace tak, že **Administration**vyberete  >  **nastavení aplikace**pro správu. Chcete-li zvolit obrázek, který se má nahrát jako obrázek aplikace, vyberte tlačítko **Vybrat obrázek** .
1. Nakonec můžete **motiv** změnit také tak, že vyberete ikonu **Nastavení** v pravém horním rohu aplikace.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete tuto aplikaci nadále používat, odstraňte ji.

1. V levém podokně aplikace Azure IoT Central vyberte **Správa** .
1. Vyberte **nastavení aplikace**a potom v dolní části stránky vyberte **Odstranit** .

## <a name="next-steps"></a>Další kroky

* Další informace: 

> [!div class="nextstepaction"]
> [Koncepce monitorování spotřeby vody](./concepts-waterconsumptionmonitoring-architecture.md).
