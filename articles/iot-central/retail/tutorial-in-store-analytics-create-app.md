---
title: Kurz – vytvoření analytické aplikace v obchodě v Azure IoT Central
description: Tento kurz ukazuje, jak vytvořit maloobchodní aplikaci analýzy v obchodě v IoT Central. Vytvoříte ji, přizpůsobíte ji a přidáte senzorová zařízení.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.custom:
- iot-storeAnalytics-checkout
- iot-p0-scenario
ms.author: timlt
author: timlt
ms.date: 11/12/2019
ms.openlocfilehash: 50dd6038a8642f13cea7840fff723a5cf12ce2dd
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "81000249"
---
# <a name="tutorial-create-an-in-store-analytics-application-in-azure-iot-central"></a>Kurz: Vytvoření analytické aplikace v obchodě v Azure IoT Central



Kurz ukazuje tvůrcům řešení, jak vytvořit analytickou aplikaci Azure IoT Central v obchodě. Ukázková aplikace je pro maloobchodní prodejnu. Je to řešení společné obchodní potřeby sledovat a přizpůsobit se obsazenosti a podmínkám prostředí.

Ukázková aplikace, kterou vytvoříte, obsahuje tři skutečná zařízení: bránu Rigado Cascade 500 a dva senzory RuuviTag. Kurz také ukazuje, jak používat simulovaný snímač obsazenosti obsažený v šabloně aplikace pro účely testování. Brána Rigado C500 slouží jako komunikační centrum ve vaší aplikaci. Komunikuje se senzory ve vašem obchodě a spravuje jejich připojení ke cloudu. RuuviTag je senzor prostředí, který poskytuje telemetrii včetně teploty, vlhkosti a tlaku. Simulovaný snímač obsazenosti umožňuje sledovat pohyb a přítomnost v pokladních oblastech obchodu. 

Tento kurz obsahuje pokyny pro připojení zařízení Rigado a RuuviTag k vaší aplikaci. Pokud máte jinou bránu a senzory, můžete stále postupovat podle kroků k vytvoření aplikace. Výukový program také ukazuje, jak vytvořit simulované senzory RuuviTag. Simulované senzory umožňují vytvářet aplikace, pokud nemáte skutečná zařízení. 

Řešení pro monitorování pokladny a stavu vyvíjíte ve třech částech:

* Vytvoření aplikace a připojení zařízení ke sledování podmínek
* Přizpůsobení řídicího panelu tak, aby operátoři mohli monitorovat a spravovat zařízení
* Konfigurace exportu dat tak, aby správci obchodů mohli spouštět analýzy a vizualizovat přehledy

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Použití šablony Azure IoT Central **in store – pokladny** k vytvoření aplikace maloobchodu
> * Přizpůsobení nastavení aplikace
> * Vytváření a přizpůsobení šablon zařízení IoT
> * Připojení zařízení k aplikaci
> * Přidání pravidel a akcí pro sledování podmínek

## <a name="prerequisites"></a>Požadavky

