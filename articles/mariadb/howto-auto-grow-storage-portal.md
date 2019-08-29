---
title: Automatické zvětšení úložiště v Azure Database for MariaDB pomocí Azure Portal
description: Tento článek popisuje, jak můžete povolit automatické zvětšování úložiště pro Azure Database for MariaDB pomocí Azure Portal
author: ambhatna
ms.author: ambhatna
ms.service: mariadb
ms.topic: conceptual
ms.date: 5/29/2019
ms.openlocfilehash: 9d7b126b5c07cc8661cf50b4c4e13f78592b118e
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/29/2019
ms.locfileid: "70136703"
---
# <a name="auto-grow-storage-in-azure-database-for-mariadb-using-the-azure-portal"></a>Automatické zvětšení úložiště v Azure Database for MariaDB pomocí Azure Portal
Tento článek popisuje, jak můžete nakonfigurovat úložiště serveru Azure Database for MariaDB pro růst, aniž by to ovlivnilo zatížení.

Když server dosáhne přiděleného limitu úložiště, je server označený jen pro čtení. Pokud ale povolíte automatické zvětšování úložiště, zvýší se úložiště serveru, aby se vešlo na rostoucí data. U serverů s úložištěm menším než 100 GB zřízené úložiště se velikost zřízeného úložiště zvyšuje o 5 GB, jakmile bude volné úložiště nižší než 1 GB nebo 10% zřízené úložiště. U serverů s více než 100 GB zřízeného úložiště se velikost zřízeného úložiště zvyšuje o 5%, pokud je volný prostor úložiště pod 5% velikosti zřízeného úložiště. Maximální limity úložiště, jak je uvedeno [zde](https://docs.microsoft.com/azure/mariadb/concepts-pricing-tiers#storage) , platí.

## <a name="prerequisites"></a>Požadavky
K dokončení této příručce s postupy, potřebujete:
- [Server Azure Database for MariaDB](./quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="enable-storage-auto-grow"></a>Povolit automatické zvětšování úložiště 

Pomocí těchto kroků nastavíte automatické zvětšování úložiště serveru MariaDB:

1. V [Azure Portal](https://portal.azure.com/)vyberte svůj existující Azure Database for MariaDB Server.

2. Na stránce MariaDB Server klikněte v části **Nastavení** na možnost **cenová úroveň** a otevřete stránku cenová úroveň.

3. V části Automatické zvětšování vyberte **Ano** , pokud chcete povolit automatické zvětšování úložiště.

    ![Azure Database for MariaDB – Settings_Pricing_tier – automatické zvětšování](./media/howto-auto-grow-storage-portal/3-auto-grow.png)

4. Změny uložíte kliknutím na tlačítko **OK** .

5. Oznámení potvrdí, že automatické zvětšování bylo úspěšně povoleno.

    ![Úspěch Azure Database for MariaDB – automatické zvětšování](./media/howto-auto-grow-storage-portal/5-auto-grow-successful.png)

## <a name="next-steps"></a>Další kroky

Přečtěte si informace o [tom, jak vytvářet výstrahy na metrikách](howto-alert-metric.md).
