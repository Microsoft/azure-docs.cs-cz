---
title: Odstranit skupinu prostředků Azure Resource Manageru
description: Popisuje, jak Azure Resource Manageru orders odstranění prostředků při odstranění skupiny prostředků.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2018
ms.author: tomfitz
ms.openlocfilehash: 8b0711cab07584aa84ab437a2a4efb5aab92f3d1
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2018
ms.locfileid: "52319355"
---
# <a name="azure-resource-manager-resource-group-deletion"></a>Odstranění skupiny prostředků služby Azure Resource Manageru

Tento článek popisuje, jak Azure Resource Manageru orders odstranění prostředky, když odstraníte skupinu prostředků.

## <a name="determine-order-of-deletion"></a>Určení pořadí odstranění

Když odstraníte skupinu prostředků, Resource Manager určuje pořadí, odstraňte prostředky. Použije následujícím pořadí:

1. Se odstraní všechny prostředky vnořená ().

2. Dále se odstraní prostředky, které spravují další prostředky. Prostředek může mít `managedBy` nastavenou k označení, že jiný prostředek spravuje ho. Pokud je tato vlastnost nastavena, před dalším prostředkům odstranění prostředku, který spravuje jiný prostředek.

3. Zbývající prostředky odstraní po předchozí dvě kategorie.

## <a name="resource-deletion"></a>Odstranění prostředku

Po pořadí je určeno, vydá Resource Manageru operace odstranění pro každý zdroj. Čeká nějak záviset na dokončení, než budete pokračovat.

U synchronních operací může kódy úspěšné odpovědi očekávané jsou:

* 200
* 204
* 404

Pro asynchronní operace je očekávané úspěšné odpovědi 202. Resource Manager sleduje hlavičky location nebo hlavičce azure asynchronní operace k určení stavu asynchronní operaci.
  
### <a name="errors"></a>Chyby

Při operaci delete vrátí chybu, správce prostředků zopakuje pokus o volání aktualizace. Opakované pokusy se provede pro 5xx, 429 a 408 stavové kódy. Výchozí časový interval opakování je 15 minut.

## <a name="after-deletion"></a>Po jeho odstranění

Resource Manager vydává volání GET u jednotlivých prostředků, které se pokusil odstranit. Odpověď na toto volání GET má být 404. Resource Manager získá 404, považuje se odstraňování byly úspěšně dokončeny. Resource Manager Odstraní prostředek uloženou v mezipaměti.

Ale pokud volání GET s prostředkem vrátí 200 nebo 201, znovu správce prostředků vytvoří prostředek.

### <a name="errors"></a>Chyby

Pokud operace GET vrátí chybu, Resource Manager opakuje GET pro následující kód chyby:

* Méně než 100
* 408
* 429
* Větší než 500

Pro další kódy chyb nezdaří správce prostředků odstranění prostředku.

## <a name="next-steps"></a>Další postup

* Koncepce Resource Manageru, najdete v článku [přehled Azure Resource Manageru](resource-group-overview.md).
* Operace pro poskytovatele prostředků najdete v tématu [rozhraní Azure REST API](/rest/api/).
