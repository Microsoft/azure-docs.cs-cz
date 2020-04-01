---
title: Kurz – přizpůsobení řídicího panelu operátora v Azure IoT Central
description: Tento kurz ukazuje, jak přizpůsobit řídicí panel operátora v aplikaci IoT Central a spravovat zařízení.
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
ms.openlocfilehash: fea3440dd7c017b85d6fffa2ec0fe5d26cd98b97
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77022167"
---
# <a name="tutorial--customize-the-operator-dashboard-and-manage-devices-in-azure-iot-central"></a>Kurz: Přizpůsobení řídicího panelu operátora a správa zařízení v Azure IoT Central


V tomto kurzu jako tvůrce se dozvíte, jak přizpůsobit řídicí panel operátora ve vaší analytické aplikaci Azure IoT Central v obchodě. Operátoři aplikací mohou pomocí přizpůsobeného řídicího panelu spustit aplikaci a spravovat připojená zařízení.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Změna názvu řídicího panelu
> * Přizpůsobení dlaždic obrázků na řídicím panelu
> * Uspořádání dlaždic pro úpravu rozložení
> * Přidání dlaždic telemetrie k zobrazení podmínek
> * Přidání dlaždic vlastností pro zobrazení podrobností o zařízení
> * Přidání dlaždic příkazů pro spouštění příkazů

## <a name="prerequisites"></a>Požadavky

Tvůrce by měl dokončit kurz k vytvoření analytické aplikace Azure IoT Central v obchodě a přidat zařízení:

* [Vytvoření analytické aplikace v obchodě v Azure IoT Central](./tutorial-in-store-analytics-create-app-pnp.md) (povinné)

## <a name="change-the-dashboard-name"></a>Změna názvu řídicího panelu
Chcete-li přizpůsobit řídicí panel operátora, musíte upravit výchozí řídicí panel ve vaší aplikaci. Můžete také vytvořit další nové řídicí panely. Prvním krokem k přizpůsobení řídicího panelu v aplikaci je změna názvu.

