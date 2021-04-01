---
title: Možnosti konfigurace – Citus (Velká měřítko) – Azure Database for PostgreSQL
description: Možnosti pro skupinu serverů Citus (), včetně výpočetního rozsahu, úložiště a oblastí.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 1/12/2021
ms.openlocfilehash: 48537483501165d4a978afdbd05560613170d187
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98165607"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-configuration-options"></a>Azure Database for PostgreSQL – možnosti konfigurace Citus (AutoScale)

## <a name="compute-and-storage"></a>Výpočetní výkon a úložiště
 
Nastavení výpočtů a úložiště můžete vybrat nezávisle pro pracovní uzly a uzel koordinátora ve skupině serverů Citus (velká škála).  Výpočetní prostředky se poskytují jako virtuální jádra, což představuje logický procesor základního hardwaru. Velikost úložiště pro zřizování odkazuje na kapacitu dostupnou pro koordinátora a pracovní uzly ve skupině serverů Citus (s vaším škálováním). Úložiště zahrnuje soubory databáze, dočasné soubory, transakční protokoly a protokoly serveru Postgres.
 
| Prostředek              | Pracovní uzel           | Uzel koordinátora      |
|-----------------------|-----------------------|-----------------------|
| COMPUTE, virtuální jádra       | 4, 8, 16, 32, 64      | 4, 8, 16, 32, 64      |
| Paměť na vCore, GiB | 8                     | 4                     |
| Velikost úložiště, TiB     | 0,5, 1, 2             | 0,5, 1, 2             |
| Typ úložiště          | Pro obecné účely (SSD) | Pro obecné účely (SSD) |
| IOPS                  | Až 3 IOPS/GiB      | Až 3 IOPS/GiB      |

Celková velikost paměti RAM v uzlu s jedním škálováním (Citus) je založena na zvoleném počtu virtuální jádra.

| Virtuální jádra | Jeden pracovní uzel, GiB RAM | Uzel koordinátora, GiB RAM |
|--------|--------------------------|---------------------------|
| 4      | 32                       | 16                        |
| 8      | 64                       | 32                        |
| 16     | 128                      | 64                        |
| 32     | 256                      | 128                       |
| 64     | 432                      | 256                       |

Celková velikost úložiště, kterou zřizujete, také definuje kapacitu v/v k dispozici pro jednotlivé pracovní procesy a uzly koordinátora.

| Velikost úložiště, TiB | Maximální IOPS |
|-------------------|--------------|
| 0,5               | 1 536        |
| 1                 | 3 072        |
| 2                 | 6 148        |

U celého clusteru Citus () se agregované IOPS doplňují s následujícími hodnotami:

| Pracovní uzly | 0,5 TiB, celkem IOPS | 1 TiB, celkem IOPS | 2 TiB, celkem IOPS |
|--------------|---------------------|-------------------|-------------------|
| 2            | 3 072               | 6 144             | 12 296            |
| 3            | 4 608               | 9 216             | 18 444            |
| 4            | 6 144               | 12 288            | 24 592            |
| 5            | 7 680               | 15 360            | 30 740            |
| 6            | 9 216               | 18 432            | 36 888            |
| 7            | 10 752              | 21 504            | 43 036            |
| 8            | 12 288              | 24 576            | 49 184            |
| 9            | 13 824              | 27 648            | 55 332            |
| 10           | 15 360              | 30 720            | 61 480            |
| 11           | 16 896              | 33 792            | 67 628            |
| 12           | 18 432              | 36 864            | 73 776            |
| 13           | 19 968              | 39 936            | 79 924            |
| 14           | 21 504              | 43 008            | 86 072            |
| 15           | 23 040              | 46 080            | 92 220            |
| 16           | 24 576              | 49 152            | 98 368            |
| 17           | 26 112              | 52 224            | 104 516           |
| 18           | 27 648              | 55 296            | 110 664           |
| 19           | 29 184              | 58 368            | 116 812           |
| 20           | 30 720              | 61 440            | 122 960           |

