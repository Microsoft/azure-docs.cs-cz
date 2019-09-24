---
title: Monitorujte svá data pomocí řídicích panelů na základě Azure Monitorch sešitů v Azure Sentinel | Microsoft Docs
description: V tomto kurzu se naučíte monitorovat data pomocí řídicích panelů na základě sešitů v Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 3f5e01a0ceebac092084e339994161763a11c49b
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240425"
---
# <a name="tutorial-monitor-your-data"></a>Kurz: Sledujte svoje



Po [připojení zdrojů](quickstart-onboard.md) dat ke službě Azure Sentinel můžete monitorovat data pomocí integrace služby Azure Sentinel s Azure monitor sešity, které poskytují univerzálnost při vytváření vlastních řídicích panelů. Přestože se sešity ve službě Azure Sentinel zobrazují odlišně, může být užitečné, abyste viděli, jak [vytvářet interaktivní sestavy pomocí Azure monitor sešity](../azure-monitor/app/usage-workbooks.md). Azure Sentinel umožňuje vytvářet vlastní sešity napříč vašimi daty a také nabízí předdefinované šablony sešitu, které vám umožní rychle získat přehled o vašich datech, jakmile připojíte zdroj dat.


Tento kurz vám pomůže monitorovat data v rámci Azure Sentinel.
> [!div class="checklist"]
> * Použití vestavěných sešitů
> * Vytváření nových sešitů

## <a name="prerequisites"></a>Požadavky

- Pro skupinu prostředků pracovního prostoru Azure Sentinel musíte mít alespoň oprávnění čtenář nebo přispěvatel sešitu.

> [!NOTE]
> Sešity, které vidíte ve službě Azure Sentinel, se ukládají do skupiny prostředků pracovního prostoru Azure Sentinel a jsou označené pracovním prostorem, ve kterém byly vytvořeny.

## <a name="use-built-in-workbooks"></a>Použití vestavěných sešitů

1. Přejděte na **sešity** a pak vyberte **šablony** . zobrazí se úplný seznam integrovaných sešitů Sentinel Azure. Chcete-li zjistit, které z nich jsou relevantní pro datové typy, které jste připojili, pole s **požadovanými datovými typy** v jednotlivých sešitích zobrazí datový typ vedle zelené značky zaškrtnutí, pokud už máte ke streamování relevantních dat do Azure Sentinel.
  ![Přejít na sešity](./media/tutorial-monitor-data/access-workbooks.png)
1. Kliknutím na **Zobrazit sešit** zobrazíte šablonu vyplněnou daty.
  
1. Chcete-li upravit sešit, vyberte možnost **Uložit**a poté vyberte umístění, kam chcete uložit soubor JSON pro šablonu. 

   > [!NOTE]
   > Tím se vytvoří prostředek Azure založený na příslušné šabloně a uloží samotný soubor JSON šablony a ne data.


1. Vyberte **Zobrazit sešit**. Pak klikněte na tlačítko **Upravit** v horní části. Teď můžete sešit upravit a přizpůsobit ho podle svých potřeb. Další informace o tom, jak sešit přizpůsobit, najdete v tématu [vytváření interaktivních sestav pomocí Azure monitor sešitů](../azure-monitor/app/usage-workbooks.md).
![Zobrazit sešity](./media/tutorial-monitor-data/workbook-graph.png)
1. Až změny provedete, můžete sešit uložit. 

1. Můžete také naklonovat sešit: Vyberte **Upravit** a pak **Uložit jako**a nezapomeňte ho uložit s jiným názvem v rámci stejného předplatného a skupiny prostředků. Tyto sešity se zobrazí na kartě **Moje sešity** .


## <a name="create-new-workbook"></a>Vytvořit nový sešit

1. Chcete-li vytvořit nový sešit od začátku, klikněte na **sešity** a pak vyberte **Přidat sešit** .
  ![Přejít na sešity](./media/tutorial-monitor-data/create-workbook.png)

1. Chcete-li upravit sešit, vyberte možnost **Upravit**a pak podle potřeby přidejte text, dotazy a parametry. Další informace o tom, jak sešit přizpůsobit, najdete v tématu [vytváření interaktivních sestav pomocí Azure monitor sešitů](../azure-monitor/app/usage-workbooks.md). 

1. Při sestavování dotazu nastavte **zdroj dat** na **protokoly**, **typ prostředku** je nastaven na **Log Analytics** a pak zvolte relevantní pracovní prostor (y). 

1. Po vytvoření sešitu sešit uložte a ujistěte se, že jste ho uložili v rámci předplatného a skupiny prostředků pracovního prostoru Azure Sentinel.

1. Pokud chcete ostatním uživatelům ve vaší organizaci umožnit používání sešitu, v části **Uložit na** výběr **sdílených sestav**. Pokud chcete, aby byl tento sešit dostupný jenom pro vás, vyberte **Moje sestavy**.

1. Chcete-li přepínat mezi sešity v pracovním prostoru, můžete v](./media/tutorial-monitor-data/switch.png)horním podokně libovolného sešitu vybrat možnost **otevřít** ![sešity Switch. V okně, které se otevře vpravo, přepínejte mezi sešity.

   ![Přepnout sešity](./media/tutorial-monitor-data/switch-workbooks.png)


## <a name="how-to-delete-workbooks"></a>Odstraňování sešitů

Můžete odstranit sešity vytvořené ze šablony Sentinel Azure. 

Postup odstranění přizpůsobeného sešitu:
1.  Vyberte **Zobrazit sešit**.
2.  V horní části vyberte **otevřít** .
3.  V tabulce se seznamem sešitů na řádku sešitu, který chcete odstranit, vyberte tři tečky na konci řádku... Chcete-li otevřít nabídku, vyberte možnost **Odstranit**. Tím se odstraní uložený sešit.

> [!NOTE]
> Tím se odstraní prostředek i všechny změny, které jste v šabloně udělali. Původní šablona zůstane dostupná.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak zobrazit data ve službě Azure Sentinel.

Informace o tom, jak automatizovat odpovědi na hrozby, najdete v tématu [Nastavení automatických odpovědí na hrozby v Azure Sentinel](tutorial-respond-threats-playbook.md).
