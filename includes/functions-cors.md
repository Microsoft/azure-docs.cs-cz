---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/06/2020
ms.author: glenga
ms.openlocfilehash: a5f829fc0e153c3a427fd1c7cc7d5c613cd624f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "83648275"
---
Azure Functions podporuje sdílení prostředků mezi zdroji (CORS). CORS se konfiguruje na [portálu](../articles/azure-functions/functions-how-to-use-azure-function-app-settings.md#cors) a prostřednictvím rozhraní příkazového [řádku Azure CLI](/cli/azure/functionapp/cors). Seznam povolených zdrojů CORS se vztahuje na úrovni aplikace Function App. Pokud je povolená CORS, obsahují odpovědi `Access-Control-Allow-Origin` hlavičku. Další informace naleznete v tématu [Sdílení prostředků různého původu](../articles/azure-functions/functions-how-to-use-azure-function-app-settings.md#cors). 