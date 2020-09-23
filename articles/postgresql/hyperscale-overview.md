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
ms.openlocfilehash: e62d1bf0e9db5e80193cb0615d0a9d31e3041d63
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90946926"
---
# <a name="what-is-azure-database-for-postgresql---hyperscale-citus"></a>Co je Azure Database for PostgreSQL – Citus (škálování)?

Azure Database for PostgreSQL je služba relačních databází v cloudu Microsoftu vytvořená pro vývojáře. Vychází z komunitní verze open source databázového stroje [PostgreSQL](https://www.postgresql.org/) .

Citus) je možnost nasazení, která vodorovně škáluje dotazy napříč více počítači pomocí horizontálního dělení. Jeho dotazovací modul parallelizes příchozí dotazy SQL napříč těmito servery, aby bylo rychlejší odezvy na velké datové sady. Obsluhuje aplikace, které vyžadují větší rozsah a výkon než jiné možnosti nasazení: všeobecně používané úlohy, které se blíží--100 GB dat.

Citus (škálování na úrovni) nabízí:

- Horizontální škálování napříč několika počítači pomocí horizontálního dělení
- Dotazování paralelního zpracování na těchto serverech pro rychlejší odezvy na velké datové sady
- Vynikající podpora pro víceklientské aplikace, provozní analýzy v reálném čase a transakční úlohy s vysokou propustností

Aplikace vytvořené pro PostgreSQL můžou spouštět distribuované dotazy na úrovni Citus (s standardními [knihovnami připojení](./concepts-connection-libraries.md) a minimálními změnami).

## <a name="next-steps"></a>Další kroky

- Začněte tím, že [vytvoříte svou první](./quickstart-create-hyperscale-portal.md) skupinu serverů Azure Database for PostgreSQL – Citus (škálování).
- Porovnání nákladů a kalkulačky najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/postgresql/) . Citus) nabízí i předplacené slevy za rezervované instance. Podrobnosti najdete na stránkách s [cenami na úrovni](concepts-hyperscale-reserved-pricing.md) služby.
- Určení nejlepší [počáteční velikosti](howto-hyperscale-scaling.md#picking-initial-size) pro skupinu serverů
