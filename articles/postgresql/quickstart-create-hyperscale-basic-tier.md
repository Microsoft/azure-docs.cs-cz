---
title: 'Rychlý Start: Vytvoření skupiny serverů úrovně Basic – Citus (Velká vrstva) – Azure Database for PostgreSQL'
description: Začněte s úrovní Basic Azure Database for PostgreSQL Citus (škálování na úrovni Basic).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: quickstart
ms.date: 04/07/2021
ms.openlocfilehash: 07ace217e5299662a1c3145a225abc25f4f1f337
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "107024068"
---
# <a name="create-a-hyperscale-citus-basic-tier-server-group-in-the-azure-portal"></a>Vytvořte Citus (Basic) skupinu serverů vrstvy na úrovni Basic v Azure Portal

Azure Database for PostgreSQL – Citus) je spravovaná služba, která slouží ke spouštění, správě a škálování vysoce dostupných databází PostgreSQL v cloudu. Jeho [Základní vrstva](concepts-hyperscale-tiers.md) je pohodlný způsob nasazení pro počáteční vývoj a testování.

V tomto rychlém startu se dozvíte, jak pomocí Azure Portal vytvořit skupinu serverů základní vrstvy (Citus) na úrovni Basic. Zřídíte skupinu serverů a ověříte, že se k ní můžete připojit a spustit dotazy.

> [!IMPORTANT]
> Úroveň Basic úrovně Citus () je aktuálně ve verzi Preview.  Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
>
> Úplný seznam dalších nových funkcí najdete v části [funkce ve verzi Preview pro Citus (škálování)](hyperscale-preview-features.md).

[!INCLUDE [azure-postgresql-hyperscale-create-basic-tier](../../includes/azure-postgresql-hyperscale-create-basic-tier.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se dozvěděli, jak zřídit skupinu serverů (Citus). K němu jste se připojili pomocí psql, vytvořili schéma a distribuovaná data.

- Postupujte podle kurzu pro [vytváření škálovatelných víceklientské aplikací](./tutorial-design-database-hyperscale-multi-tenant.md) .
- Určení nejlepší [počáteční velikosti](howto-hyperscale-scale-initial.md) pro skupinu serverů
