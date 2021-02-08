---
title: 'Kurz: Vytvoření aplikace pro správu propojených odpadů pomocí Azure IoT Central'
description: 'Kurz: Naučte se vytvářet aplikace pro správu propojených odpadů pomocí šablon aplikací Azure IoT Central'
author: miriambrus
ms.author: miriamb
ms.date: 12/11/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 65245b3c4b7e18670682f7e9e890453e32337644
ms.sourcegitcommit: 2501fe97400e16f4008449abd1dd6e000973a174
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2021
ms.locfileid: "99820072"
---
# <a name="tutorial-create-a-connected-waste-management-app"></a>Kurz: Vytvoření aplikace pro správu propojených odpadů

V tomto kurzu se dozvíte, jak pomocí Azure IoT Central vytvořit aplikaci pro správu propojených odpadů. 

Konkrétně se dozvíte, jak: 

> [!div class="checklist"]
> K vytvoření aplikace použijte šablonu pro *správu propojených odpadů* v Azure IoT Central.
> Prozkoumejte a přizpůsobte řídicí panel operátora. Prozkoumejte šablonu zařízení připojené odpadní přihrádky.
> Prozkoumejte simulovaná zařízení.
> Prozkoumejte a nakonfigurujte pravidla.
> Nakonfigurujte úlohy.
> Přizpůsobení brandingu aplikace

## <a name="prerequisites"></a>Požadavky

