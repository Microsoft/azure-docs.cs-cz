---
title: Systémová témata v Azure Event Grid
description: Popisuje systémová témata v Azure Event Grid.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 06/02/2020
ms.author: spelluru
ms.openlocfilehash: c34ada2c7437d8415b52c68fb66103ec3aa81d95
ms.sourcegitcommit: ba8df8424d73c8c4ac43602678dae4273af8b336
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2020
ms.locfileid: "84456925"
---
# <a name="system-topics-in-azure-event-grid"></a>Systémová témata v Azure Event Grid
Systémová témata jsou témata vytvořená pro služby Azure, například Azure Storage a Azure Event Hubs. Můžete vytvořit systémová témata pomocí Azure Portal, PowerShellu, rozhraní příkazového řádku nebo šablony Azure Resource Manager.  

> [!NOTE]
> Tato funkce momentálně není povolená pro Azure Government Cloud. 

## <a name="create-system-topics"></a>Vytvořit systémová témata
Systémové téma můžete vytvořit dvěma způsoby: 

- Vytvořte systémové téma pro prostředek Azure a pak vytvořte odběr událostí pro toto systémové téma.
- Vytvořte odběr událostí pro prostředek Azure, který interně vytvoří systémové téma.

Při použití prvního přístupu se systémové téma neodstraní automaticky, když se odstraní poslední odběr události pro toto systémové téma. Při použití druhého přístupu se systémové téma automaticky odstraní při odstranění posledního odběru události. 

Podrobné pokyny k vytváření systémových témat pomocí Azure Portal, PowerShellu nebo rozhraní příkazového řádku najdete v následujících článcích:

- [Pomocí Azure Portal vytvářet, zobrazovat a spravovat systémová témata](create-view-manage-system-topics.md).
- [Vytváření, zobrazování a Správa systémových témat Event Grid pomocí Azure CLI](create-view-manage-system-topics-cli.md)
- [Vytváření Event Grid systémových témat pomocí šablon Azure Resource Manager](create-view-manage-system-topics-arm.md)

## <a name="system-topic-name"></a>Název systémového tématu
Event Grid nevytvořila systémová témata pro zdroje Azure (Azure Storage, Azure Event Hubs atd.), která byla vytvořená před 15. března 2020. Pokud jste vytvořili předplatné pro událost vyvolanou zdroji Azure mezi 3/15/2020 a 6/2/2020, Služba Event Grid automaticky vytvořila systémové téma s **náhodně generovaným názvem**. Po 6/2/2020 můžete při vytváření odběru událostí pro zdroj Azure zadat název systémového tématu. 

## <a name="location-and-resource-group"></a>Umístění a skupina prostředků
V případě zdrojů událostí Azure, které jsou v určité oblasti nebo umístění, je systémové téma vytvořeno ve stejném umístění jako zdroj události Azure. Pokud třeba vytvoříte odběr událostí pro úložiště objektů BLOB v Azure v Východní USA, bude se v systému vytvořit téma v Východní USA. U globálních zdrojů událostí Azure, jako jsou například předplatná Azure, skupiny prostředků nebo Azure Maps, Event Grid vytvoří systémové téma v **globálním** umístění. 

Obecně platí, že je systémové téma vytvořeno ve stejné skupině prostředků, ve které je zdroj události Azure. V případě odběrů událostí vytvořených v oboru předplatného Azure se v rámci skupiny prostředků **Default-EventGrid**vytvoří systémové téma. Pokud skupina prostředků neexistuje, Azure Event Grid ji vytvoří před vytvořením systémového tématu. 

## <a name="next-steps"></a>Další kroky
Viz následující články: 

- [Pomocí Azure Portal vytvářet, zobrazovat a spravovat systémová témata](create-view-manage-system-topics.md).
- [Vytváření, zobrazování a Správa systémových témat Event Grid pomocí Azure CLI](create-view-manage-system-topics-cli.md)
- [Vytváření Event Grid systémových témat pomocí šablon Azure Resource Manager](create-view-manage-system-topics-arm.md)
