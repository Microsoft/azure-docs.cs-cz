---
title: Kurz – vytvoření analytické aplikace v obchodě v Azure IoT Central
description: V tomto kurzu se dozvíte, jak v IoT Central vytvořit maloobchodní aplikaci analýzy v obchodě. Vytvoříte ho, přizpůsobíte ho a přidáte zařízení snímače.
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
ms.openlocfilehash: caa61170504c3ee4b7162cc06db4df16d1232395
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2021
ms.locfileid: "103014362"
---
# <a name="tutorial-create-an-in-store-analytics-application-in-azure-iot-central"></a>Kurz: vytvoření analytické aplikace v obchodě v Azure IoT Central

V tomto kurzu se dozvíte, jak vytvořit Azure IoT Central v aplikaci pro analýzu v rámci obchodu. Ukázková aplikace je určena pro maloobchodní obchod. Jedná se o řešení běžných obchodních potřeb pro sledování a přizpůsobení podmínek obsazení a životního prostředí.

Ukázková aplikace, kterou sestavíte, zahrnuje tři skutečná zařízení: Rigado na sebe 500 a dva snímače RuuviTag. V tomto kurzu se také dozvíte, jak používat simulovaný senzor obsazení obsažený v šabloně aplikace pro účely testování. Brána Rigado C500 slouží jako komunikační centrum ve vaší aplikaci. Komunikuje se senzory ve vašem úložišti a spravuje jejich připojení ke cloudu. RuuviTag je senzor prostředí, který poskytuje telemetrii, včetně teploty, vlhkosti a tlaku. Senzor simulovaného obsazení nabízí způsob, jak sledovat pohyb a přítomnost v oblastech rezervace v obchodě. 

Tento kurz obsahuje pokyny pro připojení zařízení Rigado a RuuviTag k vaší aplikaci. Pokud máte jinou bránu a senzory, můžete postupovat podle pokynů k sestavení aplikace. Tento kurz také ukazuje, jak vytvořit simulované senzory RuuviTag. Simulované senzory umožňují vytvořit aplikaci, pokud nemáte skutečná zařízení. 

Řešení pro monitorování rezervace a podmínky vyvíjíte ve třech částech:

* Vytvoření aplikace a připojení zařízení k monitorování podmínek
* Přizpůsobení řídicího panelu pro povolení operátorů pro monitorování a správu zařízení
* Konfigurace exportu dat, aby správci Storu mohli spouštět analýzy a vizualizovat přehledy

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Použití šablony Azure IoT Central **in-Store Analytics – registrace** k vytvoření aplikace pro maloobchodní obchod
> * Přizpůsobení nastavení aplikace
> * Vytvoření a přizpůsobení šablon zařízení IoT
> * Připojení zařízení k aplikaci
> * Přidat pravidla a akce pro monitorování podmínek

## <a name="prerequisites"></a>Požadavky

