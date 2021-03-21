---
title: Konfigurace na řídicí panel Azure IoT Central | Microsoft Docs
description: Jako tvůrce se naučíte, jak nakonfigurovat výchozí řídicí panel aplikace IoT Central pro Azure pomocí dlaždic.
author: TheJasonAndrew
ms.author: v-anjaso
ms.date: 12/19/2020
ms.topic: how-to
ms.service: iot-central
ms.openlocfilehash: 73c78f514f974b97eb178177f12dc4ceff7ede63
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97797005"
---
# <a name="configure-the-application-dashboard"></a>Konfigurace řídicího panelu aplikace

**Řídicí panel** je první stránka, kterou vidíte při připojování k aplikaci IoT Central. Pokud vytvoříte aplikaci z jedné ze [šablon aplikací](./concepts-app-templates.md)zaměřených na obor, má vaše aplikace předem definovaný řídicí panel, který se má spustit. Pokud vytvoříte aplikaci z vlastní [šablony aplikace](./concepts-app-templates.md), na řídicím panelu se zobrazí několik tipů, které vám pomohou začít.

> [!TIP]
> Uživatelé mohou kromě výchozího řídicího panelu aplikace [vytvořit několik řídicích panelů](howto-create-personal-dashboards.md) . Tyto řídicí panely mohou být osobní pouze uživateli nebo sdíleny napříč všemi uživateli aplikace.  

## <a name="add-tiles"></a>Přidat dlaždice

Následující snímek obrazovky ukazuje řídicí panel v aplikaci vytvořené z vlastní šablony **aplikace** . Chcete-li upravit aktuální řídicí panel, vyberte možnost **Upravit**, chcete-li přidat vlastní osobní nebo sdílený řídicí panel, vyberte možnost **nové**:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/dashboard-sample-contoso.png" alt-text="Řídicí panel pro aplikace založené na šabloně vlastní aplikace":::

Po výběru **Upravit** nebo **Nový** je řídicí panel v režimu *úprav* . Pomocí nástrojů na panelu **Upravit řídicí** panel můžete přidat dlaždice na řídicí panel a přizpůsobit a odebrat dlaždice na samotném řídicím panelu. Například chcete-li přidat dlaždici **telemetrie** pro zobrazení aktuální teploty hlášené jedním nebo více zařízeními:

1. Vyberte **skupinu zařízení** a potom zvolte zařízení v rozevíracím seznamu **zařízení** , která chcete zobrazit na dlaždici. Nyní se zobrazí dostupná telemetrie, vlastnosti a příkazy ze zařízení.

1. V případě potřeby můžete použít rozevírací seznam k výběru hodnoty telemetrie, která se má zobrazit na dlaždici. Další položky můžete do dlaždice přidat tak, že vyberete **+ telemetrie**, **+ vlastnost** nebo **+ cloudovou vlastnost**.

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/device-details.png" alt-text="Přidání dlaždice telemetrie na teplotu na řídicí panel":::

Po výběru všech hodnot, které mají být na dlaždici zobrazeny, klikněte na tlačítko **Přidat dlaždici.** Dlaždice se nyní zobrazí na řídicím panelu, kde můžete změnit vizualizaci, změnit její velikost, přesunout ji a nakonfigurovat ji.

Až dokončíte přidávání a přizpůsobování dlaždic na řídicím panelu, vyberte **Uložit** a uložte změny na řídicí panel, který vás převezme z režimu úprav.

## <a name="customize-tiles"></a>Přizpůsobení dlaždic

