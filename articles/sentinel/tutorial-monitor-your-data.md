---
title: Vizualizace dat pomocí Azure Monitorch sešitů v Azure Sentinel | Microsoft Docs
description: V tomto kurzu se naučíte vizualizovat data pomocí sešitů v Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/04/2021
ms.author: yelevin
ms.openlocfilehash: c26d86c98c83d9762acb8a75bba8fe464cc2a58e
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491451"
---
# <a name="tutorial-visualize-and-monitor-your-data"></a>Kurz: Vizualizace a monitorování dat

Po [připojení zdrojů dat](quickstart-onboard.md) ke službě Azure Sentinel můžete vizualizovat a monitorovat data pomocí přijetí Azure Monitorch sešitů pomocí Azure Sentinel, což zajišťuje univerzálnost při vytváření vlastních řídicích panelů. Přestože se sešity ve službě Azure Sentinel zobrazují odlišně, může být užitečné, abyste viděli, jak [vytvářet interaktivní sestavy pomocí Azure monitor sešity](../azure-monitor/visualize/workbooks-overview.md). Azure Sentinel umožňuje vytvářet vlastní sešity napříč vašimi daty a také nabízí předdefinované šablony sešitu, které vám umožní rychle získat přehled o vašich datech, jakmile připojíte zdroj dat.

Tento kurz vám pomůže vizualizovat data v Azure Sentinel.
> [!div class="checklist"]
> * Použití vestavěných sešitů
> * Vytváření nových sešitů

## <a name="prerequisites"></a>Požadavky

Pro skupinu prostředků pracovního prostoru Azure Sentinel musíte mít alespoň oprávnění čtenář nebo **Přispěvatel** **sešitu** .

> [!NOTE]
> Sešity, které vidíte v Azure Sentinel, se ukládají do skupiny prostředků pracovního prostoru Azure Sentinel a jsou označené pracovním prostorem, ve kterém byly vytvořeny.

## <a name="use-built-in-workbooks"></a>Použití vestavěných sešitů

