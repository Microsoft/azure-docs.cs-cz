---
title: Omezení – Azure Database for PostgreSQL – flexibilní Server
description: Tento článek popisuje omezení Azure Database for PostgreSQL flexibilního serveru, jako je třeba počet připojení a možnosti modulu úložiště.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: f270ac736bb5dbc429dc8659cc88e63d0b51a523
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2020
ms.locfileid: "94366634"
---
# <a name="limits-in-azure-database-for-postgresql---flexible-server"></a>Omezení Azure Database for PostgreSQL – flexibilní Server

> [!IMPORTANT]
> Azure Database for PostgreSQL – flexibilní Server je ve verzi Preview.

Následující části popisují kapacitu a funkční omezení v databázové službě. Pokud se chcete dozvědět víc o úrovních prostředků (výpočetních, paměť, úložiště), přečtěte si článek [výpočetní výkon a úložiště](concepts-compute-storage.md) .

## <a name="maximum-connections"></a>Maximální počet připojení

Níže jsou uvedené maximální počty připojení na cenové úrovni a virtuální jádra. Systém Azure vyžaduje tři připojení pro monitorování Azure Database for PostgreSQLho flexibilního serveru.

| Název SKU             | Virtuální jádra | Velikost paměti | Maximální počet připojení | Maximální počet připojení uživatelů |
|----------------------|--------|-------------|-----------------|----------------------|
| **S shluky**        |        |             |                 |                      |
| B1ms                 | 1      | 2 GiB       | 50              | 47                   |
| B2s                  | 2      | 4 GiB       | 100             | 97                   |
| **Obecné použití**  |        |             |                 |                      |
| D2s_v3               | 2      | 8 GiB       | 214             | 211                  |
| D4s_v3               | 4      | 16 GiB      | 429             | 426                  |
| D8s_v3               | 8      | 32 GiB      | 859             | 856                  |
| D16s_v3              | 16     | 64 GiB      | 1718            | 1715                 |
| D32s_v3              | 32     | 128 GiB     | 3437            | 3434                 |
| D48s_v3              | 48     | 192 GiB     | 5000            | 4997                 |
| D64s_v3              | 64     | 256 GB     | 5000            | 4997                 |
| **Paměťově optimalizovaná** |        |             |                 |                      |
| E2s_v3               | 2      | 16 GiB      | 1718            | 1715                 |
| E4s_v3               | 4      | 32 GiB      | 3437            | 3434                 |
| E8s_v3               | 8      | 64 GiB      | 5000            | 4997                 |
| E16s_v3              | 16     | 128 GiB     | 5000            | 4997                 |
| E32s_v3              | 32     | 256 GB     | 5000            | 4997                 |
| E48s_v3              | 48     | 384 GiB     | 5000            | 4997                 |
| E64s_v3              | 64     | 432 GiB     | 5000            | 4997                 |

Když připojení překročí limit, může se zobrazit následující chyba:
> Závažná chyba: je nám líto, ale příliš mnoho klientů.

> [!IMPORTANT]
> Pro dosažení optimálního prostředí doporučujeme, abyste k efektivní správě připojení používali připojení Pooler jako PgBouncer.

