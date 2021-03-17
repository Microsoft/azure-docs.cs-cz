---
title: zahrnout soubor
description: zahrnout soubor
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: include
ms.date: 10/09/2020
ms.author: banders
ms.reviwer: ''
ms.custom: include file
ms.openlocfilehash: fb34b4ef1cf209a15c7e6ac9a59cc11dd4b0056e
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/14/2020
ms.locfileid: "92026323"
---
## <a name="transform-data-before-using-large-usage-files"></a>Transformace dat před prací s velkými soubory s daty využití

Někdy jsou soubory s daty využití nebo pro sesouhlasení moc velké na to, aby se daly otevřít. Případně můžete k vyřešení nějakého problému potřebovat jenom část informací. Můžete třeba chtít jenom informace o konkrétním prostředku nebo jenom informace o spotřebě pro několik služeb nebo skupin prostředků. Před vytvořením kontingenční tabulky můžete data transformovat a vytvořit jejich souhrn.

1. V Excelu otevřete prázdný sešit.
1. V horní nabídce vyberte **Data** > **Z textu/CSV**, vyberte soubor s daty využití a potom vyberte **Importovat**.
1. Ve spodní části okna vyberte možnost **Transformovat data**. V novém okně se zobrazí souhrn dat.  
    :::image type="content" source="./media/cost-management-billing-transform-data-before-using-large-usage-files/summarized-data.png" alt-text="Příklad zobrazující sumarizovaná data" lightbox="./media/cost-management-billing-transform-data-before-using-large-usage-files/summarized-data.png" :::
1. Pokud máte smlouvu se zákazníkem Microsoftu, tento krok přeskočte a pokračujte na další, protože soubory s daty využití v případě smluv MCA mají většinou nadpisy sloupců v prvním řádku. V rámci přípravy dat vytvořte tabulku. Odeberte horní řádky a nechte jenom nadpisy. Vyberte **Odebrat řádky** > **Odebrat horní řádky**.  
     :::image type="content" source="./media/cost-management-billing-transform-data-before-using-large-usage-files/remove-top-rows.png" alt-text="Příklad zobrazující sumarizovaná data" :::
1. V okně Odebrat horní řádky zadejte počet horních řádků, které se mají odebrat. V případě smluv EA jsou to obvykle 2 řádky, u programu CSP většinou 1 řádek. Vyberte **OK**.
1. Vyberte **Použít první řádek jako záhlaví**.  
    :::image type="content" source="./media/cost-management-billing-transform-data-before-using-large-usage-files/use-first-row-as-header.png" alt-text="Příklad zobrazující sumarizovaná data" :::
    
    V zobrazení tabulky zůstanou nahoře nadpisy sloupců.
1. Potom přidejte filtr. K filtrování použijte šipky pro výběr vpravo od nadpisů sloupců. Zobrazí se navrhované filtry Subscription ID, Service Name (Meter category), Instance ID, resource group. V jednom dokumentu můžete použít více filtrů. Doporučujeme použít všechny možné filtry, aby byl dokument co nejmenší a dalo se s ním lépe pracovat.
1. Po použití filtrů vyberte **Zavřít a načíst**.  
    :::image type="content" source="./media/cost-management-billing-transform-data-before-using-large-usage-files/close-and-load.png" alt-text="Příklad zobrazující sumarizovaná data" :::

Soubor se načte a zobrazí se tabulka s filtrovanými daty využití. Teď můžete vytvořit novou kontingenční tabulku a vyřešit problémy s využitím.