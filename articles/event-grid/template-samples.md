---
title: Ukázkové šablony Azure Resource Manageru – Event Grid | Microsoft Docs
description: Tento článek obsahuje seznam ukázek šablon Azure Resource Manager pro Azure Event Grid na GitHubu.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 38d8db0bcc504760595fe51b63072f63e785577a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "76720618"
---
# <a name="azure-resource-manager-templates-for-event-grid"></a>Šablony Azure Resource Manageru pro Event Grid

Syntaxe json a vlastnosti pro použití v šabloně naleznete v [tématu Microsoft.EventGrid typy prostředků](/azure/templates/microsoft.eventgrid/allversions). Následující tabulka obsahuje odkazy na šablony Azure Resource Manageru pro Event Grid.

| | |
|-|-|
|**Odběry gridu událostí**||
| [Vlastní témata a odběry s využitím koncového bodu webhooku](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid)| Nasadí vlastní téma Event Gridu. Vytvoří odběr tohoto vlastního tématu, který využívá koncový bod webhooku. |
| [Odběr vlastního tématu s využitím koncového bodu centra událostí](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| Vytvoří odběr vlastního tématu Event Gridu. Odběr jako koncový bod využívá centrum událostí. |
| [Předplatné Azure nebo odběr skupiny prostředků](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-resource-events-to-webhook)| Přihlásí se k odběru událostí skupiny prostředků nebo předplatného Azure. Zdrojem událostí je skupina prostředků, kterou při nasazování zadáte jako cíl. Odběr jako koncový bod využívá Webhook. |
| [Účet úložiště objektů blob a jeho odběr](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-subscription-and-storage)| Nasadí účet úložiště objektů blob v Azure a přihlásí se k odběru událostí tohoto účtu úložiště. |
| | |
