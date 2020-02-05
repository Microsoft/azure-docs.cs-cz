---
title: Přidání dlaždic do řídicího panelu | Microsoft Docs
description: Jako tvůrce se dozvíte, jak nakonfigurovat výchozí řídicí panel aplikace Azure IoT Central.
author: mavoge
ms.author: mavoge
ms.date: 10/17/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 35965f238ed5b7768dd174788453838c917adb32
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2020
ms.locfileid: "77023221"
---
# <a name="configure-the-application-dashboard"></a>Konfigurace řídicího panelu aplikace

**Řídicí panel** je stránka, která se načte, když uživatelé, kteří mají přístup k aplikaci, navigují na adresu URL aplikace. Pokud jste aplikaci vytvořili z některé z **šablon aplikace**, bude mít aplikace předem definovaný řídicí panel. Pokud jste aplikaci vytvořili ze šablony **starší verze** aplikace, bude řídicí panel prázdný, aby se spouštěl.

> [!NOTE]
> Uživatelé mohou kromě výchozího řídicího panelu aplikace [vytvořit několik řídicích panelů](howto-create-personal-dashboards.md) . Tyto řídicí panely mohou být osobní pouze uživateli nebo sdíleny napříč všemi uživateli aplikace. 

## <a name="add-tiles"></a>Přidat dlaždice

Následující snímek obrazovky ukazuje řídicí panel v aplikaci vytvořené z vlastní šablony **aplikace** . Pokud chcete pro svou aplikaci přizpůsobit výchozí řídicí panel, vyberte v levém horním rohu stránky **Upravit** .

> [!div class="mx-imgBorder"]
> Řídicí panel ![pro aplikace založené na šabloně Sample contoso](media/howto-add-tiles-to-your-dashboard/dashboard-sample-contoso.png)

Výběr možnosti **Upravit** otevře panel Knihovna řídicích panelů. Knihovna obsahuje prvky dlaždic a řídicích panelů, které můžete použít k přizpůsobení řídicího panelu.

