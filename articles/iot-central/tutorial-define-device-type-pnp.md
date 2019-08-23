---
title: Definování nového typu zařízení v aplikaci Azure IoT Central | Microsoft Docs
description: V tomto kurzu se dozvíte jako tvůrce, jak v aplikaci Azure IoT Central vytvořit nový typ zařízení. Definujete telemetrii, stav, vlastnosti a příkazy pro svůj typ.
author: dominicbetts
ms.author: dobett
ms.date: 08/06/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: d58cec644c75baaac37862f445477da92075c44d
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2019
ms.locfileid: "69907352"
---
# <a name="tutorial-define-a-new-device-type-in-your-azure-iot-central-application-preview-features"></a>Kurz: Definování nového typu zařízení v aplikaci Azure IoT Central (funkce ve verzi Preview)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

Tento kurz vám jako tvůrci ukáže, jak pomocí šablony zařízení definovat nový typ zařízení v aplikaci Microsoft Azure IoT Central. Šablona zařízení definuje možnosti vašeho zařízení. Mezi možnosti patří telemetrie, kterou zařízení odesílá, vlastnosti zařízení a příkazy, na které zařízení reaguje.

V tomto kurzu vytvoříte šablonu zařízení **snímače prostředí** . Zařízení snímače prostředí:

* Odesílá telemetrii jako teplotu.
* Oznamuje vlastnosti specifické pro zařízení, jako je úroveň jasu.
* Reaguje na příkazy, jako je zapnutí a vypnutí.
* Sestavy obecných vlastností zařízení, jako je například verze firmwaru a sériové číslo.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořte novou šablonu zařízení.
> * Importujte model schopností zařízení.
> * Vytvořte vlastnosti cloudu.
> * Definujte vizualizaci pro telemetrii zařízení.
> * Publikujte šablonu zařízení.
> * Vytvořte simulované zařízení pro šablonu zařízení.
> * Zobrazit simulované zařízení.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete aplikaci Azure IoT Central. Pokud jste dokončili rychlý start [Vytvoření aplikace Azure IoT Central](quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json), můžete znovu využít aplikaci, kterou jste v tomto rychlém startu vytvořili. Jinak použijte následující postup a vytvořte prázdnou aplikaci Azure IoT Central:

