---
title: Řešení potíží s Azure Monitor přehledy na základě sešitu
description: Poskytuje pokyny k odstraňování potíží s Azure Monitormi přehledy na základě sešitu pro služby, jako je Azure Key Vault, Azure CosmosDB, Azure Storage a Azure cache pro Redis.
services: azure-monitor
ms.author: mbullwin
author: mrbullwinkle
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: 90743a213092aacfa10139c12f47bc11faf0d86d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86498708"
---
# <a name="troubleshooting-workbook-based-insights"></a>Řešení potíží s přehledy na základě sešitu

Tento článek vám pomůže s diagnostikou a řešením potíží s některými běžnými problémy, se kterými se můžete setkat při používání Azure Monitor přehledy na základě sešitu.


## <a name="why-can-i-only-see-200-resources"></a>Proč můžu zobrazit jenom 200 prostředků

Počet vybraných prostředků má limit 200, bez ohledu na počet vybraných předplatných.

## <a name="what-happens-when-i-click-on-a-recently-pinned-tile-in-the-dashboard"></a>Co se stane po kliknutí na nedávno připnuté dlaždici na řídicím panelu

* Pokud kliknete kamkoli na dlaždici, přejdete na kartu, ze které se dlaždice připnula. Pokud například připnete graf na kartu Přehled, potom když na řídicím panelu kliknete na tuto dlaždici, otevře se výchozí zobrazení. když ale připnete graf z vlastního uloženého kopírování, otevře se vaše uložené zobrazení kopie.
* Ikona filtru v levém horním rohu nadpisu otevře kartu konfigurovat nastavení dlaždic.
* Ikona elipsy v pravém horním rohu vám poskytne možnosti pro přizpůsobení dat nadpisu, přizpůsobení, aktualizace a odebrání z řídicího panelu.

## <a name="what-happens-when-i-save-a-workbook"></a>Co se stane, když sešit ukládám

* Když sešit uložíte, umožní vám vytvořit novou kopii sešitu s úpravami a změnit jeho název. Při uložení se sešit nepřepisuje, aktuální sešit bude vždycky výchozí zobrazení.
* **Neuložený** sešit je pouze výchozím zobrazením.

## <a name="why-dont-i-see-all-my-subscriptions-in-the-portal"></a>Proč na portálu nevidím všechna moje předplatná

Portál zobrazí data pouze pro vybraná předplatná při spuštění portálu. Pokud chcete změnit, které odběry se mají vybrat, přejděte vpravo nahoře a klikněte na Poznámkový blok s ikonou filtru. Tato možnost zobrazí kartu **adresář a předplatná** .

![Adresář a předplatné](./media/storage-insights-overview/fqa3.png)

## <a name="what-is-time-range"></a>Co je časový rozsah

Časový rozsah zobrazuje data z určitého časového rámce. Pokud je časový rozsah například 24 hodin, pak se zobrazuje data za posledních 24 hodin.

## <a name="what-is-time-granularity-time-grain"></a>Co je časové rozlišení (časový interval)

Časová členitost je časový rozdíl mezi dvěma datovými body. Například pokud je časový interval nastaven na 1 sekundu, znamená to, že jsou metriky shromažďovány každou sekundu.

## <a name="what-is-the-time-granularity-once-we-pin-any-part-of-the-workbooks-to-a-dashboard"></a>Jaká je časová prodleva, když připnete všechny části sešitů na řídicí panel

Výchozí časová členitost je nastavená na automatické, aktuálně se v tuto chvíli nedá změnit.

## <a name="how-do-i-change-the-timespan-time-range-of-the-workbook-step-on-my-dashboard"></a>Návody změnit rozsah TimeSpan/time v kroku sešitu na řídicím panelu

Ve výchozím nastavení je rozsah TimeSpan/Time na dlaždici řídicího panelu nastavený na 24 hodin. Pokud chcete toto kliknutí změnit na tři tečky vpravo nahoře, vyberte **přizpůsobit data dlaždice**, zaškrtněte políčko Přepsat nastavení času řídicího panelu na úrovni nadpisu a potom vyberte časové rozpětí pomocí rozevírací nabídky.  

![Vyberte tři tečky v pravém horním rohu dlaždice a zvolte možnost přizpůsobit tato data.](./media/storage-insights-overview/fqa-data-settings.png)

![V nastavení konfigurovat dlaždici vyberte rozevírací seznam TimeSpan pro změnu rozsahu TimeSpan/Time.](./media/storage-insights-overview/fqa-timespan.png)

## <a name="how-do-i-change-the-title-of-the-workbook-or-a-workbook-step-i-pinned-to-a-dashboard"></a>Návody změnit název sešitu nebo krok sešitu připnuté na řídicí panel

Název kroku sešitu nebo sešitu, který je připnuté na řídicí panel, si zachová stejný název, který měl v sešitu. Chcete-li změnit název, je nutné uložit vlastní kopii sešitu. Pak budete moci sešit pojmenovat před tím, než kliknete na tlačítko Uložit.

![Vyberte Uložit v horní části, pokud chcete uložit kopii sešitu a změnit jeho název.](./media/storage-insights-overview/fqa-change-workbook-name.png)

Pokud chcete změnit název kroku v uloženém sešitu, v kroku vyberte Upravit a pak vyberte ozubené kolečko v dolní části nastavení.

![Výběrem možnosti upravit v dolní části kroku sešitu otevřete nastavení ](./media/storage-insights-overview/fqa-edit.png)
 ![ v části nastavení vyberte ozubené kolečko, abyste mohli změnit název kroku.](./media/storage-insights-overview/fqa-change-name.png)

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o scénářích, které jsou navržené tak, aby podporovaly, jak vytvářet nové a přizpůsobovat stávající sestavy a které se dají provádět pomocí kontroly [vytváření interaktivních sestav pomocí Azure monitor sešitů](../platform/workbooks-overview.md)
