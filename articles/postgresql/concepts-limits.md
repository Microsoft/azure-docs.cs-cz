---
title: Omezení – Azure Database for PostgreSQL – jeden server
description: Tento článek popisuje omezení v Azure Database for PostgreSQL jednom serveru, například počet připojení a možnosti modulu úložiště.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/28/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 7cee2be3c1d7a97b87560873d6cef07ec361ee99
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105605174"
---
# <a name="limits-in-azure-database-for-postgresql---single-server"></a>Omezení v Azure Database for PostgreSQL – jeden server
Následující části popisují kapacitu a funkční omezení v databázové službě. Pokud se chcete dozvědět víc o úrovních prostředků (výpočetních, paměť, úložiště), přečtěte si článek o [cenových úrovních](concepts-pricing-tiers.md) .


## <a name="maximum-connections"></a>Maximální počet připojení
Níže jsou uvedené maximální počty připojení na cenové úrovni a virtuální jádra. Pro monitorování Azure Database for PostgreSQL serveru vyžaduje systém Azure pět připojení. 

|**Cenová úroveň**| **vCore (celkem)**| **Maximální počet připojení** | **Maximální počet připojení uživatelů** |
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

Když připojení překročí limit, může se zobrazit následující chyba:
> Závažná chyba: je nám líto, ale příliš mnoho klientů.

> [!IMPORTANT]
> Pro dosažení optimálního prostředí doporučujeme, abyste k efektivní správě připojení používali připojení Pooler jako pgBouncer.

Připojení PostgreSQL, dokonce nečinné, může zabírat přibližně 10 MB paměti. Vytváření nových připojení také trvá déle. Většina aplikací vyžaduje mnoho krátkodobých připojení, což je v této situaci celé. Výsledkem je méně prostředků dostupných pro vaše skutečné zatížení, což vede ke snížení výkonu. Připojení Pooler, které zkracuje nečinné připojení a znovu používá existující připojení, jim pomůže vyhnout se. Další informace najdete v našem [blogovém příspěvku](https://techcommunity.microsoft.com/t5/azure-database-for-postgresql/not-all-postgres-connection-pooling-is-equal/ba-p/825717).

## <a name="functional-limitations"></a>Funkční omezení
### <a name="scale-operations"></a>Operace škálování
- Dynamické škálování na cenové úrovně Basic a z se v tuto chvíli nepodporuje.
- Zmenšení velikosti úložiště serveru se v tuto chvíli nepodporuje.

### <a name="server-version-upgrades"></a>Upgrady verze serveru
- Automatizovaná migrace mezi hlavními verzemi databázového stroje není v současnosti podporovaná. Pokud chcete upgradovat na další hlavní verzi, [vystavte výpis a obnovte](./howto-migrate-using-dump-and-restore.md) ho na serveru, který byl vytvořen s novou verzí modulu.

> Všimněte si, že před PostgreSQL verze 10 se [zásady správy verzí PostgreSQL](https://www.postgresql.org/support/versioning/) považovaly za upgradované na _hlavní verzi_ , aby se zvýšila první _nebo_ druhé číslo (například 9,5 až 9,6 byla považována za upgrade _hlavní_ verze).
> Od verze 10 se jako upgrade hlavní verze považuje jenom změna v prvním čísle (například 10,0 až 10,1 je upgrade _menší_ verze a 10 až 11 je upgrade _hlavní_ verze).

### <a name="vnet-service-endpoints"></a>Koncové body služeb virtuální sítě
- Podpora koncových bodů služby virtuální sítě je určená jenom pro Pro obecné účely a paměťově optimalizované servery.

### <a name="restoring-a-server"></a>Obnovení serveru
- Při použití funkce PITR se nový server vytvoří se stejnou konfigurací cenové úrovně jako server, na kterém je založena.
- Nový server vytvořený během obnovy nemá pravidla brány firewall, která existovala na původním serveru. Pravidla brány firewall je třeba nastavit samostatně pro tento nový server.
- Obnovení odstraněného serveru se nepodporuje.

### <a name="utf-8-characters-on-windows"></a>Znaky UTF-8 ve Windows
- V některých scénářích nejsou znaky UTF-8 v systému Open Source PostgreSQL ve Windows plně podporované, což má vliv na Azure Database for PostgreSQL. Další informace najdete v tématu věnovaném [chybě #15476 v archivu](https://www.postgresql-archive.org/BUG-15476-Problem-on-show-trgm-with-4-byte-UTF-8-characters-td6056677.html) .

### <a name="gss-error"></a>Chyba služby GSS
Pokud se vám zobrazí chyba související s **GSS**, pravděpodobně používáte novější verzi klienta nebo ovladače, kterou jednoúčelový server Azure Postgres ještě plně nepodporuje. Tato chyba se týká [ovladače JDBC verze 42.2.15 a 42.2.16](https://github.com/pgjdbc/pgjdbc/issues/1868).
   - Do konce listopadu plánujeme dokončit aktualizaci. Mezitím zvažte použití funkční verze ovladače.
   - Nebo zvažte zakázání žádosti služby GSS.  Použijte parametr připojení, jako je `gssEncMode=disable`.

### <a name="storage-size-reduction"></a>Omezení velikosti úložiště
Velikost úložiště nejde snížit. Je nutné vytvořit nový server s požadovanou velikostí úložiště, provést ruční [Výpis paměti a obnovení](./howto-migrate-using-dump-and-restore.md) a migrovat databáze na nový server.

## <a name="next-steps"></a>Další kroky
- Informace o [tom, co je k dispozici v každé cenové úrovni](concepts-pricing-tiers.md)
- Informace o [podporovaných verzích databáze PostgreSQL](concepts-supported-versions.md)
- Přečtěte si, [Jak zálohovat a obnovit server v Azure Database for PostgreSQL pomocí Azure Portal](howto-restore-server-portal.md)
