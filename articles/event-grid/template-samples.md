---
title: Ukázkové šablony Azure Resource Manageru – Event Grid | Microsoft Docs
description: Ukázkové šablony Azure Resource Manageru pro Event Grid
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.date: 03/27/2018
ms.author: tomfitz
ms.openlocfilehash: f4d6a663b4e0d2c2166028051e713668534b20bc
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="azure-resource-manager-templates-for-event-grid"></a>Šablony Azure Resource Manageru pro Event Grid

Následující tabulka obsahuje odkazy na šablony Azure Resource Manageru pro Event Grid.

| | |
|-|-|
|**Předplatná Event Gridu**||
| [Vlastní témata a odběry s využitím koncového bodu webhooku](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid)| Nasadí vlastní téma Event Gridu. Vytvoří odběr tohoto vlastního tématu, který využívá koncový bod webhooku. |
| [Odběr vlastního tématu s využitím koncového bodu centra událostí](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/subscribeCustomTopicToEventHub.json)| Vytvoří odběr vlastního tématu Event Gridu, které již existuje. Odběr jako koncový bod využívá centrum událostí. |
| [Odběr skupiny prostředků](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/subscribeResourceGroupToWebHook.json)| Přihlásí se k odběru událostí skupiny prostředků. Zdrojem událostí je skupina prostředků, kterou při nasazování zadáte jako cíl. Odběr jako koncový bod využívá centrum událostí. |
| [Účet úložiště objektů blob a jeho odběr](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/createBlobAndSubscribe.json)| Nasadí účet úložiště objektů blob v Azure a přihlásí se k odběru událostí tohoto účtu úložiště. |
| | |
