---
title: Vlastní témata v Azure Event Grid
description: Popisuje vlastní témata ve službě Azure Event Grid.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: spelluru
ms.openlocfilehash: 166a48d74d32c8b0a3a59310e693ea96ada29116
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/15/2020
ms.locfileid: "81394355"
---
# <a name="custom-topics-in-azure-event-grid"></a>Vlastní témata v Azure Event Grid
Téma mřížky událostí poskytuje koncový bod, kam zdroj odesílá události. Vydavatel vytvoří téma mřížky událostí a rozhodne, zda zdroj události potřebuje jedno téma nebo více než jedno téma. Téma se používá pro kolekci souvisejících událostí. Chcete-li reagovat na určité typy událostí, odběratelé rozhodnout, která témata se přihlásit k odběru.

**Vlastní témata** jsou témata aplikace a třetích stran. Po vytvoření vlastního tématu nebo po přiřazení vašeho přístupu k vlastnímu tématu se dané vlastní téma zobrazí ve vašem předplatném. 

Při navrhování aplikace máte flexibilitu při rozhodování o tom, kolik témat chcete vytvořit. Pro velká řešení vytvořte **vlastní téma** pro každou **kategorii souvisejících událostí**. Představme si například aplikaci, která odesílá události související s úpravami uživatelských účtů a zpracováním objednávek. Není pravděpodobné, že nějaká obslužná rutina události chce přijímat obě kategorie událostí. Vytvořte dvě vlastní témata a nechte obslužné rutiny událostí odebírat to téma, které je zajímá. U malých řešení můžete raději odeslat všechny události na jedno téma. Odběratelé událostí mohou filtrovat typy událostí, které chtějí.

## <a name="event-schema"></a>Schéma událostí
Podrobný přehled schématu událostí najdete v tématu [Schéma událostí služby Azure Event Grid](event-schema.md). Pro vlastní témata určuje datový **objekt** vydavatel události. Data nejvyšší úrovně by měla mít stejná pole jako standardní události definované prostředky.

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

Následující části obsahují odkazy na kurzy k vytvoření vlastních témat pomocí šablon Azure Portal, CLI, PowerShell a Azure Resource Manager (ARM). 


## <a name="azure-portal-tutorials"></a>Kurzy na portál Azure
|Nadpis  |Popis  |
|---------|---------|
| [Úvodní příručka: vytváření a směrování vlastních událostí pomocí portálu Azure](custom-event-quickstart-portal.md) | Ukazuje, jak pomocí portálu odesílat vlastní události. |
| [Úvodní příručka: směrování vlastních událostí do úložiště Azure Queue](custom-event-to-queue-storage.md) | Popisuje, jak odeslat vlastní události do úložiště fronty. |
| [Jak: příspěvek na vlastní téma](post-to-custom-topic.md) | Ukazuje, jak zveřejnit událost do vlastního tématu. |


## <a name="azure-cli-tutorials"></a>Kurzy Azure CLI
|Nadpis  |Popis  |
|---------|---------|
| [Úvodní příručka: vytváření a směrování vlastních událostí pomocí azure CLI](custom-event-quickstart.md) | Ukazuje, jak používat Azure CLI k odesílání vlastních událostí. |
| [Nastavení příkazu k řešení Azure: vytvoření vlastního tématu gridu událostí](./scripts/event-grid-cli-create-custom-topic.md)|Ukázkový skript, který vytvoří vlastní téma. Skript načte koncový bod a klíč.|
| [Nastavení příkazu k příkazu Azure: přihlášení k odběru událostí pro vlastní téma](./scripts/event-grid-cli-subscribe-custom-topic.md)|Ukázkový skript, který vytvoří odběr pro vlastní téma. Odesílá události webhooku.|

## <a name="azure-powershell-tutorials"></a>Kurzy Azure PowerShellu
|Nadpis  |Popis  |
|---------|---------|
| [Úvodní příručka: vytváření a směrování vlastních událostí pomocí Azure PowerShellu](custom-event-quickstart-powershell.md) | Ukazuje, jak používat Azure PowerShell k odesílání vlastních událostí. |
| [PowerShell: vytvoření vlastního tématu event gridu](./scripts/event-grid-powershell-create-custom-topic.md)|Ukázkový skript, který vytvoří vlastní téma. Skript načte koncový bod a klíč.|
| [PowerShell: Přihlášení k odběru událostí pro vlastní téma](./scripts/event-grid-powershell-subscribe-custom-topic.md)|Ukázkový skript, který vytvoří odběr pro vlastní téma. Odesílá události webhooku.|

## <a name="arm-template-tutorials"></a>Kurzy předlohy ARM
|Nadpis  |Popis  |
|---------|---------|
| [Šablona Správce prostředků: vlastní téma a koncový bod WebHooku](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid) | Šablona Správce prostředků, která vytvoří vlastní téma a předplatné pro toto vlastní téma. Odesílá události webhooku. |
| [Šablona Správce prostředků: vlastní téma a koncový bod Centra událostí](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| Šablona Správce prostředků, která vytvoří předplatné pro vlastní téma. Odesílá události do Centra událostí Azure. |

## <a name="next-steps"></a>Další kroky
Viz následující články: 

- [Systémová témata](system-topics.md)
- [Domény](event-domains.md)