---
title: Případy použití pro domény událostí v Azure Event Grid
description: Tento článek popisuje několik případů použití pro používání domén událostí v Azure Event Grid.
ms.topic: conceptual
ms.date: 03/04/2021
ms.openlocfilehash: 00318fc78053ed55e3599c329746d89d2eee4f99
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102204313"
---
# <a name="use-cases-for-event-domains-in-azure-event-grid"></a>Případy použití pro domény událostí v Azure Event Grid
Tento článek popisuje několik případů použití pro používání domén událostí v Azure Event Grid. 

## <a name="use-case-1"></a>Případ použití 1 
[!INCLUDE [event-grid-domain-example-use-case.md](../../includes/event-grid-domain-example-use-case.md)]

## <a name="use-case-2"></a>Případ použití 2
Při použití systémových témat je povolený limit 500ch odběrů událostí. Pokud potřebujete více než 500 odběrů událostí pro systémové téma, můžete použít domény. 

Předpokládejme, že jste vytvořili systémové téma pro Azure Blob Storage a potřebujete vytvořit více než 500 odběrů v tématu, ale není to možné z důvodu omezení (500 odběrů na téma). V takovém případě můžete použít následující řešení, které používá doménu události. 

1. Vytvořte doménu, která může podporovat až 100 000 témat a každé téma v doméně může obsahovat 500 odběrů událostí. Tento model vám poskytne 500 × 100 000 odběrů událostí. 
1. Vytvoření předplatného Azure Functions pro Azure Storage systémové téma. Když funkce přijímá události ze služby Azure Storage, může rozšířit a publikovat události do vhodného tématu v doméně. Například funkce by mohla analyzovat název souboru objektu BLOB a určit tak téma cílové domény a publikovat událost do domény. 

:::image type="content" source="./media/event-domains-use-cases/use-case-2.jpg" alt-text="Domény AzureEvent Grid – případ použití 2":::


## <a name="next-steps"></a>Další kroky
Další informace o nastavení domén událostí, vytváření témat, vytváření odběrů událostí a publikování událostí najdete v tématu [Správa domén událostí](./how-to-event-domains.md).
