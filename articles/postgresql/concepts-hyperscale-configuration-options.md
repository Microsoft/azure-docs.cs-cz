---
title: Azure Database for PostgreSQL – možnosti výkonu Citus (škálovatelná velikost)
description: Možnosti pro skupinu serverů Citus (), včetně výpočetního rozsahu, úložiště a oblastí.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: 2b848827d3d2017e5e787989553c0bf1e26d48e7
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73482628"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-performance-options"></a>Azure Database for PostgreSQL – možnosti výkonu Citus (škálovatelná velikost)

## <a name="compute-and-storage"></a>Výpočetní výkon a úložiště
 
Nastavení výpočtů a úložiště můžete vybrat nezávisle pro pracovní uzly a uzel koordinátora ve skupině serverů Citus (velká škála).  Výpočetní prostředky se poskytují jako virtuální jádra, což představuje logický procesor základního hardwaru. Velikost úložiště pro zřizování odkazuje na kapacitu dostupnou pro koordinátora a pracovní uzly ve skupině serverů Citus (s vaším škálováním). Úložiště zahrnuje soubory databáze, dočasné soubory, transakční protokoly a protokoly serveru Postgres. Celková velikost úložiště, kterou zřizujete, také definuje kapacitu v/v k dispozici pro jednotlivé pracovní procesy a uzly koordinátora.
 
|                       | Pracovní uzel           | Uzel koordinátora      |
|-----------------------|-----------------------|-----------------------|
| COMPUTE, virtuální jádra       | 4, 8, 16, 32, 64      | 4, 8, 16, 32, 64      |
| Paměť na vCore, GiB | 8                     | 4                     |
| Velikost úložiště, TiB     | 0,5, 1, 2             | 0,5, 1, 2             |
| Typ úložiště          | Pro obecné účely (SSD) | Pro obecné účely (SSD) |
| IOPS                  | Až 3 IOPS/GiB      | Až 3 IOPS/GiB      |


## <a name="regions"></a>Oblasti
Skupiny serverů Citus () jsou k dispozici v následujících oblastech Azure:

* Jižní
    * Kanada – střed *
    * USA – východ
    * Východ USA 2
    * Střed USA – sever *
    * Západní USA 2
* Asie a Tichomoří:
    * Austrálie – východ *
    * Jihovýchodní Asie
* Evropským
    * Severní Evropa
    * Velká Británie – jih
    * Západní Evropa

Oblasti s hvězdičkou (\*) ještě nepodporují [vysokou dostupnost](concepts-hyperscale-high-availability.md).

## <a name="pricing"></a>Ceny
Nejaktuálnější informace o cenách najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/postgresql/)služeb.
Chcete-li zobrazit náklady na konfiguraci, kterou požadujete, [Azure Portal](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) na kartě **Konfigurace** na základě možností, které jste vybrali, zobrazí měsíční náklady. Pokud nemáte předplatné Azure, můžete získat odhadovanou cenu pomocí cenové kalkulačky Azure. Na webu [cenové kalkulačky Azure](https://azure.microsoft.com/pricing/calculator/) vyberte **Přidat položky**, rozbalte kategorii **databáze** a zvolte **Azure Database for PostgreSQL – Citus (škálování)** a upravte možnosti.
 
## <a name="next-steps"></a>Další kroky
Přečtěte si, jak na [portálu vytvořit skupinu serverů na úrovni Citus ()](quickstart-create-hyperscale-portal.md).
