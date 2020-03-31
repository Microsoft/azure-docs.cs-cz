---
title: Limity – databáze Azure pro PostgreSQL – jeden server
description: Tento článek popisuje omezení v Azure Database pro PostgreSQL – jeden server, jako je například počet možností připojení a modulu úložiště.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/28/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 047e722a0e0ade60d1eb93a48e37333fffafd674
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76836452"
---
# <a name="limits-in-azure-database-for-postgresql---single-server"></a>Limity v databázi Azure pro PostgreSQL – jeden server
Následující části popisují omezení kapacity a funkčnosti v databázové službě. Pokud se chcete dozvědět o vrstvách prostředků (výpočetní prostředky, paměť, úložiště), přečtěte si článek [cenové úrovně.](concepts-pricing-tiers.md)


## <a name="maximum-connections"></a>Maximální počet připojení
Maximální počet připojení na cenovou úroveň a virtuální jádra jsou uvedeny níže. Systém Azure vyžaduje pět připojení ke sledování Azure Database pro PostgreSQL server. 

|**Cenová úroveň**| **virtuální jádra (hlavní)**| **Maximální počet připojení** | **Maximální počet uživatelských připojení** |
|---|---|---|---|
|Basic| 1| 55 | 50|
|Basic| 2| 105 | 100|
|Pro obecné účely| 2| 150| 145|
|Pro obecné účely| 4| 250| 245|
|Pro obecné účely| 8| 480| 475|
|Pro obecné účely| 16| 950| 945|
|Pro obecné účely| 32| 1 500| 1495|
|Pro obecné účely| 64| 1900| 1895|
|Optimalizováno pro paměť| 2| 300| 295|
|Optimalizováno pro paměť| 4| 500| 495|
|Optimalizováno pro paměť| 8| 960| 955|
|Optimalizováno pro paměť| 16| 1900| 1895|
|Optimalizováno pro paměť| 32| 1987| 1982|

Pokud připojení překročí limit, může se zobrazit následující chyba:
> FATAL: omlouvám se, příliš mnoho klientů již

> [!IMPORTANT]
> Pro nejlepší zkušenosti, doporučujeme použít připojení sdružovací jako pgBouncer efektivně spravovat připojení.

PostgreSQL připojení, a to i nečinnosti, může zabírat asi 10MB paměti. Vytvoření nových připojení také nějakou dobu trvá. Většina aplikací požadovat mnoho krátkodobé připojení, což komplikuje tuto situaci. Výsledkem je méně prostředků, které jsou k dispozici pro skutečné zatížení, což vede ke snížení výkonu. Sdružovací připojení, které snižuje nečinné připojení a opakovaně používá existující připojení, vám pomůže tomu zabránit. Chcete-li se dozvědět více, navštivte náš [blogový příspěvek](https://techcommunity.microsoft.com/t5/azure-database-for-postgresql/not-all-postgres-connection-pooling-is-equal/ba-p/825717).

## <a name="functional-limitations"></a>Funkční omezení
### <a name="scale-operations"></a>Operace v měřítku
- Dynamické škálování na základní cenové úrovně a z nich není aktuálně podporováno.
- Snížení velikosti úložiště serveru není aktuálně podporováno.

### <a name="server-version-upgrades"></a>Upgrady verzí serveru
- Automatická migrace mezi hlavními verzemi databázového stroje není v současné době podporována. Pokud chcete upgradovat na další hlavní verzi, převzít [výpis a obnovit](./howto-migrate-using-dump-and-restore.md) jej na server, který byl vytvořen s novou verzi motoru.

> Všimněte si, že před PostgreSQL verze [10, PostgreSQL versioning zásady](https://www.postgresql.org/support/versioning/) považovány za _hlavní verzi_ upgrade za zvýšení první _nebo_ druhé číslo (například 9.5 až 9.6 byl považován za _hlavní_ verzi upgrade).
> Od verze 10 je pouze změna prvního čísla považována za hlavní verzi upgradu (například 10.0 až 10.1 je _upgrade dílčí_ verze a 10 až 11 je _hlavní_ verze upgrade).

### <a name="vnet-service-endpoints"></a>Koncové body služeb virtuální sítě
- Podpora koncových bodů služby virtuální sítě je jenom pro servery optimalizované pro obecné účely a paměť.

### <a name="restoring-a-server"></a>Obnovení serveru
- Při použití funkce PITR je nový server vytvořen se stejnými konfiguracemi cenové úrovně jako server, na který je založen.
- Nový server vytvořený během obnovení nemá pravidla brány firewall, která existovala na původním serveru. Pro tento nový server je třeba nastavit pravidla brány firewall samostatně.
- Obnovení odstraněného serveru není podporováno.

### <a name="utf-8-characters-on-windows"></a>Znaky UTF-8 ve Windows
- V některých scénářích znaky UTF-8 nejsou plně podporovány v open source PostgreSQL v systému Windows, což ovlivňuje Azure Database pro PostgreSQL. Další informace naleznete ve vlákně na [#15476 chyb v postgresql-archivu.](https://www.postgresql-archive.org/BUG-15476-Problem-on-show-trgm-with-4-byte-UTF-8-characters-td6056677.html)

## <a name="next-steps"></a>Další kroky
- Informace [o tom, co je k dispozici v jednotlivých cenových úrovních](concepts-pricing-tiers.md)
- Další informace o [podporovaných verzích databáze PostgreSQL](concepts-supported-versions.md)
- Přečtěte [si, jak zálohovat a obnovovat server v Azure Database for PostgreSQL pomocí webu Azure Portal](howto-restore-server-portal.md)
