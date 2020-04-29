---
title: Systémová témata v Azure Event Grid
description: Popisuje systémová témata v Azure Event Grid.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: spelluru
ms.openlocfilehash: 46bceeb31fa38068c6c4f9f3a86ed556ad39effb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81393149"
---
# <a name="system-topics-in-azure-event-grid"></a>Systémová témata v Azure Event Grid
Služba Azure Event Grid vytvoří systémová témata při vytváření prvního odběru událostí pro zdroj události Azure. V současné době Event Grid nevytváří systémová témata pro zdroje témat, které byly vytvořeny před 15. března 2020. Pro všechny zdroje témat, které jste vytvořili v nebo po tomto datu, Event Grid automaticky vytvoří systémová témata. Tento článek popisuje **Systémová témata** v Azure Event Grid.

> [!NOTE]
> Tato funkce momentálně není povolená pro Azure Government Cloud. 

## <a name="overview"></a>Přehled
Když vytvoříte první odběr událostí pro zdroj událostí Azure, například Azure Storage účet, proces zřizování pro předplatné vytvoří další prostředek typu **Microsoft. EventGrid/systemTopics**. Když se odstraní poslední odběr události ke zdroji událostí Azure, automaticky se odstraní téma systému.

Systémové téma se nevztahuje na vlastní scénáře, to znamená Event Grid témata a Event Grid domény. 

## <a name="location"></a>Umístění
V případě zdrojů událostí Azure, které jsou v určité oblasti nebo umístění, je systémové téma vytvořeno ve stejném umístění jako zdroj události Azure. Pokud třeba vytvoříte odběr událostí pro úložiště objektů BLOB v Azure v Východní USA, bude se v systému vytvořit téma v Východní USA. U globálních zdrojů událostí Azure, jako jsou například předplatná Azure, skupiny prostředků nebo Azure Maps, Event Grid vytvoří systémové téma v **globálním** umístění. 

## <a name="resource-group"></a>Skupina prostředků 
Obecně platí, že je systémové téma vytvořeno ve stejné skupině prostředků, ve které je zdroj události Azure. V případě odběrů událostí vytvořených v oboru předplatného Azure se v rámci skupiny prostředků **Default-EventGrid**vytvoří systémové téma. Pokud skupina prostředků neexistuje, Azure Event Grid ji vytvoří před vytvořením systémového tématu. 

Když se pokusíte odstranit skupinu prostředků s účtem úložiště, zobrazí se v seznamu ovlivněných prostředků téma systém.  

![Odstranění skupiny prostředků](./media/system-topics/delete-resource-group.png)

## <a name="next-steps"></a>Další kroky
Viz následující články: 

- [Vlastní témata](custom-topics.md)
- [Domény](event-domains.md)