---
title: Počáteční velikost skupiny serverů – Citus (velký rozsah) – Azure Database for PostgreSQL
description: Vyberte správnou počáteční velikost pro váš případ použití.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 11/17/2020
ms.openlocfilehash: 7e68e9f8caad7d7e4bc44bc4e1e55150a78b4a98
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "95026399"
---
# <a name="pick-initial-size-for-hyperscale-citus-server-group"></a>Vybrat počáteční velikost pro skupinu serverů Citus (velký rozsah)

Je [možné snadno změnit](howto-hyperscale-scale-grow.md)velikost skupiny serverů, jejich počet uzlů i jejich kapacitu hardwaru. Pořád ale musíte zvolit počáteční velikost pro novou skupinu serverů. Zde jsou některé tipy pro rozumnou volbu.

## <a name="multi-tenant-saas-use-case"></a>Multi-tenant SaaS – případ použití

Při migraci do škálování (Citus) z existující instance databáze PostgreSQL s jedním uzlem vyberte cluster, ve kterém se celkový počet pracovních virtuální jádra a paměti RAM rovná původní instanci. V takových scénářích jsme viděli 3x vylepšení výkonu, protože horizontálního dělení vylepšuje využití prostředků a povoluje menší indexy atd.

Jediným rozhodnutím je vCore Count. Přidělení paměti RAM je aktuálně určeno na základě počtu vCore, jak je popsáno na stránce [Možnosti konfigurace Citus ()](concepts-hyperscale-configuration-options.md) .
Uzel koordinátora nevyžaduje jako pracovní procesy tolik paměti RAM, ale neexistuje způsob, jak zvolit RAM a virtuální jádra nezávisle.

## <a name="real-time-analytics-use-case"></a>Případ použití analýzy v reálném čase

Total virtuální jádra: při práci s daty v paměti RAM můžete očekávat lineární vylepšení výkonu na úrovni Citus (s poměrem k počtu jader pracovních procesů). Pro určení správného počtu virtuální jádra pro vaše potřeby zvažte aktuální latenci pro dotazy v databázi s jedním uzlem a požadovanou latenci v rámci škálování (Citus). Aktuální latenci vydělte požadovanou latencí a výsledek zaokrouhlete.

Pracovní paměť RAM: Ideální by bylo zajistit dostatek paměti, aby se do ní vešla pracovní sady. Typ dotazů, které vaše aplikace používá, ovlivňuje požadavky na paměť. Pokud chcete zjistit, kolik paměti vyžaduje, můžete spustit příkaz vysvětlit analyzovat na dotazu. Pamatujte na to, že virtuální jádra a paměť RAM se škálují společně, jak je popsáno v článku [Možnosti konfigurace pro Citus (AutoScale)](concepts-hyperscale-configuration-options.md) .

## <a name="next-steps"></a>Další kroky

- [Škálování skupiny serverů](howto-hyperscale-scale-grow.md)
- Přečtěte si další informace o [možnostech výkonu](concepts-hyperscale-configuration-options.md)skupiny serverů.
