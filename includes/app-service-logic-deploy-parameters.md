---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: f188f2c7bea511f1109d37ef49563e0f745a770e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66167419"
---
S Azure Resource Manageru, můžete definovat parametry pro hodnoty pro použití při nasazování šablony. Šablona obsahuje `parameters` oddíl, který obsahuje všechny hodnoty parametrů. Každá hodnota parametru používá k definování prostředků, které chcete nasadit šablonu.

> [!NOTE]
> Nedefinujte parametry pro hodnoty, které jsou vždy stejné. Definujte parametry jenom pro hodnoty, které se liší podle projektu, který nasazujete nebo podle prostředí, do kterého nasazujete.

Při definování parametrů:

* Pokud chcete zadat povolené hodnoty, které můžete během nasazení zadat uživatele, použijte **allowedValues** pole.

* Pokud chcete přiřadit výchozí hodnoty parametru, když se nezadají žádné hodnoty během nasazení, použijte **defaultValue** pole. 
