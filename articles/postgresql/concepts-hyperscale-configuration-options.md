---
title: Možnosti konfigurace – Hyperscale (Citus) – databáze Azure pro PostgreSQL
description: Možnosti pro skupinu serverů Hyperscale (Citus), včetně výpočetních uzlů, úložiště a oblastí.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 4/6/2020
ms.openlocfilehash: a2c376ec2bd1f03b626c11b0d6a6c3850c9ef8c4
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804584"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-configuration-options"></a>Azure Database for PostgreSQL – možnosti konfigurace hyperškálování (Citus)

## <a name="compute-and-storage"></a>Výpočetní výkon a úložiště
 
Nastavení výpočetních prostředků a úložiště můžete vybrat nezávisle pro pracovní uzly a uzel koordinátora ve skupině serverů Hyperscale (Citus).  Výpočetní prostředky jsou k dispozici jako virtuální jádra, které představují logický procesor základního hardwaru. Velikost úložiště pro zřizování odkazuje na kapacitu, která je k dispozici koordinátorovi a pracovníu uzlům ve skupině serverů Hyperscale (Citus). Úložiště obsahuje databázové soubory, dočasné soubory, protokoly transakcí a protokoly serveru Postgres.
 
|                       | Uzel pracovníka           | Uzel koordinátora      |
|-----------------------|-----------------------|-----------------------|
| Výpočetní výkon, virtuální jádra       | 4, 8, 16, 32, 64      | 4, 8, 16, 32, 64      |
| Paměť na virtuální jádro, GiB | 8                     | 4                     |
| Velikost úložiště, TiB     | 0.5, 1, 2             | 0.5, 1, 2             |
| Typ úložiště          | Pro všeobecné použití (SSD) | Pro všeobecné použití (SSD) |
| IOPS                  | Až 3 IOPS/GiB      | Až 3 IOPS/GiB      |

Celkové množství paměti RAM v jednom uzlu Hyperscale (Citus) je založeno na vybraném počtu virtuálních jader.

| virtuální jádra | Jeden pracovní uzel, GiB RAM | Uzel koordinátora, GiB RAM |
|--------|--------------------------|---------------------------|
| 4      | 32                       | 16                        |
| 8      | 64                       | 32                        |
| 16     | 128                      | 64                        |
| 32     | 256                      | 128                       |
| 64     | 432                      | 256                       |

Celková velikost úložiště, které zřídíte, také definuje kapacitu vstupně-videa, která je k dispozici pro každý uzel pracovníka a koordinátora.

| Velikost úložiště, TiB | Maximální viopy |
|-------------------|--------------|
| 0,5               | 1 536        |
| 1                 | 3 072        |
| 2                 | 6,148        |

Pro celý cluster Hyperscale (Citus) agregované IOPS vypracovat na následující hodnoty:

| Pracovní uzly | 0,5 TiB, celkový IOPS | 1 TiB, celkový IOPS | 2 TiB, celkový IOPS |
|--------------|---------------------|-------------------|-------------------|
| 2            | 3 072               | 6 144             | 12,296            |
| 3            | 4,608               | 9,216             | 18,444            |
| 4            | 6 144               | 12,288            | 24,592            |
| 5            | 7,680               | 15,360            | 30,740            |
| 6            | 9,216               | 18,432            | 36,888            |
| 7            | 10,752              | 21,504            | 43,036            |
| 8            | 12,288              | 24,576            | 49,184            |
| 9            | 13,824              | 27,648            | 55,332            |
| 10           | 15,360              | 30,720            | 61,480            |
| 11           | 16,896              | 33,792            | 67,628            |
| 12           | 18,432              | 36,864            | 73,776            |
| 13           | 19,968              | 39,936            | 79,924            |
| 14           | 21,504              | 43,008            | 86,072            |
| 15           | 23,040              | 46,080            | 92,220            |
| 16           | 24,576              | 49,152            | 98,368            |
| 17           | 26,112              | 52,224            | 104,516           |
| 18           | 27,648              | 55,296            | 110,664           |
| 19           | 29,184              | 58,368            | 116,812           |
| 20           | 30,720              | 61,440            | 122,960           |

## <a name="regions"></a>Oblasti
Skupiny serverů Hyperscale (Citus) jsou dostupné v následujících oblastech Azure:

* Americas:
    * Střední Kanada
    * USA – střed
    * USA – východ
    * USA – východ 2
    * USA – středosever
    * USA – západ 2
* Asie a Tichomoří:
    * Austrálie – východ
    * Japonsko – východ
    * Jižní Korea – střed
    * Jihovýchodní Asie
* Evropě:
    * Severní Evropa
    * Spojené království – jih
    * Západní Evropa

Některé z těchto oblastí nemusí být zpočátku aktivovány ve všech předplatných Azure. Pokud chcete použít oblast z výše uvedeného seznamu a nevidíte ji v předplatném, nebo pokud chcete použít oblast, která není v tomto seznamu, otevřete [žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="pricing"></a>Ceny
Nejaktuálnější informace o cenách najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/postgresql/)služeb .
Chcete-li zobrazit náklady na požadovanou konfiguraci, [portál Azure](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) zobrazuje měsíční náklady na kartě **Konfigurace** na základě možností, které vyberete. Pokud nemáte předplatné Azure, můžete použít cenovou kalkulačku Azure k získání odhadované ceny. Na webu [Cenové kalkulačky Azure](https://azure.microsoft.com/pricing/calculator/) vyberte **Přidat položky**, rozbalte kategorii **Databáze** a zvolte Azure Database **for PostgreSQL – Hyperscale (Citus)** a přizpůsobte si možnosti.
 
## <a name="next-steps"></a>Další kroky
Přečtěte si, jak [na portálu vytvořit skupinu serverů Hyperscale (Citus).](quickstart-create-hyperscale-portal.md)
