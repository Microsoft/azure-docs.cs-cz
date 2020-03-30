---
title: Zobrazení metrik využití rozhraní AZURE Maps API | Mapy Microsoft Azure
description: V tomto článku se dozvíte, jak zobrazit metriky pro vaše volání rozhraní API Microsoft Azure Maps api na webu Azure Portal.
author: philmea
ms.author: philmea
ms.date: 08/06/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 0eb117af712b3b1f63a3f99c96cba9775f8e3996
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335150"
---
# <a name="view-azure-maps-api-usage-metrics"></a>Zobrazení metrik využití rozhraní API služby Azure Maps

Tento článek ukazuje, jak zobrazit metriky využití rozhraní API pro váš účet Azure Maps na [webu Azure Portal](https://portal.azure.com). Metriky jsou zobrazeny ve vhodném formátu grafu podél přizpůsobitelné doby trvání.

## <a name="view-metric-snapshot"></a>Zobrazit snímek metriky

Některé běžné metriky si můžete zobrazit na stránce **Přehled** svého účtu Mapy. Aktuálně zobrazuje *celkový počet požadavků*, *celkový počet chyb*a *dostupnost* za volitelné časové období.

![Přehled metrik využití Azure Maps](media/how-to-view-api-usage/portal-overview.png)

Pokud potřebujete přizpůsobit tyto grafy pro konkrétní analýzu, přejdete k další části.

## <a name="view-detailed-metrics"></a>Zobrazit podrobné metriky

1. Přihlaste se ke svému předplatnému Azure na [portálu](https://portal.azure.com).

2. Klikněte na položku nabídky **Všechny zdroje** na levé straně a přejděte na svůj účet *Azure Maps*.

3. Jakmile je váš účet Mapy otevřený, klikněte na nabídku **Metriky** vlevo.

4. V podokně **Metriky** zvolte jednu z následujících možností:

   1. **Dostupnost** – která zobrazuje *průměr* dostupnosti rozhraní API za určité časové období.
   2. **Využití** – což ukazuje, jak *se počet* využití pro váš účet.

      ![Podokno metrik využití Azure Maps](media/how-to-view-api-usage/portal-metrics.png)

5. Dále můžete vybrat *časový rozsah* klepnutím na **poslední 24 hodin (automaticky).** Ve výchozím nastavení je časový rozsah nastaven na 24 hodin. Po kliknutí se zobrazí všechny volitelné časové rozsahy. Můžete vybrat *rozlišovací schopnost Čas* a zvolit zobrazení času jako *místní* nebo *GMT* ve stejném rozevíracím souboru. Klikněte na **Použít**.

    ![Časový rozsah metrik Azure Maps](media/how-to-view-api-usage/time-range.png)

6. Jakmile metriku přidáte, můžete **přidat filtr** z vlastností relevantních pro tuto metriku. Potom vyberte hodnotu vlastnosti, která má být v grafu zobrazena.

    ![Filtr metrik využití Azure Maps](media/how-to-view-api-usage/filter.png)

7. Můžete také **použít rozdělení** pro metriku na základě vybrané vlastnosti metriky. Umožňuje graf rozdělit do více grafů, pro každou hodnotu této vlastnosti. Na následujícím obrázku odpovídá barva každého grafu hodnotě vlastnosti zobrazené v dolní části grafu.

    ![Rozdělení metrik využití Azure Maps](media/how-to-view-api-usage/splitting.png)

8. Můžete také sledovat více metrik na stejném grafu, jednoduše kliknutím na tlačítko **Přidat metriku** nahoře.

## <a name="next-steps"></a>Další kroky

Další informace o rozhraních API Azure Maps, u kterých chcete sledovat využití:
> [!div class="nextstepaction"] 
> [Azure Maps Web SDK jak na to](how-to-use-map-control.md)

> [!div class="nextstepaction"] 
> [Azure Maps Android SDK jak na to](how-to-use-android-map-control-library.md)

> [!div class="nextstepaction"]
> [Dokumentace rozhraní API Azure Maps REST](https://docs.microsoft.com/rest/api/maps)
