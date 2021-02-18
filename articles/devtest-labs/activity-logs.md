---
title: Protokoly aktivit v Azure DevTest Labs | Microsoft Docs
description: Tento článek popisuje kroky pro zobrazení protokolů aktivit pro Azure DevTest Labs.
ms.topic: how-to
ms.date: 07/10/2020
ms.openlocfilehash: 51bdfc6c3857a3e59d75094b4c847c80c58de045
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100582770"
---
# <a name="view-activity-logs-for-labs-in-azure-devtest-labs"></a>Zobrazení protokolů aktivit pro cvičení v Azure DevTest Labs 
Po vytvoření jedné nebo více testovacích prostředí budete pravděpodobně chtít monitorovat, jak a kdy jsou cvičení k dispozici, upravována a spravována a kým. Azure DevTest Labs používá pro poskytování informací o těchto operacích s Labs Azure Monitor, konkrétně **protokoly aktivit**. 

Tento článek vysvětluje, jak zobrazit protokoly aktivit pro testovací prostředí v Azure DevTest Labs.

## <a name="view-activity-log-for-a-lab"></a>Zobrazení protokolu aktivit pro testovací prostředí

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. Vyberte **všechny služby** a potom v části **DEVOPS** vyberte **DevTest Labs** . Pokud vyberete * (hvězdička) vedle **DevTest Labs** v části **DEVOPS** . Tato akce přidá **DevTest Labs** do levé navigační nabídky, abyste k ní mohli snadno získat přístup později. Pak můžete v levé navigační nabídce vybrat **DevTest Labs** .

    ![Všechny služby – výběr DevTest Labs](./media/devtest-lab-create-lab/all-services-select.png)
1. V seznamu cvičení vyberte testovací prostředí.
1. Na domovské stránce testovacího prostředí v nabídce vlevo vyberte **Konfigurace a zásady** . 

    :::image type="content" source="./media/activity-logs/configuration-policies-link.png" alt-text="V levé nabídce vyberte konfigurace a zásady.":::
1. Na stránce **Konfigurace a zásady** v nabídce vlevo v části **Spravovat** vyberte **Protokol aktivit** . Měli byste vidět záznamy o operacích, které se v testovacím prostředí udělaly. 

    :::image type="content" source="./media/activity-logs/activity-log.png" alt-text="Protokol aktivit":::    
1. Vyberte událost, pro kterou chcete zobrazit podrobnosti. Na stránce **Souhrn** uvidíte informace, jako je název operace, časové razítko a to, kdo tuto operaci provedl. 
    
    :::image type="content" source="./media/activity-logs/stop-vm-event.png" alt-text="Událost zastavení virtuálního počítače – souhrn":::        
1. Pokud chcete zobrazit další podrobnosti, přejděte na kartu **JSON** . V následujícím příkladu se zobrazí název virtuálního počítače a operace, která se na virtuálním počítači provedla (zastaveno).

    :::image type="content" source="./media/activity-logs/stop-vm-event-json.png" alt-text="Zastavení události virtuálního počítače – JSON":::           
1. Přepněte na kartu **historie změn (Preview)** a zobrazte historii změn. V následujícím příkladu vidíte změnu, která byla provedena na virtuálním počítači. 

    :::image type="content" source="./media/activity-logs/change-history.png" alt-text="Zastavení události virtuálního počítače – historie změn":::             
1. Kliknutím na změnu v seznamu historie změn zobrazíte další podrobnosti o této změně. 

    :::image type="content" source="./media/activity-logs/change-details.png" alt-text="Zastavení události-Podrobnosti o změně virtuálního počítače":::             

Další informace o protokolech aktivit najdete v tématu [Azure Activity log](../azure-monitor/essentials/activity-log.md).

## <a name="next-steps"></a>Další kroky

- Další informace o nastavení **výstrah** v protokolech aktivit najdete v tématu věnovaném [vytváření výstrah](create-alerts.md).
- Další informace o protokolech aktivit najdete v tématu  [Azure Activity log](../azure-monitor/essentials/activity-log.md).

