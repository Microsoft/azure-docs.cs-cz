---
title: 'Kurz: Vytvoření připojené aplikace pro nakládání s odpady s Azure IoT Central'
description: 'Kurz: Naučte se vytvářet propojenou aplikaci pro nakládání s odpady pomocí šablon aplikací Azure IoT Central.'
author: miriambrus
ms.author: miriamb
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 274d725a0e7987c8cb15363ba276aec7ebc541d5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77426356"
---
# <a name="tutorial-create-a-connected-waste-management-application-in-iot-central"></a>Kurz: Vytvoření připojené aplikace pro nakládání s odpady v ioT central



Tento kurz vás provede vytvořením aplikace pro nakládání s odpady připojené k Azure IoT Central ze šablony aplikace **pro nakládání s odpady** pro centrální připojení IoT. 

V tomto kurzu se naučíte: 

> [!div class="checklist"]

> * Pomocí šablony **pro nakládání s odpady** Azure IoT Central Connected pro nakládání s odpady vytvořte připojenou aplikaci pro nakládání s odpady
> * Prozkoumání a přizpůsobení řídicího panelu operátora 
> * Prozkoumat šablonu připojeného zařízení pro odpadkové koše
> * Prozkoumejte simulovaná zařízení
> * Prozkoumání a konfigurace pravidel
> * Konfigurace úloh
> * Přizpůsobení značky aplikace pomocí whitelabelingu

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:
-  Doporučujese předplatné Azure. Volitelně můžete použít bezplatnou 7denní zkušební verzi. Pokud nemáte předplatné Azure, můžete ho vytvořit na [stránce registrace Azure](https://aka.ms/createazuresubscription).

## <a name="create-connected-waste-management-app-in-iot-central"></a>Vytvoření aplikace Connected Waste Management v IoT Central

V této části použijete **šablonu pro nakládání s odpady** Azure IoT Central Connected k vytvoření připojené aplikace pro nakládání s odpady v IoT Central.

Vytvoření nové aplikace azure ioT centrální ho správu odpadu:  

1. Přejděte na web [domovské stránky Azure IoT central.](https://aka.ms/iotcentral)

    Pokud máte předplatné Azure, přihlaste se pomocí přihlašovacích údajů, které používáte k přístupu k němu, jinak se přihlaste pomocí účtu Microsoft:

    ![Zadání účtu organizace](./media/tutorial-connectedwastemanagement/sign-in.png)

2. Klikněte na **Stavět** v levém podokně a vyberte kartu **Vláda.** Na stránce vlády se zobrazí několik šablon vládních aplikací.

    ![Vytváření šablon aplikací pro státní správu](./media/tutorial-connectedwastemanagement/iotcentral-government-tab-overview.png)

1. Vyberte šablonu aplikace **Connected Waste Management.** Tato šablona obsahuje ukázkovou šablonu připojeného zařízení pro odpad, simulované zařízení, řídicí panel operátora a předem nakonfigurovaná pravidla monitorování.    

2. Klikněte na **Vytvořit aplikaci**, která otevře formulář Pro vytvoření **nové aplikace** s následujícími poli:
    * **Název aplikace**. Ve výchozím nastavení aplikace používá *propojené nakládání s odpady* následované jedinečným řetězcem ID, který generuje IoT Central. Volitelně zvolte popisný název aplikace. Název aplikace můžete změnit i později.
    * **URL** – Volitelně si můžete vybrat požadovanou adresu URL. Adresu URL můžete také později změnit. 
    * Pokud máte předplatné Azure, zadejte *svůj adresář, předplatné Azure a oblast*. Pokud nemáte předplatné, můžete povolit **7denní bezplatnou zkušební verzi** a vyplnit požadované kontaktní informace.  

    Další informace o adresářích a předplatných najdete v [rychlém startu k vytvoření aplikace](../core/quick-deploy-iot-central.md).

5. V dolní části stránky klikněte na **tlačítko Vytvořit.** 

    ![Stránka aplikace Azure IoT Central Create Connected Waste Application](./media/tutorial-connectedwastemanagement/new-application-connectedwastemanagement.png)
    
    ![Azure IoT centrální informace o propojené fakturaci](./media/tutorial-connectedwastemanagement/new-application-connectedwastemanagement-billinginfo.png)

 
6. Nyní jste vytvořili připojenou aplikaci pro nakládání s odpady pomocí **šablony pro nakládání s odpady**Azure IoT Central Connected . 

Blahopřejeme! Nově vytvořená aplikace je dodávána s předkonfigurovanou aplikací:
* Ukázkové řídicí panely operátorů
* Ukázka předdefinovaných šablon připojených zásobníků odpadu
* Simulovaná připojená zařízení pro odpadkové koše
* Předem nakonfigurovaná pravidla a úlohy
* Ukázkové značky s použitím bílého označení 

Je to vaše aplikace a můžete ji kdykoli vyúpravit. Pojďme nyní prozkoumat aplikaci a provést některé úpravy.  

## <a name="explore-and-customize-operator-dashboard"></a>Prozkoumání a přizpůsobení řídicího panelu operátora 
Po vytvoření aplikace přistanete na **přístrojové desce pro nakládání s odpady wide waste .**

   ![Připojená řídicí panel pro nakládání s odpady](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard1.png)

Jako tvůrce můžete vytvářet a přizpůsobovat zobrazení na řídicím panelu pro operátory. Než se pokusíte přizpůsobit, pojďme prozkoumat řídicí panel. 

> [!NOTE]
> Všechna data zobrazená na řídicím panelu jsou založena na simulovaných datech zařízení, která budou prozkoumána v další části. 

Přístrojová deska se skládá z různých druhů dlaždic:

* ***Wide World Waste utility image tile***: první dlaždice v přístrojové desce je obrázek dlaždice fiktivní odpad utility "Wide World Waste". Můžete přizpůsobit dlaždice a dát svůj vlastní obrázek nebo odstranit. 

* ***Dlaždice obrázku zásobníku odpadu***: Pomocí dlaždic s obrázky a obsahem můžete vytvořit vizuální reprezentaci zařízení, které je monitorováno spolu s popisným textem. 

* ***Dlaždice klíčového ukazatele výkonu úrovně naplnění***: na dlaždici se zobrazí hodnota vykázaná snímačem *hladiny plnění* v koši na odpad. *Úroveň naplnění* a další senzory, jako *je zápach metr* nebo *hmotnost* v odpadkovém koši lze vzdáleně sledovat. Operátor může provést akci, jako je odeslání sběrného vozíku pro sběr odpadků. 

*  ***Mapa oblasti monitorování odpadu:*** mapa používá Azure Maps, kterou můžete nakonfigurovat přímo v Azure IoT Central. Na dlaždici mapy je zobrazeno umístění zařízení. Pokuste se najet na mapu a vyzkoušet ovládací prvky nad mapou, jako je přiblížení, oddálení nebo rozbalení.

     ![Mapa řídicího panelu propojeného nakládání s odpady](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard-map.png)


* ***Výplňový, zápachový pruhový graf**úrovně hmotnosti : v pruhovém grafu můžete vizualizovat jedno nebo více dat telemetrie zařízení. Pruhový graf můžete také rozbalit.  

  ![Panelový graf panelu propojeného nakládání s odpady](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard-barchart.png)


* **Dlaždice obsahu field services:** řídicí panel obsahuje odkaz na integraci se službami Dynamics 365 Field Services z vaší aplikace Azure IoT Central. Jako příklad můžete použít Field Services k vytvoření lístky pro odesílání služby sběru koše. 


### <a name="customize-dashboard"></a>Přizpůsobení řídicího panelu 

Jako tvůrce můžete přizpůsobit zobrazení v řídicím panelu pro operátory. Můžete zkusit:
1. Kliknutím na **upravit** přizpůsobíte řídicí panel pro **nakládání s odpady připojený do celého světa**. Řídicí panel můžete přizpůsobit kliknutím na nabídku **Upravit.** Jakmile je řídicí panel v režimu **úprav,** můžete přidat nové dlaždice nebo můžete nakonfigurovat 

    ![Úprava řídicího panelu](./media/tutorial-connectedwastemanagement/edit-dashboard.png)

2. Můžete také kliknout na **+ Nový** vytvořit nový řídicí panel a konfigurovat od nuly. Můžete mít více řídicích panelů a můžete procházet mezi řídicími panely z nabídky řídicího panelu. 

## <a name="explore-connected-waste-bin-device-template"></a>Prozkoumat šablonu připojeného zařízení pro odpadkové koše

Šablona zařízení v Azure IoT Central definuje možnosti zařízení, které může být telemetrie, vlastnosti nebo příkaz. Jako tvůrce můžete definovat šablony zařízení, které představují schopnosti zařízení, která se připojíte. 
 

Aplikace **Connected waste management** je dodávána se šablonou vzorového připojeného zařízení pro odpadkový koš.

Zobrazení šablony zařízení:

1. Klikněte na **Šablony zařízení** z levého podokna aplikace v IoT Central. 

    ![Šablona zařízení](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate.png)

2. V seznamu šablony zařízení se zobrazí **připojená odpadková přihrádka**. Otevřete kliknutím na jméno.

3. Seznamte se s možnostmi šablony zařízení. Můžete vidět, že definuje senzory jako *úroveň náplně*, *Zápach metr*, *hmotnost*, *umístění,* a další.

   ![Šablona zařízení](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate-connectedbin.png)

   

### <a name="customizing-the-device-template"></a>Přizpůsobení šablony zařízení

Zkuste přizpůsobit následující:
1. Přechod na **Přizpůsobit** z nabídky šablony zařízení
2. Vyhledání `Odor meter` typu telemetrie
3. Aktualizujte **zobrazovaný** `Odor meter` název`Odor level`
4. Můžete také vyzkoušet aktualizační měrnou jednotku nebo nastavit *hodnotu Min* a *hodnotu Max*
5. **Uložení** všech změn 

### <a name="add-a-cloud-property"></a>Přidání vlastnosti cloudu 

1. Přechod na **vlastnost Cloud** z nabídky šablony zařízení
2. Přidejte novou vlastnost cloudu kliknutím na **+ Přidat vlastnost cloudu**. V IoT Central můžete přidat vlastnost, která je relevantní pro zařízení, ale neočekává se, že bude odeslána zařízením. Jako příklad může být cloudová vlastnost prahová hodnota pro upozornění specifická pro oblast instalace, informace o majetku nebo informace o údržbě a další informace. 
3. **Uložení** všech změn 
 
### <a name="views"></a>Zobrazení 
* Šablona připojeného zásobníku odpadu je dodávána s předdefinovanými zobrazeními. Prozkoumejte zobrazení a můžete provádět aktualizace. Zobrazení definují, jak operátoři uvidí data zařízení, ale také zadávání vlastností cloudu. 

  ![Zobrazení šablony zařízení](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate-views.png)

### <a name="publish"></a>Publikování 

* Pokud jste provedli nějaké změny, ujistěte se, že šablonu zařízení **publikujete.** 

### <a name="create-a-new-device-template"></a>Vytvoření nové šablony zařízení 

* Výběrem **možnosti + Nový** vytvořte novou šablonu zařízení a postupujte podle procesu vytváření. Budete moct vytvořit vlastní šablonu zařízení od začátku nebo si můžete vybrat šablonu zařízení z katalogu zařízení Azure. 

## <a name="explore-simulated-devices"></a>Prozkoumejte simulovaná zařízení

V IoT Central můžete vytvořit simulovaná zařízení pro testování šablony zařízení a aplikace. 

Aplikace **Connected waste management** má dvě simulovaná zařízení namapovaná na šablonu připojeného zásobníku odpadu. 

### <a name="to-view-the-devices"></a>Zobrazení zařízení:

1. Přejděte na **zařízení** z levého podokna IoT Central. 

   ![Zařízení](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devices.png)

2. Vyberte a klikněte na připojené masce odpadu.  

     ![Zařízení 1](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devices-bin1.png)

3. Přejděte na kartu **Vlastnosti cloudu,** zkuste aktualizovat hodnotu `Bin full alert threshold` z `95` na `100`. 
* Prozkoumejte kartu **Vlastnosti zařízení** a kartu **Řídicí panel zařízení.** 

> [!NOTE]
> Všimněte si, že všechny karty byly nakonfigurovány ze **zobrazení šablony zařízení**.

### <a name="add-new-devices"></a>Přidání nových zařízení

* Nová zařízení můžete přidat kliknutím na **+ Nový** na kartě **Zařízení.** 

## <a name="explore-and-configure-rules"></a>Prozkoumání a konfigurace pravidel

Ve službě Azure IoT Central můžete vytvořit pravidla pro automatické monitorování telemetrie zařízení a aktivaci akcí, když je splněna jedna nebo více podmínek. Akce mohou zahrnovat odesílání e-mailových oznámení, spuštění akce Microsoft Flow nebo akci webhooku pro odesílání dat jiným službám.

Aplikace **Connected waste management** má čtyři vzorová pravidla.

### <a name="to-view-rules"></a>Zobrazení pravidel:
1. Přechod na **pravidla** z levého podokna IoT Central

   ![Pravidla](./media/tutorial-connectedwastemanagement/connectedwastemanagement-rules.png)

2. Vyberte **úplnou výstrahu přihrádky**

     ![Úplná výstraha přihrádky](./media/tutorial-connectedwastemanagement/connectedwastemanagement-binfullalert.png)

 3. Zkontroluje, `Bin full alert` kdy **Condition** `Fill level is greater than or equal to Bin full alert threshold`.

    Jedná `Bin full alert threshold` se o *vlastnost* `Connected waste bin` cloud definovanou v šabloně zařízení. 

Nyní vytvoříme e-mailovou akci.

### <a name="create-an-email-action"></a>Vytvoření akce e-mailu
Postup konfigurace akce e-mailu v seznamu akcí pravidla:
1. Vyberte **+ E-mail**. 
2. Zadejte *výstrahu Vysoké ho uH* jako popisný **zobrazovaný název** akce.
3. Zadejte e-mailovou adresu přidruženou k účtu IoT Central v **části Do**. 
4. Volitelně zadejte poznámku, která bude obsahovat text e-mailu.
5. Chcete-li akci dokončit, vyberte **Hotovo.**
6. Chcete-li uložit a aktivovat nové pravidlo, vyberte **Uložit.** 

Měli byste dostávat e-maily při splnění nakonfigurované **podmínky.**

> [!NOTE]
> Aplikace odešle e-mail pokaždé, když je splněna podmínka. **Zakažte** pravidlo, chcete-li zastavit příjem e-mailů z automatického pravidla. 
  
Vytvoření nového pravidla: 
1. V levém podokně vyberte **v levém** podokně možnost **+Nový.**

## <a name="configure-jobs"></a>Konfigurace úloh

Ve službě IoT Central umožňují úlohy spouštět aktualizace vlastností zařízení nebo cloudu na více zařízeních. Kromě vlastností můžete také použít úlohy k aktivaci příkazů zařízení na více zařízeních. IoT Central bude automatizovat pracovní postup za vás. 

1. Z levého podokna přejděte na **Úlohy.** 
2. Klikněte na **+Nový** a nakonfigurujte jednu nebo více úloh. 


## <a name="customize-your-application"></a>Přizpůsobení aplikace 

Jako tvůrce můžete změnit několik nastavení přizpůsobit uživatelské prostředí ve vaší aplikaci.

### <a name="to-change-the-application-theme"></a>Změna motivu aplikace:

1. Přejděte na **název Správy > Přizpůsobení aplikace**.
3. Pomocí tlačítka **Změnit** vyberte obrázek, který chcete nahrát jako **logo aplikace**.
4. Pomocí tlačítka **Změnit** vyberte obrázek **ikony prohlížeče,** který se zobrazí na kartách prohlížeče.
5. Výchozí **barvy prohlížeče** můžete také nahradit přidáním šestnáctkových barevných kódů HTML.

   ![Azure IoT Central přizpůsobí vaši aplikaci](./media/tutorial-connectedwastemanagement/connectedwastemanagement-customize-your-application.png)

6. Obrázky aplikací můžete také změnit tak, že přejdete na **nastavení > aplikace a** vyberte tlačítko Vybrat **obrázek** a vyberte obrázek, který chcete nahrát jako bitovou kopii aplikace.
7. Nakonec můžete také změnit **motiv** kliknutím na **Nastavení** v nadpisu aplikace.

  
## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete pokračovat v používání této aplikace, odstraňte aplikaci pomocí následujících kroků:

1. Otevřete kartu Správa v levém podokně aplikace IoT Central.
2. Vyberte Nastavení aplikace a v dolní části stránky klikněte na Tlačítko Odstranit.

  

## <a name="next-steps"></a>Další kroky

* Další informace o [konceptech propojeného nakládání s odpady](./concepts-connectedwastemanagement-architecture.md)
