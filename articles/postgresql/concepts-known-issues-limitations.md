---
title: Známé problémy a omezení – Azure Database for PostgreSQL – jeden server a flexibilní Server (Preview)
description: Uvádí známé problémy, o kterých si zákazníci můžou uvědomit.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/05/2020
ms.openlocfilehash: 3c26368e6281ad74d617891ba15c980117b25a6e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100106407"
---
# <a name="azure-database-for-postgresql---known-issues-and-limitations"></a>Azure Database for PostgreSQL – známé problémy a omezení

Tato stránka poskytuje seznam známých problémů v Azure Database for PostgreSQL, které by mohly mít vliv na vaši aplikaci. Také uvádí případné zmírnění a doporučení k tomuto problému.

## <a name="intelligent-performance---query-store"></a>Inteligentní výkon – úložiště dotazů

Platí pro Azure Database for PostgreSQL – jeden server.

| To | Příčina | Náprava|
| ----- | ------ | ---- | 
| PostgreSQL 9,6, 10, 11 | Zapnutí parametru serveru `pg_qs.replace_parameter_placeholders` může způsobit vypnutí serveru ve výjimečných scénářích. | Na webu Azure Portal, oddílu parametry serveru, zapněte `pg_qs.replace_parameter_placeholders` `OFF` a uložte hodnotu parametru.   | 

## <a name="server-parameters"></a>Parametry serveru

Platí pro Azure Database for PostgreSQL s jedním serverem a flexibilním serverem

| To | Příčina | Náprava| 
| ----- | ------ | ---- | 
| PostgreSQL 9,6, 10, 11 | Změna parametru serveru `max_locks_per_transaction` na vyšší hodnotu, než je [doporučeno](https://www.postgresql.org/docs/11/kernel-resources.html) , může způsobit, že se server nemůže po restartování spustit. | Ponechte tuto výchozí hodnotu (32 nebo 64) nebo se změňte na rozumnou hodnotu na PostgreSQL [dokumentaci](https://www.postgresql.org/docs/11/kernel-resources.html). <br> <br> Na straně služby se tento postup zpracovává, aby se omezila vysoká hodnota na základě SKU.  | 

## <a name="next-steps"></a>Další kroky
- Viz [osvědčené postupy](./concepts-query-store-best-practices.md) pro úložiště dotazů
