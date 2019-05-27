---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: f188f2c7bea511f1109d37ef49563e0f745a770e
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66167419"
---
S Azure Resource Manageru, můžete definovat parametry pro hodnoty pro použití při nasazování šablony. Šablona obsahuje `parameters` oddíl, který obsahuje všechny hodnoty parametrů. Každá hodnota parametru používá k definování prostředků, které chcete nasadit šablonu.

> [!NOTE]
> Nedefinujte parametry pro hodnoty, které jsou vždy stejné. Definujte parametry jenom pro hodnoty, které se liší podle projektu, který nasazujete nebo podle prostředí, do kterého nasazujete.

Při definování parametrů:

* Pokud chcete zadat povolené hodnoty, které můžete během nasazení zadat uživatele, použijte **allowedValues** pole.

* Pokud chcete přiřadit výchozí hodnoty parametru, když se nezadají žádné hodnoty během nasazení, použijte **defaultValue** pole. 