Chcete-li upravit dlaždici, musíte být v režimu úprav.  Dostupné možnosti vlastního nastavení závisí na [typu dlaždice](#tile-types):

* Ikona pravítka na dlaždici umožňuje změnit vizualizaci. Mezi vizualizace patří spojnicové grafy, pruhové grafy, výsečové grafy, poslední známé hodnoty, klíčové ukazatele výkonu (neboli klíčové ukazatele výkonu), Heat mapy a mapy.

* Čtvercová ikona umožňuje změnit velikost dlaždice.

* Ikona ozubeného kolečka umožňuje nakonfigurovat vizualizaci. Například pro vizualizaci spojnicového grafu můžete zvolit zobrazení legendy a osy a zvolit časový rozsah pro vykreslení.


## <a name="tile-types"></a>Typy dlaždic

Následující tabulka popisuje různé typy dlaždic, které můžete přidat na řídicí panel:

| dlaždici             | Description |
| ---------------- | ----------- |
| Markdown         | Dlaždice Markdownu jsou kliknutí na dlaždice, které zobrazují nadpis a text popisu formátovaného pomocí Markdownu. Adresa URL může být relativní odkaz na jinou stránku v aplikaci nebo absolutní odkaz na externí Web.|
| Image            | Dlaždice obrázku zobrazují vlastní obrázek a lze na něj kliknout. Adresa URL může být relativní odkaz na jinou stránku v aplikaci nebo absolutní odkaz na externí Web.|
| Popisek            | Dlaždice popisku zobrazují na řídicím panelu vlastní text. Můžete zvolit velikost textu. Pomocí dlaždice popisku můžete na řídicím panelu Přidat relevantní informace, jako jsou popisy, kontaktní údaje nebo pomocníka.|
| Počet            | Počet dlaždic – zobrazí počet zařízení ve skupině zařízení.|
| Mapa              | Dlaždice mapy zobrazují umístění jednoho nebo více zařízení na mapě. Můžete také zobrazit až 100 bodů historie umístění zařízení. Můžete například zobrazit ukázku trasy, kde se zařízení nachází v minulém týdnu.|
| KPI              |  Dlaždice klíčových ukazatelů výkonu zobrazují agregované hodnoty telemetrie pro jedno nebo více zařízení za časové období. Můžete ho například použít k zobrazení maximální teploty a tlaku pro jedno nebo více zařízení během poslední hodiny.|
| Spojnicový graf       | Dlaždice spojnicového grafu vykreslí jednu nebo více agregovaných hodnot telemetrie pro jedno nebo více zařízení za časové období. Můžete například zobrazit spojnicový graf, který vykreslí průměrnou teplotu a tlak jednoho nebo více zařízení za poslední hodinu.|
| Pruhový graf        | Dlaždice pruhového grafu vykreslovat jednu nebo více agregovaných hodnot telemetrie pro jedno nebo více zařízení za časové období. Můžete například zobrazit pruhový graf, který zobrazuje průměrnou teplotu a tlak jednoho nebo více zařízení za poslední hodinu.|
| Výsečový graf        | Dlaždice výsečového grafu zobrazují jednu nebo více agregovaných hodnot telemetrie pro jedno nebo více zařízení za určité časové období.|
| Heat mapa         | Dlaždice Heat mapy zobrazují informace o jednom nebo více zařízeních, reprezentovaných jako barvy.|
| Poslední známá hodnota | Dlaždice Poslední známá hodnota zobrazí nejnovější hodnoty telemetrie pro jedno nebo více zařízení. Tuto dlaždici můžete například použít k zobrazení nejaktuálnějších hodnot teploty, tlaku a vlhkosti pro jedno nebo více zařízení. |
| Historie událostí    | Dlaždice historie událostí zobrazují události pro zařízení za časové období. Můžete ho například použít k zobrazení všech událostí otevření a zavření ventilů pro jedno nebo více zařízení za poslední hodinu.|
| Vlastnost         |  Dlaždice vlastností zobrazují aktuální hodnotu vlastností a vlastností cloudu jednoho nebo více zařízení. Tuto dlaždici můžete například použít k zobrazení vlastností zařízení, jako je výrobce nebo verze firmwaru pro zařízení. |

V současné době můžete přidat až 10 zařízení do dlaždic, které podporují více zařízení.

### <a name="customizing-visualizations"></a>Přizpůsobení vizualizací

Ve výchozím nastavení zobrazují spojnicové grafy data v časovém intervalu. Vybraný časový rozsah je rozdělen na 50, které mají stejnou velikost, a data zařízení se pak agreguje na jeden kontejner a poskytnou 50 datových bodů přes vybraný časový rozsah. Pokud chcete zobrazit nezpracovaná data, můžete změnit výběr tak, aby se zobrazily posledních 100 hodnot. Chcete-li změnit časový rozsah nebo vybrat vizualizaci nezpracovaných dat, použijte rozevírací seznam rozsah zobrazení na panelu **Konfigurovat graf** .

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/display-range.png" alt-text="Změna rozsahu zobrazení spojnicového grafu":::

Pro dlaždice, které zobrazují agregované hodnoty, vyberte pro výběr agregace ikonu ozubeného kolečka vedle typu telemetrie na panelu **Konfigurovat graf** . Můžete zvolit z hodnot průměr, součet, maximum, minimum a počet.

U spojnicových grafů, pruhových grafů a výsečových grafů můžete přizpůsobit barvu různých hodnot telemetrie. Vyberte ikonu palety vedle telemetrie, kterou chcete upravit:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/color-customization.png" alt-text="Změna barvy hodnoty telemetrie":::

Pro dlaždice, které zobrazují vlastnosti řetězce nebo hodnoty telemetrie, můžete zvolit, jak se má text zobrazit. Pokud například zařízení ukládá adresu URL do řetězcové vlastnosti, můžete ji zobrazit jako odkaz s možnostmi kliknutí. Pokud adresa URL odkazuje na obrázek, můžete obrázek vykreslit v poslední známé hodnotě nebo dlaždici Vlastnosti. Chcete-li změnit způsob zobrazení řetězce, v konfiguraci dlaždic vyberte ikonu ozubeného kolečka vedle typu nebo vlastnosti telemetrie:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/string-customization.png" alt-text="Změna způsobu zobrazení řetězce na dlaždici":::

Pro číselný **klíčový ukazatel výkonu**, **poslední známou hodnotu** a dlaždice **vlastností** můžete použít podmíněné formátování k přizpůsobení barvy dlaždice na základě její aktuální hodnoty. Pokud chcete přidat podmíněné formátování, na dlaždici vyberte **Konfigurovat** a potom vyberte ikonu **podmíněného formátování** vedle hodnoty k přizpůsobení:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/conditional-formatting-1.png" alt-text="Snímek obrazovky ukazující, jak najít možnost konfigurace pro dlaždici a potom ikonu podmíněného formátování":::

Přidejte pravidla podmíněného formátování:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/conditional-formatting-2.png" alt-text="Snímek obrazovky zobrazující pravidla podmíněného formátování pro průměrný tok Existují tři pravidla – méně než 20 je zelených, méně než 50 je žluté a vše nad 50 je červené.":::
   
Následující snímek obrazovky ukazuje efekt pravidla podmíněného formátování:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/conditional-formatting-3.png" alt-text="Snímek obrazovky zobrazující červenou barvu pozadí na dlaždici Průměrná voda Flow Číslo dlaždice je 50,54.":::

### <a name="tile-formatting"></a>formátování "dlaždice"
Tato funkce, která je dostupná v dlaždicích klíčového ukazatele výkonu, LKV a vlastností, umožňuje uživatelům upravovat velikost písma, zvolit desítkovou přesnost, zkrátit číselné hodnoty (například Format 1 700 jako 1.7 K) nebo hodnoty řetězce ve svých dlaždicích.

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/tile-format.png" alt-text="Formát dlaždic":::

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak nakonfigurovat výchozí řídicí panel aplikace Azure IoT Central, si můžete [přečíst, jak vytvořit osobní řídicí panel](howto-create-personal-dashboards.md).