1. Přejděte na stránku [správce aplikací](https://aka.ms/iotcentral) Azure IoT Central.

1. Pokud máte předplatné Azure, přihlaste se pomocí přihlašovacích údajů, které používáte pro přístup k němu, jinak se přihlaste pomocí účet Microsoft:

    ![Zadání účtu organizace](./media/tutorial-define-device-type-pnp/sign-in.png)

1. Pokud chcete začít vytvářet novou aplikaci Azure IoT Central, vyberte **New Application** (Nová aplikace).

Vytvoření nové aplikace IoT Central v Azure, která používá funkce verze Preview, včetně technologie Plug and Play IoT:

1. Zvolte **Trial** (Zkušební verze). K vytvoření zkušební aplikace nepotřebujete předplatné Azure.

    Další informace o adresářích a předplatných najdete v [rychlém startu k vytvoření aplikace](quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

1. Vyberte **aplikace Preview**.

1. Volitelně můžete zvolit popisný název aplikace, jako je třeba **Klimatizace Contoso**. Azure IoT Central pro vás vygeneruje jedinečnou předponu URL. Tuto předponu URL můžete změnit, aby byla snáze zapamatovatelná.

1. Pokud vytváříte zkušební aplikaci, musíte zadat své kontaktní údaje.

1. Vyberte **Vytvořit**.

    ![Stránka vytvoření aplikace Azure IoT Central](./media/tutorial-define-device-type-pnp/iotcentralcreate.png)

    Další informace najdete v [rychlém startu k vytvoření aplikace](quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

Budete také potřebovat místní kopii souboru **EnvironmentalSensorInline. capabilitymodel. JSON** , který obsahuje model schopností zařízení [IoT technologie Plug and Play](https://aka.ms/iot-pnp-docs) . Můžete si ho stáhnout [tady](https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/EnvironmentalSensorInline.capabilitymodel.json). Klikněte pravým tlačítkem na stránku a vyberte **Uložit jako**.

Po stažení souboru jej otevřete v textovém editoru a nahraďte tyto dvě instance `<YOUR_COMPANY_NAME_HERE>` názvem. Používejte pouze znaky a-z, A-Z, 0-9 a podtržítko.

## <a name="create-a-template"></a>Vytvoření šablony

Jako tvůrce můžete v aplikaci vytvořit a upravit šablony zařízení. Po publikování šablony zařízení můžete vygenerovat simulované zařízení nebo propojit skutečná zařízení, která implementují šablonu zařízení. Simulovaná zařízení umožňují otestovat chování aplikace před připojením reálného zařízení.

Chcete-li do aplikace přidat novou šablonu zařízení, otevřete stránku **šablony zařízení** . Pokud to chcete udělat, vyberte v levé navigační nabídce kartu **šablony zařízení** .

![Stránka šablony zařízení](./media/tutorial-define-device-type-pnp/devicedefinitions.png)

### <a name="add-a-device-capability-model"></a>Přidání modelu schopností zařízení

K dispozici je několik možností pro vytváření modelu schopností zařízení v IoT Central. Můžete si vytvořit vlastní model od začátku, importovat ze souboru, vybrat z katalogu zařízení nebo připojit zařízení IoT technologie Plug and Play přes připojení zařízení, kde je model schopností zařízení publikovaný ve veřejném úložišti. V tomto kurzu naimportujete model schopností zařízení ze souboru.

Následující kroky ukazují, jak naimportovat model schopností pro zařízení **snímače životního prostředí** . Tato zařízení odesílají telemetrii, například teplotu, do vaší aplikace:

1. Chcete-li přidat novou šablonu zařízení, vyberte **+ Nový** na stránce **šablony zařízení** .

1. V seznamu šablon vyberte možnost **vlastní** .

1. Jako název své šablony zařízení zadejte **senzor životního prostředí** .

1. Pokud chcete vytvořit nový model schopností zařízení ze souboru JSON, vyberte **importovat model schopností** . Přejděte do složky, kam jste uložili soubor **EnvironmentalSensorInline. capabilitymodel. JSON** na místním počítači. Vyberte soubor **EnvironmentalSensorInline. capabilitymodel. JSON** a pak vyberte **otevřít**. Model schopností environmentálního senzoru zahrnuje rozhraní **snímače životního prostředí** a **informace o zařízení** :

    ![Model schopností zařízení snímače prostředí](./media/tutorial-define-device-type-pnp/newdevicecapabilitymodel.png)

    Tato rozhraní definují možnosti zařízení snímače **životního prostředí** . Mezi možnosti patří telemetrie, kterou zařízení odesílá, vlastnosti, které zařízení hlásí, a příkazy, na které zařízení reaguje.

### <a name="add-cloud-properties"></a>Přidat vlastnosti cloudu

Šablona zařízení může obsahovat vlastnosti cloudu. Vlastnosti cloudu existují jenom v aplikaci IoT Central a nikdy se neodesílají do zařízení nebo se z něj nepřijímají.

1. Vyberte **vlastnosti cloudu** a pak **+ přidat cloudovou vlastnost**. Pomocí informací v následující tabulce můžete přidat vlastnost cloudu do šablony zařízení.

    | Zobrazovaný název      | Sémantický typ | Schéma |
    | ----------------- | ------------- | ------ |
    | Last Service Date (Datum poslední údržby) | Žádné          | Date   |
    | Název zákazníka     | Žádné          | Řetězec |

1. Kliknutím na **Uložit** uložte změny:

    ![Vlastnosti cloudu](media/tutorial-define-device-type-pnp/cloudproperties.png)

### <a name="add-customizations"></a>Přidat přizpůsobení

Vlastní nastavení použijte v případě, že potřebujete změnit rozhraní nebo přidat funkce specifické pro IoT Central, které nevyžadují, abyste si využívali verzi modelu schopností zařízení. Můžete přizpůsobit pole, když je model schopností v konceptu nebo publikovaném stavu. Můžete přizpůsobit pouze pole, která neruší kompatibilitu rozhraní. Můžete například provést následující věci:

- Přizpůsobení zobrazovaného názvu a jednotek schopnosti.
- Přidejte výchozí barvu, která se použije, když se hodnota zobrazí v grafu.
- Zadejte počáteční, minimální a maximální hodnoty vlastnosti.

Nemůžete přizpůsobit název schopnosti ani typ schopnosti.


## <a name="create-views"></a>Vytváření zobrazení

Jako tvůrce můžete aplikaci přizpůsobit tak, aby zobrazovala relevantní informace o zařízení snímače životního prostředí pro operátora. Vlastní nastavení umožňuje operátorovi spravovat zařízení senzorů pro životní prostředí připojená k aplikaci. Můžete vytvořit dva typy zobrazení pro operátora pro práci se zařízeními:

* Formuláře pro zobrazení a úpravy vlastností zařízení a cloudu.
* Řídicí panely k vizualizaci zařízení.

### <a name="generate-default-views"></a>Generovat výchozí zobrazení

Generování výchozích zobrazení představuje rychlý způsob, jak začít s vizualizací důležitých informací o zařízení. Pro šablonu zařízení můžete mít vygenerované až tři výchozí zobrazení:

* Zobrazení **příkazy** umožňuje operátorovi odeslat příkazy do vašeho zařízení.
* Zobrazení **přehledu** používá grafy a metriky k zobrazení telemetrie zařízení.
* V zobrazení **informace** se zobrazí vlastnosti zařízení.

Když vyberete možnost **Generovat výchozí zobrazení**, automaticky se přidají do části **zobrazení** v šabloně zařízení.

### <a name="configure-a-view-to-visualize-devices"></a>Konfigurace zobrazení pro vizualizaci zařízení

Řídicí panel zařízení umožňuje, aby operátor vizualizuje zařízení pomocí grafů a metrik. Jako tvůrce můžete definovat, které informace se zobrazí na řídicím panelu zařízení. Pro zařízení můžete definovat několik řídicích panelů. Pokud chcete vytvořit řídicí panel pro vizualizaci telemetrie senzorů pro životní prostředí, vyberte **zobrazení** a pak **Vizualizujte zařízení**:

1. V části **vlastnosti**jsou uvedeny všechny vlastnosti zařízení, vlastnosti cloudu, telemetrie a statické možnosti. Jakoukoli z těchto položek můžete přetáhnout do zobrazení. Přetáhněte vlastnost **úroveň jasu** do zobrazení. Dlaždici můžete nakonfigurovat pomocí ikony ozubeného kolečka.

1. Chcete-li přidat graf, který vykresluje telemetrii, vyberte **vlhkost** a **teplotu**a pak vyberte **kombinovat**. Chcete-li zobrazit tento graf v jiném formátu, například výsečového grafu nebo pruhový graf, vyberte tlačítko **změnit vizualizaci** v horní části dlaždice.

1. Kliknutím na **Uložit** uložte zobrazení:

Můžete přidat další dlaždice, které zobrazují další vlastnosti nebo hodnoty telemetrie. Můžete také přidat statický text, odkazy a obrázky. Chcete-li přesunout nebo změnit velikost dlaždice na řídicím panelu, přesuňte ukazatel myši na dlaždici a přetáhněte dlaždici na nové umístění nebo změňte jeho velikost.

### <a name="add-a-device-form"></a>Přidat formulář zařízení

Formulář zařízení umožňuje operátorovi upravovat vlastnosti zapisovatelného zařízení a cloudové vlastnosti. Jako tvůrce můžete definovat několik forem a vybrat, které vlastnosti zařízení a cloudu se mají na každém formuláři zobrazit. Ve formuláři můžete také zobrazit vlastnosti zařízení jen pro čtení.

Vytvoření formuláře pro zobrazení a úpravy vlastností senzoru životního prostředí:

1. Přejděte k **zobrazením** v šabloně **environmentálního senzoru** . Kliknutím na dlaždici pro **úpravu zařízení a cloudových dat** přidejte nové zobrazení.

1. Zadejte název formuláře **vlastnosti senzoru prostředí**.

1. Přetáhněte **název zákazníka** a poslední vlastnosti cloudového **data služby** do existující části formuláře.

1. Vyberte **úroveň jasu** a vlastnosti zařízení **stavu zařízení** . Pak vyberte **přidat oddíl**. Upravte název oddílu na **vlastnosti senzoru**. Vyberte **Použít**.

1. Vyberte **model zařízení**, **verzi softwaru**, **výrobce**a **výrobce procesoru** – vlastnosti zařízení. Pak vyberte **přidat oddíl**. Upravte název oddílu na **vlastnosti zařízení**. Vyberte **Použít**.

1. Kliknutím na **Uložit** uložte zobrazení.

## <a name="publish-device-template"></a>Publikovat šablonu zařízení

Než budete moct vytvořit simulovaný senzor pro životní prostředí nebo připojit reálný senzor pro životní prostředí, budete muset publikovat šablonu zařízení.

Publikování šablony zařízení:

1. Na stránce **šablony zařízení** můžete přejít na šablonu zařízení.

1. Vyberte **Publikovat**.

1. V dialogovém okně **publikovat šablonu zařízení** vyberte **publikovat**:

    ![Publikovaný model](media/tutorial-define-device-type-pnp/publishedmodel.png)

Po publikování je šablona zařízení zobrazená na stránce **zařízení** a v operátoru. V publikované šabloně zařízení nemůžete upravovat model schopností zařízení bez vytváření nové verze. Můžete ale dělat aktualizace vlastností cloudu, přizpůsobení a zobrazení v publikované šabloně zařízení bez správy verzí. Po provedení změn vyberte **publikovat** , aby se tyto změny převedly do vašeho operátoru.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

* Vytvoření nové šablony zařízení
* Importujte model schopností zařízení.
* Vytvořte vlastnosti cloudu.
* Vytvořte vlastní nastavení.
* Definujte vizualizaci pro telemetrii zařízení.
* Publikujte šablonu zařízení.

Teď, když jste vytvořili šablonu zařízení v aplikaci Azure IoT Central, je tady doporučený další krok:

> [!div class="nextstepaction"]
> [Přidat zařízení](tutorial-add-device-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
