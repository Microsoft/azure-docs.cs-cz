---
title: Přidání simulovaného zařízení do Azure IoT Central | Microsoft Docs
description: Vytvořte šablonu zařízení a přidejte do aplikace IoT Central simulované zařízení.
author: dominicbetts
ms.author: dobett
ms.date: 10/24/2019
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 29d40556cb2335abf894c7fc6c0df7a6b7fbde7e
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2019
ms.locfileid: "72958144"
---
# <a name="quickstart-add-a-simulated-device-to-your-iot-central-application-preview-features"></a>Rychlý Start: Přidání simulovaného zařízení do aplikace IoT Central (funkce ve verzi Preview)

*Tento článek je pro operátory, tvůrce a správce.*

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Šablona zařízení definuje možnosti zařízení, které se připojuje k vaší IoT Central aplikaci. Mezi možnosti patří telemetrie, kterou zařízení odesílá, vlastnosti zařízení a příkazy, na které zařízení reaguje. V rámci šablony zařízení může tvůrce nebo operátor přidat do aplikace skutečná i simulovaná zařízení. Simulovaná zařízení jsou užitečná pro testování chování aplikace IoT Central před propojením reálných zařízení.

V tomto rychlém startu vytvoříte šablonu zařízení **senzoru pro životní prostředí** a přidáte simulované zařízení. Zařízení snímače prostředí:

* Odesílá telemetrii jako teplotu.
* Oznamuje vlastnosti specifické pro zařízení, jako je úroveň jasu.
* Reaguje na příkazy, jako je zapnutí a vypnutí.
* Sestavy obecných vlastností zařízení, jako je například verze firmwaru a sériové číslo.

## <a name="prerequisites"></a>Předpoklady

Dokončete průvodce [vytvořením aplikace Azure IoT Central (náhled funkcí)](./quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) a vytvořte aplikaci IoT Central pomocí vlastní šablony aplikace **> Preview** .

Budete také potřebovat místní kopii souboru **EnvironmentalSensorInline. capabilitymodel. JSON** , který obsahuje model schopností zařízení [IoT technologie Plug and Play](../../iot-pnp/overview-iot-plug-and-play.md) . Můžete si ho stáhnout [tady](https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/EnvironmentalSensorInline.capabilitymodel.json). Klikněte pravým tlačítkem na stránku a vyberte **Uložit jako**.

Po stažení souboru jej otevřete v textovém editoru a nahraďte tyto dvě instance `<YOUR_COMPANY_NAME_HERE>` názvem. Používejte pouze znaky a-z, A-Z, 0-9 a podtržítko.

## <a name="create-a-template"></a>Vytvoření šablony

Jako tvůrce můžete v aplikaci vytvořit a upravit šablony zařízení. Po publikování šablony zařízení můžete vygenerovat simulované zařízení nebo propojit skutečná zařízení, která implementují šablonu zařízení. Simulovaná zařízení umožňují otestovat chování aplikace před připojením reálného zařízení.

Chcete-li do aplikace přidat novou šablonu zařízení, otevřete stránku **šablony zařízení** . To provedete tak, že v levém podokně vyberete kartu **šablony zařízení** .

![Stránka šablony zařízení](./media/quick-create-pnp-device-pnp/devicedefinitions.png)

### <a name="add-a-device-capability-model"></a>Přidání modelu schopností zařízení

K dispozici je několik možností pro vytváření modelu schopností zařízení v IoT Central. Můžete si vytvořit vlastní model od začátku, importovat ze souboru, vybrat z katalogu zařízení nebo připojit zařízení IoT technologie Plug and Play přes připojení zařízení, kde je model schopností zařízení publikovaný ve veřejném úložišti. V tomto kurzu naimportujete model schopností zařízení ze souboru.

Následující kroky ukazují, jak naimportovat model schopností pro zařízení **snímače životního prostředí** . Tato zařízení odesílají telemetrii, například teplotu, do vaší aplikace:

1. Chcete-li přidat novou šablonu zařízení, vyberte **+** na stránce **šablony zařízení** .

1. Zvolte **zařízení IoT** ze seznamu vlastních šablon zařízení, vyberte **Další: přizpůsobit**, pak vybrat **Další: zkontrolovat**a pak vyberte **vytvořit**.

1. Jako název své šablony zařízení zadejte **senzor životního prostředí** .

1. Pokud chcete vytvořit nový model schopností zařízení ze souboru JSON, vyberte **importovat model schopností** . Přejděte do složky, kam jste uložili soubor **EnvironmentalSensorInline. capabilitymodel. JSON** na místním počítači. Vyberte soubor **EnvironmentalSensorInline. capabilitymodel. JSON** a pak vyberte **otevřít**. Model schopností environmentálního senzoru zahrnuje rozhraní **snímače životního prostředí** a **informace o zařízení** :

    ![Model schopností zařízení snímače prostředí](./media/quick-create-pnp-device-pnp/newdevicecapabilitymodel.png)

    Tato rozhraní definují možnosti zařízení **snímače životního prostředí** . Mezi možnosti patří telemetrie, kterou zařízení odesílá, vlastnosti, které zařízení hlásí, a příkazy, na které zařízení reaguje.