K dokončení této série kurzů potřebujete:
* Doporučuje se předplatné Azure. Volitelně můžete použít bezplatnou 7 dní zkušební verzi. Pokud nemáte předplatné Azure, můžete ho vytvořit na [stránce registrace do Azure](https://aka.ms/createazuresubscription).
* Přístup k zařízení brány a dvěma senzorům pro životní prostředí (Volitelně můžete použít simulovaná zařízení, jak je popsáno v tomto kurzu)
* Šablony zařízení pro zařízení, která používáte (šablony jsou k dispozici pro všechna zařízení použitá v tomto kurzu)

## <a name="create-an-application"></a>Vytvoření aplikace
V této části vytvoříte novou aplikaci Azure IoT Central ze šablony. Tuto aplikaci použijete v celé sérii kurzů k sestavení kompletního řešení.

Vytvoření nové aplikace Azure IoT Central:

1. Přejděte na web [Azure IoT Central Správce aplikací](https://aka.ms/iotcentral) .

1. Pokud máte předplatné Azure, přihlaste se pomocí přihlašovacích údajů, které používáte pro přístup k němu, jinak se přihlaste pomocí účet Microsoft:

    ![Zadání účtu organizace](./media/tutorial-in-store-analytics-create-app/sign-in.png)

1. Pokud chcete začít vytvářet novou aplikaci Azure IoT Central, vyberte **New Application** (Nová aplikace).

1. Vyberte **Retail (maloobchod**).  Na stránce prodej se zobrazí několik šablon maloobchodních aplikací.

Vytvoření nové aplikace pro rezervaci v rámci úložiště:  

1. Vyberte šablonu aplikace pro **analýzu v rámci úložiště** . Tato šablona obsahuje šablony zařízení pro všechna zařízení použitá v tomto kurzu s výjimkou senzorů RuuviTag. Šablona také poskytuje řídicí panel operátora pro monitorování podmínek a stavu životního prostředí a stavu zařízení. 

1. Volitelně můžete zvolit popisný **název aplikace**. Tato aplikace je založená na fiktivním maloobchodním obchodě s názvem contoso. V tomto kurzu se používá rezervace **název aplikace** *Contoso*. Šablona aplikace je založena na fiktivní společnosti Northwind. V tomto kurzu se seznámíte s postupem přizpůsobení aplikace pomocí contoso.

    > [!NOTE]
    > Použijete-li popisný **název aplikace**, je stále nutné použít jedinečnou hodnotu pro **adresu URL** aplikace.

1. Pokud máte předplatné Azure, zadejte svůj *adresář, předplatné Azure a oblast*. Pokud předplatné nemáte, můžete povolit **7 dní bezplatnou zkušební verzi** a dokončit požadované kontaktní údaje.  

    Další informace o adresářích a předplatných najdete v [rychlém startu k vytvoření aplikace](../core/quick-deploy-iot-central.md).

1. Vyberte **Vytvořit**.

    ![Stránka aplikace Azure IoT Central](./media/tutorial-in-store-analytics-create-app/preview-application-template.png)

    ![Azure IoT Central vytvořit informace o fakturaci aplikace](./media/tutorial-in-store-analytics-create-app/preview-application-template-billinginfo.png)

## <a name="customize-application-settings"></a>Přizpůsobení nastavení aplikace

Jako tvůrce můžete změnit několik nastavení pro přizpůsobení uživatelského prostředí aplikace. V této části vyberete předdefinovaný motiv aplikace. Volitelně se naučíte, jak vytvořit vlastní motiv a aktualizovat image aplikace. Vlastní motiv vám umožní nastavit barvy prohlížeče aplikace, ikonu prohlížeče a logo aplikace, které se zobrazí v záhlaví grafiky.

Výběr předdefinovaného motivu aplikace:

1. Vyberte **Nastavení** na hlavním panelu.

    ![Nastavení aplikace pro Azure IoT Central](./media/tutorial-in-store-analytics-create-app/settings-icon.png)

2. Vyberte nový **motiv**.

3. Vyberte **Uložit**.

Místo použití předdefinovaného motivu můžete vytvořit vlastní motiv. Pokud chcete k přizpůsobení aplikace a dokončení kurzu použít sadu ukázkových imagí, Stáhněte si [ukázkové image společnosti Contoso](https://github.com/Azure-Samples/iot-central-docs-samples/tree/master/retail).

Vytvoření vlastního motivu:

1. Rozbalí levé podokno, pokud ještě není rozbalené.

    ![Levé podokno Azure IoT Central](./media/tutorial-in-store-analytics-create-app/dashboard-expand.png)

1. Vyberte možnost **správa > přizpůsobení aplikace**.

1. Pomocí tlačítka **změnit** můžete zvolit obrázek, který se má nahrát jako **logo aplikace**. Volitelně můžete zadat hodnotu pro **alternativní text loga**. 

1. Pomocí tlačítka **změnit** můžete zvolit obrázek **ikony prohlížeče** , který se zobrazí na kartách prohlížeče.

1. V případě potřeby nahraďte výchozí **barvy v prohlížeči** přidáním hexadecimálních kódů HTML. Pro **záhlaví** přidejte *#008575*.  Pro **zvýraznění** přidejte *#A1F3EA*. 

1. Vyberte **Uložit**. 

    ![Vlastní logo pro Azure IoT Central](./media/tutorial-in-store-analytics-create-app/select-application-logo.png)

    Po uložení bude aplikace aktualizovat barvy prohlížeče, logo v nadpisu a ikonu prohlížeče. 

    ![Aktualizace nastavení aplikace v Azure IoT Central](./media/tutorial-in-store-analytics-create-app/saved-application-settings.png)

Aktualizace image aplikace:

1. Vyberte možnost **správa > nastavení aplikace**.

1. Pomocí tlačítka **Vybrat obrázek** můžete zvolit obrázek, který se má nahrát jako obrázek aplikace. Tento obrázek se zobrazí na dlaždici aplikace na stránce **Moje aplikace** ve správci aplikací IoT Central.

1. Vyberte **Uložit**.

1. Volitelně můžete přejít na zobrazení **Moje aplikace** na webu [Azure IoT Central Správce aplikací](https://aka.ms/iotcentral) . Dlaždice aplikace zobrazuje aktualizovaný obrázek aplikace.

    ![Přizpůsobení image aplikace v Azure IoT Central](./media/tutorial-in-store-analytics-create-app/customize-application-image.png)

## <a name="create-device-templates"></a>Vytvoření šablon zařízení
Jako tvůrce můžete vytvořit šablony zařízení, které vám umožní a obsluha aplikací pro konfiguraci a správu zařízení. Šablonu vytvoříte tak, že vytvoříte vlastní šablonu, naimportujete existující soubor šablony nebo naimportujete šablonu z katalogu zařízení Azure IoT. Když vytvoříte a přizpůsobíte šablonu zařízení, použijte ji k připojení skutečných zařízení k vaší aplikaci. Volitelně můžete pomocí šablony zařízení generovat simulovaná zařízení pro testování.

Šablona aplikace **s rezervací v rámci obchodu** má šablony zařízení pro několik zařízení.  Existují šablony zařízení pro dvě ze tří zařízení, která používáte v aplikaci. Šablona zařízení RuuviTag není zahrnutá v šabloně aplikace **v rámci úložiště analýz** . V této části přidáte do své aplikace šablonu zařízení pro senzory RuuviTag.

Přidání šablony zařízení RuuviTag do vaší aplikace:

1. V levém podokně vyberte **šablony zařízení** .

1. Vyberte **+ Nová** a vytvořte novou šablonu zařízení.

1. Vyhledejte a vyberte šablonu zařízení **RuuviTag** snímače v katalogu zařízení Azure IoT. 

1. Vyberte **Další: Přizpůsobit**.

    ![Snímek obrazovky, který zvýrazní tlačítko Další: přizpůsobení](./media/tutorial-in-store-analytics-create-app/ruuvitag-device-template.png)

1. Vyberte **Vytvořit**. Aplikace přidá šablonu zařízení RuuviTag.

1. V levém podokně vyberte **šablony zařízení** . Na stránce se zobrazí všechny šablony zařízení, které jsou součástí šablony aplikace, a šablonu zařízení RuuviTag, kterou jste právě přidali.

    ![Šablona zařízení RuuviTag pro senzory Azure IoT Central](./media/tutorial-in-store-analytics-create-app/device-templates-list.png)

## <a name="customize-device-templates"></a>Přizpůsobení šablon zařízení
Šablony zařízení v aplikaci můžete přizpůsobit třemi způsoby. Nejdřív v zařízeních přizpůsobíte nativní Vestavěná rozhraní změnou možností zařízení. Například pomocí snímače teploty můžete změnit podrobnosti, jako je zobrazovaný název rozhraní teploty, datový typ, jednotky měření a minimální a maximální počet provozních rozsahů. 

Potom Přizpůsobte šablony zařízení přidáním vlastností cloudu. Vlastnosti cloudu nejsou součástí integrovaných možností zařízení. Vlastnosti cloudu jsou vlastní data, která vaše aplikace IoT Central v Azure vytvoří, uloží a přidruží k vašim zařízením. Příkladem cloudové vlastnosti může být Počítaná hodnota nebo metadata, jako je například umístění, které chcete přidružit k sadě zařízení. 

Třetí, přizpůsobení šablon zařízení vytvářením vlastních zobrazení. Zobrazení umožňují operátorům vizualizovat telemetrie a metadata pro vaše zařízení, jako jsou metriky a stav zařízení.

Tady použijete první dvě metody k přizpůsobení šablony zařízení pro senzory RuuviTag. Informace o vytváření zobrazení pro senzory najdete v tématu [Přidání simulovaného zařízení do rychlého startu aplikace IoT Central](../core/quick-create-simulated-device.md) .

Přizpůsobení vestavěných rozhraní šablony zařízení RuuviTag:

1. V levém podokně vyberte **šablony zařízení** . 

1. Vyberte šablonu pro senzory RuuviTag. 

1. Skryje levé podokno. V souhrnném zobrazení šablony se zobrazí možnosti zařízení.

    ![Souhrnné zobrazení šablony zařízení v Azure IoT Central RuuviTag](./media/tutorial-in-store-analytics-create-app/ruuvitag-device-summary-view.png)

1. V nabídce šablona zařízení RuuviTag vyberte **přizpůsobit** . 

1. Posuňte se v seznamu možností a najděte `humidity` typ telemetrie. Je to položka řádku s upravitelným **zobrazovaným názvem** hodnoty *vlhkosti*.

V následujících krocích si přizpůsobíte `humidity` typ telemetrie pro senzory RuuviTag. Volitelně můžete přizpůsobit některé z ostatních typů telemetrie.

Pro `humidity` typ telemetrie proveďte následující změny:

1. Výběrem **rozbalovacího** ovládacího prvku rozbalíte podrobnosti o schématu pro daný řádek.

1. Aktualizujte hodnotu **zobrazovaného názvu** z *vlhkosti* na vlastní hodnotu, jako je *relativní vlhkost*.

1. Změňte možnost **sémantického typu** z *none* na *vlhkost*.  Volitelně můžete nastavit hodnoty schématu pro typ telemetrie vlhkosti v rozšířeném zobrazení schématu. Nastavení schématu vám umožní vytvořit podrobné požadavky na ověření pro data, která vaše senzory sledují. Můžete například nastavit minimální a maximální hodnoty v operačním rozsahu pro dané rozhraní.

1. Vyberte **Uložit** a uložte tak provedené změny.

    ![Snímek obrazovky, který zobrazuje obrazovku přizpůsobit a zvýrazní tlačítko Uložit.](./media/tutorial-in-store-analytics-create-app/ruuvitag-device-template-customize.png)

Přidání vlastnosti cloudu do šablony zařízení v aplikaci:

1. V nabídce šablona zařízení RuuviTag vyberte **vlastnosti cloudu** .

1. Vyberte **Přidat vlastnost cloudu**. 

Zadejte následující hodnoty pro vytvoření vlastní vlastnosti pro uložení umístění každého zařízení:

1. Zadejte *umístění* hodnoty pro **Zobrazovaný název**. Tato hodnota se automaticky zkopíruje do pole **název** , což je popisný název vlastnosti. Můžete použít zkopírovanou hodnotu nebo ji změnit.

1. V rozevíracím seznamu **schématu** vyberte *řetězec* . Typ řetězce umožňuje přidružit řetězec názvu umístění k libovolnému zařízení na základě šablony. Například můžete k jednotlivým zařízením přidružit oblast v úložišti. Volitelně můžete nastavit **sémantický typ** vlastnosti na umístění a to automaticky nastaví **schéma** na *geografickou* *hodnotu.* Umožňuje přidružit k zařízení souřadnice GPS. 

1. Nastavte **minimální délku** na *2*. 

1. Nastavte **Oříznutí prázdných znaků** na **zapnuto**.

1. Vyberte **Uložit** a uložte svoji vlastní cloudovou vlastnost.

    ![Přizpůsobení šablony zařízení v Azure IoT Central RuuviTag](./media/tutorial-in-store-analytics-create-app/ruuvitag-device-template-cloud-property.png)

1. Vyberte **Publikovat**. 

    Publikování šablony zařízení je viditelné pro operátory aplikace. Po publikování šablony ji můžete použít ke generování simulovaných zařízení pro testování nebo k připojení skutečných zařízení k vaší aplikaci. Pokud již máte zařízení připojená k vaší aplikaci, publikování přizpůsobené šablony vloží změny do zařízení.

## <a name="add-devices"></a>Přidání zařízení
Po vytvoření a přizpůsobení šablon zařízení je čas přidat zařízení. 

Pro účely tohoto kurzu použijete pro sestavení aplikace následující sadu reálných a simulovaných zařízení:
- Skutečná brána Rigado C500
- Dva reálné senzory RuuviTag
- Senzor simulovaného **obsazení** . Simulovaný senzor je součástí šablony aplikace, takže ho nemusíte vytvářet. 

> [!NOTE]
> Pokud nemáte skutečná zařízení, můžete i přesto dokončit tento kurz vytvořením simulovaných senzorů RuuviTag. Následující pokyny obsahují kroky pro vytvoření simulovaného RuuviTag. Nemusíte vytvářet simulovanou bránu.

Provedením kroků v následujících dvou článcích spojíte skutečnou Rigado bránu a snímače RuuviTag. Až budete hotovi, vraťte se k tomuto kurzu. Vzhledem k tomu, že už jste v tomto kurzu vytvořili šablony zařízení, nemusíte je vytvářet znovu v následující sadě pokynů.

- Pokud chcete připojit bránu Rigado, přečtěte si článek [připojení 500 Rigado do aplikace Azure IoT Central](../core/howto-connect-rigado-cascade-500.md).
- Pokud chcete připojit senzory RuuviTag, přečtěte si téma [připojení snímače RuuviTag k aplikaci Azure IoT Central](../core/howto-connect-ruuvi.md). V případě potřeby můžete také použít tyto pokyny k vytvoření dvou simulovaných senzorů.

## <a name="add-rules-and-actions"></a>Přidat pravidla a akce
V rámci používání senzorů ve vaší aplikaci Azure IoT Central k monitorování podmínek můžete vytvořit pravidla ke spouštění akcí, když jsou splněné určité podmínky. Pravidlo je spojeno se šablonou zařízení a jedním nebo více zařízeními a obsahuje podmínky, které musí být splněny na základě telemetrie a událostí zařízení. Pravidlo má také jednu nebo více přidružených akcí. Akce můžou zahrnovat posílání e-mailových oznámení nebo aktivaci akce Webhooku pro posílání dat do jiných služeb. Šablona aplikace v rámci služby **Store Analytics – rezervace** obsahuje některá předdefinovaná pravidla pro zařízení v aplikaci.

V této části vytvoříte nové pravidlo, které kontroluje maximální relativní úroveň vlhkosti na základě telemetrie senzorů RuuviTag. Do pravidla přidáte akci, aby v případě, že vlhkost překračuje maximum, aplikace pošle e-mail. 

Vytvoření pravidla: 

1. Rozbalte levé podokno.

1. Vyberte **pravidla**.

1. Vyberte **+ Nový**.

1. Jako název pravidla zadejte *hladinu vlhkosti* . 

1. V části **obory** vyberte šablonu zařízení RuuviTag. Pravidlo, které definujete, bude platit pro všechny senzory založené na této šabloně. Volitelně můžete vytvořit filtr, který by pravidlo použilo jenom pro definovanou podmnožinu senzorů. 

1. Vyberte si `Relative humidity` jako **telemetrii**. Je to funkce zařízení, kterou jste přizpůsobili v předchozím kroku.

1. Vyberte `Is greater than` jako **operátor**. 

1. Jako **hodnotu** zadejte typickou vnitřní úroveň vlhkosti v horním rozsahu pro vaše prostředí. Zadejte například *65*. Nastavili jste podmínku pro pravidlo, ke kterému dochází, když relativní vlhkost v jakémkoli RuuviTag reálném nebo simulovaném senzoru překročí tuto hodnotu. V závislosti na normálním rozsahu vlhkosti ve vašem prostředí možná budete muset upravit hodnotu nahoru nebo dolů.  

   ![Azure IoT Central – přidat podmínky pravidla](./media/tutorial-in-store-analytics-create-app/rules-add-conditions.png)

Postup přidání akce k pravidlu:

1. Vyberte **+ e-mail**. 

1. Jako popisný **Zobrazovaný název** pro akci zadejte *velké oznámení vlhkosti* . 

1. Zadejte e-mailovou adresu přidruženou k **vašemu účtu v.** Pokud používáte jiný e-mail, adresa, kterou použijete, musí být pro uživatele, který byl přidán do aplikace. Uživatel se taky musí přihlašovat a odhlásit aspoň jednou.

1. Volitelně můžete zadat poznámku, která se má zahrnout do textu e-mailu.

1. Akci dokončete výběrem možnosti **Hotovo** .

   ![Azure IoT Central přidání akcí do pravidel](./media/tutorial-in-store-analytics-create-app/rules-add-action.png)

1. Kliknutím na **Uložit** uložte a aktivujte nové pravidlo. 

    Během několika minut by měl zadaný e-mailový účet začínat příjem e-mailů. Aplikace pošle e-mail pokaždé, když senzor indikuje, že úroveň vlhkosti překročila hodnotu ve vaší podmínce.

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

* Použití šablony Azure IoT Central **in-Store Analytics – registrace** k vytvoření aplikace pro maloobchodní obchod
* Přizpůsobení nastavení aplikace
* Vytvoření a přizpůsobení šablon zařízení IoT
* Připojení zařízení k aplikaci
* Přidat pravidla a akce pro monitorování podmínek

Teď, když jste vytvořili aplikaci pro monitorování podmínky Azure IoT Central, je tady doporučený další krok:

> [!div class="nextstepaction"]
> [Přizpůsobení řídicího panelu operátoru](./tutorial-in-store-analytics-customize-dashboard.md)