## <a name="regions"></a>Oblasti
Skupiny serverů Citus () jsou k dispozici v následujících oblastech Azure:

* Jižní
    * Střední Kanada
    * Střední USA
    * East US
    * USA – východ 2
    * USA – středosever
    * Západní USA 2
* Asie a Tichomoří:
    * Austrálie – východ
    * Japonsko – východ
    * Jižní Korea – střed
    * Southeast Asia
* Evropským
    * Severní Evropa
    * Spojené království – jih
    * West Europe

Některé z těchto oblastí se nemusí zpočátku aktivovat ve všech předplatných Azure. Pokud chcete použít oblast ze seznamu výše a nevidíte ji v předplatném, nebo pokud chcete použít oblast, která není v tomto seznamu, otevřete [žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="limits-and-limitations"></a>Omezení

Následující část popisuje kapacitu a funkční omezení služby Citus (The scaleing).

### <a name="maximum-connections"></a>Maximální počet připojení

Každé připojení PostgreSQL (dokonce nečinné) využívá alespoň 10 MB paměti, takže je důležité omezit souběžná připojení. Tady jsou limity, které jsme zvolili pro udržení uzlů v pořádku:

* Uzel koordinátora
   * Maximální počet připojení: 300
   * Maximální počet uživatelských připojení: 297
* Pracovní uzel
   * Maximální počet připojení: 600
   * Maximální počet uživatelských připojení: 597

Pokusy o připojení nad rámec těchto limitů selžou a dojde k chybě. Systém vyhrazuje tři připojení pro uzly monitorování, což je důvod, proč jsou k dispozici tři méně připojení pro uživatelské dotazy než celkem připojení.

Vytváření nových připojení trvá déle. Který funguje u většiny aplikací, které vyžadují mnoho krátkodobých připojení. Doporučujeme použít Pooler připojení, aby se snížily nečinné transakce a znovu opakovaně používaly existující připojení. Další informace najdete v našem [blogovém příspěvku](https://techcommunity.microsoft.com/t5/azure-database-for-postgresql/not-all-postgres-connection-pooling-is-equal/ba-p/825717).

### <a name="storage-scaling"></a>Škálování úložiště

Úložiště v koordinátoru a uzlech pracovních procesů je možné škálovat nahoru (vyšší), ale nedá se škálovat dolů (zmenšeno).

### <a name="storage-size"></a>Velikost úložiště

V koordinátorech a uzlech pracovních procesů je podporováno až 2 TiB úložiště. V části dostupné možnosti úložiště a výpočet IOPS [výše](#compute-and-storage) najdete pro velikost uzlů a clusterů.

### <a name="database-creation"></a>Vytvoření databáze

Azure Portal poskytuje přihlašovací údaje pro připojení k přesně jedné databázi na skupinu serverů Citus (databáze), `citus` databázi. Vytvoření jiné databáze není aktuálně povoleno a příkaz CREATE DATABASE selže a dojde k chybě.

## <a name="pricing"></a>Ceny
Nejaktuálnější informace o cenách najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/postgresql/)služeb.
Chcete-li zobrazit náklady na konfiguraci, kterou požadujete, [Azure Portal](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) na kartě **Konfigurace** na základě možností, které jste vybrali, zobrazí měsíční náklady. Pokud nemáte předplatné Azure, můžete získat odhadovanou cenu pomocí cenové kalkulačky Azure. Na webu [cenové kalkulačky Azure](https://azure.microsoft.com/pricing/calculator/) vyberte **Přidat položky**, rozbalte kategorii **databáze** a zvolte **Azure Database for PostgreSQL – Citus (škálování)** a upravte možnosti.
 
## <a name="next-steps"></a>Další kroky
Přečtěte si, jak na [portálu vytvořit skupinu serverů na úrovni Citus ()](quickstart-create-hyperscale-portal.md).