> [!div class="mx-imgBorder"]
> ](media/howto-add-tiles-to-your-dashboard/dashboard-library.png) knihovny řídicích panelů ![

Můžete například přidat dlaždici **telemetrie** pro aktuální teplotu zařízení. Postupujte následovně:
1. Vybrat **šablonu zařízení**
1. Vyberte **instanci zařízení** pro zařízení, které chcete zobrazit na dlaždici řídicího panelu. Pak se zobrazí seznam vlastností zařízení, které se dají na dlaždici použít.
1. Pokud chcete vytvořit dlaždici na řídicím panelu, klikněte na **teplotu** a přetáhněte ji do oblasti řídicího panelu. Můžete také kliknout na zaškrtávací políčko vedle **teploty** a kliknout na **zkombinovat**. Následující snímek obrazovky ukazuje, jak vybrat šablonu zařízení a instanci zařízení a pak na řídicím panelu vytvořit dlaždici telemetrie s teplotou.
1. Vyberte **Uložit** v levém horním rohu a uložte dlaždici na řídicí panel.

> [!div class="mx-imgBorder"]
> ![formuláře "konfigurace podrobností o zařízení" s podrobnostmi o nastavení a vlastnostech](media/howto-add-tiles-to-your-dashboard/device-details.png)

Když teď operátor zobrazí výchozí řídicí panel aplikace, uvidí novou dlaždici s **teplotou** pro zařízení. Každá dlaždice má předem vybraný graf, graf atd., který se zobrazí při vytvoření dlaždice. Uživatelé ale můžou tuto vizualizaci upravit a změnit. 

> [!div class="mx-imgBorder"]
> karta řídicího panelu se zobrazeným nastavením a vlastnostmi pro dlaždici ![](media/howto-add-tiles-to-your-dashboard/settings-and-properties.png)

## <a name="edit-tiles"></a>Upravit dlaždice

Chcete-li upravit dlaždici na řídicím panelu, nejprve klikněte na tlačítko **Upravit** v levém horním rohu stránky, čímž otevřete režim úprav řídicího panelu a všech jeho dlaždic. 

> [!div class="mx-imgBorder"]
> obrazovka řídicího panelu ![s režimem úprav aktivovaným pro vybranou dlaždici](media/howto-add-tiles-to-your-dashboard/edit-mode.png)

Pak klikněte na ikonu **ozubeného kolečka** v pravém horním rohu dlaždice, kterou chcete upravit. Tady můžete upravit aspekty dlaždice včetně jejího názvu, vizualizace, agregace atd.

> [!div class="mx-imgBorder"]
> Rozevírací seznam ![pro nastavení agregace dlaždic](media/howto-add-tiles-to-your-dashboard/aggregation-settings.png)

Vizualizaci grafu můžete také změnit kliknutím na ikonu **pravítka** na dlaždici.

> [!div class="mx-imgBorder"]
> Rozevírací seznam ![pro nastavení vizualizace dlaždice](media/howto-add-tiles-to-your-dashboard/visualization-settings.png)

## <a name="tile-types"></a>Typy dlaždic

Následující tabulka shrnuje použití dlaždic v Azure IoT Central:
 
| Podobě | Řídicí panel | Popis
| ----------- | ------- | ------- |
| Obsah | Řídicí panely pro aplikace a nastavení zařízení |Dlaždice podporované Markdownu jsou kliknutí na dlaždice, které zobrazují nadpis a text popisu. Tuto dlaždici můžete použít také jako dlaždici odkazů a povolit tak uživateli přejít na adresu URL, která se vztahuje k vaší aplikaci.|
| Obrázek | Řídicí panely pro aplikace a nastavení zařízení |Dlaždice obrázku zobrazují vlastní obrázek a lze na něj kliknout. Pomocí dlaždice obrázek můžete přidat grafiku na řídicí panel a volitelně povolit uživateli přejít na adresu URL, která se vztahuje k vaší aplikaci.|
| Štítek | Řídicí panely aplikace |Dlaždice popisku zobrazují na řídicím panelu vlastní text. Můžete zvolit velikost textu. Pomocí dlaždice popisku můžete na řídicím panelu Přidat relevantní informace, jako jsou popisy, kontaktní údaje nebo pomocníka.|
| Mapa | Řídicí panely pro aplikace a nastavení zařízení |Dlaždice mapy zobrazují umístění a stav zařízení na mapě. Můžete například zobrazit, kde je zařízení a zda je jeho ventilátor zapnutý.|
| Spojnicový graf | Řídicí panely aplikací a zařízení |Dlaždice spojnicového grafu zobrazují graf agregovaného měření pro zařízení za určité časové období. Můžete například zobrazit spojnicový graf, který zobrazuje průměrnou teplotu a tlak zařízení za poslední hodinu.|
| Pruhový graf | Řídicí panely aplikací a zařízení |Dlaždice pruhového grafu zobrazují graf agregovaných měření pro zařízení za určité časové období. Můžete například zobrazit pruhový graf, který zobrazuje průměrnou teplotu a tlak zařízení za poslední hodinu.|
| Výsečový graf | Řídicí panely pro aplikace a nastavení zařízení |Dlaždice výsečového grafu zobrazují graf agregovaných měření pro zařízení za určité časové období.|
| Heat mapa | Řídicí panely pro aplikace a nastavení zařízení |Dlaždice Heat mapy zobrazují informace o sadě zařízení reprezentované jako barvy.|
| Historie událostí | Řídicí panely aplikací a zařízení |Dlaždice historie událostí zobrazují události pro zařízení za časové období. Můžete ho například použít k zobrazení všech změn teploty zařízení během poslední hodiny.|
| Historie stavu | Řídicí panely aplikací a zařízení |Dlaždice historie stavu zobrazují hodnoty měření za časové období. Můžete ho například použít k zobrazení hodnot teploty pro zařízení během poslední hodiny.|
| Klíčové ukazatele výkonu | Řídicí panely aplikací a zařízení | Dlaždice klíčových ukazatelů výkonu zobrazují agregovanou telemetrii nebo měření událostí za časové období. Můžete ho například použít k zobrazení maximální teploty zařízení během poslední hodiny.|
| Poslední známá hodnota | Řídicí panely aplikací a zařízení |Dlaždice Poslední známá hodnota zobrazují nejnovější hodnotu pro telemetrii nebo měření stavu. Pomocí této dlaždice můžete například zobrazit nejnovější měření teploty, tlaku a vlhkosti zařízení.|

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak nakonfigurovat výchozí řídicí panel aplikace Azure IoT Central, si můžete [přečíst, jak vytvořit osobní řídicí panel](howto-create-personal-dashboards.md).
