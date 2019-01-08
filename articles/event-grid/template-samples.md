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
ms.date: 01/04/2019
ms.author: tomfitz
ms.openlocfilehash: 788d23c7bddd90c1e12a118742c651eb9759529c
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/07/2019
ms.locfileid: "54062957"
---
# <a name="azure-resource-manager-templates-for-event-grid"></a>Šablony Azure Resource Manageru pro Event Grid

Syntaxi JSON a vlastnosti, které chcete použít v šabloně najdete v tématu [typy prostředků Microsoft.EventGrid](/azure/templates/microsoft.eventgrid/allversions). Následující tabulka obsahuje odkazy na šablony Azure Resource Manageru pro Event Grid.

| | |
|-|-|
|**Předplatná Event Gridu**||
| [Vlastní témata a odběry s využitím koncového bodu webhooku](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid)| Nasadí vlastní téma Event Gridu. Vytvoří odběr tohoto vlastního tématu, který využívá koncový bod webhooku. |
| [Odběr vlastního tématu s využitím koncového bodu centra událostí](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| Vytvoří odběr vlastního tématu Event Gridu. Odběr jako koncový bod využívá centrum událostí. |
| [Předplatné Azure nebo odběr skupiny prostředků](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-resource-events-to-webhook)| Přihlásí se k odběru událostí skupiny prostředků nebo předplatného Azure. Zdrojem událostí je skupina prostředků, kterou při nasazování zadáte jako cíl. Odběr jako koncový bod využívá Webhook. |
| [Účet úložiště objektů blob a jeho odběr](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-subscription-and-storage)| Nasadí účet úložiště objektů blob v Azure a přihlásí se k odběru událostí tohoto účtu úložiště. |
| | |
