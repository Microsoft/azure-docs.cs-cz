---
title: Odstranění dat z Průzkumníku dat Azure
description: Tento článek popisuje hromadné odstranění scénáře v prozkoumat Data Azure, včetně vyprázdnění a uchovávání dat na základě odstraní.
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 571a005dd3f50690f291a7ffa3c1174ea15cb0ed
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47047332"
---
# <a name="delete-data-from-azure-data-explorer"></a>Odstranění dat z Průzkumníku dat Azure

Průzkumník služby Azure Data podporuje několik přístupů hromadné odstranění, které probereme v tomto článku. Jednotlivé záznamy odstranění nepodporuje v reálném čase, protože je optimalizována pro rychlý přístup pro čtení.

* Pokud databázi už je nepotřebujete, odstraňte ji pomocí příkazu drop database.

    ```Kusto
    .drop database <DatabaseName>
    ```

* Pokud jeden nebo více tabulek je už je nepotřebujete, odstraňte je pomocí přetažení tabulky nebo vyřadit příkaz tabulky.

    ```Kusto
    .drop table <TableName>

    .drop tables (<TableName1>, <TableName2>,...)
    ```

* Stará data se už je nepotřebujete, odstraňte ho tak, že změníte dobu uchování na úrovni databáze nebo tabulky.

    Vezměte v úvahu databázi nebo tabulku, která je nastaveno na 90 dní uchování. Obchodní musí změnit, takže teď je potřeba data pouze 60 dnů. V takovém případě odstraňte starší data v jednom z následujících způsobů.

    ```Kusto
    .alter-merge database <DatabaseName> policy retention softdelete = 60d

    .alter-merge table <TableName> policy retention softdelete = 60d
    ```

    Další informace najdete v tématu [zásady uchovávání informací](https://docs.microsoft.com/azure/kusto/concepts/retentionpolicy).

* Můžete odstranit jednotlivé záznamy pomocí *vyprázdnit* operace založené na predikátu lajk `where CustomerName == 'contoso'`. Ale nutné dodat, vyprázdnění je hromadné odstranění, který není určený pro odstranění v reálném čase. Následující příklad ukazuje vyprázdnění.

    ```Kusto
    .purge table Customer records
    | where CustomerName =='contoso'
    ```

Pokud potřebujete pomoc s problémy odstranění dat, otevřete prosím v žádosti o podporu [webu Azure portal](https://portal.azure.com).