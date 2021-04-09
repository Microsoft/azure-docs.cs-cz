---
title: Kurz – přizpůsobení řídicího panelu operátora v Azure IoT Central
description: V tomto kurzu se dozvíte, jak přizpůsobit řídicí panel operátora v aplikaci IoT Central a jak spravovat zařízení.
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
ms.openlocfilehash: 74ce36652b1a00ac6813c62a17d44e2a5486645f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "99831705"
---
# <a name="tutorial--customize-the-operator-dashboard-and-manage-devices-in-azure-iot-central"></a>Kurz: přizpůsobení řídicího panelu operátora a Správa zařízení v Azure IoT Central


V tomto kurzu se jako tvůrce naučíte, jak přizpůsobit řídicí panel operátoru v aplikaci Azure IoT Central v rámci analytické aplikace v obchodě. Obsluha aplikace může pomocí přizpůsobeného řídicího panelu spustit aplikaci a spravovat připojená zařízení.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Změnit název řídicího panelu
> * Přizpůsobení dlaždic obrázku na řídicím panelu
> * Uspořádat dlaždice a upravit rozložení
> * Přidat dlaždice telemetrie pro zobrazované podmínky
> * Přidat dlaždice vlastností pro zobrazení podrobností o zařízení
> * Přidání dlaždic příkazů ke spuštění příkazů

## <a name="prerequisites"></a>Požadavky

Tvůrce by měl dokončit kurz pro vytvoření aplikace Azure IoT Central in-Store Analytics a přidat zařízení:

* [Vytvoření analytické aplikace v obchodě v Azure IoT Central](./tutorial-in-store-analytics-create-app.md) (povinné)

## <a name="change-the-dashboard-name"></a>Změnit název řídicího panelu
Chcete-li přizpůsobit řídicí panel operátora, je nutné upravit výchozí řídicí panel v aplikaci. Můžete také vytvořit další nové řídicí panely. Prvním krokem pro přizpůsobení řídicího panelu ve vaší aplikaci je změna názvu.

