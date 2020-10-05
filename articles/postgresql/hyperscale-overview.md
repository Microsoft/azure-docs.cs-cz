---
title: Přehled Azure Database for PostgreSQL – Citus (škálování)
description: Poskytuje přehled možností nasazení Citus ().
author: jonels-msft
ms.author: jonels
ms.custom: mvc
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: overview
ms.date: 09/01/2020
ms.openlocfilehash: 90d3cb106fa93649f7d6dda5ab5755061d118f66
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91268379"
---
# <a name="what-is-azure-database-for-postgresql---hyperscale-citus"></a>Co je Azure Database for PostgreSQL – Citus (škálování)?

Azure Database for PostgreSQL je služba relačních databází v cloudu Microsoftu vytvořená pro vývojáře. Vychází z komunitní verze open source databázového stroje [PostgreSQL](https://www.postgresql.org/) .

Citus) je možnost nasazení, která vodorovně škáluje dotazy napříč více počítači pomocí horizontálního dělení. Její dotazovací modul paralelizuje příchozí dotazy SQL na více serverech, aby byla u velkých datových sad zajištěná rychlejší odezva. Obsluhuje aplikace, které vyžadují větší rozsah a výkon než jiné možnosti nasazení: všeobecně používané úlohy, které se blíží--100 GB dat.

Citus (škálování na úrovni) nabízí:

- Horizontální škálování do více počítačů pomocí shardování
- Paralelizaci dotazů na více serverech za účelem rychlejší odezvy u velkých datových sad
- Vynikající podpora pro víceklientské aplikace, provozní analýzy v reálném čase a transakční úlohy s vysokou propustností

Aplikace vytvořené pro PostgreSQL můžou spouštět distribuované dotazy na úrovni Citus (s standardními [knihovnami připojení](./concepts-connection-libraries.md) a minimálními změnami).

## <a name="next-steps"></a>Další kroky

- Začněte tím, že [vytvoříte svou první](./quickstart-create-hyperscale-portal.md) skupinu serverů Azure Database for PostgreSQL – Citus (škálování).
- Porovnání nákladů a kalkulačky najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/postgresql/) . Citus) nabízí také předplacené slevy za rezervované instance. Podrobnosti najdete na stránkách s [cenami na úrovni služby (Citus)](concepts-hyperscale-reserved-pricing.md) .
- Určení nejlepší [počáteční velikosti](howto-hyperscale-scaling.md#picking-initial-size) pro skupinu serverů