Doporučuje se předplatné Azure. Alternativně můžete použít bezplatnou, 7. zkušební verzi. Pokud nemáte předplatné Azure, můžete ho vytvořit na [stránce registrace do Azure](https://aka.ms/createazuresubscription).

## <a name="create-your-app-in-azure-iot-central"></a>Vytvoření aplikace v Azure IoT Central

V této části použijete šablonu pro správu propojených odpadů k vytvoření aplikace v Azure IoT Central. Jak na to:

1. Přejít na [Azure IoT Central](https://aka.ms/iotcentral).

    Pokud máte předplatné Azure, přihlaste se pomocí přihlašovacích údajů, které používáte pro přístup k němu. V opačném případě se přihlaste pomocí účet Microsoft:

    ![Snímek obrazovky Microsoftu pro přihlášení](./media/tutorial-connectedwastemanagement/sign-in.png)

1. V levém podokně vyberte **sestavení**. Pak vyberte kartu **státní správa** . Stránka pro státní správu zobrazuje několik šablon aplikací pro státní správu.

    ![Snímek obrazovky se stránkou sestavení Azure IoT Central.](./media/tutorial-connectedwastemanagement/iotcentral-government-tab-overview.png)

1. Vyberte šablonu aplikace **pro správu propojených odpadů** . Tato šablona obsahuje ukázkovou šablonu zařízení bin, simulované zařízení, řídicí panel operátora a předem nakonfigurovaná pravidla monitorování.    

1. Vyberte **vytvořit aplikaci**, která otevře dialogové okno **Nová aplikace** . Vyplňte informace pro následující pole:
    * **Název aplikace** Ve výchozím nastavení používá aplikace **správu připojení**, následované jedinečným řetězcem ID, který vygeneruje služba Azure IoT Central. Volitelně můžete zvolit popisný název aplikace. Později můžete změnit název aplikace.
    * **Adresa URL:** Volitelně můžete zvolit požadovanou adresu URL. Adresu URL můžete později změnit. 
    * **Cenový tarif**. Pokud máte předplatné Azure, zadejte svůj adresář, předplatné Azure a oblast do příslušných polí v dialogovém okně **informace o fakturaci** . Pokud předplatné nemáte, vyberte **zdarma** , aby se povolilo 7 dní zkušební předplatné, a dokončete požadované kontaktní údaje.  

    Další informace o adresářích a předplatných najdete v tématu [rychlý Start – vytvoření aplikace IoT Central v Azure](../core/quick-deploy-iot-central.md).

1. V dolní části stránky vyberte **vytvořit**. 

    ![Snímek obrazovky s dialogovým oknem Azure IoT Central vytvořit novou aplikaci.](./media/tutorial-connectedwastemanagement/new-application-connectedwastemanagement.png)
    
    ![Snímek obrazovky s dialogovým oknem informace o fakturaci Azure IoT Central](./media/tutorial-connectedwastemanagement/new-application-connectedwastemanagement-billinginfo.png)

 
Vaše nově vytvořená aplikace je dodávána s předem nakonfigurovaným:
* Řídicí panely s ukázkovým operátorem
* Ukázka předdefinovaných šablon zařízení pro připojené odpadní přihrádku
* Zařízení s simulovanými propojenými odpadními přihrádkami.
* Pravidla a úlohy.
* Ukázka značky. 

Je to vaše aplikace a můžete ji kdykoli upravit. Pojďme teď prozkoumat aplikaci a udělat některá vlastní nastavení.  

## <a name="explore-and-customize-the-operator-dashboard"></a>Prozkoumejte a přizpůsobte řídicí panel operátora. 

Podívejte se na **řídicí panel světového řízení odpadů**, který vidíte po vytvoření aplikace.

   ![Snímek obrazovky se celosvětovým řídicím panelem pro správu odpadních odpadů.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard1.png)

Jako tvůrce můžete na řídicím panelu vytvořit a přizpůsobit zobrazení operátorů. Nejdřív se podívejme na řídicí panel. 

>[!NOTE]
>Všechna data zobrazená na řídicím panelu jsou založená na simulovaných datech zařízení, která se zobrazí v další části. 

Řídicí panel se skládá z různých dlaždic:

* **Dlaždice obrázek nástroje World odpad**: první dlaždice na řídicím panelu je dlaždice obrázku fiktivního odpadního nástroje, "celosvětový odpad". Můžete přizpůsobit dlaždici a umístit ji do vlastní image, nebo ji můžete odebrat. 

* **Dlaždice s obrázkem odpadkového koše**: dlaždice obrázků a obsahu můžete použít k vytvoření vizuální reprezentace monitorovaného zařízení, spolu s popisem. 

* **Dlaždice ukazatel KPI na úrovni výplně**: Tato dlaždice zobrazuje hodnotu oznámenou senzorem na *úrovni výplně* v přihrádce odpadu. Na dálku se dá vzdáleně monitorovat úroveň výplně a další senzory, jako je *měřič Odor* nebo *váha* v přihrádce odpadu. Operátor může provést akci, jako je odesílání vozíku pro sběr odpadků. 

* **Mapa oblasti monitorování odpadů**: Tato dlaždice používá Azure Maps, které můžete nakonfigurovat přímo v Azure IoT Central. Dlaždice Mapa zobrazuje umístění zařízení. Zkuste ukazatel myši najeďte na mapu a zkuste ovládací prvky na mapě, jako je přiblížení nebo oddálení nebo rozbalení.

     ![Snímek obrazovky mapy řídicího panelu šablony pro správu připojeného odpadu](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard-map.png)


* **Výplň, Odor, pruhový graf úrovně váhy**: v pruhovém grafu můžete vizualizovat jeden nebo víc druhů dat telemetrie zařízení. Pruhový graf můžete také rozbalit.  

  ![Snímek obrazovky s pruhem řídicího panelu šablony pro správu připojeného odpadu](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard-barchart.png)


* **Služby pole**: řídicí panel obsahuje odkaz na integraci s poli služeb Dynamics 365 z vaší aplikace IoT Central Azure. Můžete například použít služby pole k vytvoření lístků pro odesílání služeb sběru odpadků. 


### <a name="customize-the-dashboard"></a>Přizpůsobit řídicí panel 

Řídicí panel můžete přizpůsobit tak, že vyberete nabídku **Upravit** . Pak můžete přidat nové dlaždice nebo nakonfigurovat existující. Řídicí panel vypadá jako v režimu úprav: 

![Snímek obrazovky řídicího panelu šablony pro správu připojeného odpadu v režimu úprav.](./media/tutorial-connectedwastemanagement/edit-dashboard.png)

Můžete také vybrat **+ Nová** a vytvořit nový řídicí panel a nakonfigurovat od začátku. Můžete mít více řídicích panelů a mezi řídicími panely můžete přepínat v nabídce řídicího panelu. 

## <a name="explore-the-device-template"></a>Prozkoumat šablonu zařízení

Šablona zařízení v Azure IoT Central definuje možnosti zařízení, které můžou zahrnovat telemetrii, vlastnosti nebo příkazy. Jako tvůrce můžete definovat šablony zařízení, které reprezentují možnosti zařízení, která budete připojovat. 

Aplikace pro správu propojených odpadů je dodávána s ukázkovou šablonou zařízení s propojenými odpadními přihrádkami.

Postup zobrazení šablony zařízení:

1. V Azure IoT Central v levém podokně aplikace vyberte **šablony zařízení**. 

    ![Snímek obrazovky zobrazující seznam šablon zařízení v aplikaci](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate.png)

1. V seznamu **šablony zařízení** vyberte možnost **připojené odpadní přihrádka**.

1. Projděte si možnosti šablony zařízení. Vidíte, že definuje senzory, jako je **úroveň výplně**, **měřič Odor**, **váhy** a **umístění**.

   ![Snímek obrazovky s podrobnostmi o šabloně zařízení připojené odpadní přihrádky](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate-connectedbin.png)


### <a name="customize-the-device-template"></a>Přizpůsobení šablony zařízení

Zkuste upravit následující:
1. V nabídce šablona zařízení vyberte **přizpůsobit**.
1. Najděte typ telemetrie **měřiče Odor** .
1. Aktualizujte **Zobrazovaný název** **měřiče Odor** na **úroveň Odor**.
1. Pokuste se aktualizovat jednotku měření nebo nastavit **minimální hodnotu** a **maximální hodnotu**.
1. Vyberte **Uložit**. 

### <a name="add-a-cloud-property"></a>Přidat vlastnost cloudu 

Jak na to:
1. V nabídce šablony zařízení vyberte **vlastnost Cloud**.
1. Vyberte **+ Přidat vlastnost cloudu**. V Azure IoT Central můžete přidat vlastnost, která je relevantní pro zařízení, ale neočekává se, že se zařízení pošle. Cloudovou vlastností může být například prahová hodnota pro výstrahy specifická pro oblast instalace, informace o aktivech nebo informace o údržbě. 
1. Vyberte **Uložit**. 
 
### <a name="views"></a>Zobrazení 
Šablona zařízení připojené odpadní přihrádka je dodávána s předdefinovanými zobrazeními. Prozkoumejte zobrazení a v případě potřeby je aktualizujte. Zobrazení definují, jak operátory uvidí vlastnosti dat zařízení a vstupní Cloud. 

  ![Snímek obrazovky šablony správy propojených odpadů – zobrazení šablon zařízení.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate-views.png)

### <a name="publish"></a>Publikovat 

Pokud jste provedli nějaké změny, nezapomeňte publikovat šablonu zařízení. 

### <a name="create-a-new-device-template"></a>Vytvoření nové šablony zařízení 

Pokud chcete vytvořit novou šablonu zařízení, vyberte **+ Nová** a postupujte podle pokynů. Můžete vytvořit vlastní šablonu zařízení od začátku nebo můžete zvolit šablonu zařízení z katalogu zařízení Azure. 

## <a name="explore-simulated-devices"></a>Prozkoumat simulovaná zařízení

V Azure IoT Central můžete vytvořit simulovaná zařízení, abyste mohli testovat šablonu a aplikaci vašich zařízení. 

Aplikace pro správu propojených odpadů má dvě simulovaná zařízení, která jsou přidružená k šabloně zařízení s připojenými odpadními. 

### <a name="view-the-devices"></a>Zobrazit zařízení

1. V levém podokně Azure IoT Central vyberte **zařízení**. 

   ![Snímek obrazovky se zařízeními šablon správy propojených odpadů.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devices.png)

1. Vyberte zařízení **připojené k přihrádce** .  

     ![Snímek obrazovky s vlastnostmi zařízení šablony pro správu propojených odpadů.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devices-bin1.png)

1. Přejít na kartu **vlastnosti cloudu** . Aktualizujte hodnotu **celé prahové hodnoty pro výstrahu na přihrádku** z **95** na **100**. 

Prozkoumejte karty **vlastností zařízení** a **řídicích panelů zařízení** . 

> [!NOTE]
> Všechny karty byly nakonfigurovány ze zobrazení šablony zařízení.

### <a name="add-new-devices"></a>Přidat nová zařízení

Nová zařízení můžete přidat tak, že na kartě **zařízení** vyberete **+ Nová** . 

## <a name="explore-and-configure-rules"></a>Prozkoumat a nakonfigurovat pravidla

V Azure IoT Central můžete vytvořit pravidla pro automatické monitorování telemetrie zařízení a aktivaci akcí, když se splní jedna nebo víc podmínek. Akce můžou zahrnovat odesílání e-mailových oznámení, aktivaci akce v Power automatu nebo spuštění akce Webhooku k odesílání dat do jiných služeb.

Aplikace pro správu propojených odpadů má čtyři vzorová pravidla.

### <a name="view-rules"></a>Zobrazit pravidla
1. V levém podokně Azure IoT Central vyberte **pravidla**.

   ![Snímek pravidel šablony správy propojených odpadů](./media/tutorial-connectedwastemanagement/connectedwastemanagement-rules.png)

1. Vyberte položku **Úplná výstraha bin**.

     ![Snímek úplné výstrahy z přihrádky](./media/tutorial-connectedwastemanagement/connectedwastemanagement-binfullalert.png)

 1. **Úplná výstraha bin** kontroluje následující podmínku: **úroveň výplně je větší nebo rovna hodnotě celé prahové hodnoty pro výstrahu z přihrádky**.

    **Prahová hodnota úplné výstrahy přihrádky** je cloudová vlastnost, která je definována v šabloně zařízení připojené odpadní přihrádky. 

Nyní vytvoříme akci e-mailu.

### <a name="create-an-email-action"></a>Vytvoření e-mailové akce

V seznamu **akcí** v pravidle můžete nakonfigurovat akci e-mailu:
1. Vyberte **+ e-mail**. 
1. Jako **Zobrazovaný název** zadejte **upozornění s vysokým pH**.
1. **Do** zadejte e-mailovou adresu přidruženou k vašemu účtu Azure IoT Central. 
1. Volitelně můžete zadat poznámku, která se má zahrnout do textu e-mailu.
1. Vyberte **Hotovo**  >  **Uložit**. 

Po splnění nakonfigurované podmínky vám teď dostanete e-mail.

>[!NOTE]
>Aplikace pošle e-mail pokaždé, když je splněna podmínka. Zakáže pravidlo pro zastavení přijímání e-mailů od automatizovaného pravidla. 
  
Pokud chcete vytvořit nové pravidlo, v levém podokně **pravidel** vyberte **+ Nový**.

## <a name="configure-jobs"></a>Konfigurace úloh

V Azure IoT Central úlohy umožňují aktivovat aktualizace vlastností zařízení nebo cloudu na více zařízeních. Úlohy můžete použít také k aktivaci příkazů zařízení na více zařízeních. Azure IoT Central automatizuje pracovní postup za vás. 

1. V levém podokně Azure IoT Central vyberte **úlohy**. 
1. Vyberte **+ Nový** a nakonfigurujte jednu nebo víc úloh. 

## <a name="customize-your-application"></a>Přizpůsobení aplikace 

Jako tvůrce můžete změnit několik nastavení pro přizpůsobení uživatelského prostředí aplikace.

### <a name="change-the-application-theme"></a>Změna motivu aplikace

Jak na to:
1. Pro   >  **přizpůsobení aplikace** použijte možnosti Správa.
1. Vyberte **změnit** a zvolte obrázek, který se má nahrát pro **logo aplikace**.
1. Vyberte **změnit** a zvolte obrázek, který se má nahrát pro **ikonu prohlížeče** (obrázek, který se zobrazí na kartách prohlížeče).
1. Můžete také nahradit výchozí barvy v prohlížeči přidáním hexadecimálních kódů HTML. Pro tento účel použijte pole **záhlaví** a **zvýraznění** .

   ![Snímek obrazovky propojené nespojitelné šablony pro správu přizpůsobení aplikace](./media/tutorial-connectedwastemanagement/connectedwastemanagement-customize-your-application.png)

1. Můžete také změnit image aplikace. Vyberte možnost **Správa**  >  **nastavení aplikace**  >  **Vybrat obrázek** a zvolte obrázek, který se má nahrát jako obrázek aplikace.
1. Nakonec můžete motiv změnit také tak, že vyberete **Nastavení** v záhlaví aplikace.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete tuto aplikaci nadále používat, odstraňte aplikaci pomocí následujících kroků:

1. V levém podokně aplikace Azure IoT Central vyberte možnost **Správa**.
1. Vyberte **nastavení aplikace**  >  **Odstranit**.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Koncepce správy propojených odpadů](./concepts-connectedwastemanagement-architecture.md)
