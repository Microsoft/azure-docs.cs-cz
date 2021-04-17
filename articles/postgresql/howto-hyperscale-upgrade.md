---
title: Upgrade skupiny serverů – Citus (-Scale) – Azure Database for PostgreSQL
description: Tento článek popisuje, jak můžete upgradovat PostgreSQL a Citus v Azure Database for PostgreSQL – s měřítkem (Citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 4/5/2021
ms.openlocfilehash: 161204bf02ac36c1f5a3969cf57c61e98560c9b5
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518894"
---
# <a name="upgrade-hyperscale-citus-server-group"></a>Upgradovat skupinu serverů Citus (s rozšířením)

Tyto pokyny popisují, jak upgradovat na novou hlavní verzi PostgreSQL na všech uzlech skupin serverů.

## <a name="test-the-upgrade-first"></a>Nejprve otestujte upgrade.

Upgrade PostgreSQL způsobí větší změny, než kolik jich možná představte, protože [rozšíření databáze](concepts-hyperscale-extensions.md)(Citus) bude také upgradovat, včetně rozšíření Citus.
Důrazně doporučujeme, abyste před upgradem produkčního prostředí otestovali vaši aplikaci s novou verzí PostgreSQL a Citus.

Pohodlný způsob, jak testovat, je vytvořit kopii skupiny serverů pomocí obnovení k určitému [bodu v čase](concepts-hyperscale-backup.md#restore). Upgradujte kopii a otestujte svoji aplikaci. Jakmile všechno ověříte, že vše funguje správně, upgradujte původní skupinu serverů.

## <a name="upgrade-a-server-group-in-the-azure-portal"></a>Upgrade skupiny serverů v Azure Portal

1. V části **Přehled** skupiny serverů Citus () vyberte tlačítko **upgradovat** .
1. Zobrazí se dialogové okno, ve kterém se zobrazuje aktuální verze PostgreSQL a Citus.
   V seznamu **upgrade na** vyberte novou verzi PostgreSQL.
1. Ověřte hodnotu ve **verzi Citus po upgradu** , kterou očekáváte.
   Tato hodnota se změní v závislosti na vybrané verzi PostgreSQL.
1. Pokračujte kliknutím na tlačítko **upgradovat** .

## <a name="next-steps"></a>Další kroky

* Přečtěte si o [podporovaných verzích PostgreSQL](concepts-hyperscale-versions.md).
* Podívejte se [, která rozšíření](concepts-hyperscale-extensions.md) jsou zabalená s každou verzí PostgreSQL ve skupině serverů Citus ().
