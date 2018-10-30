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
ms.openlocfilehash: 485ee48bf197ad996bba8a516b80882f44ba623a
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2018
ms.locfileid: "50212909"
---
# <a name="delete-data-from-azure-data-explorer"></a>Odstranění dat z Průzkumníku dat Azure

Průzkumník služby Azure Data podporuje několik přístupů hromadné odstranění, které probereme v tomto článku. Jednotlivé záznamy odstranění nepodporuje v reálném čase, protože je optimalizována pro rychlý přístup pro čtení.

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

Pokud potřebujete pomoc s problémy odstranění dat, otevřete prosím v žádosti o podporu [webu Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).
