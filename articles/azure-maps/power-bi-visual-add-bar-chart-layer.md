---
title: Přidání vrstvy pruhového grafu do Azure Maps Power BI vizuálu | Mapy Microsoft Azure
description: V tomto článku se naučíte, jak použít vrstvu pruhového grafu ve vizuálu Microsoft Azure Maps pro Power BI.
author: rbrundritt
ms.author: richbrun
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 6a686f6915e332893f619e66944591999063a07c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87288162"
---
# <a name="add-a-bar-chart-layer"></a>Přidání vrstvy pruhového grafu

**Vrstva pruhového grafu** je užitečná pro přebírání dat do další dimenze tím, že umožňuje vizualizaci dat umístění jako prostorových pruhů nebo lahví na mapě. Podobně jako bublinová vrstva, pruhový graf později může snadno vizualizovat dvě metriky současně pomocí barvy a relativní výšky. Aby pruhy měly výšku, je nutné přidat míru do kontejneru **Size** v podokně **pole** . Pokud není zadána míra, pruhy bez výšky se zobrazí jako ploché čtverce nebo kružnice v závislosti na možnosti **tvar pruhu** .

> [!div class="mx-imgBorder"]
> ![Mapa znázorňující data bodů pomocí vrstvy pruhového grafu](media/power-bi-visual/bar-chart-layer-styled.png)

Uživatelé mohou naklonovat a otáčet mapu, aby viděli data z různých perspektiv. Mapu lze nakloněnou nebo proporcovat pomocí jedné z následujících metod.

-   V **Nastavení mapování** podokna **Formát** zapněte možnost **navigační ovládací prvky** . Tím se přidá tlačítko pro naklonění mapy.
-   Stiskněte pravé tlačítko myši a přetáhněte ukazatel myši nahoru nebo dolů.
-   Pomocí dotykové obrazovky můžete mapu kontaktovat dvěma prsty a přetáhnout je nahoru nebo dolů.
-   V případě, že se mapuje s fokusem, podržte klávesu **SHIFT** a stiskněte klávesy šipka **nahoru** nebo **dolů** .

Mapu lze otáčet pomocí jedné z následujících metod.

-   V **Nastavení mapování** podokna **Formát** zapněte možnost **navigační ovládací prvky** . Tím se přidá tlačítko pro otočení mapy.
-   Stiskněte pravé tlačítko myši a přetáhněte ukazatel myši doleva nebo doprava.
-   Pomocí dotykové obrazovky se připojte k mapě dvěma prsty a otáčejte.
-   V případě, že se mapuje s fokusem, podržte klávesu **SHIFT** a stiskněte klávesy šipka **vlevo** nebo **vpravo** .

Níže jsou všechna nastavení v podokně **Formát** , která jsou k dispozici v sekci **vrstva pruhového grafu** .

| Nastavení              | Popis      |
|----------------------|------------------|
| Tvar pruhu            | Tvar 3D pruhu<br/><br/>&nbsp;&nbsp;&nbsp;&nbsp;• Box – pruhy vykreslené jako obdélníková pole.<br/>&nbsp;&nbsp;&nbsp;&nbsp;• Válec – pruhy vykreslené jako válce. |
| Height (Výška)               | Výška jednotlivých pruhů. Pokud je pole předáno do kontejneru **Velikost** v podokně **pole** , budou škálované vzhledem k této hodnotě výšky. |
| Škálovat výšku při přiblížení | Určuje, zda má být Výška pruhů zvětšena vzhledem k úrovni přiblížení. |
| Width (Šířka)                | Šířka jednotlivých pruhů.  |
| Šířka škály při přiblížení  | Určuje, zda se šířka pruhů má škálovat relativně k úrovni přiblížení.  |
| Barva výplně           | Barva jednotlivých pruhů Tato možnost je skrytá při předání pole do kontejneru **legendy** podokna **pole** a v podokně **Formát** se zobrazí samostatná část **barvy dat** . |
| Transparentnost         | Průhlednost jednotlivých pruhů. |
| Minimální přiblížení             | Minimální dlaždice úrovně přiblížení jsou k dispozici. |
| Maximální přiblížení             | K dispozici jsou tyto dlaždice s maximální úrovní přiblížení. |
| Pozice vrstvy       | Určuje umístění vrstvy vzhledem k ostatním vrstvám mapy. |

> [!NOTE]
> Pokud mají pruhy hodnotu s malou šířkou a možnost **Šířka měřítka při přiblížení** je zakázaná, může zmizet při přiblížení většího množství, než je velikost pixelu. Pokud je však zapnuta možnost **Šířka škály u možnosti zvětšení** , jsou při změně úrovně přiblížení provedeny další výpočty, které mohou ovlivnit výkon velkých datových sad.

## <a name="next-steps"></a>Další kroky

Přidat k mapě další kontext:

> [!div class="nextstepaction"]
> [Přidání referenční vrstvy](power-bi-visual-add-reference-layer.md)

> [!div class="nextstepaction"]
> [Přidání vrstvy dlaždic](power-bi-visual-add-tile-layer.md)

> [!div class="nextstepaction"]
> [Zobrazení provozu v reálném čase](power-bi-visual-show-real-time-traffic.md)

Přizpůsobení vizuálu:

> [!div class="nextstepaction"]
> [Tipy a triky pro formátování barev v Power BI](https://docs.microsoft.com/power-bi/visuals/service-tips-and-tricks-for-color-formatting)

> [!div class="nextstepaction"]
> [Přizpůsobení názvů, pozadí a legend vizualizací](https://docs.microsoft.com/power-bi/visuals/power-bi-visualization-customize-title-background-and-legend)