1. Přejděte na web [Azure IoT Central Správce aplikací](https://aka.ms/iotcentral) .

1. Otevřete aplikaci pro monitorování stavu, kterou jste vytvořili v části [Vytvoření analytické aplikace v rámci úložiště v Azure IoT Central](./tutorial-in-store-analytics-create-app.md) kurzu.

1. Na panelu nástrojů řídicí panel vyberte **Upravit** . V režimu úprav můžete přizpůsobit vzhled, rozložení a obsah řídicího panelu.

    ![Řídicí panel pro úpravy Azure IoT Central](./media/tutorial-in-store-analytics-customize-dashboard/dashboard-edit.png)

1. Volitelně můžete skrýt levé podokno. Skrytím levého podokna získáte větší pracovní oblast pro úpravy řídicího panelu.

1. Zadejte popisný název řídicího panelu v **názvu řídicího panelu.** V tomto kurzu se používá fiktivní společnost s názvem contoso a Příkladem názvu řídicího panelu je *řídicí panel contoso*. 

1. Vyberte **Uložit**. Změny jsou uloženy na řídicím panelu a režim úprav je zakázán.

    ![Azure IoT Central změnit název řídicího panelu](./media/tutorial-in-store-analytics-customize-dashboard/dashboard-change-name.png)

## <a name="customize-image-tiles-on-the-dashboard"></a>Přizpůsobení dlaždic obrázku na řídicím panelu
Řídicí panel aplikace Azure IoT Central se skládá z jedné nebo několika dlaždic. Dlaždice je obdélníkový kontejner pro zobrazení obsahu na řídicím panelu. K dlaždicím můžete přidružit různé typy obsahu a přetažením, přetažením a změnou velikosti dlaždic přizpůsobit rozložení řídicího panelu. Existuje několik typů dlaždic pro zobrazení obsahu. Dlaždice obrázků obsahují obrázky a můžete přidat adresu URL, která umožňuje uživatelům kliknout na obrázek. Dlaždice popisku zobrazují prostý text. Dlaždice Markdownu obsahují formátovaný obsah a umožňují nastavit obrázek, adresu URL, název a Markdownu kód, který se vykresluje jako HTML. Telemetrie, vlastnosti nebo příkazové dlaždice zobrazují data specifická pro zařízení. 

V této části se dozvíte, jak přizpůsobit dlaždice obrázků na řídicím panelu.

Pokud chcete přizpůsobit dlaždici s obrázkem, která zobrazuje obrázek značky na řídicím panelu:

1. Na panelu nástrojů řídicí panel vyberte **Upravit** . 

1. Na dlaždici s obrázkem, která zobrazuje obrázek značky Northwind, vyberte **Konfigurovat** . 

    ![Obrázek značky pro úpravy v Azure IoT Central](./media/tutorial-in-store-analytics-customize-dashboard/brand-image-edit.png)

1. Změňte **název**. Nadpis se zobrazí, když uživatel najede na obrázek.

1. Vyberte **Obrázek**. Otevře se dialogové okno a umožní vám nahrát vlastní image. 

1. Volitelně můžete zadat adresu URL pro obrázek.

1. Vyberte **Konfigurace aktualizace**. Tlačítko **aktualizovat konfiguraci** uloží změny na řídicí panel a nechá režim úprav povolený.

    ![Obrázek značky uložení v Azure IoT Central](./media/tutorial-in-store-analytics-customize-dashboard/brand-image-save.png)

1. Volitelně můžete vybrat **Konfigurovat** na dlaždici s názvem **dokumentace** a zadat adresu URL pro obsah podpory. 

Pokud chcete přizpůsobit dlaždici s obrázkem, která zobrazuje mapu zón senzorů ve Storu:

1. Na dlaždici obrázek vyberte **Konfigurovat** , kde se zobrazuje výchozí mapa zóny úložiště. 

1. Vyberte **Image** a pomocí dialogového okna nahrajte vlastní image mapy zóny úložiště. 

1. Vyberte **Konfigurace aktualizace**.

    ![Mapa úložiště pro Azure IoT Central Uložit](./media/tutorial-in-store-analytics-customize-dashboard/store-map-save.png)

    Ukázka mapa společnosti Contoso zobrazuje čtyři zóny: dvě zóny rezervace, oblast pro oděvy a osobní péči a zónu pro nákup a do. V tomto kurzu přidružíte senzory k těmto zónám za účelem poskytování telemetrie.

    ![Zóny Azure IoT Central Store](./media/tutorial-in-store-analytics-customize-dashboard/store-zones.png)

1. Vyberte **Uložit**. 

## <a name="arrange-tiles-to-modify-the-layout"></a>Uspořádat dlaždice a upravit rozložení
Hlavním krokem při přizpůsobování řídicího panelu je změna uspořádání dlaždic za účelem vytvoření užitečného zobrazení. Obsluha aplikace pomocí řídicího panelu vizualizuje telemetrie zařízení, spravuje zařízení a monitoruje podmínky v obchodě. Azure IoT Central zjednodušuje úlohu sestavovatele aplikace při vytváření řídicího panelu. Režim úprav řídicího panelu vám umožní rychle přidat, přesunout, změnit velikost a odstranit dlaždice. Šablona aplikace **s rezervací v rámci Storu** také zjednodušuje úlohu vytváření řídicího panelu. Poskytuje pracovní řídicí panel s připojenými senzory a dlaždice, které zobrazují počty řádků rezervace a podmínky prostředí.

V této části změníte uspořádání řídicího panelu v šabloně aplikace **v rámci úložiště analýzy – rezervace** a vytvoříte vlastní rozložení.

Chcete-li odebrat dlaždice, které nechcete použít v aplikaci:

1. Na panelu nástrojů řídicí panel vyberte **Upravit** . 

1. Vyberte **X Delete** pro odebrání následujících dlaždic: **zpět do všech zón**, **navštívit řídicí panel obchodu**, **čekací doba** a všechny tři dlaždice spojené s **rezervací 3**. Řídicí panel společnosti Contoso tyto dlaždice nepoužívá. 

    ![Odstranění dlaždic v Azure IoT Central](./media/tutorial-in-store-analytics-customize-dashboard/delete-tiles.png)

1. Posuňte se a přepněte zbývající dlaždice řídicího panelu na zobrazení.

1. Pokud chcete odebrat následující dlaždice, vyberte **X Delete** : **zóna zahřívání**, **zóna pro vyjmutí** z **elektrického prostředí, nastavení senzoru pro vyobsazení**, **nastavení snímače termostatu** a **podmínky prostředí**. 

   ![Zbývající dlaždice odstranění služby Azure IoT Central](./media/tutorial-in-store-analytics-customize-dashboard/delete-tiles-2.png)

1. Vyberte **Uložit**. Odebrání nepoužívaných dlaždic uvolní místo na stránce pro úpravy a zjednodušuje zobrazení řídicího panelu pro operátory.

1. Prohlédněte si změny na řídicím panelu.

   ![IoT Central Azure po odstranění dlaždic](./media/tutorial-in-store-analytics-customize-dashboard/after-delete-tiles.png)

Po odebrání nepoužívaných dlaždic znovu uspořádejte zbývající dlaždice a vytvořte uspořádané rozložení. Nové rozložení obsahuje místo pro dlaždice, které přidáte v pozdějším kroku.

Změna uspořádání zbývajících dlaždic:

1. Vyberte **Upravit**.

1. Vyberte dlaždici s **firmwarem obsazení** a přetáhněte ji napravo od dlaždice **obsazení** baterie.

1. Vyberte dlaždici **termostatu pro firmware** a přetáhněte ji napravo od dlaždice **termostatu** na baterii.

1. Vyberte **Uložit**.

1. Zobrazit změny v rozložení. 

    ![Dlaždice baterie pro firmware Azure IoT Central](./media/tutorial-in-store-analytics-customize-dashboard/firmware-battery-tiles.png)

## <a name="add-telemetry-tiles-to-display-conditions"></a>Přidat dlaždice telemetrie pro zobrazované podmínky
Po přizpůsobení rozložení řídicího panelu jste připraveni přidat dlaždice pro zobrazení telemetrie. Chcete-li vytvořit dlaždici telemetrie, vyberte šablonu zařízení a instanci zařízení a pak vyberte telemetrii pro konkrétní zařízení, která se zobrazí na dlaždici. Šablona aplikace **s rezervací v rámci obchodu** obsahuje na řídicím panelu několik dlaždic telemetrie. Čtyři dlaždice ve dvou zónách pro rezervaci zobrazují telemetrii ze senzoru simulovaného obsazení. Dlaždice **provoz osob** zobrazuje počty ve dvou zónách rezervace. 

V této části přidáte dvě další dlaždice telemetrie pro zobrazení telemetrie z prostředí RuuviTag senzorů, které jste přidali v tématu [Vytvoření analytické aplikace v rámci úložiště v Azure IoT Central](./tutorial-in-store-analytics-create-app.md) kurzu. 

Přidání dlaždic pro zobrazení dat o životním prostředí ze senzorů RuuviTag:

1. Vyberte **Upravit**.

1. `RuuviTag`V seznamu **Šablona zařízení** vyberte. 

1. Vyberte **instanci zařízení** jednoho ze dvou senzorů RuuviTag. V ukázkovém obchodě contoso vyberte `Zone 1 Ruuvi` vytvořit dlaždici telemetrie pro zóna 1. 

1. `Relative humidity` `temperature` V seznamu **telemetrie** vyberte a. Jedná se o položky telemetrie, které se zobrazují pro každou zónu na dlaždici.

1. Vyberte **kombinovat**. 

    ![Azure IoT Central – přidat dlaždici RuuviTag 1](./media/tutorial-in-store-analytics-customize-dashboard/add-zone1-ruuvi.png)

    Zobrazí se nová dlaždice pro zobrazení kombinované vlhkosti a telemetrie teploty pro vybraný senzor. 

1. Na nové dlaždici pro senzor RuuviTag vyberte **Konfigurovat** . 

1. Změňte **název** na *zóna 1 prostředí*. 

1. Vyberte **Konfigurace aktualizace**.

1. Zopakováním předchozích kroků vytvořte dlaždici druhé instance snímače. Nastavte **název** na *zóna 2 prostředí* a pak vyberte **aktualizovat konfiguraci.**

1. Přetáhněte dlaždici s názvem **zóna 2 prostředí** pod dlaždici **termostatu pro firmware** . 

1. Přetáhněte dlaždici s názvem **zóna 1 prostředí** pod dlaždici **přenosů osob** . 

1. Vyberte **Uložit**. Řídicí panel zobrazuje telemetrii zóny v těchto dvou nových dlaždicích.

    ![Azure IoT Central všechny dlaždice RuuviTag](./media/tutorial-in-store-analytics-customize-dashboard/all-ruuvitag-tiles.png)

Postup úpravy dlaždice **provoz osob** pro zobrazení telemetrie jenom pro dvě zóny rezervace:

1. Vyberte **Upravit**. 

1. Na dlaždici **provoz osob** vyberte **Konfigurovat** .

1. V **telemetrie** vyberte **počet 1**, **Počet 2** a **počet 3**. 

1. Vyberte **Konfigurace aktualizace**. Vymaže existující konfiguraci na dlaždici. 

1. Na dlaždici **provoz osob** znovu vyberte **Konfigurovat** .

1. V **telemetrie** vyberte **počet 1** a **Počet 2**. 

1. Vyberte **Konfigurace aktualizace**. 

1. Vyberte **Uložit**.  Aktualizovaný řídicí panel zobrazuje počty jenom pro vaše dvě zóny rezervací, které jsou založené na simulovaném snímači obsazení.

    ![Provoz Azure IoT Central lidé dva dráhy](./media/tutorial-in-store-analytics-customize-dashboard/people-traffic-two-lanes.png)

## <a name="add-property-tiles-to-display-device-details"></a>Přidat dlaždice vlastností pro zobrazení podrobností o zařízení
Operátoři aplikace používají řídicí panel ke správě zařízení a stav monitorování. Přidejte dlaždici pro každý RuuviTag a povolte tak operátorům zobrazení verze softwaru. 

Chcete-li přidat dlaždici vlastností pro jednotlivé RuuviTag:

1. Vyberte **Upravit**.

1. `RuuviTag`V seznamu **Šablona zařízení** vyberte. 

1. Vyberte **instanci zařízení** jednoho ze dvou senzorů RuuviTag. V ukázkovém obchodě contoso vyberte `Zone 1 Ruuvi` vytvořit dlaždici telemetrie pro zóna 1. 

1. Vyberte **vlastnosti > verze softwaru**.

1. Vyberte **kombinovat**. 

1. Na nově vytvořené dlaždici s názvem **verze softwaru** vyberte **Konfigurovat** . 

1. Změňte **název** na *Ruuvi 1 verzi softwaru*.

1. Vyberte **Konfigurace aktualizace**. 

1. Přetáhněte dlaždici s názvem **Ruuv 1 verze softwaru** pod dlaždici **zóna 1 prostředí** .

1. Zopakováním předchozích kroků vytvořte dlaždici vlastností verze softwaru pro druhý RuuviTag. 

1. Vyberte **Uložit**.  

    ![Dlaždice vlastností RuuviTag v Azure IoT Central](./media/tutorial-in-store-analytics-customize-dashboard/add-ruuvi-property-tiles.png)

## <a name="add-command-tiles-to-run-commands"></a>Přidání dlaždic příkazů ke spuštění příkazů
Operátoři aplikace také používají řídicí panel ke správě zařízení spuštěním příkazů. Do řídicího panelu, který bude spouštět předdefinované příkazy na zařízení, můžete přidat příkazové dlaždice. V této části přidáte dlaždici příkazů, která umožní operátorům restartovat bránu Rigado. 

Chcete-li přidat dlaždici příkazu pro restartování brány:

1. Vyberte **Upravit**. 

1. `C500`V seznamu **Šablona zařízení** vyberte. Jedná se o šablonu pro bránu Rigado C500. 

1. Vyberte instanci brány v **instanci zařízení**.

1. Vyberte **příkaz > restartovat** a přetáhněte ho na řídicí panel vedle mapy Storu. 

1. Vyberte **Uložit**. 

1. Zobrazte si dokončený řídicí panel společnosti Contoso. 

    ![Dokončení přizpůsobení řídicího panelu Azure IoT Central](./media/tutorial-in-store-analytics-customize-dashboard/completed-dashboard.png)

1. Volitelně můžete vybrat dlaždici pro **restartování** a spustit příkaz k restartování v bráně.

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

* Změnit název řídicího panelu
* Přizpůsobení dlaždic obrázku na řídicím panelu
* Uspořádat dlaždice a upravit rozložení
* Přidat dlaždice telemetrie pro zobrazované podmínky
* Přidat dlaždice vlastností pro zobrazení podrobností o zařízení
* Přidání dlaždic příkazů ke spuštění příkazů

Teď, když jste přizpůsobili řídicí panel v aplikaci Azure IoT Central v rámci služby Azure Analytics, je zde doporučený další krok:

> [!div class="nextstepaction"]
> [Export dat a vizualizace přehledů](./tutorial-in-store-analytics-export-data-visualize-insights.md)
