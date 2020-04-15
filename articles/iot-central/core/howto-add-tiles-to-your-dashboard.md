---
title: Přidání dlaždic na řídicí panel | Dokumenty společnosti Microsoft
description: Jako tvůrce se dozvíte, jak nakonfigurovat výchozí řídicí panel aplikace Azure IoT Central.
author: mavoge
ms.author: mavoge
ms.date: 10/17/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: f75e5a28f7ec56750432e74ee48ba68491a5e481
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81310287"
---
# <a name="configure-the-application-dashboard"></a>Konfigurace řídicího panelu aplikace

**Řídicí panel** je stránka, která se načte, když uživatelé, kteří mají přístup k aplikaci, přejde na adresu URL aplikace. Pokud jste aplikaci vytvořili z jedné ze **šablon aplikací**, bude mít aplikace předdefinovaný řídicí panel, který bude spouštět. Pokud jste aplikaci vytvořili ze šablony aplikace **Starší verze,** bude řídicí panel prázdný a spustí se.

> [!NOTE]
> Uživatelé mohou kromě výchozího řídicího panelu aplikace [vytvořit více řídicích panelů.](howto-create-personal-dashboards.md) Tyto řídicí panely mohou být osobní pouze pro uživatele nebo sdíleny mezi všemi uživateli aplikace. 

## <a name="add-tiles"></a>Přidat dlaždice

Následující snímek obrazovky ukazuje řídicí panel v aplikaci vytvořené ze šablony **vlastní aplikace.** Pokud chcete přizpůsobit výchozí řídicí panel aplikace, vyberte **Upravit** v levém horním rohu stránky.

> [!div class="mx-imgBorder"]
> ![Řídicí panel pro aplikace založený na šabloně "Ukázka contoso"](media/howto-add-tiles-to-your-dashboard/dashboard-sample-contoso.png)

Když vyberete **Upravit,** otevře se panel knihovny řídicího panelu. Knihovna obsahuje dlaždice a základní prvky řídicího panelu, které můžete použít k přizpůsobení řídicího panelu.

> [!div class="mx-imgBorder"]
> ![Knihovna řídicího panelu](media/howto-add-tiles-to-your-dashboard/dashboard-library.png)

Můžete například přidat dlaždici **Telemetrie** pro aktuální teplotu zařízení. Postupujte následovně:
1. Výběr **šablony zařízení**
1. Vyberte **instanci zařízení** pro zařízení, které chcete zobrazit na dlaždici řídicího panelu. Pak uvidíte seznam vlastností zařízení, které lze použít na dlaždici.
1. Chcete-li vytvořit dlaždici na řídicím panelu, klikněte na **Teplota** a přetáhněte ji do oblasti řídicího panelu. Můžete také kliknout na zaškrtávací políčko vedle **položky Teplota** a klepnout na tlačítko **Kombinovat**. Následující snímek obrazovky ukazuje výběr šablony zařízení a instance zařízení a poté vytvoření dlaždice Teplotní telemetrie na řídicím panelu.
1. V levém horním rohu vyberte **Uložit,** chcete-li dlaždici uložit na řídicí panel.

> [!div class="mx-imgBorder"]
> ![Formulář Konfigurovat podrobnosti o zařízení s podrobnostmi o nastavení a vlastnostech](media/howto-add-tiles-to-your-dashboard/device-details.png)

Nyní, když operátor zobrazí výchozí řídicí panel aplikace, uvidí novou dlaždici s **teplotou** pro zařízení. Každá dlaždice má předem vybraný graf, graf atd., který se zobrazí při vytvoření dlaždice. Uživatelé se však mohou rozhodnout tuto vizualizaci upravit a změnit. 

> [!div class="mx-imgBorder"]
> ![Karta "Řídicí panel" se zobrazenými nastaveními a vlastnostmi dlaždice](media/howto-add-tiles-to-your-dashboard/settings-and-properties.png)

## <a name="edit-tiles"></a>Úpravy dlaždic

Pokud chcete upravit dlaždici na řídicím panelu, klikněte nejdřív v levém horním rohu stránky na **Upravit,** který otevře režim úprav řídicího panelu a všech jeho dlaždic. 

> [!div class="mx-imgBorder"]
> ![Obrazovka řídicího panelu s aktivovaným režimem úprav pro vybranou dlaždici](media/howto-add-tiles-to-your-dashboard/edit-mode.png)

