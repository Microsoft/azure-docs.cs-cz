---
title: 'Kurz: Vytvoření aplikace pro správu propojených odpadů pomocí Azure IoT Central'
description: 'Kurz: Naučte se vytvářet aplikace pro správu propojených odpadů pomocí šablon aplikací Azure IoT Central.'
author: miriambrus
ms.author: miriamb
ms.date: 12/11/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 5ecfa3e4c46c17a3b381e9743953c7d97309ea3b
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/16/2020
ms.locfileid: "97587243"
---
# <a name="tutorial-create-a-connected-waste-management-application-in-iot-central"></a>Kurz: Vytvoření aplikace pro správu propojených odpadů v IoT Central

Tento kurz vás provede vytvořením aplikace pro správu nepřipojeného odpadu v Azure IoT Central ze šablony IoT Central **připojené aplikace pro správu odpadu** . 

V tomto kurzu se naučíte, jak: 

* Použití šablony **správy propojených odpadů** v Azure IoT Central k vytvoření aplikace pro správu připojení
* Řídicí panel pro prohlížení a přizpůsobení operátoru 
* Prozkoumat šablonu zařízení připojené odpadní přihrádky
* Prozkoumat simulovaná zařízení
* Prozkoumat a nakonfigurovat pravidla
* Konfigurace úloh
* Přizpůsobení značky aplikace pomocí bílých popisků

## <a name="prerequisites"></a>Předpoklady

