---
title: Automatické zvětšování úložiště – Azure Portal-Azure Database for MariaDB
description: Tento článek popisuje, jak můžete povolit automatické zvětšování úložiště pro Azure Database for MariaDB pomocí Azure Portal
author: rothja
ms.author: jroth
ms.service: mariadb
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: c310d55db0b30a30d3964df622f7bb112f4e7552
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "106552134"
---
# <a name="auto-grow-storage-in-azure-database-for-mariadb-using-the-azure-portal"></a>Automatické zvětšení úložiště v Azure Database for MariaDB pomocí Azure Portal
Tento článek popisuje, jak můžete nakonfigurovat úložiště serveru Azure Database for MariaDB pro růst, aniž by to ovlivnilo zatížení.

Když server dosáhne přiděleného limitu úložiště, je server označený jen pro čtení. Pokud ale povolíte automatické zvětšování úložiště, zvýší se úložiště serveru, aby se vešlo na rostoucí data. U serverů s úložištěm menším než 100 GB zřízené úložiště se velikost zřízeného úložiště zvyšuje o 5 GB, jakmile bude volné úložiště nižší než 1 GB nebo 10% zřízené úložiště. U serverů s více než 100 GB zřízeného úložiště se velikost zřízeného úložiště zvyšuje o 5%, pokud je volný prostor úložiště pod 5% velikosti zřízeného úložiště. Maximální limity úložiště, jak je uvedeno [zde](concepts-pricing-tiers.md#storage) , platí.

## <a name="prerequisites"></a>Požadavky
K dokončení tohoto průvodce budete potřebovat:
- [Server Azure Database for MariaDB](./quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="enable-storage-auto-grow"></a>Povolit automatické zvětšování úložiště 

Pomocí těchto kroků nastavíte automatické zvětšování úložiště serveru MariaDB:

1. V [Azure Portal](https://portal.azure.com/)vyberte svůj existující Azure Database for MariaDB Server.

2. Na stránce MariaDB Server klikněte v části **Nastavení** na možnost **cenová úroveň** a otevřete stránku cenová úroveň.

3. V části Automatické zvětšování vyberte **Ano** , pokud chcete povolit automatické zvětšování úložiště.

    ![Azure Database for MariaDB – Settings_Pricing_tier – automatické zvětšování](./media/howto-auto-grow-storage-portal/3-auto-grow.png)

4. Kliknutím na tlačítko **OK** uložte změny.

5. Oznámení potvrdí, že automatické zvětšování bylo úspěšně povoleno.

    ![Úspěch Azure Database for MariaDB – automatické zvětšování](./media/howto-auto-grow-storage-portal/5-auto-grow-successful.png)

## <a name="next-steps"></a>Další kroky

Přečtěte si informace o [tom, jak vytvářet výstrahy na metrikách](howto-alert-metric.md).
