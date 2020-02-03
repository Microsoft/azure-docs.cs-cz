---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/20/2018
ms.author: glenga
ms.openlocfilehash: 82d122ed236dc72ced7ebafe2301ef5f1143897f
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2020
ms.locfileid: "76963647"
---
Pro maximalizaci výkonu použijte pro každou aplikaci Function App samostatný účet úložiště. To je důležité hlavně v případě, že máte funkce aktivované Durable Functions nebo centra událostí, které generují velké objemy transakcí úložiště. Pokud vaše logika aplikace komunikuje s Azure Storage, buď přímo (pomocí sady SDK úložiště), nebo prostřednictvím jedné z vazeb úložiště, měli byste použít vyhrazený účet úložiště. Pokud například máte funkci aktivovanou centrem událostí, která zapisuje nějaká data do úložiště objektů blob, použijte dva účty úložiště&mdash;jednu pro aplikaci Function App a druhou pro objekty blob uložené funkcí.