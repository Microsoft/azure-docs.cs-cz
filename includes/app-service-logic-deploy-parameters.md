---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: f188f2c7bea511f1109d37ef49563e0f745a770e
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2019
ms.locfileid: "68385755"
---
Pomocí Azure Resource Manager můžete definovat parametry pro hodnoty, které se mají použít při nasazování šablony. Šablona obsahuje `parameters` oddíl, který obsahuje všechny hodnoty parametrů. Každá hodnota parametru je používána šablonou k definování prostředků, které chcete nasadit.

> [!NOTE]
> Nedefinujte parametry pro hodnoty, které jsou vždy stejné. Definujte parametry pouze pro hodnoty, které se liší v závislosti na projektu, který nasazujete, nebo v závislosti na prostředí, ve kterém nasazujete.

Při definování parametrů:

* Chcete-li určit povolené hodnoty, které může uživatel poskytnout během nasazení, použijte pole **allowedValues** .

* Pokud chcete přiřadit výchozí hodnoty k parametru, když během nasazování nejsou k dispozici žádné hodnoty, použijte pole **DefaultValue** . 