### <a name="add-cloud-properties"></a>Přidat vlastnosti cloudu

Šablona zařízení může obsahovat vlastnosti cloudu. Vlastnosti cloudu existují jenom v aplikaci IoT Central a nikdy se neodesílají do zařízení nebo se z něj nepřijímají.

1. Vyberte **vlastnosti cloudu** a pak **+ přidat cloudovou vlastnost**. Pomocí informací v následující tabulce můžete přidat vlastnost cloudu do šablony zařízení.

    | Zobrazovaný název      | Sémantický typ | Schéma |
    | ----------------- | ------------- | ------ |
    | Last Service Date (Datum poslední údržby) | Žádné          | Datum   |
    | Jméno zákazníka     | Žádné          | Řetězec |

1. Kliknutím na **Uložit** uložte změny:

    ![Vlastnosti cloudu](media/quick-create-pnp-device-pnp/cloudproperties.png)

## <a name="create-views"></a>Vytváření zobrazení

Jako tvůrce můžete aplikaci přizpůsobit tak, aby zobrazovala relevantní informace o zařízení snímače životního prostředí pro operátora. Vlastní nastavení umožňuje operátorovi spravovat zařízení senzorů pro životní prostředí připojená k aplikaci. Můžete vytvořit dva typy zobrazení pro operátora pro práci se zařízeními:

* Formuláře pro zobrazení a úpravy vlastností zařízení a cloudu.
* Řídicí panely k vizualizaci zařízení.

### <a name="generate-default-views"></a>Generovat výchozí zobrazení

Generování výchozích zobrazení představuje rychlý způsob, jak začít s vizualizací důležitých informací o zařízení. Pro šablonu zařízení můžete mít vygenerované až tři výchozí zobrazení:

* Zobrazení **příkazy** umožňuje operátorovi odeslat příkazy do vašeho zařízení.
* Zobrazení **přehledu** používá grafy a metriky k zobrazení telemetrie zařízení.
* V zobrazení **informace** se zobrazí vlastnosti zařízení.

Vyberte **zobrazení** a pak **vygenerujte výchozí zobrazení**.

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

    ![Publikovaný model](media/quick-create-pnp-device-pnp/publishedmodel.png)

Po publikování je šablona zařízení zobrazená na stránce **zařízení** a v operátoru. V publikované šabloně zařízení nemůžete upravovat model schopností zařízení bez vytváření nové verze. Můžete ale dělat aktualizace vlastností cloudu, přizpůsobení a zobrazení v publikované šabloně zařízení bez správy verzí. Po provedení změn vyberte **publikovat** , aby se tyto změny převedly do vašeho operátoru.

## <a name="add-a-simulated-device"></a>Přidání simulovaného zařízení

K přidání simulovaného zařízení do aplikace použijete šablonu zařízení **snímače prostředí** , kterou jste vytvořili.

1. Pokud chcete přidat nové zařízení jako operátor v levém podokně vyberte **zařízení** . Karta **zařízení** zobrazuje **všechna zařízení** a šablonu zařízení **snímače životního prostředí** . Vyberte **senzor prostředí**.

1. Pokud chcete přidat simulované zařízení snímače životního prostředí, vyberte **+ Nový**. Použijte navržené **ID zařízení** nebo zadejte svoje **ID zařízení**s malými písmeny. Můžete také zadat název nového zařízení. Přepněte **simulované** přepnutí na **zapnuto** a pak vyberte **vytvořit**.

    ![Simulované zařízení](./media/quick-create-pnp-device-pnp/simulated-device.png)

Nyní můžete pracovat se zobrazeními vytvořenými tvůrcem pro šablonu zařízení pomocí simulovaných dat.

## <a name="use-a-simulated-device-to-improve-views"></a>Použití simulovaného zařízení ke zlepšení zobrazení

Po vytvoření nového simulovaného zařízení může tvůrce pomocí tohoto zařízení pokračovat ve zlepšování a sestavování zobrazení pro šablonu zařízení.

1. V levém podokně zvolte **šablony zařízení** a vyberte šablonu **environmentálního senzoru** .

1. Vyberte libovolné zobrazení, které chcete upravit, nebo vytvořte nové zobrazení. Klikněte na **Konfigurovat verzi Preview zařízení**a pak **Vyberte ze spuštěného zařízení**. Tady si můžete vybrat, jestli nemá žádné zařízení ve verzi Preview, a to pomocí reálného zařízení, které můžete nakonfigurovat pro testování, nebo z existujícího zařízení, které jste přidali do IoT Central.

1. V seznamu vyberte simulované zařízení. Pak vyberte **použít**. Teď můžete vidět stejné simulované zařízení v zobrazeních šablon zařízení. Toto zobrazení je užitečné pro grafy a další vizualizace.

    ![Konfigurace zařízení Preview](./media/quick-create-pnp-device-pnp/configure-preview.png)

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se dozvěděli, jak vytvořit šablonu zařízení **snímače prostředí** a přidat do aplikace simulované zařízení.

Další informace o monitorování zařízení připojených k vaší aplikaci získáte, když budete pokračovat v rychlém startu:

> [!div class="nextstepaction"]
> [Konfigurace pravidel a akcí](./quick-configure-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
