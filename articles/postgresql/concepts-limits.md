---
title: Omezení v Azure Database for PostgreSQL – jeden server
description: Tento článek popisuje omezení v Azure Database for PostgreSQL jednom serveru, například počet připojení a možnosti modulu úložiště.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/25/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: b9cef4753b6fd324b38d7254139fe288463a0c0c
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123888"
---
# <a name="limits-in-azure-database-for-postgresql---single-server"></a>Omezení v Azure Database for PostgreSQL – jeden server
Následující části popisují kapacitu a funkční omezení v databázové službě. Pokud se chcete dozvědět víc o úrovních prostředků (výpočetních, paměť, úložiště), přečtěte si článek o [cenových úrovních](concepts-pricing-tiers.md) .


## <a name="maximum-connections"></a>Maximální počet připojení
Maximální počet připojení na cenová úroveň a virtuálními jádry jsou následující: 

|**Cenová úroveň**| **počet virtuálních jader:**| **Maximální počet připojení** | **Maximální počet připojení uživatelů** |
|---|---|---|---|
|Basic| 1| 55 | 50|
|Basic| 2| 105 | 100|
|Pro obecné účely| 2| 150| 145|
|Pro obecné účely| 4| 250| 245|
|Pro obecné účely| 8| 480| 475|
|Pro obecné účely| 16| 950| 945|
|Pro obecné účely| 32| 1500| 1495|
|Pro obecné účely| 64| 1900| 1895|
|Paměťově optimalizované| 2| 300| 295|
|Paměťově optimalizované| 4| 500| 495|
|Paměťově optimalizované| 8| 960| 955|
|Paměťově optimalizované| 16| 1900| 1895|
|Paměťově optimalizované| 32| 1987| 1982|

Při připojení překročí limit, může se zobrazit následující chyba:
> Závažná chyba: je nám líto, ale příliš mnoho klientů.

Pro monitorování Azure Database for PostgreSQL serveru vyžaduje systém Azure pět připojení. 

## <a name="functional-limitations"></a>Funkční omezení
### <a name="scale-operations"></a>Operace škálování
- Dynamické škálování do a ze základní cenové úrovně se aktuálně nepodporuje.
- Zmenšení velikosti úložiště serveru se v tuto chvíli nepodporuje.

### <a name="server-version-upgrades"></a>Upgrady verze serveru
- Automatizovaný přenos mezi verzí vyhledávacích strojů hlavní databáze se aktuálně nepodporuje. Pokud chcete upgradovat na novou hlavní verzi, [výpisu a obnovení](./howto-migrate-using-dump-and-restore.md) ho na server, který byl vytvořen v nové verzi modulu.

> Všimněte si, že před PostgreSQL verze 10 se [zásady správy verzí PostgreSQL](https://www.postgresql.org/support/versioning/) považovaly za upgradované na _hlavní verzi_ , aby se zvýšila první _nebo_ druhé číslo (například 9,5 až 9,6 byla považována za upgrade _hlavní_ verze).
> Od verze 10 se jako upgrade hlavní verze považuje jenom změna v prvním čísle (například 10,0 až 10,1 je upgrade _menší_ verze a 10 až 11 je upgrade _hlavní_ verze).

### <a name="vnet-service-endpoints"></a>Koncové body služby virtuální sítě
- Podpora pro koncové body služby virtuální sítě je pouze pro servery pro obecné účely a optimalizovaný pro paměť.

### <a name="restoring-a-server"></a>Obnovení serveru
- Při použití funkce PITR se nový server vytvoří se stejnou konfigurací cenové úrovně jako server, na kterém je založena.
- Nový server vytvořený během obnovy nemá pravidla brány firewall, která existovala na původním serveru. Pravidla brány firewall je třeba nastavit samostatně pro tento nový server.
- Obnovení odstraněné serveru není podporováno.

### <a name="utf-8-characters-on-windows"></a>Znaky UTF-8 ve Windows
- V některých scénářích nejsou znaky UTF-8 v systému Open Source PostgreSQL ve Windows plně podporované, což má vliv na Azure Database for PostgreSQL. Další informace najdete v tématu věnovaném [chybě #15476 v archivu](https://www.postgresql-archive.org/BUG-15476-Problem-on-show-trgm-with-4-byte-UTF-8-characters-td6056677.html) .

## <a name="next-steps"></a>Další kroky
- Informace o [tom, co je k dispozici v každé cenové úrovni](concepts-pricing-tiers.md)
- Informace o [podporovaných verzích databáze PostgreSQL](concepts-supported-versions.md)
- Přečtěte si, [Jak zálohovat a obnovit server v Azure Database for PostgreSQL pomocí Azure Portal](howto-restore-server-portal.md)
