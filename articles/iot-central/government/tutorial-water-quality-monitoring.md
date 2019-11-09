---
title: Vytvoření aplikace pro monitorování kvality vody pomocí Azure IoT Central
description: Naučte se vytvářet aplikace monitorování kvality vody pomocí šablon aplikací Azure IoT Central.
author: miriambrus
ms.author: miriamb
ms.date: 10/23/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 51c84410de39516312d2058eeda575023dbe32ab
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2019
ms.locfileid: "73890765"
---
# <a name="tutorial-create-a-water-quality-monitoring-application-in-iot-central"></a>Kurz: Vytvoření aplikace pro monitorování kvality vody v IoT Central

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Tento kurz vás provede vytvořením aplikace monitorování kvality služby Azure IoT Central voda ze šablony IoT Central monitorování kvality vody. 

V tomto kurzu se naučíte: 

> [!div class="checklist"]
> * Použití šablony **monitorování kvality vody** Azure IoT Central k vytvoření aplikace pro monitorování kvality vody
> * Řídicí panel pro prohlížení a přizpůsobení operátoru 
> * Prozkoumat šablonu zařízení monitorování kvality vody
> * Prozkoumat simulovaná zařízení
> * Prozkoumat a nakonfigurovat pravidla
> * Konfigurace úloh
> * Přizpůsobení brandingu aplikace pomocí whitelabeling


## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete:
-  Doporučuje se předplatné Azure. Pokud nemáte předplatné Azure, můžete ho vytvořit na [stránce registrace do Azure](https://aka.ms/createazuresubscription).


## <a name="create-water-quality-monitoring-app-in-iot-central"></a>Vytvořit aplikaci pro monitorování kvality vody v IoT Central

V této části použijeme **šablonu monitorování kvality vody** Azure IoT Central k vytvoření vaší aplikace monitorování kvality vody v IoT Central.


Vytvoření nové aplikace pro monitorování kvality IoT Central v Azure:  

1. Přejděte na web [Azure IoT Central domovskou stránku](https://aka.ms/iotcentral) .

      Pokud máte předplatné Azure, přihlaste se pomocí přihlašovacích údajů, které používáte pro přístup k němu, jinak se přihlaste pomocí účet Microsoft:

    ![Zadání účtu organizace](./media/tutorial-waterqualitymonitoring/sign-in.png)

2. V levém podokně klikněte na **sestavovat** a vyberte kartu **státní správa** . Stránka pro státní správu zobrazuje několik šablon aplikací pro státní správu.

    ![Vytváření šablon aplikací pro státní správu](./media/tutorial-waterqualitymonitoring/iotcentral-government-tab-overview1.png)


1. Vyberte šablonu aplikace **monitorování kvality vody** . Tato šablona obsahuje ukázkovou šablonu zařízení v kvalitě voda, simulované zařízení, řídicí panel operátora a předem nakonfigurovaná pravidla monitorování.    

2. Klikněte na **vytvořit aplikaci**a otevře se formulář pro vytváření **nových aplikací** s následujícími poli:
    * **Název aplikace** Ve výchozím nastavení používá aplikace *monitorování kvality vody* následovaný jedinečným řetězcem ID, který IoT Central generuje. Volitelně můžete zvolit popisný název aplikace. Můžete změnit název aplikace i později.
    * **Adresa URL** – Volitelně můžete zvolit požadovanou adresu URL. Adresu URL můžete později změnit. 
    * Pokud máte předplatné Azure, zadejte svůj *adresář, předplatné Azure a oblast*. Pokud předplatné nemáte, můžete povolit **7 dní bezplatnou zkušební verzi** a dokončit požadované kontaktní údaje.  

    Další informace o adresářích a předplatných najdete v [rychlém startu k vytvoření aplikace](../preview/quick-deploy-iot-central.md).

5. V dolní části stránky klikněte na tlačítko **vytvořit** . 

    ![Stránka vytvoření aplikace Azure IoT Central](./media/tutorial-waterqualitymonitoring/new-application-waterqualitymonitoring1.png)


6. Nyní jste vytvořili aplikaci pro monitorování kvality vody pomocí **šablony monitorování kvality**služby Azure IoT Central voda. 

Vaše nově vytvořená aplikace je dodávána s předem nakonfigurovaným:
* Ukázky řídicích panelů operátorů
* Ukázka předem definovaných šablon zařízení monitorování kvality vody
* Monitorovaná zařízení pro sledování kvality vody
* Předem konfigurovaná pravidla a úlohy
* Ukázka brandingu pomocí označení bílé 

Je to vaše aplikace a můžete ji kdykoli upravit. Pojďme teď prozkoumat aplikaci a udělat některá vlastní nastavení.  


## <a name="explore-and-customize-operator-dashboard"></a>Řídicí panel pro prohlížení a přizpůsobení operátoru 
Po vytvoření aplikace, kterou nastavíte, na **řídicím panelu pro monitorování kvality velkých vod**.


   ![Řídicí panel monitorování kvality vody](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-dashboard1.png)

Jako tvůrce můžete na řídicím panelu vytvořit a přizpůsobit zobrazení operátorů. Než se pokusíte přizpůsobit, Podívejme se na řídicí panel. 

Všechna data zobrazená na řídicím panelu jsou založená na simulovaných datech zařízení, která se budou zkoumat v další části. 

Řídicí panel se skládá z různých druhů dlaždic:

* **Dlaždice s obrázkem v celém světě**: první dlaždice na řídicím panelu je dlaždice obrázku fiktivního vodního nástroje "World voda". Můžete přizpůsobit dlaždici a umístit vlastní image nebo ji odebrat. 

* **Průměrná hodnota indikátoru výkonnosti ukazatele KPI**: vidíte, že **v posledních 30 minutách**jsou dlaždice klíčových ukazatelů výkonu nahoře, jako je průměrně pH. Můžete přizpůsobit dlaždice klíčových ukazatelů výkonu a nastavit na jiný typ a časový rozsah.

*  **Mapa oblasti monitorování vody**: IoT Central používá Azure Maps, které lze přímo nastavit v aplikaci a zobrazit umístění zařízení. Z aplikace můžete také namapovat informace o umístění do zařízení a použít Azure Maps k jeho zobrazení na mapě. Zkuste ukazatel myši najeďte na mapu a zkuste ovládací prvky na mapě. 

* **Průměrně heatmapu graf distribuce**: můžete zvolit různé grafy vizualizace, abyste zobrazili data telemetrie zařízení způsobem, který je pro vaši aplikaci nejvhodnější. Heatmapu 

* **Indikátory kritické kvality spojnicový graf**: můžete vizualizovat jedno nebo několik telemetrií zařízení vykreslených jako spojnicový graf v požadovaném časovém rozsahu.  

* **Soustředění pruhového grafu chemických agentů**: v ukázce můžete vizualizovat jedno nebo více dat telemetrie zařízení v pruhovém grafu. 

* **Tlačítko akce**: řídicí panel obsahuje příklad dlaždice akce, kterou operátor může iniciovat přímo z řídicího panelu monitorování, jako je například inicializace akce pro resetování vlastností zařízení. 

* **Vlastnosti seznamu dlaždic**: řídicí panel má více dlaždic vlastností představujících informace o prahových hodnotách, informace o stavu zařízení a informace o údržbě. 


### <a name="customize-dashboard"></a>Přizpůsobení řídicího panelu 

Jako tvůrce můžete přizpůsobit zobrazení pro operátory na řídicím panelu. 
1. Klikněte na **Upravit** a přizpůsobte si **řídicí panel monitorování celé světové kvality vody**. Řídicí panel můžete přizpůsobit kliknutím na nabídku **Upravit** . Jakmile je řídicí panel v režimu **úprav** , můžete přidat nové dlaždice nebo můžete nakonfigurovat

    ![Upravit řídicí panel](./media/tutorial-waterqualitymonitoring/edit-dashboard.png)

2. Kliknutím na **+ Nový** vytvořte nový řídicí panel a nakonfigurujte úplně od začátku. Můžete mít více řídicích panelů a mezi řídicími panely můžete přecházet z nabídky řídicího panelu.

## <a name="explore-water-quality-monitor-device-template"></a>Prozkoumat šablonu zařízení monitorování kvality vody
Šablona zařízení v Azure IoT Central definuje schopnost zařízení, což může být telemetrie, vlastnosti a příkazy. Jako tvůrce můžete definovat šablony zařízení v IoT Central, které reprezentují schopnost zařízení, která budou připojena. V IoT Central můžete také vytvořit simulovaná zařízení, abyste mohli testovat šablonu a aplikaci vaší zařízení. 
 

Aplikace pro **monitorování kvality vody** , kterou jste vytvořili z šablony aplikace, je dodávána se šablonou zařízení referenční kvality pro monitorování kvality vody.

Postup zobrazení šablony zařízení:

1.  V IoT Central v levém navigačním podokně aplikace klikněte na **šablony zařízení** . 
2. V seznamu šablony zařízení se zobrazí **monitorování kvality vody**. Otevřete kliknutím na název.

    ![Šablona zařízení](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devicetemplate.png)

### <a name="customizing-the-device-template"></a>Přizpůsobení šablony zařízení

Zkuste upravit následující:
1. V nabídce šablona zařízení přejděte na **přizpůsobení** .
2. Najít `Temperature` typ telemetrie
3. Aktualizujte **zobrazované jméno** `Temperature` na `Reported temperature`
4. Aktualizovat měrnou jednotku nebo nastavit *minimální hodnotu* a *maximální hodnotu*
5. **Uložit** všechny změny 

#### <a name="add-a-cloud-property"></a>Přidat vlastnost cloudu 
1. Přejít na **vlastnost Cloud** z nabídky šablony zařízení
2. Kliknutím na **+ přidat cloudovou vlastnost**přidejte novou vlastnost cloudu. V IoT Central můžete přidat vlastnost, která je relevantní pro zařízení, ale neočekává se, aby se odesílala zařízením. Jako příklad může být cloudová vlastnost Výstražná prahová hodnota specifická pro oblast instalace, informace o aktivech nebo informace o údržbě atd. 
3. **Uložit** všechny změny 
 
### <a name="views"></a>Zobrazení 
Šablona zařízení monitorování kvality vody obsahuje předdefinované zobrazení. Prozkoumejte zobrazení a můžete provádět aktualizace. Zobrazení definují, jak operátory uvidí data zařízení, ale také zapisuje cloudové vlastnosti. 

  ![Zobrazení šablon zařízení](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devicetemplate-views.png)

### <a name="publish"></a>Publikování 

Pokud jste udělali nějaké změny, ujistěte se, že jste šablonu zařízení **publikovali** . 


### <a name="create-a-new-device-template"></a>Vytvoření nové šablony zařízení 
1. Vyberte **+ Nová** a vytvořte novou šablonu zařízení a postupujte podle procesu vytváření. 
2. Vytvořte vlastní šablonu zařízení od začátku nebo si můžete vybrat šablonu zařízení z katalogu zařízení Azure. 


## <a name="explore-simulated-devices"></a>Prozkoumat simulovaná zařízení

Aplikace **monitorování kvality vody** , kterou jste vytvořili z šablony aplikace, má dvě simulovaná zařízení namapovaná na šablonu zařízení monitorování kvality vody. 

### <a name="to-view-the-devices"></a>Postup zobrazení zařízení:
1. Z IoT Central levém navigačním podokně přejděte do **zařízení** . 

   ![Zařízení](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devices.png)


2. Vyberte a klikněte na jedno simulované zařízení. 

    ![Zařízení 1](./media/tutorial-waterqualitymonitoring/waterqualitymonitor-device1.png)

3. Na kartě **vlastnosti cloudu** zkuste aktualizovat hodnotu `Acidity (pH) Threshold` z `8` na `9`. 
4. Prozkoumejte kartu **vlastnosti zařízení** a **řídicí panel zařízení** . 

> [!NOTE]
> Všimněte si, že všechny karty byly nakonfigurovány ze **zobrazení šablon zařízení**.


### <a name="add-new-devices"></a>Přidat nová zařízení
Nová zařízení můžete přidat kliknutím na **+ Nový** na kartě **zařízení** . 


## <a name="explore-and-configure-rules"></a>Prozkoumat a nakonfigurovat pravidla

V Azure IoT Central můžete vytvořit pravidla pro automatické monitorování telemetrie zařízení a aktivovat akci, když se splní jedna nebo víc podmínek. Akce můžou zahrnovat posílání e-mailových oznámení nebo aktivaci akce Microsoft Flow nebo akce Webhooku k odesílání dat do jiných služeb.

Vytvořená aplikace pro **monitorování kvality vody** má dvě předem nakonfigurovaná pravidla.

### <a name="to-view-rules"></a>Postup zobrazení pravidel:
1. Přejděte na **pravidla** z IoT Central levém navigačním podokně. 

   ![Pravidla](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-rules.png)


2. Vyberte a klikněte na **vysoce pH výstrahy** , což je jedno z předem nakonfigurovaných pravidel v aplikaci. 

     ![Výstraha s vysokým pH](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-highphalert.png)

    Pravidlo `High pH alert` je nakonfigurováno pro kontrolu proti `Acidity (pH) is greater than 8`podmínky.

Nyní vytvoříme akci e-mailu.

Postup přidání akce k pravidlu:

1. Vyberte **+ e-mail**. 
2.  Jako popisný **Zobrazovaný název** pro akci zadejte *vysoké výstrahy pH* .
3. Zadejte e-mailovou adresu přidruženou k vašemu IoT Central účtu **v.** 
4. Volitelně můžete zadat poznámku, která se má zahrnout do textu e-mailu.
5. Akci dokončete výběrem možnosti **Hotovo** .
6. Kliknutím na **Uložit** uložte a aktivujte nové pravidlo. 

Během několika minut byste měli dostávat e-maily, když je splněna nakonfigurovaná **Podmínka** .

> [!NOTE]
> Aplikace odešle e-mail pokaždé, když je splněna podmínka. **Zakáže** pravidlo pro zastavení přijímání e-mailů od automatizovaného pravidla. 
  
Vytvoření nového pravidla: 
1. V levém navigačním podokně vyberte **+ Nový** u **pravidel** .

## <a name="configure-jobs"></a>Konfigurace úloh

V IoT Central úlohy umožňují aktivovat aktualizace vlastností zařízení nebo cloudu na více zařízeních. Kromě vlastností můžete k aktivaci příkazů zařízení na několika zařízeních použít taky úlohy. IoT Central bude pracovní postup automatizovat. 

1. V levém navigačním podokně přejdete na **úlohy** . 
2. Klikněte na **+ Nový** a nakonfigurujte jednu nebo víc úloh. 


## <a name="customize-your-application"></a>Přizpůsobení aplikace 
Jako tvůrce můžete změnit několik nastavení pro přizpůsobení uživatelského prostředí aplikace.

1. Vyberte možnost **správa > přizpůsobení aplikace**.
2. Pomocí tlačítka **změnit** můžete zvolit obrázek, který se má nahrát jako **logo aplikace**.
3.  Pomocí tlačítka **změnit** můžete zvolit obrázek **ikony prohlížeče** , který se zobrazí na kartách prohlížeče.
4. Můžete také nahradit výchozí barvy v **prohlížeči** přidáním hexadecimálních kódů HTML.
5. Změňte také **motiv** kliknutím na **Nastavení** na hlavním panelu.

   ![Azure IoT Central přizpůsobení aplikace](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-customize-your-application1.png)

### <a name="to-update-the-application-image"></a>Aktualizace image aplikace:

6.  Vyberte možnost **správa > nastavení aplikace**.

7. Pomocí tlačítka **Vybrat obrázek** můžete zvolit obrázek, který se má nahrát jako obrázek aplikace. 


  
## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete tuto aplikaci nadále používat, odstraňte aplikaci pomocí následujících kroků:

1. Otevřete kartu Správa v levém podokně aplikace IoT Central.
2. Vyberte nastavení aplikace a klikněte na tlačítko Odstranit v dolní části stránky.

    ![Odstranění aplikace](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-application-settings-delete-app1.png)        



## <a name="next-steps"></a>Další kroky

* Další informace o [konceptech monitorování kvality vody](./concepts-waterqualitymonitoring-architecture.md)