---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/31/2021
ms.author: glenga
ms.openlocfilehash: 9a5c143927f549124cb6e69a4c8eb4829709a9e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "99493448"
---
+ [Skupina prostředků](../articles/azure-resource-manager/management/overview.md), což je logický kontejner pro související prostředky.
+ Účet standard [Azure Storage](../articles/storage/common/storage-account-create.md), který uchovává stav a další informace o vašich projektech.
+ Plán spotřeby, který definuje základního hostitele pro aplikaci s funkcí bez serveru. 
+ Aplikace Function App, která poskytuje prostředí pro spuštění kódu vaší funkce. Aplikace Function App umožňuje seskupit funkce jako logickou jednotku pro snadnější správu, nasazování a sdílení prostředků v rámci stejného plánu hostování.
+ Instance Application Insights připojená k aplikaci Function App, která sleduje využití funkce bez serveru.