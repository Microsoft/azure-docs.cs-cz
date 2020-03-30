---
title: Vizualizace dat pomocí řídicích panelů založených na sešitech Azure Monitor v Azure Sentinelu | Dokumenty společnosti Microsoft
description: V tomto kurzu se dozvíte, jak vizualizovat data pomocí řídicích panelů založených na sešitech v Azure Sentinelu.
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
ms.date: 01/01/2020
ms.author: yelevin
ms.openlocfilehash: b4461ac43e9356536914b345ef28f5de62fc9f82
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77585216"
---
# <a name="tutorial-visualize-and-monitor-your-data"></a>Kurz: Vizualizace a sledování dat



Po [připojení zdrojů](quickstart-onboard.md) dat k Azure Sentinelu můžete vizualizovat a monitorovat data pomocí přijetí Azure Sentinelu v sešitech Azure Monitor, které poskytuje všestrannost při vytváření vlastních řídicích panelů. Zatímco sešity se v Azure Sentinelu zobrazují jinak, můžou být užitečné, když uvidíte, jak [vytvořit interaktivní sestavy pomocí sešitů Azure Monitor](../azure-monitor/app/usage-workbooks.md). Azure Sentinel umožňuje vytvářet vlastní sešity napříč daty a také obsahuje integrované šablony sešitu, které vám umožní rychle získat přehled o vašich datech, jakmile připojíte zdroj dat.


Tento kurz vám pomůže vizualizovat data v Azure Sentinelu.
> [!div class="checklist"]
> * Použití předdefinovaných sešitů
> * Vytvoření nových sešitů

## <a name="prerequisites"></a>Požadavky

- Musíte mít alespoň čtečku sešitů nebo oprávnění přispěvatele sešitu ve skupině prostředků pracovního prostoru Azure Sentinel.

> [!NOTE]
> Sešity, které vidíte v Azure Sentinelu jsou uloženy v rámci skupiny prostředků pracovního prostoru Azure Sentinel a jsou označeny pracovní prostor, ve kterém byly vytvořeny.

## <a name="use-built-in-workbooks"></a>Použití předdefinovaných sešitů

1. Přejděte do **sešitů** a pak vyberte **Šablony,** abyste viděli úplný seznam předdefinovaných sešitů Azure Sentinelu. Chcete-li zjistit, které jsou relevantní pro datové typy, které jste připojili, pole **Požadované datové typy** v každém sešitu zobrazí datový typ vedle zelené zaškrtnutí, pokud už streamujete relevantní data do Azure Sentinelu.
  ![přejít do sešitů](./media/tutorial-monitor-data/access-workbooks.png)
1. Kliknutím na **Zobrazit sešit** zobrazíte šablonu naplněnou daty.
  
1. Pokud chcete sešit upravit, vyberte **Uložit**a pak vyberte umístění, kam chcete uložit soubor json pro šablonu. 

   > [!NOTE]
   > Tím se vytvoří prostředek Azure na základě příslušné šablony a uloží šablonu Json soubor sám a ne data.


1. Vyberte **Zobrazit sešit**. Potom klikněte na tlačítko **Upravit** nahoře. Nyní můžete sešit upravit a přizpůsobit podle svých potřeb. Další informace o přizpůsobení sešitu najdete v tématu [Vytváření interaktivních sestav pomocí sešitů Azure Monitor](../azure-monitor/app/usage-workbooks.md).
![zobrazení sešitů](./media/tutorial-monitor-data/workbook-graph.png)
1. Po provádění změn můžete sešit uložit. 

1. Sešit můžete také naklonovat: Vyberte **Upravit** a potom **Uložit jako**, nezapomeňte ho uložit pod jiným názvem pod stejným předplatným a skupinou prostředků. Tyto sešity se zobrazí na kartě **Moje sešity.**


## <a name="create-new-workbook"></a>Vytvoření nového sešitu

1. Přejděte do **sešitů** a pak vyberte **Přidat sešit** a vytvořte nový sešit od začátku.
  ![přejít do sešitů](./media/tutorial-monitor-data/create-workbook.png)

1. Chcete-li sešit upravit, vyberte **Upravit**a podle potřeby přidejte text, dotazy a parametry. Další informace o přizpůsobení sešitu najdete v tématu [Vytváření interaktivních sestav pomocí sešitů Azure Monitor](../azure-monitor/app/usage-workbooks.md). 

1. Při vytváření dotazu nastavte **zdroj dat** nastavený na **protokoly**, **typ prostředku** je nastaven na **Log Analytics** a pak zvolte příslušné pracovní prostory. 

1. Po vytvoření sešitu si sešit uložte a ujistěte se, že ho uložíte pod předplatným a skupinou prostředků pracovního prostoru Azure Sentinel.

1. Pokud chcete, aby ostatní uživatelé ve vaší organizaci používali sešit, vyberte v části **Uložit sdílené** **sestavy**. Pokud chcete, aby byl tento sešit dostupný jenom vám, vyberte **moje sestavy**.

1. Pokud chcete přepínat mezi sešity v](./media/tutorial-monitor-data/switch.png)pracovním prostoru, můžete v horním podokně libovolného sešitu vybrat **Otevřít** ![přepínač sešitů. V okně, které se otevře vpravo, přepněte mezi sešity.

   ![Přepnutí sešitů](./media/tutorial-monitor-data/switch-workbooks.png)


## <a name="how-to-delete-workbooks"></a>Odstraňování sešitů

Sešity, které byly vytvořeny ze šablony Azure Sentinelu, můžete odstranit. 

Pokud chcete odstranit vlastní sešit, vyberte na stránce Sešity uložený sešit, který chcete odstranit, a vyberte **Odstranit**. Tím odeberete uložený sešit.

> [!NOTE]
> Tím odeberete prostředek i všechny změny provedené v šabloně. Původní šablona zůstane k dispozici.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili, jak zobrazit data v Azure Sentinelu.

Informace o automatizaci odpovědí na hrozby najdete v tématu [Nastavení automatických reakcí na hrozby v Azure Sentinelu](tutorial-respond-threats-playbook.md).
