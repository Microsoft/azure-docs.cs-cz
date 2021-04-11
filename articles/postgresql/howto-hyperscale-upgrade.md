---
title: Upgrade skupiny serverů – Citus (-Scale) – Azure Database for PostgreSQL
description: Tento článek popisuje, jak můžete upgradovat PostgreSQL a Citus v Azure Database for PostgreSQL – s měřítkem (Citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 4/5/2021
ms.openlocfilehash: 3758e2d458e1a6bd052ac746ac361de033d508e9
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "107023953"
---
# <a name="upgrade-hyperscale-citus-server-group"></a>Upgradovat skupinu serverů Citus (s rozšířením)

Tyto pokyny popisují, jak upgradovat na novou hlavní verzi PostgreSQL na všech uzlech skupin serverů.

## <a name="test-the-upgrade-first"></a>Nejprve otestujte upgrade.

Upgrade PostgreSQL způsobí větší změny, než kolik jich možná představte, protože [rozšíření databáze](concepts-hyperscale-extensions.md)(Citus) bude také upgradovat, včetně rozšíření Citus.
Důrazně doporučujeme, abyste před upgradem produkčního prostředí otestovali vaši aplikaci s novou verzí PostgreSQL a Citus.

Pohodlný způsob, jak testovat, je vytvořit kopii skupiny serverů pomocí obnovení k určitému [bodu v čase](concepts-hyperscale-backup.md#point-in-time-restore-pitr). Upgradujte kopii a otestujte svoji aplikaci. Jakmile všechno ověříte, že vše funguje správně, upgradujte původní skupinu serverů.

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
