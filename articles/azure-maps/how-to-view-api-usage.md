---
title: Jak zobrazit využití rozhraní API služby Azure Maps | Dokumentace Microsoftu
description: Zjistěte, jak na portálu zobrazovat metriky pro volání rozhraní API služby Azure Maps.
author: dsk-2015
ms.author: dkshir
ms.date: 08/06/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: e62d2ff1fdd6bc94244511a2de95c4268a58d6f9
ms.sourcegitcommit: 0fcd6e1d03e1df505cf6cb9e6069dc674e1de0be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/14/2018
ms.locfileid: "42055436"
---
# <a name="how-to-view-the-azure-maps-api-usage"></a>Jak zobrazit využití rozhraní API služby Azure Maps
Tento článek popisuje, jak chcete zobrazit metriky využití rozhraní API pro váš účet Azure Maps portálu [portál](https://portal.azure.com). Metriky jsou zobrazeny ve formátu pohodlný grafu podél přizpůsobitelné dobu trvání. 

## <a name="view-metric-snapshot"></a>Zobrazit metriky snímek 

Zobrazí se některé běžné metriky na **přehled** stránku vašeho účtu mapy. Aktuálně ukazuje *celkový počet požadavků*, *celkový počet chyb*, a *dostupnosti* přes vybrat dobu trvání. 

![Přehled metriky Azure Maps](media/how-to-view-api-usage/portal-overview.png)

Pokračujte k další části, pokud je potřeba upravit tyto grafy pro konkrétní analýzy.


## <a name="view-detailed-metrics"></a>Zobrazit podrobné metriky

1. Přihlaste se ke svému předplatnému Azure v [portál](https://portal.azure.com). 

2. Klikněte na tlačítko **všechny prostředky** nabídky položku na levé straně a přejděte do vaší *účet Azure Maps*.

3. Jakmile váš účet v Maps se otevře, klikněte na **metriky** nabídky na levé straně.

4. Na **metriky** podokně zvolit jednu z následujících akcí:

    1. **Dostupnost**, který ukazuje *průměrné* dostupnost rozhraní API po určitou dobu, nebo 
    2. **Využití**, který ukazuje způsob použití *počet* pro váš účet. 

    ![Podokno Azure Maps metriky](media/how-to-view-api-usage/portal-metrics.png)

5. Jakmile vyberete metriky, můžete vybrat *časový rozsah* výběrem na **posledních 12 hodin (automaticky)** což je výchozí hodnota. Můžete si vybrat i *časové intervaly*, stejně jako se rozhodnete zobrazit čas jako *místní* nebo *GMT* ve stejném rozevíracího seznamu. Klikněte na tlačítko **Použít**.

    ![Azure Maps metriky časový rozsah](media/how-to-view-api-usage/time-range.png)
 
6. Jakmile přidáte vaše metriky, pak můžete **přidat filtr** v mezi vlastnosti, které odpovídají metriky a pak vyberte hodnotu vlastnosti, které chcete zobrazit grafu pro. 

    ![Filtrovat metriky Azure Maps](media/how-to-view-api-usage/filter.png)

7. Můžete také **použít rozdělení** vaše metriky na základě vaší vybrané metriky vlastnosti. To umožňuje graf, který se má rozdělit do více grafů, jeden pro každou hodnotu této vlastnosti. Na následujícím obrázku, například barvu každého grafu odpovídá hodnotě vlastnosti se zobrazí v dolní části grafu.

    ![Azure Maps metriky rozdělení](media/how-to-view-api-usage/splitting.png)
 
8. Můžete také sledovat několik metrik do stejného grafu, jednoduše tak, že kliknete na **přidat metriku** tlačítko v horní části.


## <a name="next-steps"></a>Další postup

Teď, když jste se naučili, jak sledovat využití Azure Maps, mohou pokračovat na další informace o rozhraních API, při použití se:

* [Dokumentace k rozhraní REST API pro mapy Azure](https://docs.microsoft.com/rest/api/maps)
