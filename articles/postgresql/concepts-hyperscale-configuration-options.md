---
title: Možnosti výkonu – Hyperscale (Citus) – databáze Azure pro PostgreSQL
description: Možnosti pro skupinu serverů Hyperscale (Citus), včetně výpočetních uzlů, úložiště a oblastí.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 2/18/2020
ms.openlocfilehash: 1c9b4b1099bda69764aa7a1a5a984a6316e1047d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77462407"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-performance-options"></a>Azure Database for PostgreSQL – možnosti výkonu hyperškálování (Citus)

## <a name="compute-and-storage"></a>Výpočetní výkon a úložiště
 
Nastavení výpočetních prostředků a úložiště můžete vybrat nezávisle pro pracovní uzly a uzel koordinátora ve skupině serverů Hyperscale (Citus).  Výpočetní prostředky jsou k dispozici jako virtuální jádra, které představují logický procesor základního hardwaru. Velikost úložiště pro zřizování odkazuje na kapacitu, která je k dispozici koordinátorovi a pracovníu uzlům ve skupině serverů Hyperscale (Citus). Úložiště obsahuje databázové soubory, dočasné soubory, protokoly transakcí a protokoly serveru Postgres. Celková velikost úložiště, které zřídíte, také definuje kapacitu vstupně-videa, která je k dispozici pro každý uzel pracovníka a koordinátora.
 
|                       | Uzel pracovníka           | Uzel koordinátora      |
|-----------------------|-----------------------|-----------------------|
| Výpočetní výkon, virtuální jádra       | 4, 8, 16, 32, 64      | 4, 8, 16, 32, 64      |
| Paměť na virtuální jádro, GiB | 8                     | 4                     |
| Velikost úložiště, TiB     | 0.5, 1, 2             | 0.5, 1, 2             |
| Typ úložiště          | Pro všeobecné použití (SSD) | Pro všeobecné použití (SSD) |
| IOPS                  | Až 3 IOPS/GiB      | Až 3 IOPS/GiB      |


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