Pro absolvování tohoto kurzu potřebujete:
*  Doporučuje se předplatné Azure. Volitelně můžete použít bezplatnou 7 dní zkušební verzi. Pokud nemáte předplatné Azure, můžete ho vytvořit na [stránce registrace do Azure](https://aka.ms/createazuresubscription).

## <a name="create-connected-waste-management-app-in-iot-central"></a>Vytvořit aplikaci pro správu propojených odpadů v IoT Central

V této části použijete **šablonu správy propojených odpadů** v Azure IoT Central k vytvoření aplikace pro správu propojených odpadů v IoT Central.

Vytvoření nové aplikace pro správu nepřipojených odpadů v Azure IoT Central:  

1. Přejděte na web [Azure IoT Central domovskou stránku](https://aka.ms/iotcentral) .

    Pokud máte předplatné Azure, přihlaste se pomocí přihlašovacích údajů, které používáte pro přístup k němu, jinak se přihlaste pomocí účet Microsoft:

    ![Zadání účtu organizace](./media/tutorial-connectedwastemanagement/sign-in.png)

1. V levém podokně klikněte na **sestavovat** a vyberte kartu **státní správa** . Stránka pro státní správu zobrazuje několik šablon aplikací pro státní správu.

    ![Vytváření šablon aplikací pro státní správu](./media/tutorial-connectedwastemanagement/iotcentral-government-tab-overview.png)

1. Vyberte šablonu aplikace **pro správu propojených odpadů** . Tato šablona obsahuje ukázkovou šablonu zařízení bin, simulované zařízení, řídicí panel operátora a předem nakonfigurovaná pravidla monitorování.    

1. Klikněte na **vytvořit aplikaci** a otevře se formulář pro vytváření **nových aplikací** s následujícími poli:
    * **Název aplikace** Ve výchozím nastavení používá aplikace *připojenou správu odpadu* následovaný jedinečným řetězcem ID, který IoT Central generuje. Volitelně můžete zvolit popisný název aplikace. Můžete změnit název aplikace i později.
    * **Adresa URL** – Volitelně můžete zvolit požadovanou adresu URL. Adresu URL můžete později změnit. 
    * Pokud máte předplatné Azure, zadejte svůj *adresář, předplatné Azure a oblast*. Pokud předplatné nemáte, můžete povolit **7 dní bezplatnou zkušební verzi** a dokončit požadované kontaktní údaje.  

    Další informace o adresářích a předplatných najdete v [rychlém startu k vytvoření aplikace](../core/quick-deploy-iot-central.md).

1. V dolní části stránky klikněte na tlačítko **vytvořit** . 

    ![Stránka aplikace pro vytvoření propojeného odpadu v Azure IoT Central](./media/tutorial-connectedwastemanagement/new-application-connectedwastemanagement.png)
    
    ![Informace o fakturaci pro vytvoření propojeného služby Azure IoT Central](./media/tutorial-connectedwastemanagement/new-application-connectedwastemanagement-billinginfo.png)

 
1. Nyní jste vytvořili aplikaci pro správu připojení k síti pomocí **šablony správy propojených odpadů** v Azure IoT Central. 

Blahopřejeme vám. Vaše nově vytvořená aplikace je dodávána s předem nakonfigurovaným:
* Ukázky řídicích panelů operátorů
* Ukázka předdefinovaných šablon zařízení bin pro připojené odpady
* Zařízení s simulovaným propojenými odpadními přihrádkami
* Předem konfigurovaná pravidla a úlohy
* Ukázka brandingu pomocí označení bílé 

Je to vaše aplikace a můžete ji kdykoli upravit. Pojďme teď prozkoumat aplikaci a udělat některá vlastní nastavení.  

## <a name="explore-and-customize-operator-dashboard"></a>Řídicí panel pro prohlížení a přizpůsobení operátoru 
Po vytvoření aplikace, kterou nastavíte, na **řídicím panelu pro správu s velkou odpadní propojenou odpadní** sítě.

   ![Řídicí panel pro správu připojeného odpadu](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard1.png)

Jako tvůrce můžete na řídicím panelu vytvořit a přizpůsobit zobrazení operátorů. Než se pokusíte přizpůsobit, Podívejme se na řídicí panel. 

>>[!NOTE]
>> Všechna data zobrazená na řídicím panelu jsou založená na simulovaných datech zařízení, která se budou zkoumat v další části. 

Řídicí panel se skládá z různých druhů dlaždic:

* ***Dlaždice obrázek nástroje World odpad** _: první dlaždice na řídicím panelu je dlaždice obrázku fiktivního odpadního programu "World odpad". Můžete přizpůsobit dlaždici a umístit vlastní image nebo ji odebrat. 

_ ***Dlaždice s obrázkem odpadní přihrádky** _: k vytvoření vizuální reprezentace zařízení, které se sleduje spolu s popisným textem, můžete použít dlaždice obrázků a obsahu. 

_ ***Dlaždice ukazatel KPI na úrovni výplně** _: dlaždice zobrazuje hodnotu oznámenou senzorem _Fill Level * v přihrádce odpadu. Na dálku se dá monitorovat *úroveň výplně* a jiné snímače, jako je *měřič Odor* nebo *váhy* v odpadkovém koši. Operátor může provést akci, jako je odesílání odpadkového vozíku. 

* ***Mapa oblasti monitorování odpadů** _: mapa používá Azure Maps, kterou můžete nakonfigurovat přímo v Azure IoT Central. Dlaždice Mapa zobrazuje umístění zařízení. Zkuste ukazatel myši najeďte na mapu a vyzkoušejte ovládací prvky na mapě, jako je přiblížení, zmenšení nebo rozbalení.

     ![Mapa řídicího panelu správy propojených odpadů](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard-map.png)


_ ***Výplň, Odor, pruhový graf na úrovni váhy**: v pruhovém grafu můžete vizualizovat jedno nebo několik dat telemetrie zařízení. Pruhový graf můžete také rozbalit.  

  ![Pruhový graf správy propojených odpadů](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard-barchart.png)


* **Dlaždice obsahu služeb pole**: řídicí panel obsahuje odkaz na integraci s poli služeb Dynamics 365 z vaší aplikace Azure IoT Central. Jako příklad můžete použít služby pole k vytvoření lístků pro odesílání služeb sběru odpadků. 


### <a name="customize-dashboard"></a>Přizpůsobení řídicího panelu 

Jako tvůrce můžete přizpůsobit zobrazení pro operátory na řídicím panelu. Můžete vyzkoušet:
1. Klikněte na **Upravit** a přizpůsobte si **řídicí panel pro správu celého světa propojených odpadů**. Řídicí panel můžete přizpůsobit kliknutím na nabídku **Upravit** . Jakmile je řídicí panel v režimu **úprav** , můžete přidat nové dlaždice nebo můžete nakonfigurovat 

    ![Úprava řídicího panelu](./media/tutorial-connectedwastemanagement/edit-dashboard.png)

1. Můžete také kliknout na **+ Nový** a vytvořit nový řídicí panel a nakonfigurovat od začátku. Můžete mít více řídicích panelů a mezi řídicími panely můžete přecházet z nabídky řídicího panelu. 

## <a name="explore-connected-waste-bin-device-template"></a>Prozkoumat šablonu zařízení připojené odpadní přihrádky

Šablona zařízení v Azure IoT Central definuje schopnost zařízení, což může být telemetrie, vlastnosti nebo příkaz. Jako tvůrce můžete definovat šablony zařízení, které reprezentují schopnost zařízení, která budete připojovat. 

Aplikace **pro správu propojených odpadů** je dodávána s ukázkovou šablonou zařízení s propojenými odpadními.

Postup zobrazení šablony zařízení:

1. Klikněte na **šablony zařízení** v levém podokně aplikace v IoT Central. 

    ![Snímek obrazovky zobrazující seznam šablon zařízení v aplikaci](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate.png)

1. V seznamu šablony zařízení se zobrazí složka **připojené odpadní přihrádka**. Otevřete kliknutím na název.

1. Seznamte se s možnostmi šablon zařízení. Můžete vidět, že definuje senzory, jako je *úroveň výplně*, *měřič Odor*, *váhy*, *umístění* a další.

   ![Snímek obrazovky s podrobnostmi o šabloně zařízení připojené odpadní přihrádky](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate-connectedbin.png)


### <a name="customizing-the-device-template"></a>Přizpůsobení šablony zařízení

Zkuste upravit následující:
1. V nabídce šablona zařízení přejděte na **přizpůsobení** .
1. Najít `Odor meter` typ telemetrie
1. Aktualizovat **zobrazované jméno** `Odor meter` na `Odor level`
1. Můžete také zkusit aktualizovat měrnou jednotku nebo nastavit *minimální hodnotu* a *maximální hodnotu* .
1. **Uložit** všechny změny 

### <a name="add-a-cloud-property"></a>Přidat vlastnost cloudu 

1. Přejít na **vlastnost Cloud** z nabídky šablony zařízení
1. Kliknutím na **+ přidat cloudovou vlastnost** přidejte novou vlastnost cloudu. V IoT Central můžete přidat vlastnost, která je relevantní pro zařízení, ale neočekává se, aby se odesílala zařízením. Cloudová vlastnost může být například prahová hodnota pro výstrahy specifická pro oblast instalace, informace o aktivech nebo informace o údržbě a další informace. 
1. **Uložit** všechny změny 
 
### <a name="views"></a>Zobrazení 
* Šablona zařízení připojené odpadní přihrádka obsahuje předdefinované zobrazení. Prozkoumejte zobrazení a můžete provádět aktualizace. Zobrazení definují, jak operátory uvidí data zařízení, ale také zapisuje cloudové vlastnosti. 

  ![Zobrazení šablon zařízení](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate-views.png)

### <a name="publish"></a>Publikování 

* Pokud jste udělali nějaké změny, ujistěte se, že jste šablonu zařízení **publikovali** . 

### <a name="create-a-new-device-template"></a>Vytvoření nové šablony zařízení 

* Vyberte **+ Nová** a vytvořte novou šablonu zařízení a postupujte podle procesu vytváření. Budete moct vytvořit vlastní šablonu zařízení od začátku nebo můžete zvolit šablonu zařízení z katalogu zařízení Azure. 

## <a name="explore-simulated-devices"></a>Prozkoumat simulovaná zařízení

V IoT Central můžete vytvořit simulovaná zařízení, abyste mohli testovat šablonu a aplikaci pro svoje zařízení. 

Aplikace **pro správu propojených odpadů** má dvě simulovaná zařízení namapovaná na šablonu zařízení v připojené odpadní přihrádce. 

### <a name="to-view-the-devices"></a>Postup zobrazení zařízení:

1. Z IoT Central levém podokně přejděte do **zařízení** . 

   ![Zařízení](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devices.png)

1. Vyberte a klikněte na zařízení připojené odpadní přihrádky.  

     ![Zařízení 1](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devices-bin1.png)

1. Přejděte na kartu **vlastnosti cloudu** , zkuste aktualizovat `Bin full alert threshold` hodnotu z `95` na `100` . 
* Prozkoumejte kartu **vlastnosti zařízení** a **řídicí panel zařízení** . 

>> [!NOTE]
>> Všechny karty byly nakonfigurovány ze **zobrazení šablony zařízení**.

### <a name="add-new-devices"></a>Přidat nová zařízení

* Nová zařízení můžete přidat kliknutím na **+ Nový** na kartě **zařízení** . 

## <a name="explore-and-configure-rules"></a>Prozkoumat a nakonfigurovat pravidla

V Azure IoT Central můžete vytvořit pravidla pro automatické monitorování telemetrie zařízení a aktivovat akce, když se splní jedna nebo víc podmínek. Akce mohou zahrnovat odesílání e-mailových oznámení, aktivaci akce automatizace a akci Webhooku k odesílání dat do jiných služeb.

Aplikace **pro správu propojených odpadů** má čtyři vzorová pravidla.

### <a name="to-view-rules"></a>Postup zobrazení pravidel:
1. Přejít k **pravidlům** z IoT Central levém podokně

   ![Pravidla](./media/tutorial-connectedwastemanagement/connectedwastemanagement-rules.png)

1. Vybrat **úplnou výstrahu přihrádky**

     ![Přihrádka – úplná výstraha](./media/tutorial-connectedwastemanagement/connectedwastemanagement-binfullalert.png)

 1. `Bin full alert`Kontroluje **podmínky** `Fill level is greater than or equal to Bin full alert threshold` .

    `Bin full alert threshold`Je *cloudová vlastnost* definovaná v `Connected waste bin` šabloně zařízení. 

Nyní vytvoříme akci e-mailu.

### <a name="create-an-email-action"></a>Vytvoření e-mailové akce
Postup konfigurace e-mailové akce v seznamu akcí tohoto pravidla:
1. Vyberte **+ e-mail**. 
1. Jako popisný **Zobrazovaný název** pro akci zadejte *vysoké výstrahy pH* .
1. Zadejte e-mailovou adresu přidruženou k vašemu IoT Central účtu **v.** 
1. Volitelně můžete zadat poznámku, která se má zahrnout do textu e-mailu.
1. Akci dokončete výběrem možnosti **Hotovo** .
1. Kliknutím na **Uložit** uložte a aktivujte nové pravidlo. 

Při splnění nakonfigurované **podmínky** byste měli obdržet e-mail.

>[!NOTE]
>Aplikace odešle e-mail pokaždé, když je splněna podmínka. **Zakáže** pravidlo pro zastavení přijímání e-mailů od automatizovaného pravidla. 
  
Vytvoření nového pravidla: 
1. V levém podokně vyberte **+ Nový** u **pravidel** .

## <a name="configure-jobs"></a>Konfigurace úloh

V IoT Central úlohy umožňují aktivovat aktualizace vlastností zařízení nebo cloudu na více zařízeních. Kromě vlastností můžete k aktivaci příkazů zařízení na několika zařízeních použít taky úlohy. IoT Central bude pracovní postup automatizovat. 

1. V levém podokně přejdete na **úlohy** . 
1. Klikněte na **+ Nový** a nakonfigurujte jednu nebo víc úloh. 


## <a name="customize-your-application"></a>Přizpůsobení aplikace 

Jako tvůrce můžete změnit několik nastavení pro přizpůsobení uživatelského prostředí aplikace.

### <a name="to-change-the-application-theme"></a>Změna motivu aplikace:

1. **> přizpůsobení aplikace v nastavení Správa**.
1. Pomocí tlačítka **změnit** můžete zvolit obrázek, který se má nahrát jako **logo aplikace**.
1. Pomocí tlačítka **změnit** můžete zvolit obrázek **ikony prohlížeče** , který se zobrazí na kartách prohlížeče.
1. Můžete také nahradit výchozí barvy v **prohlížeči** přidáním hexadecimálních kódů HTML.

   ![Azure IoT Central přizpůsobení aplikace](./media/tutorial-connectedwastemanagement/connectedwastemanagement-customize-your-application.png)

1. Můžete také změnit image aplikace tak, že na tlačítko **správa > nastavení aplikace** a **Vybrat obrázek** zvolíte obrázek, který chcete nahrát jako obrázek aplikace.
1. Nakonec můžete také změnit **motiv** kliknutím na **Nastavení** v záhlaví aplikace.

  
## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete tuto aplikaci nadále používat, odstraňte aplikaci pomocí následujících kroků:

1. Otevřete kartu Správa v levém podokně aplikace IoT Central.
1. Vyberte nastavení aplikace a klikněte na tlačítko Odstranit v dolní části stránky.

## <a name="next-steps"></a>Další kroky

* Další informace o nástroji
 
> [!div class="nextstepaction"]
> [Koncepce správy propojených odpadů](./concepts-connectedwastemanagement-architecture.md)
