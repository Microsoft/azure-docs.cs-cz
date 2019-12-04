---
title: Omezení – Azure Database for PostgreSQL – jeden server
description: Tento článek popisuje omezení v Azure Database for PostgreSQL jednom serveru, například počet připojení a možnosti modulu úložiště.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/25/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: d74206ebdf35a8f5b353553cb89e954cb2313611
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74768533"
---
# <a name="limits-in-azure-database-for-postgresql---single-server"></a>Omezení v Azure Database for PostgreSQL – jeden server
Následující části popisují kapacitu a funkční omezení v databázové službě. Pokud se chcete dozvědět víc o úrovních prostředků (výpočetních, paměť, úložiště), přečtěte si článek o [cenových úrovních](concepts-pricing-tiers.md) .


## <a name="maximum-connections"></a>Maximální počet připojení
Maximální počet připojení na cenové úrovni a virtuální jádra je následující: 

|**Cenová úroveň**| **vCore (celkem)**| **Maximální počet připojení** | **Maximální počet připojení uživatelů** |
|---|---|---|---|
|Úroveň Basic| 1\. místo| 55 | 50|
|Úroveň Basic| 2| 105 | 100|
|Obecné použití| 2| 150| 145|
|Obecné použití| 4| 250| 245|
|Obecné použití| 8| 480| 475|
|Obecné použití| 16| 950| 945|
|Obecné použití| 32| 1 500| 1495|
|Obecné použití| 64| 1900| 1895|
|Paměťově optimalizované| 2| 300| 295|
|Paměťově optimalizované| 4| 500| 495|
|Paměťově optimalizované| 8| 960| 955|
|Paměťově optimalizované| 16| 1900| 1895|
|Paměťově optimalizované| 32| 1987| 1982|

Když připojení překročí limit, může se zobrazit následující chyba:
> Závažná chyba: je nám líto, ale příliš mnoho klientů.

Pro monitorování Azure Database for PostgreSQL serveru vyžaduje systém Azure pět připojení. 

## <a name="functional-limitations"></a>Funkční omezení
### <a name="scale-operations"></a>Operace škálování
- Dynamické škálování na cenové úrovně Basic a z se v tuto chvíli nepodporuje.
- Zmenšení velikosti úložiště serveru se v tuto chvíli nepodporuje.

### <a name="server-version-upgrades"></a>Upgrady verze serveru
- Automatizovaná migrace mezi hlavními verzemi databázového stroje není v současnosti podporovaná. Pokud chcete upgradovat na další hlavní verzi, [vystavte výpis a obnovte](./howto-migrate-using-dump-and-restore.md) ho na serveru, který byl vytvořen s novou verzí modulu.

> Všimněte si, že před PostgreSQL verze 10 se [zásady správy verzí PostgreSQL](https://www.postgresql.org/support/versioning/) považovaly za upgradované na _hlavní verzi_ , aby se zvýšila první _nebo_ druhé číslo (například 9,5 až 9,6 byla považována za upgrade _hlavní_ verze).
> Od verze 10 se jako upgrade hlavní verze považuje jenom změna v prvním čísle (například 10,0 až 10,1 je upgrade _menší_ verze a 10 až 11 je upgrade _hlavní_ verze).

### <a name="vnet-service-endpoints"></a>Koncové body služby virtuální sítě
- Podpora koncových bodů služby virtuální sítě je určená jenom pro Pro obecné účely a paměťově optimalizované servery.

### <a name="restoring-a-server"></a>Obnovení serveru
- Při použití funkce PITR se nový server vytvoří se stejnou konfigurací cenové úrovně jako server, na kterém je založena.
- Nový server vytvořený během obnovy nemá pravidla brány firewall, která existovala na původním serveru. Pravidla brány firewall je třeba nastavit samostatně pro tento nový server.
- Obnovení odstraněného serveru se nepodporuje.

### <a name="utf-8-characters-on-windows"></a>Znaky UTF-8 ve Windows
- V některých scénářích nejsou znaky UTF-8 v systému Open Source PostgreSQL ve Windows plně podporované, což má vliv na Azure Database for PostgreSQL. Další informace najdete v tématu věnovaném [chybě #15476 v archivu](https://www.postgresql-archive.org/BUG-15476-Problem-on-show-trgm-with-4-byte-UTF-8-characters-td6056677.html) .

## <a name="next-steps"></a>Další kroky
- Informace o [tom, co je k dispozici v každé cenové úrovni](concepts-pricing-tiers.md)
- Informace o [podporovaných verzích databáze PostgreSQL](concepts-supported-versions.md)
- Přečtěte si, [Jak zálohovat a obnovit server v Azure Database for PostgreSQL pomocí Azure Portal](howto-restore-server-portal.md)