Chcete-li dokončit tuto sérii kurzů, potřebujete:
* Doporučujese předplatné Azure. Volitelně můžete použít bezplatnou 7denní zkušební verzi. Pokud nemáte předplatné Azure, můžete ho vytvořit na [stránce registrace Azure](https://aka.ms/createazuresubscription).
* Přístup k zařízení brány a dvěma senzorům prostředí (volitelně můžete použít simulovaná zařízení, jak je popsáno v tutoriálu)
* Šablony zařízení pro zařízení, která používáte (šablony jsou k dispozici pro všechna zařízení používaná v kurzu)

## <a name="create-an-application"></a>Vytvoření aplikace
V této části vytvoříte novou aplikaci Azure IoT Central ze šablony. Tuto aplikaci použijete v celé řadě kurzů k vytvoření kompletního řešení.

Vytvoření nové aplikace Azure IoT Central:

1. Přejděte na web [správce aplikací Azure IoT Central.](https://aka.ms/iotcentral)

1. Pokud máte předplatné Azure, přihlaste se pomocí přihlašovacích údajů, které používáte k přístupu k němu, jinak se přihlaste pomocí účtu Microsoft:

    ![Zadání účtu organizace](./media/tutorial-in-store-analytics-create-app/sign-in.png)

1. Pokud chcete začít vytvářet novou aplikaci Azure IoT Central, vyberte **New Application** (Nová aplikace).

1. Vyberte **možnost Maloobchodní**.  Maloobchodní stránka zobrazuje několik šablon maloobchodních aplikací.

Vytvoření nové aplikace pro analýzu v obchodě:  

1. Vyberte šablonu **aplikace V obchodě - pokladna.** Tato šablona obsahuje šablony zařízení pro všechna zařízení používaná v kurzu s výjimkou senzorů RuuviTag. Šablona také poskytuje řídicí panel operátora pro monitorování pokladny a podmínek prostředí a stavu zařízení. 

1. Volitelně můžete zvolit popisný **název aplikace**. Tato aplikace je založena na fiktivní maloobchodní prodejně s názvem Contoso. Kurz používá **název aplikace** *Contoso pokladna*. Šablona aplikace je založena na fiktivní společnosti Northwind. V tomto kurzu pomocí contoso se dozvíte, jak přizpůsobit aplikaci.

    > [!NOTE]
    > Pokud používáte popisný **název aplikace**, je stále nutné použít jedinečnou hodnotu adresy **URL**aplikace .

1. Pokud máte předplatné Azure, zadejte *svůj adresář, předplatné Azure a oblast*. Pokud nemáte předplatné, můžete povolit **7denní bezplatnou zkušební verzi** a vyplnit požadované kontaktní informace.  

    Další informace o adresářích a předplatných najdete v [rychlém startu k vytvoření aplikace](../core/quick-deploy-iot-central.md).

1. Vyberte **Vytvořit**.

    ![Stránka aplikace Azure IoT Central](./media/tutorial-in-store-analytics-create-app/preview-application-template.png)

    ![Azure IoT centrální informace o fakturaci aplikace](./media/tutorial-in-store-analytics-create-app/preview-application-template-billinginfo.png)

## <a name="customize-application-settings"></a>Přizpůsobení nastavení aplikace

Jako tvůrce můžete změnit několik nastavení přizpůsobit uživatelské prostředí ve vaší aplikaci. V této části vyberete předdefinovaný motiv aplikace. Volitelně se dozvíte, jak vytvořit vlastní motiv a aktualizovat bitovou kopii aplikace. Vlastní motiv umožňuje nastavit barvy prohlížeče aplikace, ikonu prohlížeče a logo aplikace, které se zobrazí v nadpisu.

Výběr předdefinovaného motivu aplikace:

1. Vyberte **Nastavení** na nadpisu.

    ![Nastavení aplikace Azure IoT Central](./media/tutorial-in-store-analytics-create-app/settings-icon.png)

2. Vyberte nový **motiv**.

3. Vyberte **Uložit**.

Místo použití předdefinovaného motivu můžete vytvořit vlastní motiv. Pokud chcete použít sadu ukázkových obrázků k přizpůsobení aplikace a dokončení kurzu, stáhněte si [ukázkové obrázky Contoso](https://github.com/Azure-Samples/iot-central-docs-samples/tree/master/retail).

Vytvoření vlastního motivu:

1. Rozbalte levé podokno, pokud již není rozbaleno.

    ![Azure IoT centrální levé podokno](./media/tutorial-in-store-analytics-create-app/dashboard-expand.png)

1. Vyberte **možnost Správa > Přizpůsobit aplikaci**.

1. Pomocí tlačítka **Změnit** vyberte obrázek, který chcete nahrát jako **logo aplikace**. Volitelně můžete zadat hodnotu pro **alternativní text loga**. 

1. Pomocí tlačítka **Změnit** vyberte obrázek **ikony prohlížeče,** který se zobrazí na kartách prohlížeče.

1. Volitelně můžete nahradit výchozí **barvy prohlížeče** přidáním šestnáctkových barevných kódů HTML. Pro **záhlaví**přidejte *#008575*.  Pro **zvýraznění**přidejte *#A1F3EA*. 

1. Vyberte **Uložit**. 

    ![Přizpůsobené logo Azure IoT Central](./media/tutorial-in-store-analytics-create-app/select-application-logo.png)

    Po uložení aplikace aktualizuje barvy prohlížeče, logo v nadpisu a ikonu prohlížeče. 

    ![Azure IoT Centrální aktualizované nastavení aplikací](./media/tutorial-in-store-analytics-create-app/saved-application-settings.png)

Aktualizace bitové kopie aplikace:

1. Vyberte **nastavení aplikace správa >**.

1. Pomocí tlačítka **Vybrat obrázek** vyberte obrázek, který chcete nahrát jako obrázek aplikace. Tento obrázek se zobrazí na dlaždici aplikace na stránce **Moje aplikace** správce aplikací IoT Central.

1. Vyberte **Uložit**.

1. Volitelně můžete přejděte do zobrazení **Moje aplikace** na webu [správce aplikací Azure IoT Central.](https://aka.ms/iotcentral) Dlaždice aplikace zobrazí aktualizovaný obrázek aplikace.

    ![Image aplikace Azure IoT Central přizpůsobení](./media/tutorial-in-store-analytics-create-app/customize-application-image.png)

## <a name="create-device-templates"></a>Vytváření šablon zařízení
Jako tvůrce můžete vytvářet šablony zařízení, které vám a provozovatelům aplikací umožní konfigurovat a spravovat zařízení. Šablonu vytvoříte vytvořením vlastního souboru, importem existujícího souboru šablony nebo importem šablony z katalogu zařízení Azure IoT. Po vytvoření a přizpůsobení šablony zařízení ji použijte k připojení skutečných zařízení k vaší aplikaci. Volitelně můžete použít šablonu zařízení ke generování simulovaných zařízení pro testování.

Šablona **aplikace In-Store analytics - checkout** obsahuje šablony zařízení pro několik zařízení.  Existují šablony zařízení pro dvě ze tří zařízení, která používáte v aplikaci. Šablona zařízení RuuviTag není součástí šablony **aplikace V obchodě – pokladna.** V této části přidáte do aplikace šablonu zařízení pro senzory RuuviTag.

Přidání šablony zařízení RuuviTag do aplikace:

1. V levém podokně vyberte **Šablony zařízení.**

1. Chcete-li vytvořit novou šablonu zařízení, vyberte **možnost + Nový.**

1. Najděte a vyberte šablonu zařízení **ruuviTag** v katalogu zařízení Azure IoT. 

1. Vyberte **další: Přizpůsobit**.

    ![Šablona zařízení senzoru Azure IoT Central RuuviTag](./media/tutorial-in-store-analytics-create-app/ruuvitag-device-template.png)

1. Vyberte **Vytvořit**. Aplikace přidá šablonu zařízení RuuviTag.

1. V levém podokně vyberte **Šablony zařízení.** Na stránce se zobrazí všechny šablony zařízení zahrnuté v šabloně aplikace a šablona zařízení RuuviTag, kterou jste právě přidali.

    ![Šablona zařízení senzoru Azure IoT Central RuuviTag](./media/tutorial-in-store-analytics-create-app/device-templates-list.png)

## <a name="customize-device-templates"></a>Přizpůsobení šablon zařízení
Šablony zařízení v aplikaci můžete přizpůsobit třemi způsoby. Nejprve můžete přizpůsobit nativní vestavěná rozhraní ve vašich zařízeních změnou možností zařízení. Například pomocí teplotního čidla můžete změnit podrobnosti, jako je zobrazovaný název teplotního rozhraní, datový typ, měrné jednotky a minimální a maximální provozní rozsahy. 

Za druhé, přizpůsobte si šablony zařízení přidáním vlastností cloudu. Vlastnosti cloudu nejsou součástí vestavěných funkcí zařízení. Vlastnosti cloudu jsou vlastní data, která vaše aplikace Azure IoT Central vytváří, ukládá a přidružuje k vašim zařízením. Příkladem vlastnosti cloudu může být vypočtená hodnota nebo metadata, jako je například umístění, které chcete přidružit k sadě zařízení. 

Za třetí, přizpůsobit šablony zařízení vytvořením vlastní zobrazení. Zobrazení umožňují operátorům vizualizovat telemetrii a metadata pro vaše zařízení, jako jsou metriky zařízení a stav.

Zde použijete první dvě metody k přizpůsobení šablony zařízení pro vaše senzory RuuviTag. Informace o vytváření zobrazení pro vaše senzory najdete v článku [Přidání simulované zařízení do aplikace IoT Central](../core/quick-create-simulated-device.md) rychlý start.

Přizpůsobení vestavěných rozhraní šablony zařízení RuuviTag:

1. V levém podokně vyberte **Šablony zařízení.** 

1. Vyberte šablonu pro senzory RuuviTag. 

1. Skryjte levé podokno. Souhrnné zobrazení šablony zobrazuje možnosti zařízení.

    ![Souhrnné zobrazení šablony zařízení Azure IoT Central RuuviTag](./media/tutorial-in-store-analytics-create-app/ruuvitag-device-summary-view.png)

1. V nabídce šablony zařízení RuuviTag vyberte **Přizpůsobit.** 

1. Posuňte se v `humidity` seznamu funkcí a vyhledejte typ telemetrie. Jedná se o položku řádku s upravitelnou hodnotou **zobrazovaného názvu** *vlhkosti*.

V následujících krocích `humidity` můžete přizpůsobit typ telemetrie pro senzory RuuviTag. Volitelně můžete přizpůsobit některé další typy telemetrie.

Pro `humidity` typ telemetrie proveďte následující změny:

1. Výběrem ovládacího prvku **Rozbalit** rozbalte podrobnosti schématu pro řádek.

1. Aktualizujte hodnotu **zobrazované jméno** z *vlhkosti* na vlastní hodnotu, *například Relativní vlhkost*.

1. Změňte možnost **Sémantický typ** z *Žádné* na *Vlhkost*.  Volitelně můžete nastavit hodnoty schématu pro typ telemetrie vlhkosti v rozšířeném zobrazení schématu. Nastavení schématu umožňuje vytvořit podrobné požadavky na ověření dat, která vaše senzory sledují. Můžete například nastavit minimální a maximální hodnoty provozního rozsahu pro dané rozhraní.

1. Vyberte **Uložit** a uložte tak provedené změny.

    ![Přizpůsobení šablony zařízení Azure IoT Central RuuviTag](./media/tutorial-in-store-analytics-create-app/ruuvitag-device-template-customize.png)

Přidání vlastnosti cloudu do šablony zařízení v aplikaci:

1. V nabídce šablony zařízení RuuviTag vyberte **Vlastnosti cloudu.**

1. Vyberte **Přidat vlastnost cloudu**. 

Zadejte následující hodnoty pro vytvoření vlastní vlastnosti pro uložení umístění každého zařízení:

1. Zadejte hodnotu *Umístění* **zobrazované hodnoty**. Tato hodnota se automaticky zkopíruje do pole **Název,** což je popisný název vlastnosti. Můžete použít zkopírovanou hodnotu nebo ji změnit.

1. V *String* rozevíracím souboru **Schéma** vyberte Řetězec. Typ řetězce umožňuje přidružit řetězec názvu umístění k libovolnému zařízení založenému na šabloně. Můžete například přidružit oblast v úložišti ke každému zařízení. Volitelně můžete nastavit **sémantický typ** vaší vlastnosti na *Umístění*a to automaticky nastaví **schéma** na *Geopoint*. Umožňuje přidružit GPS souřadnice k zařízení. 

1. Nastavte **minimální délku** na *2*. 

1. Nastavte **nastavení zastřihovat mezery** na **zapnuto**.

1. Vyberte **Uložit,** chcete-li uložit vlastní vlastnost cloudu.

    ![Přizpůsobení šablony zařízení Azure IoT Central RuuviTag](./media/tutorial-in-store-analytics-create-app/ruuvitag-device-template-cloud-property.png)

1. Vyberte **Publikovat**. 

    Publikování šablony zařízení ji zviditelní pro operátory aplikací. Po publikování šablony ji použijte ke generování simulovaných zařízení pro testování nebo k připojení skutečných zařízení k vaší aplikaci. Pokud už máte zařízení připojená k aplikaci, publikování vlastní šablony odešle změny do zařízení.

## <a name="add-devices"></a>Přidání zařízení
Po vytvoření a přizpůsobení šablon zařízení je čas přidat zařízení. 

V tomto kurzu použijete k vytvoření aplikace následující sadu reálných a simulovaných zařízení:
- Skutečná brána Rigado C500
- Dva skutečné senzory RuuviTag
- Simulovaný senzor **obsazenosti.** Simulovaný senzor je součástí šablony aplikace, takže jej nemusíte vytvářet. 

> [!NOTE]
> Pokud nemáte skutečná zařízení, můžete tento kurz dokončit vytvořením simulovaných senzorů RuuviTag. Následující pokyny zahrnují kroky k vytvoření simulované RuuviTag. Není nutné vytvářet simulované brány.

Proveďte kroky v následujících dvou článcích pro připojení skutečné brány Rigado a senzorů RuuviTag. Po dokončení se vraťte do tohoto kurzu. Vzhledem k tomu, že jste již vytvořili šablony zařízení v tomto kurzu, nemusíte je znovu vytvářet v následující sadě směrů.

- Pokud chcete připojit bránu Rigado, [přečtěte si informace o připojení rigado kaskády 500 k vaší aplikaci Azure IoT Central](../core/howto-connect-rigado-cascade-500.md?toc=/azure/iot-central/retail/toc.json&bc=/azure/iot-central/retail/breadcrumb/toc.json).
- Pokud chcete připojit senzory RuuviTag, [přečtěte si informace o připojení senzoru RuuviTag k aplikaci Azure IoT Central](../core/howto-connect-ruuvi.md?toc=/azure/iot-central/retail/toc.json&bc=/azure/iot-central/retail/breadcrumb/toc.json). Tyto pokyny můžete také použít k vytvoření dvou simulovaných senzorů, v případě potřeby.

## <a name="add-rules-and-actions"></a>Přidání pravidel a akcí
Jako součást použití senzorů v aplikaci Azure IoT Central ke sledování podmínek můžete vytvořit pravidla pro spouštění akcí při splnění určitých podmínek. Pravidlo je přidruženo k šabloně zařízení a jednomu nebo více zařízením a obsahuje podmínky, které musí být splněny na základě telemetrie zařízení nebo událostí. Pravidlo má také jednu nebo více přidružených akcí. Akce mohou zahrnovat odesílání e-mailových oznámení nebo aktivaci akce webhooku pro odesílání dat do jiných služeb. **Šablona aplikace In-Store Analytics - checkout** obsahuje některá předdefinovaná pravidla pro zařízení v aplikaci.

V této části vytvoříte nové pravidlo, které kontroluje maximální relativní úroveň vlhkosti na základě telemetrie senzoru RuuviTag. Přidáte akci do pravidla tak, že pokud vlhkost překročí maximální, aplikace odešle e-mail. 

Vytvoření pravidla: 

1. Rozbalte levé podokno.

1. Vyberte **Pravidla**.

1. Vyberte **+ Nový**.

1. Jako název pravidla zadejte *úroveň vlhkosti.* 

1. Zvolte šablonu zařízení RuuviTag v **oborech**. Pravidlo, které definujete, se bude vztahovat na všechny senzory založené na této šabloně. Volitelně můžete vytvořit filtr, který by pravidlo použil pouze na definovanou podmnožinu senzorů. 

1. Zvolte `Relative humidity` jako **telemetrii**. Je to funkce zařízení, kterou jste přizpůsobili v předchozím kroku.

1. Zvolte `Is greater than` jako **operátor**. 

1. Zadejte typickou úroveň vnitřní vlhkosti ve vnitřním rozsahu pro vaše prostředí jako **hodnotu**. Zadejte například *65*. Nastavili jste podmínku pro pravidlo, ke které dochází, když relativní vlhkost v libovolném reálném nebo simulovaném senzoru RuuviTag překročí tuto hodnotu. Možná budete muset upravit hodnotu nahoru nebo dolů v závislosti na normálním rozsahu vlhkosti ve vašem prostředí.  

   ![Azure IoT Central přidejte podmínky pravidla](./media/tutorial-in-store-analytics-create-app/rules-add-conditions.png)

Přidání akce do pravidla:

1. Vyberte **+ E-mail**. 

1. Zadejte *oznámení o vysoké vlhkosti* jako popisný **zobrazovaný název** akce. 

1. Zadejte e-mailovou adresu přidruženou k vašemu účtu **v části Do**. Pokud používáte jiný e-mail, musí být adresa, kterou používáte, pro uživatele, který byl přidán do aplikace. Uživatel se také musí přihlásit a odhlásit alespoň jednou.

1. Volitelně zadejte poznámku, která bude obsahovat text e-mailu.

1. Chcete-li akci dokončit, vyberte **Hotovo.**

   ![Azure IoT Central přidává akce k pravidlům](./media/tutorial-in-store-analytics-create-app/rules-add-action.png)

1. Chcete-li uložit a aktivovat nové pravidlo, vyberte **Uložit.** 

    Během několika minut by zadaný e-mailový účet měl začít přijímat e-maily. Aplikace odešle e-mail pokaždé, když senzor indikuje, že úroveň vlhkosti překročila hodnotu ve vašem stavu.

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste se naučili:

* Použití šablony Azure IoT Central **in store – pokladny** k vytvoření aplikace maloobchodu
* Přizpůsobení nastavení aplikace
* Vytváření a přizpůsobení šablon zařízení IoT
* Připojení zařízení k aplikaci
* Přidání pravidel a akcí pro sledování podmínek

Teď, když jste vytvořili aplikaci pro monitorování stavu Azure IoT Central, tady je navrhovaný další krok:

> [!div class="nextstepaction"]
> [Přizpůsobení řídicího panelu operátora](./tutorial-in-store-analytics-customize-dashboard.md)