Poté klikněte na ikonu **Ozubené kolo** v pravém horním rohu dlaždice, kterou chcete upravit. Zde můžete upravit aspekty dlaždice včetně názvu, její vizualizace, agregace atd.

> [!div class="mx-imgBorder"]
> ![Rozevírací nabídka pro nastavení agregace dlaždic](media/howto-add-tiles-to-your-dashboard/aggregation-settings.png)

Vizualizaci grafu můžete také změnit kliknutím na ikonu **Pravítko** na dlaždici.

> [!div class="mx-imgBorder"]
> ![Rozevírací nabídka pro nastavení vizualizace dlaždic](media/howto-add-tiles-to-your-dashboard/visualization-settings.png)

## <a name="tile-types"></a>Typy dlaždic

Následující tabulka shrnuje využití dlaždic v Azure IoT Central:
 
| dlaždici | Řídicí panel | Popis
| ----------- | ------- | ------- |
| Obsah | Řídicí panely sady aplikací a zařízení |Dlaždice podporované značkou jsou dlaždice, na které lze kliknout a které zobrazují nadpis a popisný text. Tuto dlaždici můžete také použít jako dlaždici odkazu, která uživateli umožní přejít na adresu URL související s vaší aplikací.|
| Image | Řídicí panely sady aplikací a zařízení |Dlaždice obrázků zobrazují vlastní obrázek a lze na ně kliknout. Pomocí dlaždice obrázku můžete přidat grafiku na řídicí panel a volitelně umožnit uživateli přejít na adresu URL relevantní pro vaši aplikaci.|
| Popisek | Řídicí panely aplikací |Dlaždice popisků zobrazují vlastní text na řídicím panelu. Můžete zvolit velikost textu. Pomocí dlaždice s popiskem můžete na řídicí panel přidat relevantní informace, jako jsou popisy, kontaktní údaje nebo nápověda.|
| Mapa | Řídicí panely aplikací a zařízení |Dlaždice mapy zobrazují umístění zařízení na mapě. Můžete také zobrazit až 100 bodů historie polohy zařízení. Můžete například zobrazit ukázkovou trasu, kde se zařízení v uplynulém týdnu nadisplejo.|
| Spojnicový graf | Řídicí panely aplikací a zařízení |Dlaždice spojnicí grafu zobrazují graf souhrnného měření pro zařízení po určitou dobu. Můžete například zobrazit spojnicový graf, který zobrazuje průměrnou teplotu a tlak zařízení za poslední hodinu.|
| Pruhový graf | Řídicí panely aplikací a zařízení |Dlaždice pruhového grafu zobrazují graf souhrnných měření pro zařízení po určitou dobu. Můžete například zobrazit pruhový graf, který zobrazuje průměrnou teplotu a tlak zařízení za poslední hodinu.|
| Výsečový graf | Řídicí panely sady aplikací a zařízení |Dlaždice výsečového grafu zobrazují graf souhrnných měření pro zařízení po určitou dobu.|
| Heat mapa | Řídicí panely sady aplikací a zařízení |Dlaždice Heat Map zobrazují informace o sadě zařízení, reprezentované jako barvy.|
| Historie událostí | Řídicí panely aplikací a zařízení |Dlaždice Historie událostí zobrazují události pro zařízení za určité časové období. Můžete ji například použít k zobrazení všech změn teploty zařízení během poslední hodiny.|
| Historie státu | Řídicí panely aplikací a zařízení |Dlaždice historie stavu zobrazují hodnoty měření za určité časové období. Můžete ji například použít k zobrazení hodnot teploty zařízení během poslední hodiny.|
| Klíčové ukazatele výkonu | Řídicí panely aplikací a zařízení | Dlaždice klíčových ukazatelů výkonu zobrazují agregotní telemetrii nebo měření událostí za určité časové období. Můžete ji například použít k zobrazení maximální teploty, které bylo dosaženo pro zařízení za poslední hodinu.|
| Poslední známá hodnota | Řídicí panely aplikací a zařízení |Dlaždice poslední známé hodnoty zobrazují nejnovější hodnotu pro telemetrii nebo měření stavu. Tuto dlaždici můžete například použít k zobrazení nejnovějších měření teploty, tlaku a vlhkosti zařízení.|

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak nakonfigurovat výchozí panel aplikace Azure IoT Central, můžete [se naučit, jak vytvořit osobní řídicí panel](howto-create-personal-dashboards.md).
