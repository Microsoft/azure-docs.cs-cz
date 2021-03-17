---
title: Vytváření výstrah protokolu aktivit pro cvičení v Azure DevTest Labs
description: Tento článek popisuje kroky pro vytvoření upozornění protokolu aktivit pro testovací prostředí v Azure DevTest Labs.
ms.topic: how-to
ms.date: 07/10/2020
ms.openlocfilehash: f774e3291961c58f55a9ed24026535e076235b98
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100588758"
---
# <a name="create-activity-log-alerts-for-labs-in-azure-devtest-labs"></a>Vytváření výstrah protokolu aktivit pro cvičení v Azure DevTest Labs
Tento článek vysvětluje, jak vytvořit výstrahy protokolu aktivit pro cvičení v Azure DevTest Labs (například při vytvoření virtuálního počítače nebo při odstranění virtuálního počítače).

## <a name="create-alerts"></a>Vytváření upozornění
V tomto příkladu vytvoříte výstrahu pro všechny operace správy v testovacím prostředí s akcí, která odešle e-mail vlastníkům předplatného. 

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. Na panelu hledání Azure Portal zadejte **monitor** a potom v seznamu výsledků vyberte **monitor** . 

    :::image type="content" source="./media/activity-logs/search-monitor.png" alt-text="Vyhledat monitor":::        
1. V nabídce vlevo vyberte **výstrahy** a pak na panelu nástrojů vyberte **nové pravidlo upozornění** . 

    :::image type="content" source="./media/activity-logs/alerts-page.png" alt-text="Stránka s výstrahami":::    
1. Na stránce **vytvořit pravidlo výstrahy** klikněte na **Vybrat prostředek**. 

    :::image type="content" source="./media/activity-logs/select-resource-link.png" alt-text="Vybrat prostředek pro výstrahu":::        
1. Vyberte **DevTest Labs** pro **Filtr podle typu prostředku**, v seznamu vyberte testovací prostředí a potom vyberte **Hotovo**.

    :::image type="content" source="./media/activity-logs/select-lab-resource.png" alt-text="Výběr testovacího prostředí jako prostředku":::
1. Zpátky na stránce **vytvořit pravidlo výstrahy** klikněte na **vybrat podmínku**. 

    :::image type="content" source="./media/activity-logs/select-condition-link.png" alt-text="Vybrat odkaz na podmínku":::    
1. Na stránce **Konfigurovat logiku signálu** vyberte signál podporovaný DevTest Labs. 

    :::image type="content" source="./media/activity-logs/select-signal.png" alt-text="Vybrat signál":::
1. Filtrovat podle **úrovně události** (podrobné, informativní, varovná, chyba, kritická, vše), **stav** (neúspěšné, spuštěno, úspěch) a **zahájení** události 
1. Pokud chcete dokončit konfiguraci podmínky, vyberte **Hotovo** . 

    :::image type="content" source="./media/activity-logs/configure-signal-logic-done.png" alt-text="Konfigurace logiky signálů – dokončeno":::
1. Zadali jste rozsah (testovací prostředí) a podmínku pro výstrahu. Nyní je třeba zadat skupinu akcí s akcemi, které mají být spuštěny při splnění podmínky. Zpět na stránce **vytvořit pravidlo výstrahy** zvolte **možnost vybrat skupinu akcí**. 

    :::image type="content" source="./media/activity-logs/select-action-group-link.png" alt-text="Odkaz pro výběr skupiny akcí":::
1. Na panelu nástrojů vyberte vytvořit odkaz na **skupinu akcí** . 

    :::image type="content" source="./media/activity-logs/create-action-group-link.png" alt-text="Vytvořit odkaz skupiny akcí":::
1. Na stránce **Přidat skupinu akcí** proveďte tyto kroky:
    1. Zadejte **název** skupiny akcí.
    1. Zadejte **krátký název** skupiny akcí. 
    1. Vyberte **skupinu prostředků** , ve které chcete vytvořit výstrahu. 
    1. Zadejte **název akce**. 
    1. Vyberte **typ akce** (v tomto příkladu **Azure Resource Manager role e-mailu**). 

        :::image type="content" source="./media/activity-logs/add-action-group.png" alt-text="Přidat stránku skupiny akcí":::
    1. Na stránce **role e-mailové Azure Resource Manager** vyberte roli. V tomto příkladu je **vlastníkem**. Pak vyberte **OK**. 

        :::image type="content" source="./media/activity-logs/select-role.png" alt-text="Vybrat roli":::            
    1. Na stránce **Přidat skupinu akcí** vyberte **OK** . 
1. Nyní na stránce **vytvořit pravidlo výstrahy** zadejte název pravidla výstrahy a pak vyberte **OK**. 

    :::image type="content" source="./media/activity-logs/create-alert-rule-done.png" alt-text="Vytvořit pravidlo upozornění – dokončeno":::

## <a name="view-alerts"></a>Zobrazení upozornění 
1. Výstrahy se **zobrazí u všech** operací správy (v tomto příkladu). Zobrazení výstrah může chvíli trvat. 

    :::image type="content" source="./media/activity-logs/alerts.png" alt-text="Snímek obrazovky zobrazí výstrahy na řídicím panelu.":::
1. Pokud vyberete číslo ve sloupci (například **Celkový počet výstrah**), zobrazí se výstrahy, které byly vyvolány. 

    :::image type="content" source="./media/activity-logs/all-alerts.png" alt-text="Všechny výstrahy":::
1. Pokud vyberete výstrahu, zobrazí se vám podrobnosti. 

    :::image type="content" source="./media/activity-logs/alert-details.png" alt-text="Podrobnosti výstrahy":::
1. V tomto příkladu obdržíte také e-mail s obsahem, jak je znázorněno v následujícím příkladu: 

    :::image type="content" source="./media/activity-logs/alert-email.png" alt-text="E-mailové upozornění":::

## <a name="next-steps"></a>Další kroky
- Další informace o vytváření skupin akcí pomocí různých typů akcí naleznete v tématu [Create and Manage Action Groups in the Azure Portal](../azure-monitor/alerts/action-groups.md).
- Další informace o protokolech aktivit najdete v tématu  [Azure Activity log](../azure-monitor/essentials/activity-log.md).
- Další informace o nastavení výstrah v protokolech aktivit najdete v tématu [výstrahy v protokolu aktivit](../azure-monitor/alerts/activity-log-alerts.md).