Připojení PostgreSQL, dokonce nečinné, může zabírat přibližně 10 MB paměti. Vytváření nových připojení také trvá déle. Většina aplikací vyžaduje mnoho krátkodobých připojení, což je v této situaci celé. Výsledkem je méně prostředků dostupných pro vaše skutečné zatížení, což vede ke snížení výkonu. Sdružování připojení lze použít ke snížení nečinných připojení a opakovaného použití existujících připojení. Další informace najdete v našem [blogovém příspěvku](https://techcommunity.microsoft.com/t5/azure-database-for-postgresql/not-all-postgres-connection-pooling-is-equal/ba-p/825717).

## <a name="functional-limitations"></a>Funkční omezení

### <a name="scale-operations"></a>Operace škálování

- Změna velikosti úložiště serveru vyžaduje restart serveru.
- Velikost úložiště serveru se dá škálovat jenom o 2x přírůstcích, podrobnosti najdete v tématu [COMPUTE a Storage](concepts-compute-storage.md) .
- Zmenšení velikosti úložiště serveru se v tuto chvíli nepodporuje.

### <a name="server-version-upgrades"></a>Upgrady verze serveru

- Automatizovaná migrace mezi hlavními verzemi databázového stroje není v současnosti podporovaná. Pokud chcete upgradovat na další hlavní verzi, [vystavte výpis a obnovte](../howto-migrate-using-dump-and-restore.md) ho na serveru, který byl vytvořen s novou verzí modulu.

### <a name="networking"></a>Sítě

- Přesun v a z virtuální sítě se v tuto chvíli nepodporuje.
- Kombinování veřejného přístupu s nasazením v rámci virtuální sítě se v tuto chvíli nepodporuje.
- Pravidla brány firewall se ve virtuální síti nepodporují. místo toho je možné použít skupiny zabezpečení sítě.
- Databázové servery veřejného přístupu se můžou připojovat k veřejnému Internetu, například prostřednictvím `postgres_fdw` , a tento přístup se nedá omezit. Servery založené na virtuální síti můžou mít omezený odchozí přístup pomocí skupin zabezpečení sítě.

### <a name="high-availability-ha"></a>Vysoká dostupnost (HA)

- Zone-Redundant HA se v tuto chvíli u serverů s vysokou dostupností nepodporuje.
- IP adresa databázového serveru se změní, když dojde k převzetí služeb při selhání serveru do úsporného režimu HA. Ujistěte se, že místo IP adresy serveru použijete záznam DNS.
- Pokud je logická replikace nakonfigurovaná pomocí flexibilního serveru s nakonfigurovaným HA, v případě převzetí služeb při selhání na pohotovostní server se logické replikační sloty nekopírují do pohotovostního serveru. 
- Další podrobnosti o redundantním HA v zóně, včetně omezení, najdete na stránce s [dokumentací koncept – ha](concepts-high-availability.md) .

### <a name="availability-zones"></a>Zóny dostupnosti

- Ruční přesun serverů do jiné zóny dostupnosti se v tuto chvíli nepodporuje.
- Zóna dostupnosti pohotovostního serveru HA nemůže být nakonfigurovaná ručně.

### <a name="postgres-engine-extensions-and-pgbouncer"></a>Modul Postgres, rozšíření a PgBouncer

- Postgres 10 a starší se nepodporují. Pokud požadujete starší verze Postgres, doporučujeme použít možnost [jeden server](../overview-single-server.md) .
- Podpora rozšíření je aktuálně omezená na rozšíření Postgres `contrib` .
- Integrované Pooler připojení PgBouncer nejsou aktuálně k dispozici pro databázové servery v rámci virtuální sítě nebo pro servery s rozstupným rozšířením.

### <a name="stopstart-operation"></a>Operace zastavení/spuštění

- Server se nedá zastavit déle než sedm dní.

### <a name="scheduled-maintenance"></a>Plánovaná údržba

- Změna časového intervalu údržby kratšího než pět dní před dokončením plánovaného upgradu nebude mít vliv na tento upgrade. Změny se projeví pouze při další plánované údržbě.

### <a name="backing-up-a-server"></a>Zálohování serveru

- Zálohy jsou spravovány systémem, momentálně neexistují způsob, jak tyto zálohy spouštět ručně. `pg_dump`Místo toho doporučujeme použít.
- Zálohy jsou vždycky úplné zálohy založené na snímcích (ne rozdílové zálohy), což může vést k vyššímu využití úložiště záloh. Všimněte si, že protokoly transakcí (Write-WAL) jsou oddělené od úplného nebo rozdílového zálohování a jsou archivovány průběžně.

### <a name="restoring-a-server"></a>Obnovení serveru

- Při použití funkce obnovení bodu v čase je nový server vytvořen se stejnou konfigurací výpočtů a úložiště jako server, na kterém je založena.
- Databázové servery založené na virtuální síti se při obnovení ze zálohy obnoví do stejné virtuální sítě.
- Nový server vytvořený během obnovy nemá pravidla brány firewall, která existovala na původním serveru. Pravidla brány firewall je třeba vytvořit samostatně pro nový server.
- Obnovení odstraněného serveru se nepodporuje.
- Obnovení mezi oblastmi není podporováno.

### <a name="other-features"></a>Další funkce

* Ověřování Azure AD ještě není podporované. Pokud požadujete ověřování Azure AD, doporučujeme použít možnost [jeden server](../overview-single-server.md) .
* Repliky pro čtení se zatím nepodporují. Pokud požadujete repliky pro čtení, doporučujeme použít možnost [jeden server](../overview-single-server.md) .


## <a name="next-steps"></a>Další kroky

- Seznamte [se s tím, co je k dispozici pro výpočetní a úložné možnosti](concepts-compute-storage.md)
- Informace o [podporovaných verzích databáze PostgreSQL](concepts-supported-versions.md)
- Přečtěte si, [Jak zálohovat a obnovit server v Azure Database for PostgreSQL pomocí Azure Portal](how-to-restore-server-portal.md)
