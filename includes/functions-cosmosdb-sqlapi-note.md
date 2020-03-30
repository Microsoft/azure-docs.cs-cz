---
title: zahrnout soubor
description: zahrnout soubor
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 08/22/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: f7495c52e36bdc207145f17ec72eb57b7ebf1784
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "76279300"
---
Vazby Azure Cosmos DB se podporují jenom pro použití s rozhraním SQL API. Pro všechny ostatní rozhraní API Db Služby Azure Cosmos byste měli přistupovat k databázi z vaší funkce pomocí statického klienta pro vaše rozhraní API, včetně [rozhraní API Služby Azure Cosmos DB pro MongoDB](../articles/cosmos-db/mongodb-introduction.md), [rozhraní API Cassandra](../articles/cosmos-db/cassandra-introduction.md), rozhraní [GREMLIN API](../articles/cosmos-db/graph-introduction.md)a rozhraní TABLE [API](../articles/cosmos-db/table-introduction.md).
