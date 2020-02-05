---
title: 'Kurz: Vytvoření aplikace pro monitorování spotřeby vody pomocí Azure IoT Central'
description: 'Kurz: Naučte se vytvářet aplikace monitorování spotřeby vody pomocí šablon aplikací Azure IoT Central.'
author: miriambrus
ms.author: miriamb
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 1bad4ab6320e757ac766776a95b8dbe6ebaa3259
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2020
ms.locfileid: "77016421"
---
# <a name="tutorial-create-a-water-consumption-monitoring-application-in-iot-central"></a>Kurz: Vytvoření aplikace monitorování spotřeby vody v IoT Central



V tomto kurzu Vás provedeme vytvořením aplikace monitorování spotřeby ve službě Azure IoT Central voda ze šablony aplikace monitorování využití vody IoT Central. 

V tomto kurzu se naučíte: 

> [!div class="checklist"]
> * Použití šablony **monitorování spotřeby vody** Azure IoT Central k vytvoření aplikace monitorování spotřeby vody
> * Řídicí panel pro prohlížení a přizpůsobení operátoru 
> * Prozkoumat šablonu zařízení
> * Prozkoumat simulovaná zařízení
> * Prozkoumat a nakonfigurovat pravidla
> * Konfigurace úloh
> * Přizpůsobení brandingu aplikace pomocí whitelabeling

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:
-  Doporučuje se předplatné Azure. Pokud nemáte předplatné Azure, můžete ho vytvořit na [stránce registrace do Azure](https://aka.ms/createazuresubscription).

## <a name="create-water-consumption-monitoring-app-in-iot-central"></a>Vytvořit aplikaci pro monitorování spotřeby vody v IoT Central

V této části použijete **šablonu monitorování spotřeby vody** Azure IoT Central k vytvoření aplikace monitorování spotřeby vody v IoT Central.

Vytvoření nové aplikace monitorování spotřeby v Azure IoT Centrale:  

1. Přejděte na web [Azure IoT Central domovskou stránku](https://aka.ms/iotcentral) .

    Pokud máte předplatné Azure, přihlaste se pomocí přihlašovacích údajů, které používáte pro přístup k němu, jinak se přihlaste pomocí účet Microsoft:

    ![Zadání účtu organizace](media/tutorial-waterconsumptionmonitoring/sign-in.png)

2. V levém podokně klikněte na **sestavovat** a vyberte kartu **státní správa** . Stránka pro státní správu zobrazuje několik šablon aplikací pro státní správu.

   ![Vytváření šablon aplikací pro státní správu](./media/tutorial-waterconsumptionmonitoring/iotcentral-government-tab-overview1.png)

1. Vyberte šablonu aplikace **monitorování spotřeby vody** . Tato šablona obsahuje ukázkovou šablonu zařízení pro spotřebu vody, simulované zařízení, řídicí panel operátora a předem nakonfigurovaná pravidla monitorování.    

2. Klikněte na **vytvořit aplikaci**a otevře se formulář pro vytváření **nových aplikací** s následujícími poli:
    * **Název aplikace**: ve výchozím nastavení používá aplikace *monitorování spotřeby vody* následovaný jedinečným řetězcem ID, který IoT Central generuje. Volitelně můžete zvolit popisný název aplikace. Můžete změnit název aplikace i později.
    * **Adresa URL**: IoT Central vygeneruje adresu URL na základě názvu aplikace. Můžete zvolit, aby se adresa URL aktualizovala na míru. Adresu URL můžete později změnit. 
    * Pokud máte předplatné Azure, zadejte svůj *adresář, předplatné Azure a oblast*. Pokud předplatné nemáte, můžete povolit **7 dní bezplatnou zkušební verzi** a dokončit požadované kontaktní údaje.  

    Další informace o adresářích a předplatných najdete v [rychlém startu k vytvoření aplikace](../core/quick-deploy-iot-central.md).

5. V dolní části stránky klikněte na tlačítko **vytvořit** . 

    ![Stránka aplikace Azure IoT Central](./media/tutorial-waterconsumptionmonitoring/new-application-waterconsumptionmonitoring.png)

    ![Azure IoT Central vytvořit informace o fakturaci aplikace](./media/tutorial-waterconsumptionmonitoring/new-application-waterconsumptionmonitoring-billinginfo.png)

6. Nyní jste vytvořili aplikaci pro monitorování spotřeby vody pomocí šablony monitorování využití služby Azure IoT Central **voda** .

Blahopřejeme! Dokončili jste tvorbu aplikace pro monitorování kvality vody, která je dodávána s předem nakonfigurovaným:
* Ukázky řídicích panelů operátorů
* Ukázka předem definovaného vodního toku a šablon zařízení ventily
* Simulovaná zařízení v toku a inteligentní ventilace
* Předem konfigurovaná pravidla a úlohy
* Ukázka brandingu pomocí označení bílé 

Je to vaše aplikace a můžete ji kdykoli upravit. Pojďme teď prozkoumat aplikaci a udělat některá vlastní nastavení.  

## <a name="explore-and-customize-operator-dashboard"></a>Řídicí panel pro prohlížení a přizpůsobení operátoru 
Po vytvoření aplikace, kterou nastavíte na řídicím panelu ukázkového operátoru, který se nazývá celosvětový **řídicí panel monitorování spotřeby**.

   ![Řídicí panel monitorování spotřeby vody](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-dashboardfull.png)

Jako tvůrce můžete na řídicím panelu vytvořit a přizpůsobit zobrazení operátorů. Než se pokusíte přizpůsobit, Podívejme se na řídicí panel. 

> [!NOTE]
> Všechna data zobrazená na řídicím panelu jsou založená na simulovaných datech zařízení, která se budou zkoumat v další části. 
  
Řídicí panel se skládá z různých druhů dlaždic:

* **Dlaždice s obrázkem v celém světě**: první dlaždice na řídicím panelu je dlaždice obrázku fiktivního vodního nástroje "World voda". Můžete přizpůsobit dlaždici a umístit vlastní image nebo ji odebrat. 

* **Průměrná voda – dlaždice KUV**: dlaždice klíčového ukazatele výkonu je nakonfigurovaná tak, aby se zobrazovala jako příklad *průměru za posledních 30 minut*. Můžete přizpůsobit dlaždice klíčových ukazatelů výkonu a nastavit na jiný typ a časový rozsah.

* Pak má na řídicím panelu *zařízení* řídicího panelu právo k **uzavření ventilu**, **otevření ventilu**nebo **nastavení pozice ventilu**. Kliknutím na příkazy přejdete na stránku s příkazem simulovaného zařízení. V IoT Central *příkaz* představuje typ *schopnosti zařízení* , který můžete prozkoumat později v **části šablona zařízení** v tomto kurzu.

*  **Mapa oblasti distribuce vody**: mapa používá Azure Maps, kterou můžete nakonfigurovat přímo v Azure IoT Central. Dlaždice Mapa zobrazuje umístění zařízení. Zkuste ukazatel myši najeďte na mapu a vyzkoušejte ovládací prvky na mapě, jako je *přiblížení*, *zmenšení* nebo *rozbalení*. 

    ![Mapa řídicího panelu monitorování spotřeby vody](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-dashboard-map.png)

* Spojnicový graf **průměrného toku vody** a **podmínky prostředí**: můžete vizualizovat jedno nebo několik telemetrií zařízení, které se vykreslí jako spojnicový graf v požadovaném časovém rozsahu.  

* **Průměrný graf tlakového ventilu heatmapu**: můžete zvolit typ vizualizace heatmapu dat telemetrie zařízení, která vás zajímá, a podívat se na distribuci v časovém rozsahu s barevným indexem.

* **Ikona resetovat obsah prahové hodnoty výstrah**: můžete zahrnout volání dlaždic obsahu akce vložení odkazu na stránku akce. V tomto případě se prahová hodnota pro výstrahu při resetování výstrahy provede na **úlohy** aplikace, ve kterých můžete spouštět aktualizace vlastností zařízení, které prozkoumáme později v části **Konfigurace úloh** v tomto kurzu.

* **Dlaždice vlastností**: na řídicím panelu se zobrazí **provozní informace o ventilech**, **prahové hodnoty výstrah toků**a **informace o údržbě**. 


### <a name="customize-dashboard"></a>Přizpůsobení řídicího panelu 

Jako tvůrce můžete přizpůsobit zobrazení pro operátory na řídicím panelu. Můžete vyzkoušet:
1. Klikněte na **Upravit** a přizpůsobte si **řídicí panel monitorování celé světové spotřeby**. Řídicí panel můžete přizpůsobit kliknutím na nabídku **Upravit** . Jakmile je řídicí panel v režimu **úprav** , můžete přidat nové dlaždice nebo můžete nakonfigurovat 

     ![Upravit řídicí panel](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-edit-dashboard.png)

2. Kliknutím na **+ Nový** vytvořte nový řídicí panel a nakonfigurujte úplně od začátku. Můžete mít více řídicích panelů a mezi řídicími panely můžete přecházet z nabídky řídicího panelu. 

## <a name="explore-device-template"></a>Prozkoumat šablonu zařízení
Šablona zařízení v Azure IoT Central definuje schopnost zařízení, což může být telemetrie, vlastnost nebo příkaz. Jako tvůrce můžete v IoT Central definovat jednu nebo více šablon zařízení, které reprezentují schopnost zařízení, ke kterým se připojíte. 
 

Aplikace **monitorování spotřeby vody** se dodává se dvěma referenčními šablonami pro zařízení, které představují *měřič toku* a zařízení s *čipovým ventilem* . 

Postup zobrazení šablony zařízení:

1. V IoT Central v levém navigačním podokně aplikace klikněte na **šablony zařízení** . 
    V seznamu šablony zařízení se zobrazí dvě **měřiče toku** a **Inteligentní ventil** šablon zařízení.

   ![Šablona zařízení](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate.png)

2. Klikněte na šablonu zařízení **měřiče měření** a seznamte se s možnostmi zařízení. 

     ![Měřič toku šablony zařízení](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate-flowmeter.png)

### <a name="customizing-the-device-template"></a>Přizpůsobení šablony zařízení

Zkuste upravit následující:
1. V nabídce šablona zařízení přejděte na **přizpůsobení** .
2. Najít `Temperature` typ telemetrie
3. Aktualizujte **zobrazované jméno** `Temperature` na `Reported temperature`
4. Aktualizovat měrnou jednotku nebo nastavit *minimální hodnotu* a *maximální hodnotu*
5. **Uložit** všechny změny 

### <a name="add-a-cloud-property"></a>Přidat vlastnost cloudu 
1. Přejít na **vlastnost Cloud** z nabídky šablony zařízení
2. Kliknutím na **+ přidat cloudovou vlastnost**přidejte novou vlastnost cloudu. 
    V IoT Central můžete přidat vlastnost, která je pro zařízení relevantní. Cloudová vlastnost může být například prahová hodnota pro výstrahy specifická pro oblast instalace, informace o aktivech nebo jiné informace o údržbě. 
3. **Uložit** všechny změny 
 
### <a name="views"></a>Zobrazení 
Šablona zařízení monitorování spotřeby vody obsahuje předem definovaná zobrazení. Prozkoumejte zobrazení a můžete provádět aktualizace. Zobrazení definují, jak operátory uvidí data zařízení, ale také zapisuje cloudové vlastnosti. 

  ![Zobrazení šablon zařízení](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate-views.png)

### <a name="publish"></a>Publikování 
Pokud jste provedli nějaké změny, nezapomeňte **publikovat** šablonu zařízení. 

### <a name="create-a-new-device-template"></a>Vytvoření nové šablony zařízení 
- Vyberte **+ Nová** a vytvořte novou šablonu zařízení a postupujte podle procesu vytváření. Budete moct vytvořit vlastní šablonu zařízení od začátku nebo můžete zvolit šablonu zařízení z katalogu zařízení Azure. 

## <a name="explore-simulated-devices"></a>Prozkoumat simulovaná zařízení
V IoT Central můžete vytvořit simulovaná zařízení, abyste mohli testovat šablonu a aplikaci pro svoje zařízení. Aplikace **monitorování spotřeby vody** má dvě simulovaná zařízení, která jsou namapovaná na *měřič toku* a šablony zařízení *čipového ventilu* . 

### <a name="to-view-the-devices"></a>Postup zobrazení zařízení:
1. Z IoT Central levém navigačním podokně přejděte do **zařízení** . 

   ![Zařízení](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devices.png)

2. Klikněte na jeden **Inteligentní ventil 1** . 

    ![Zařízení 1](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitor-device1.png)

3.  V **příkazech zařízení** vidíte tři příkazy zařízení *otevřít ventil*, *uzavřít ventil*a *nastavte polohu ventilů* , které jsou funkcemi definovanými v šabloně zařízení *čipového ventilu* . 
4. Prozkoumejte kartu **vlastnosti zařízení** a **řídicí panel zařízení** . 

> [!NOTE]
> Všimněte si, že všechny karty byly nakonfigurovány ze zobrazení šablon zařízení.

### <a name="add-new-devices"></a>Přidat nová zařízení
* Kliknutím na **+ Nový** na kartě **zařízení** přidejte nová zařízení. 

## <a name="explore-and-configure-rules"></a>Prozkoumat a nakonfigurovat pravidla

V Azure IoT Central můžete vytvořit pravidla pro automatické monitorování telemetrie zařízení a aktivovat akce, když se splní jedna nebo víc podmínek. Akce můžou zahrnovat posílání e-mailových oznámení nebo aktivaci akce Microsoft Flow nebo akce Webhooku k odesílání dat do jiných služeb.

Aplikace **monitorování spotřeby vody** , kterou jste vytvořili, má tři předem nakonfigurovaná pravidla.

### <a name="to-view-rules"></a>Postup zobrazení pravidel:
1. Přejděte na **pravidla** z IoT Central levém navigačním podokně. 

   ![Pravidla](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-rules.png)

2. Vyberte a klikněte na **výstrahu s vysokým pH**, což je jedno z předem nakonfigurovaných pravidel v aplikaci.

     ![Výstraha s vysokým pH](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-highflowalert.png)

    Pravidlo `High flow alert` je nakonfigurováno pro kontrolu proti této podmínce `Acidity (pH)` `greater than` `Max flow threshold`. Maximální prahová hodnota toku je cloudová vlastnost definovaná v šabloně zařízení pro *čipové ventily* zařízení. Hodnota `Max flow threshold` je nastavena na instanci zařízení. 

Nyní vytvoříme akci e-mailu.

Postup přidání akce k pravidlu:

1. Vyberte **+ e-mail**. 
1. Jako popisný **Zobrazovaný název** pro akci zadejte *vysoké výstrahy pH* .
    * Zadejte e-mailovou adresu přidruženou k vašemu IoT Central účtu **v.** 
1. Volitelně můžete zadat poznámku, která se má zahrnout do textu e-mailu.
1. Akci dokončete výběrem možnosti **Hotovo** .
1. Kliknutím na **Uložit** uložte a aktivujte nové pravidlo. 

Během několika minut byste měli dostávat e-maily, když je splněna nakonfigurovaná **Podmínka** .

> [!NOTE]
> Aplikace odešle e-mail pokaždé, když je splněna podmínka. **Zakáže** pravidlo pro zastavení přijímání e-mailů od automatizovaného pravidla. 
  
Vytvoření nového pravidla: 
- V levém navigačním podokně vyberte **+ Nový** u **pravidel** .

## <a name="configure-jobs"></a>Konfigurace úloh

V IoT Central úlohy umožňují aktivovat aktualizace zařízení nebo cloudových vlastností na více zařízeních. Kromě vlastností můžete k aktivaci příkazů zařízení na několika zařízeních použít taky úlohy. IoT Central bude pracovní postup automatizovat. 

1. V levém navigačním podokně přejdete na **úlohy** . 
2. Klikněte na **+ Nový** a nakonfigurujte jednu nebo víc úloh. 


## <a name="customize-your-application"></a>Přizpůsobení aplikace 
Jako tvůrce můžete změnit několik nastavení pro přizpůsobení uživatelského prostředí aplikace.

1.  **> přizpůsobení aplikace v nastavení Správa**.
3. Pomocí tlačítka **změnit** můžete zvolit obrázek, který se má nahrát jako **logo aplikace**.
4. Pomocí tlačítka **změnit** můžete zvolit obrázek **ikony prohlížeče** , který se zobrazí na kartách prohlížeče.
5. Můžete také nahradit výchozí barvy v **prohlížeči** přidáním hexadecimálních kódů HTML.

   ![Azure IoT Central přizpůsobení aplikace](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-customize-your-application.png)

1.  Můžete také změnit image aplikace tak, že na tlačítko **správa > nastavení aplikace** a **Vybrat obrázek** zvolíte obrázek, který chcete nahrát jako obrázek aplikace. 
2. Nakonec můžete také změnit **motiv** kliknutím na **Nastavení** v záhlaví aplikace. 

  
## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete tuto aplikaci nadále používat, odstraňte aplikaci pomocí následujících kroků:

1. Otevřete kartu Správa v levém podokně aplikace IoT Central. 
2. Vyberte nastavení aplikace a klikněte na tlačítko Odstranit v dolní části stránky. 


## <a name="next-steps"></a>Další kroky

* Další informace o [konceptech monitorování spotřeby vody](./concepts-waterconsumptionmonitoring-architecture.md)
