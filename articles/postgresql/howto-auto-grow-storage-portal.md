---
title: Automatické zvětšování úložiště-Azure Portal-Azure Database for PostgreSQL-Single server
description: Tento článek popisuje, jak můžete pomocí Azure Portal v Azure Database for PostgreSQL jednom serveru nakonfigurovat automatické zvětšování úložiště.
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: how-to
ms.date: 5/29/2019
ms.openlocfilehash: d49047e9b3a65da90f4a5ac9e4b3cb1e32bd59a7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92489978"
---
# <a name="auto-grow-storage-using-the-azure-portal-in-azure-database-for-postgresql---single-server"></a>Automatické zvětšení úložiště pomocí Azure Portal v Azure Database for PostgreSQL-jednom serveru
Tento článek popisuje, jak můžete nakonfigurovat úložiště serveru Azure Database for PostgreSQL pro růst, aniž by to ovlivnilo zatížení.

Když server dosáhne přiděleného limitu úložiště, je server označený jen pro čtení. Pokud ale povolíte automatické zvětšování úložiště, zvýší se úložiště serveru, aby se vešlo na rostoucí data. U serverů s úložištěm menším než 100 GB zřízené úložiště se velikost zřízeného úložiště zvyšuje o 5 GB, jakmile bude volné úložiště nižší než 1 GB nebo 10% zřízené úložiště. U serverů s více než 100 GB zřízeného úložiště se velikost zřízeného úložiště zvyšuje o 5%, pokud je volný prostor úložiště pod 5% velikosti zřízeného úložiště. Maximální limity úložiště, jak je uvedeno [zde](./concepts-pricing-tiers.md#storage) , platí.

## <a name="prerequisites"></a>Požadavky
K dokončení tohoto průvodce budete potřebovat:
- [Server Azure Database for PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="enable-storage-auto-grow"></a>Povolit automatické zvětšování úložiště 

Pomocí těchto kroků nastavíte automatické zvětšování úložiště serveru PostgreSQL:

1. V [Azure Portal](https://portal.azure.com/)vyberte svůj existující Azure Database for PostgreSQL Server.

2. Na stránce server PostgreSQL v části **Nastavení** klikněte na **cenová úroveň** a otevřete stránku cenová úroveň.

3. V části **Automatické zvětšování** vyberte **Ano** , pokud chcete povolit automatické zvětšování úložiště.

    :::image type="content" source="./media/howto-auto-grow-storage-portal/3-auto-grow.png" alt-text="Azure Database for PostgreSQL – Settings_Pricing_tier – automatické zvětšování":::

4. Kliknutím na tlačítko **OK** uložte změny.

5. Oznámení potvrdí, že automatické zvětšování bylo úspěšně povoleno.

    :::image type="content" source="./media/howto-auto-grow-storage-portal/5-auto-grow-successful.png" alt-text="Úspěch Azure Database for PostgreSQL – automatické zvětšování":::

## <a name="next-steps"></a>Další kroky

Přečtěte si informace o [tom, jak vytvářet výstrahy na metrikách](howto-alert-on-metric.md).