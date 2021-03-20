---
title: Vlastní témata v Azure Event Grid
description: Popisuje vlastní témata v Azure Event Grid.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 8f48824989c8ec51b766385188ad99e9e59cf621
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "86113781"
---
# <a name="custom-topics-in-azure-event-grid"></a>Vlastní témata v Azure Event Grid
Téma Event Grid poskytuje koncový bod, ve kterém zdroj odesílá události. Vydavatel vytvoří téma Event Grid a rozhodne, zda zdroj události potřebuje jedno téma nebo více než jedno téma. Téma se používá pro kolekci souvisejících událostí. Pro reakci na určité typy událostí předplatitelé rozhodují, která témata se přihlásí k odběru.

**Vlastní témata** jsou témata aplikací a třetích stran. Po vytvoření vlastního tématu nebo po přiřazení vašeho přístupu k vlastnímu tématu se dané vlastní téma zobrazí ve vašem předplatném. 

Při navrhování aplikace máte flexibilitu při rozhodování, kolik témat se má vytvořit. Pro velká řešení vytvořte **vlastní téma** pro **každou kategorii souvisejících událostí**. Představme si například aplikaci, která odesílá události související s úpravami uživatelských účtů a zpracováním objednávek. Není pravděpodobné, že nějaká obslužná rutina události chce přijímat obě kategorie událostí. Vytvořte dvě vlastní témata a nechte obslužné rutiny událostí odebírat to téma, které je zajímá. Pro malá řešení můžete chtít odeslat všechny události do jednoho tématu. Předplatitelé události mohou filtrovat typy událostí, které chtějí.

## <a name="event-schema"></a>Schéma událostí
Podrobný přehled schématu událostí najdete v tématu [Azure Event Grid schéma událostí](event-schema.md). Pro vlastní témata Vydavatel události Určuje **datový** objekt. Data nejvyšší úrovně by měla mít stejná pole jako standardní události definované prostředky.

```json
[
  {
    "topic": string,
    "subject": string,
    "id": string,
    "eventType": string,
    "eventTime": string,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string,
    "metadataVersion": string
  }
]
```

Následující části obsahují odkazy na kurzy k vytváření vlastních témat pomocí šablon Azure Portal, CLI, PowerShell a Azure Resource Manager (ARM). 


## <a name="azure-portal-tutorials"></a>Kurzy Azure Portal
|Nadpis  |Popis  |
|---------|---------|
| [Rychlý Start: vytvoření a směrování vlastních událostí pomocí Azure Portal](custom-event-quickstart-portal.md) | Ukazuje, jak používat portál k posílání vlastních událostí. |
| [Rychlý Start: směrování vlastních událostí do Azure Queue Storage](custom-event-to-queue-storage.md) | Popisuje, jak odesílat vlastní události do úložiště fronty. |
| [Postupy: odeslání do vlastního tématu](post-to-custom-topic.md) | Ukazuje, jak publikovat událost do vlastního tématu. |


## <a name="azure-cli-tutorials"></a>Kurzy k rozhraní příkazového řádku Azure
|Nadpis  |Popis  |
|---------|---------|
| [Rychlý Start: vytvoření a směrování vlastních událostí pomocí Azure CLI](custom-event-quickstart.md) | Ukazuje, jak používat Azure CLI k posílání vlastních událostí. |
| [Azure CLI: vytvoření Event Grid vlastního tématu](./scripts/event-grid-cli-create-custom-topic.md)|Vzorový skript, který vytvoří vlastní téma. Skript načte koncový bod a klíč.|
| [Azure CLI: přihlášení k odběru událostí pro vlastní téma](./scripts/event-grid-cli-subscribe-custom-topic.md)|Ukázkový skript, který vytvoří odběr pro vlastní téma. Odesílá události do Webhooku.|

## <a name="azure-powershell-tutorials"></a>Kurzy Azure PowerShell
|Nadpis  |Popis  |
|---------|---------|
| [Rychlý Start: vytvoření a směrování vlastních událostí pomocí Azure PowerShell](custom-event-quickstart-powershell.md) | Ukazuje, jak používat Azure PowerShell k odesílání vlastních událostí. |
| [PowerShell: vytvoření Event Grid vlastního tématu](./scripts/event-grid-powershell-create-custom-topic.md)|Vzorový skript, který vytvoří vlastní téma. Skript načte koncový bod a klíč.|
| [PowerShell: přihlášení k odběru událostí vlastního tématu](./scripts/event-grid-powershell-subscribe-custom-topic.md)|Ukázkový skript, který vytvoří odběr pro vlastní téma. Odesílá události do Webhooku.|

## <a name="arm-template-tutorials"></a>Kurzy k šablonám ARM
|Nadpis  |Popis  |
|---------|---------|
| [Správce prostředků Šablona: vlastní téma a koncový bod Webhooku](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid) | Správce prostředků šablonu, která vytvoří vlastní téma a předplatné pro toto vlastní téma. Odesílá události do Webhooku. |
| [Správce prostředků Šablona: vlastní téma a koncový bod Event Hubs](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| Správce prostředků šablonu, která vytvoří odběr pro vlastní téma. Odesílá události do Azure Event Hubs. |

## <a name="next-steps"></a>Další kroky
Viz následující články: 

- [Systémová témata](system-topics.md)
- [Domény](event-domains.md)