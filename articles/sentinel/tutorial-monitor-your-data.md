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
ms.date: 05/04/2020
ms.author: yelevin
ms.openlocfilehash: 048a089209ef7c5f20c96f77593e2cf39590147e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104600515"
---
# <a name="tutorial-visualize-and-monitor-your-data"></a>Kurz: Vizualizace a monitorování dat



Po [připojení zdrojů dat](quickstart-onboard.md) ke službě Azure Sentinel můžete vizualizovat a monitorovat data pomocí přijetí Azure Monitorch sešitů pomocí Azure Sentinel, což zajišťuje univerzálnost při vytváření vlastních řídicích panelů. Přestože se sešity ve službě Azure Sentinel zobrazují odlišně, může být užitečné, abyste viděli, jak [vytvářet interaktivní sestavy pomocí Azure monitor sešity](../azure-monitor/visualize/workbooks-overview.md). Azure Sentinel umožňuje vytvářet vlastní sešity napříč vašimi daty a také nabízí předdefinované šablony sešitu, které vám umožní rychle získat přehled o vašich datech, jakmile připojíte zdroj dat.


Tento kurz vám pomůže vizualizovat data v Azure Sentinel.
> [!div class="checklist"]
> * Použití vestavěných sešitů
> * Vytváření nových sešitů

## <a name="prerequisites"></a>Požadavky

- Pro skupinu prostředků pracovního prostoru Azure Sentinel musíte mít alespoň oprávnění čtenář nebo přispěvatel sešitu.

> [!NOTE]
> Sešity, které vidíte v Azure Sentinel, se ukládají do skupiny prostředků pracovního prostoru Azure Sentinel a jsou označené pracovním prostorem, ve kterém byly vytvořeny.

## <a name="use-built-in-workbooks"></a>Použití vestavěných sešitů

1. Přejděte na **sešity** a pak vyberte **šablony** . zobrazí se úplný seznam integrovaných sešitů Sentinel Azure. Chcete-li zjistit, které z nich jsou relevantní pro datové typy, které jste připojili, pole s **požadovanými datovými typy** v jednotlivých sešitích zobrazí datový typ vedle zelené značky zaškrtnutí, pokud už máte ke streamování relevantních dat do Azure Sentinel.
  ![Přejít na sešity](./media/tutorial-monitor-data/access-workbooks.png)
1. Kliknutím na **Zobrazit šablonu** zobrazíte šablonu vyplněnou daty.
  
1. Chcete-li upravit sešit, vyberte možnost **Uložit** a poté vyberte umístění, kam chcete uložit soubor JSON pro šablonu. 

   > [!NOTE]
   > Tím se vytvoří prostředek Azure založený na příslušné šabloně a uloží se soubor JSON sešitu, nikoli data.


1. Vyberte **Zobrazit uložený sešit**. Pak klikněte na tlačítko **Upravit** v horní části. Teď můžete sešit upravit a přizpůsobit vašim potřebám. Další informace o tom, jak sešit přizpůsobit, najdete v tématu [vytváření interaktivních sestav pomocí Azure monitor sešitů](../azure-monitor/visualize/workbooks-overview.md).
![Zobrazit sešity](./media/tutorial-monitor-data/workbook-graph.png)
1. Až změny provedete, můžete sešit uložit. 

1. Sešit můžete také klonovat: vyberte **Upravit** a pak ho **uložte jako** a nezapomeňte ho uložit s jiným názvem, a to v rámci stejného předplatného a skupiny prostředků. Tyto naklonované sešity se zobrazují na kartě **Moje sešity** .


## <a name="create-new-workbook"></a>Vytvořit nový sešit

1. Chcete-li vytvořit nový sešit od začátku, klikněte na **sešity** a pak vyberte **Přidat sešit** .
  ![Snímek obrazovky, který ukazuje obrazovku nového sešitu.](./media/tutorial-monitor-data/create-workbook.png)

1. Chcete-li upravit sešit, vyberte možnost **Upravit** a pak podle potřeby přidejte text, dotazy a parametry. Další informace o tom, jak sešit přizpůsobit, najdete v tématu [vytváření interaktivních sestav pomocí Azure monitor sešitů](../azure-monitor/visualize/workbooks-overview.md). 

1. Při vytváření dotazu se ujistěte, že je **zdroj dat** nastavený na **protokoly** a **typ prostředku** je nastavený na **Log Analytics** a pak zvolte příslušné pracovní prostory. 

1. Po vytvoření sešitu sešit uložte a ujistěte se, že jste ho uložili v rámci předplatného a skupiny prostředků pracovního prostoru Azure Sentinel.

1. Pokud chcete ostatním uživatelům ve vaší organizaci umožnit používání sešitu, v části **Uložit na** výběr **sdílených sestav**. Pokud chcete, aby byl tento sešit dostupný jenom pro vás, vyberte **Moje sestavy**.

1. Pokud chcete přepínat mezi sešity v pracovním prostoru, můžete vybrat **otevřít** ![ ikonu pro otevření sešitu. ](./media/tutorial-monitor-data/switch.png) v horním podokně libovolného sešitu. V okně, které se otevře vpravo, přepínejte mezi sešity.

   ![Přepnout sešity](./media/tutorial-monitor-data/switch-workbooks.png)


## <a name="print-a-workbook-or-save-as-pdf"></a>Tisk sešitu nebo uložení ve formátu PDF

Pokud chcete sešit vytisknout nebo ho uložit jako PDF, použijte nabídku Možnosti napravo od nadpisu sešitu.

1. Vyberte možnosti > :::image type="icon" source="media/whats-new/print-icon.png" border="false"::: **Tisk obsahu**. 
2. Na obrazovce tisk upravte podle potřeby nastavení tisku nebo vyberte **Uložit jako PDF** a uložte ho místně.

Například:

:::image type="content" source="media/whats-new/print-workbook.png" alt-text="Vytiskněte sešit nebo ho uložte jako PDF.":::

## <a name="how-to-delete-workbooks"></a>Odstraňování sešitů

Pokud chcete odstranit uložený sešit (buď uloženou šablonu, nebo přizpůsobený sešit), vyberte na stránce sešity uložený sešit, který chcete odstranit, a vyberte **Odstranit**. Tím se odstraní uložený sešit.

> [!NOTE]
> Tím se odstraní prostředek sešitu i všechny změny, které jste v šabloně udělali. Původní šablona zůstane dostupná.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak vizualizovat data v Azure Sentinel pomocí sešitů Azure.

Informace o tom, jak automatizovat odpovědi na hrozby, najdete v tématu [Nastavení automatických odpovědí na hrozby v Azure Sentinel](tutorial-respond-threats-playbook.md).
