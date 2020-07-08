---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/06/2020
ms.author: glenga
ms.openlocfilehash: a5f829fc0e153c3a427fd1c7cc7d5c613cd624f6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "83648275"
---
Azure Functions podporuje sdílení prostředků mezi zdroji (CORS). CORS se konfiguruje na [portálu](../articles/azure-functions/functions-how-to-use-azure-function-app-settings.md#cors) a prostřednictvím rozhraní příkazového [řádku Azure CLI](/cli/azure/functionapp/cors). Seznam povolených zdrojů CORS se vztahuje na úrovni aplikace Function App. Pokud je povolená CORS, obsahují odpovědi `Access-Control-Allow-Origin` hlavičku. Další informace naleznete v tématu [Sdílení prostředků různého původu](../articles/azure-functions/functions-how-to-use-azure-function-app-settings.md#cors). 