1. Přejděte na **sešity** a pak vyberte **šablony** . zobrazí se úplný seznam integrovaných sešitů Sentinel Azure. 

    Chcete-li zjistit, které z nich jsou relevantní pro datové typy, které jste připojili, pole s **požadovanými datovými typy** v jednotlivých sešitích zobrazí datový typ vedle zelené značky zaškrtnutí, pokud už máte ke streamování relevantních dat do Azure Sentinel.

    [![Přejít na sešity. ](media/tutorial-monitor-data/access-workbooks.png)](media/tutorial-monitor-data/access-workbooks.png#lightbox)

1. Výběrem **Zobrazit šablonu** zobrazíte šablonu vyplněnou daty.

1. Chcete-li upravit sešit, vyberte možnost **Uložit** a poté vyberte umístění, kam chcete uložit soubor JSON pro šablonu.

   > [!NOTE]
   > Tím se vytvoří prostředek Azure založený na příslušné šabloně a uloží se soubor JSON sešitu, nikoli data.


1. Vyberte **Zobrazit uložený sešit**. 

    [![Zobrazit sešity. ](media/tutorial-monitor-data/workbook-graph.png)](media/tutorial-monitor-data/workbook-graph.png#lightbox)

    Vyberte tlačítko **Upravit** na panelu nástrojů sešitu a upravte sešit podle vašich potřeb. Až budete hotovi, vyberte **Uložit** a uložte provedené změny.

    Další informace najdete v tématu [vytváření interaktivních sestav pomocí Azure monitor sešitů](../azure-monitor/visualize/workbooks-overview.md).

> [!TIP]
> Pokud chcete naklonovat sešit, vyberte **Upravit** a pak ho **uložte jako** a nezapomeňte ho uložit s jiným názvem v rámci stejného předplatného a skupiny prostředků.
> Naklonované sešity se zobrazují na kartě **Moje sešity** .
>
## <a name="create-new-workbook"></a>Vytvořit nový sešit

1. Chcete-li vytvořit nový sešit od začátku, klikněte na **sešity** a pak vyberte **Přidat sešit** .

    [![Nový sešit. ](media/tutorial-monitor-data/create-workbook.png)](media/tutorial-monitor-data/create-workbook.png#lightbox)

1. Chcete-li upravit sešit, vyberte možnost **Upravit** a pak podle potřeby přidejte text, dotazy a parametry. Další informace o tom, jak sešit přizpůsobit, najdete v tématu [vytváření interaktivních sestav pomocí Azure monitor sešitů](../azure-monitor/visualize/workbooks-overview.md). 

1. Při vytváření dotazu se ujistěte, že je **zdroj dat** nastavený na **protokoly** a **typ prostředku** je nastavený na **Log Analytics** a pak zvolte příslušné pracovní prostory. 

1. Po vytvoření sešitu sešit uložte a ujistěte se, že jste ho uložili v rámci předplatného a skupiny prostředků pracovního prostoru Azure Sentinel.

1. Pokud chcete ostatním uživatelům ve vaší organizaci umožnit používání sešitu, v části **Uložit na** výběr **sdílených sestav**. Pokud chcete, aby byl tento sešit dostupný jenom pro vás, vyberte **Moje sestavy**.

1. Pokud chcete přepínat mezi sešity v pracovním prostoru, vyberte **otevřít** ![ ikonu pro otevření sešitu.](./media/tutorial-monitor-data/switch.png) na panelu nástrojů libovolného sešitu. Obrazovka se přepne na seznam dalších sešitů, na které můžete přepnout.

    Vyberte sešit, který chcete otevřít:

    [![Přepne sešity. ](media/tutorial-monitor-data/switch-workbooks.png)](media/tutorial-monitor-data/switch-workbooks.png#lightbox)

## <a name="refresh-your-workbook-data"></a>Aktualizace dat sešitu

Aktualizujte sešit, aby se zobrazila aktualizovaná data. Na panelu nástrojů vyberte jednu z následujících možností:

- :::image type="icon" source="media/whats-new/manual-refresh-button.png" border="false":::**Aktualizujte**, aby se data sešitu aktualizovala ručně.

- :::image type="icon" source="media/whats-new/auto-refresh-workbook.png" border="false":::**Automaticky aktualizovat**, pokud chcete nastavit, aby se váš sešit automaticky aktualizoval v nakonfigurovaném intervalu.

    - Podporované intervaly automatických aktualizací jsou v rozsahu od **5 minut** do **1 dne**.

    - Automatická aktualizace je pozastavena při úpravách sešitu a intervaly se restartují pokaždé, když přepnete zpět do režimu zobrazení v režimu úprav.

    - Intervaly automatické aktualizace se také restartují, pokud data ručně aktualizujete.

    > [!TIP]
    > Ve výchozím nastavení je automatická aktualizace vypnutá. Pro optimalizaci výkonu je automatická aktualizace také vypnuta při každém zavření sešitu a neběží na pozadí. Při příštím otevření sešitu znovu zapněte automatické obnovení podle potřeby.
    >

## <a name="print-a-workbook-or-save-as-pdf"></a>Tisk sešitu nebo uložení ve formátu PDF

Pokud chcete sešit vytisknout nebo ho uložit jako PDF, použijte nabídku Možnosti napravo od nadpisu sešitu.

1. Vyberte možnosti > :::image type="icon" source="media/whats-new/print-icon.png" border="false"::: **Tisk obsahu**. 
2. Na obrazovce tisk upravte podle potřeby nastavení tisku nebo vyberte **Uložit jako PDF** a uložte ho místně.

Například:

[![Vytiskněte sešit nebo ho uložte jako PDF. ](media/whats-new/print-workbook.png)](media/whats-new/print-workbook.png#lightbox)

## <a name="how-to-delete-workbooks"></a>Odstraňování sešitů

Pokud chcete odstranit uložený sešit (buď uloženou šablonu, nebo přizpůsobený sešit), vyberte na stránce sešity uložený sešit, který chcete odstranit, a vyberte **Odstranit**. Tím se odstraní uložený sešit.

> [!NOTE]
> Tím se odstraní prostředek sešitu i všechny změny, které jste v šabloně udělali. Původní šablona zůstane dostupná.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak vizualizovat data v Azure Sentinel pomocí sešitů Azure.

Informace o tom, jak automatizovat odpovědi na hrozby, najdete v tématu [Nastavení automatických odpovědí na hrozby v Azure Sentinel](tutorial-respond-threats-playbook.md).