1. Přejděte na web [správce aplikací Azure IoT Central.](https://aka.ms/iotcentral)

1. Otevřete aplikaci pro monitorování podmínek, kterou jste vytvořili v [kurzu Vytvořit analytickou aplikaci v obchodě v kurzu Azure IoT Central.](./tutorial-in-store-analytics-create-app-pnp.md)

1. Na panelu nástrojů řídicího panelu vyberte **Upravit.** V režimu úprav můžete přizpůsobit vzhled, rozložení a obsah řídicího panelu.

    ![Řídicí panel úprav Azure IoT Central](./media/tutorial-in-store-analytics-customize-dashboard-pnp/dashboard-edit.png)

1. Volitelně můžete skrýt levé podokno. Skrytílevého podokna poskytuje větší pracovní plochu pro úpravy řídicího panelu.

1. Do **názvu řídicího panelu** zadejte popisný název řídicího panelu. Tento kurz používá fiktivní společnost s názvem Contoso a ukázkový název řídicího panelu *je Řídicí panel Conso*. 

1. Vyberte **Uložit**. Změny jsou uloženy na řídicím panelu a režim úprav je zakázán.

    ![Název řídicího panelu Azure IoT Central – změna](./media/tutorial-in-store-analytics-customize-dashboard-pnp/dashboard-change-name.png)

## <a name="customize-image-tiles-on-the-dashboard"></a>Přizpůsobení dlaždic obrázků na řídicím panelu
Řídicí panel aplikace Azure IoT Central se skládá z jedné nebo více dlaždic. Dlaždice je obdélníkový kontejner pro zobrazení obsahu na řídicím panelu. Přidružíte k dlaždicím různé typy obsahu a přetahování a změna velikosti dlaždic pro přizpůsobení rozložení řídicího panelu. Existuje několik typů dlaždic pro zobrazení obsahu. Dlaždice obrázků obsahují obrázky a můžete přidat adresu URL, která uživatelům umožní kliknout na obrázek. Dlaždice popisků zobrazují prostý text. Dlaždice Markdown obsahují formátovaný obsah a umožňují nastavit obrázek, adresu URL, název a kód značky, které se vykreslují jako HTML. Telemetrie, vlastnostnebo příkaz dlaždice zobrazení dat specifických pro zařízení. 

V této části se dozvíte, jak přizpůsobit dlaždice obrázků na řídicím panelu.

Přizpůsobení dlaždice obrázku, která zobrazuje obrázek značky na řídicím panelu:

1. Na panelu nástrojů řídicího panelu vyberte **Upravit.** 

1. Na dlaždici obrázku, která zobrazuje image značky Northwind, vyberte **Konfigurovat.** 

    ![Azure IoT Central upravit image značky](./media/tutorial-in-store-analytics-customize-dashboard-pnp/brand-image-edit.png)

1. Změňte **název**. Název se zobrazí, když uživatel najeví nad obrázkem.

1. Vyberte **obrázek**. Otevře se dialogové okno, které umožňuje nahrát vlastní obrázek. 

1. Volitelně zadejte adresu URL obrázku.

1. Vyberte **možnost Aktualizovat konfiguraci**. Tlačítko **Aktualizovat konfiguraci** ukládá změny na řídicí panel a ponechá režim úprav zapnutý.

    ![Obrázek značky Azure IoT Central pro ukládání](./media/tutorial-in-store-analytics-customize-dashboard-pnp/brand-image-save.png)

1. Volitelně můžete na dlaždici **Documentation** **dokumentace vybrat Konfigurovat** a zadat adresu URL obsahu podpory. 

Přizpůsobení dlaždice obrázku, která zobrazuje mapu zón snímačů v obchodě:

1. Na dlaždici obrázku, která zobrazuje výchozí mapu zóny úložiště, vyberte **Konfigurovat.** 

1. Vyberte **Obraz**a pomocí dialogového okna nahrajte vlastní obrázek mapy zóny úložiště. 

1. Vyberte **možnost Aktualizovat konfiguraci**.

    ![Mapa úložiště Azure IoT Central pro ukládání](./media/tutorial-in-store-analytics-customize-dashboard-pnp/store-map-save.png)

    Na příkladu mapy obchodu Contoso jsou zobrazeny čtyři zóny: dvě pokladní zóny, zóna pro oblečení a osobní péči a zóna pro potraviny a lahůdky. V tomto kurzu přidružíte senzory k těmto zónám a poskytnete tak telemetrii.

    ![Zóny úložiště Azure IoT Central](./media/tutorial-in-store-analytics-customize-dashboard-pnp/store-zones.png)

1. Vyberte **Uložit**. 

## <a name="arrange-tiles-to-modify-the-layout"></a>Uspořádání dlaždic pro úpravu rozložení
Klíčovým krokem při přizpůsobení řídicího panelu je změna uspořádání dlaždic a vytvoření užitečného zobrazení. Operátoři aplikací používají řídicí panel k vizualizaci telemetrie zařízení, správě zařízení a monitorování podmínek v úložišti. Azure IoT Central zjednodušuje úlohu tvůrce aplikací vytváření řídicího panelu. Režim úprav řídicího panelu umožňuje rychle přidávat, přesouvat, měnit velikost a odstraňovat dlaždice. Šablona **aplikace In-Store Analytics - pokladna** také zjednodušuje úlohu vytvoření řídicího panelu. Poskytuje funkční rozložení palubní desky s připojenými senzory a dlaždicemi, které zobrazují počty řádků pokladny a podmínky prostředí.

V této části změníte uspořádání řídicího panelu v **šabloně aplikace V obchodě – pokladna** a vytvoříte vlastní rozložení.

Odebrání dlaždic, které v aplikaci nechcete použít:

1. Na panelu nástrojů řídicího panelu vyberte **Upravit.** 

1. Výběrem **možnosti X Odstranit** odeberete následující dlaždice: **Zpět do všech zón**, Návštěva **řídicího panelu obchodu**, Doba **čekání**a všechny tři dlaždice přidružené k **pokladně 3**. Řídicí panel úložiště Contoso tyto dlaždice nepoužívá. 

    ![Azure IoT Central odstranění dlaždic](./media/tutorial-in-store-analytics-customize-dashboard-pnp/delete-tiles.png)

1. Posunutím přenesete zbývající dlaždice řídicího panelu do zobrazení.

1. Výběrem **možnosti X Odstranit** odstraníte následující dlaždice: **Zahřívací pokladna ,** **Zóna ochlazování,** **Nastavení senzoru obsazenosti**, **Nastavení senzoru termostatu**a **Podmínky prostředí**. 

   ![Azure IoT Central odstraňuje zbývající dlaždice](./media/tutorial-in-store-analytics-customize-dashboard-pnp/delete-tiles-2.png)

1. Vyberte **Uložit**. Odebráním nepoužitých dlaždic uvolníte místo na stránce úprav a zjednodušízobrazení zobrazení řídicího panelu pro operátory.

1. Zobrazení změn na řídicím panelu.

   ![Azure IoT Central po odstranění dlaždic](./media/tutorial-in-store-analytics-customize-dashboard-pnp/after-delete-tiles.png)

Po odebrání nepoužitých dlaždic změňte uspořádání zbývajících dlaždic tak, aby se vytvořilo uspořádané rozložení. Nové rozložení obsahuje prostor pro dlaždice, které přidáte v pozdějším kroku.

Změna uspořádání zbývajících dlaždic:

1. Vyberte **Upravit**.

1. Vyberte dlaždici **firmwaru obsazenosti** a přetáhněte ji napravo od dlaždice baterie **obsazenosti.**

1. Vyberte dlaždici **firmwaru termostatu** a přetáhněte ji napravo od dlaždice baterie **termostatu.**

1. Vyberte **Uložit**.

1. Zobrazení změn rozložení. 

    ![Dlaždice baterie firmwaru Azure IoT Central](./media/tutorial-in-store-analytics-customize-dashboard-pnp/firmware-battery-tiles.png)

## <a name="add-telemetry-tiles-to-display-conditions"></a>Přidání dlaždic telemetrie k zobrazení podmínek
Po přizpůsobení rozložení řídicího panelu můžete přidat dlaždice, které zobrazí telemetrii. Chcete-li vytvořit dlaždici telemetrie, vyberte šablonu zařízení a instanci zařízení a pak vyberte telemetrickou datovou metriku specifickou pro zařízení, která se má zobrazit na dlaždici. Šablona **aplikace V obchodě – pokladna** obsahuje několik dlaždic telemetrie na řídicím panelu. Čtyři dlaždice ve dvou zónách pokladny zobrazují telemetrii ze simulovaného snímače obsazenosti. Dlaždice **Návštěvnost lidé** zobrazuje počty ve dvou zónách pokladny. 

V této části přidáte další dvě telemetrické dlaždice, které zobrazily telemetrii prostředí ze senzorů RuuviTag, které jste přidali v kurzu [Vytvořit analytickou aplikaci v obchodě v kurzu Azure IoT Central.](./tutorial-in-store-analytics-create-app-pnp.md) 

Přidání dlaždic pro zobrazení dat o životním prostředí ze senzorů RuuviTag:

1. Vyberte **Upravit**.

1. Vyberte `RuuviTag` v seznamu **šablony zařízení.** 

1. Vyberte **instanci zařízení** jednoho ze dvou senzorů RuuviTag. V příkladu contoso `Zone 1 Ruuvi` úložiště, vyberte vytvořit dlaždici telemetrie pro zónu 1. 

1. `Relative humidity` Vyberte `temperature` a v seznamu **Telemetrie.** Jedná se o položky telemetrie, které se zobrazí pro každou zónu na dlaždici.

1. Vyberte **kombinovat**. 

    ![Azure IoT Central přidat dlaždici RuuviTag 1](./media/tutorial-in-store-analytics-customize-dashboard-pnp/add-zone1-ruuvi.png)

    Objeví se nová dlaždice, která zobrazuje kombinovanou telemetrii vlhkosti a teploty pro vybraný senzor. 

1. Na nové dlaždici pro senzor RuuviTag vyberte **Konfigurovat.** 

1. Změňte **prostředí Název** zóny *1*. 

1. Vyberte **možnost Aktualizovat konfiguraci**.

1. Opakováním předchozích kroků vytvořte dlaždici pro druhou instanci senzoru. Nastavte **prostředí Název** zóny *2* a pak vyberte **Aktualizovat konfiguraci.**

1. Přetáhněte dlaždici s názvem **Prostředí zóny 2** pod dlaždici **firmwaru termostatu.** 

1. Přetáhněte dlaždici s názvem **Prostředí zóny 1** pod dlaždici Návštěvnost **lidí.** 

1. Vyberte **Uložit**. Řídicí panel zobrazuje telemetrii zóny ve dvou nových dlaždicích.

    ![Azure IoT Central všechny dlaždice RuuviTag](./media/tutorial-in-store-analytics-customize-dashboard-pnp/all-ruuvitag-tiles.png)

Úprava dlaždice **Návštěvnost osob** tak, aby zobrazovala telemetrii pouze pro dvě zóny pokladny:

1. Vyberte **Upravit**. 

1. Na dlaždici **Návštěvnost osob** vyberte **Konfigurovat.**

1. V **telemetrii** vyberte **počet 1**, počet **2**a **počet 3**. 

1. Vyberte **možnost Aktualizovat konfiguraci**. Vymaže existující konfiguraci na dlaždici. 

1. Na dlaždici **Návštěvnost osob** vyberte znovu **konfigurovat.**

1. V **telemetrii** vyberte **počet 1**a počet **2**. 

1. Vyberte **možnost Aktualizovat konfiguraci**. 

1. Vyberte **Uložit**.  Aktualizovaný řídicí panel zobrazuje počty pouze pro vaše dvě pokladní zóny, které jsou založeny na simulovaném snímači obsazenosti.

    ![Azure IoT Central lidé provozu dva pruhy](./media/tutorial-in-store-analytics-customize-dashboard-pnp/people-traffic-two-lanes.png)

## <a name="add-property-tiles-to-display-device-details"></a>Přidání dlaždic vlastností pro zobrazení podrobností o zařízení
Operátoři aplikací používají řídicí panel ke správě zařízení a sledování stavu. Přidejte dlaždici pro každý RuuviTag, aby operátoři mohli zobrazit verzi softwaru. 

Přidání dlaždice vlastnosti pro každý RuuviTag:

1. Vyberte **Upravit**.

1. Vyberte `RuuviTag` v seznamu **šablony zařízení.** 

1. Vyberte **instanci zařízení** jednoho ze dvou senzorů RuuviTag. V příkladu contoso `Zone 1 Ruuvi` úložiště, vyberte vytvořit dlaždici telemetrie pro zónu 1. 

1. Vyberte **možnost Vlastnosti > verze softwaru**.

1. Vyberte **kombinovat**. 

1. Na nově vytvořené dlaždici s názvem **Verze softwaru**vyberte **Konfigurovat** . 

1. Změňte **název** na *verzi softwaru Ruuvi 1*.

1. Vyberte **možnost Aktualizovat konfiguraci**. 

1. Přetáhněte dlaždici s názvem **Verze softwaru Ruuv 1** pod dlaždici prostředí zóny **1.**

1. Opakováním předchozích kroků vytvořte dlaždici vlastností verze softwaru pro druhou položku RuuviTag. 

1. Vyberte **Uložit**.  

    ![Dlaždice vlastností Azure IoT Central RuuviTag](./media/tutorial-in-store-analytics-customize-dashboard-pnp/add-ruuvi-property-tiles.png)

## <a name="add-command-tiles-to-run-commands"></a>Přidání dlaždic příkazů pro spouštění příkazů
Operátoři aplikací také používají řídicí panel ke správě zařízení spuštěním příkazů. Na řídicí panel, který bude spouštět předdefinované příkazy na zařízení, můžete přidat dlaždice příkazů. V této části přidáte dlaždici příkazu, která operátorům umožní restartovat bránu Rigado. 

Přidání dlaždice příkazů pro restartování brány:

1. Vyberte **Upravit**. 

1. Vyberte `C500` v seznamu **šablony zařízení.** Je to šablona pro bránu Rigado C500. 

1. Vyberte instanci brány v **instanci Zařízení**.

1. Vyberte **Příkaz > Restarta** a přetáhněte ho do řídicího panelu vedle mapy obchodu. 

1. Vyberte **Uložit**. 

1. Prohlédněte si dokončený řídicí panel Contoso. 

    ![Přizpůsobení řídicího panelu Azure IoT Central– dokončení](./media/tutorial-in-store-analytics-customize-dashboard-pnp/completed-dashboard.png)

1. Volitelně můžete vybrat dlaždici **Reboot** a spustit příkaz restartovat počítač na bráně.

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste se naučili:

* Změna názvu řídicího panelu
* Přizpůsobení dlaždic obrázků na řídicím panelu
* Uspořádání dlaždic pro úpravu rozložení
* Přidání dlaždic telemetrie k zobrazení podmínek
* Přidání dlaždic vlastností pro zobrazení podrobností o zařízení
* Přidání dlaždic příkazů pro spouštění příkazů

Teď, když jste si přizpůsobili řídicí panel ve své analytické aplikaci Azure IoT Central v obchodě, tady je další doporučený krok:

> [!div class="nextstepaction"]
> [Export dat a vizualizace přehledů](./tutorial-in-store-analytics-export-data-visualize-insights-pnp.md)
