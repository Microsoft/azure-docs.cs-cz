---
title: Systémová témata v Azure Event Grid
description: Popisuje systémová témata ve službě Azure Event Grid.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: spelluru
ms.openlocfilehash: 46bceeb31fa38068c6c4f9f3a86ed556ad39effb
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393149"
---
# <a name="system-topics-in-azure-event-grid"></a>Systémová témata v Azure Event Grid
Služba Azure Event Grid vytvoří systémová témata při vytvoření předplatného první události pro zdroj událostí Azure. V současné době Event Grid nevytváří systémová témata pro tematické zdroje, které byly vytvořeny před březnem, 15, 2020. Pro všechny zdroje tématu, které jste vytvořili k tomuto datu nebo po něm, event grid automaticky vytvoří systémová témata. Tento článek popisuje **systémová témata** ve službě Azure Event Grid.

> [!NOTE]
> Tato funkce momentálně není povolená pro cloud Azure Government. 

## <a name="overview"></a>Přehled
Když vytvoříte předplatné první události pro zdroj událostí Azure, jako je například účet Azure Storage, proces zřizování předplatného vytvoří další prostředek typu **Microsoft.EventGrid/systemTopics**. Při odstranění posledního odběru událostí ve zdroji událostí Azure se automaticky odstraní systémové téma.

Systémové téma se nevztahuje na vlastní scénáře témat, tedy témata event gridu a domény Mřížky událostí. 

## <a name="location"></a>Umístění
Pro zdroje událostí Azure, které jsou v určité oblasti nebo umístění, se systémové téma vytvoří ve stejném umístění jako zdroj událostí Azure. Pokud například vytvoříte odběr událostí pro úložiště objektů blob Azure v USA – východ, vytvoří se systémové téma v USA – východ. Pro globální zdroje událostí Azure, jako jsou předplatná Azure, skupiny prostředků nebo Azure Maps, event grid vytvoří téma systému v **globálním** umístění. 

## <a name="resource-group"></a>Skupina prostředků 
Obecně platí, že systémové téma se vytvoří ve stejné skupině prostředků, ve které se nachází zdroj událostí Azure. Pro odběry událostí vytvořené v oboru předplatného Azure se systémové téma vytvoří pod skupinou prostředků **Default-EventGrid**. Pokud skupina prostředků neexistuje, Azure Event Grid ji vytvoří před vytvořením tématu systému. 

Při pokusu o odstranění skupiny prostředků s účtem úložiště se zobrazí systémové téma v seznamu ovlivněných prostředků.  

![Odstranění skupiny prostředků](./media/system-topics/delete-resource-group.png)

## <a name="next-steps"></a>Další kroky
Viz následující články: 

- [Vlastní témata](custom-topics.md)
- [Domény](event-domains.md)