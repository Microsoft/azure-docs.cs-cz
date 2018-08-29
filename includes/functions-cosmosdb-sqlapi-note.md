---
title: zahrnout soubor
description: zahrnout soubor
services: functions
author: ggailey777
manager: jeconnoc
ms.service: functions
ms.topic: include
ms.date: 08/22/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: bb915c18965a55c2b1fc7fe3cf1fa923d7f43fd6
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43052515"
---
Vazby Azure Cosmos DB se podporují jenom pro použití s rozhraním SQL API. V případě všech ostatních rozhraní API služby Azure Cosmos DB byste měli k databázi z funkce přistupovat pomocí statického klienta pro vaše rozhraní API, a to včetně rozhraní [MongoDB API](../articles/cosmos-db/mongodb-introduction.md), [Cassandra API](../articles/cosmos-db/cassandra-introduction.md), [Gremlin API](../articles/cosmos-db/graph-introduction.md) a [Table API](../articles/cosmos-db/table-introduction.md).
