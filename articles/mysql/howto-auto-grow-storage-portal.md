---
title: Automatické zvětšování úložiště – Azure Portal-Azure Database for MySQL
description: Tento článek popisuje, jak můžete povolit automatické zvětšování úložiště pro Azure Database for MySQL pomocí Azure Portal
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 7be3e7e1a0d2e48b37c319f67e3b382f825469df
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74770607"
---
# <a name="auto-grow-storage-in-azure-database-for-mysql-using-the-azure-portal"></a>Automatické zvětšení úložiště v Azure Database for MySQL pomocí Azure Portal
Tento článek popisuje, jak můžete nakonfigurovat úložiště serveru Azure Database for MySQL pro růst, aniž by to ovlivnilo zatížení.

Když server dosáhne přiděleného limitu úložiště, je server označený jen pro čtení. Pokud ale povolíte automatické zvětšování úložiště, zvýší se úložiště serveru, aby se vešlo na rostoucí data. U serverů s úložištěm menším než 100 GB zřízené úložiště se velikost zřízeného úložiště zvyšuje o 5 GB, jakmile bude volné úložiště nižší než 1 GB nebo 10% zřízené úložiště. U serverů s více než 100 GB zřízeného úložiště se velikost zřízeného úložiště zvyšuje o 5%, pokud je volný prostor úložiště pod 5% velikosti zřízeného úložiště. Maximální limity úložiště, jak je uvedeno [zde](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers#storage) , platí.

## <a name="prerequisites"></a>Předpoklady
K dokončení tohoto průvodce budete potřebovat:
- [Server Azure Database for MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="enable-storage-auto-grow"></a>Povolit automatické zvětšování úložiště 

Pomocí těchto kroků nastavte automatické zvětšování úložiště serveru MySQL:

1. V [Azure Portal](https://portal.azure.com/)vyberte svůj existující Azure Database for MySQL server.

2. Na stránce serveru MySQL v části **Nastavení** klikněte na **cenová úroveň** a otevřete stránku cenová úroveň.

3. V části Automatické zvětšování vyberte **Ano** , pokud chcete povolit automatické zvětšování úložiště.

    ![Azure Database for MySQL – Settings_Pricing_tier – automatické zvětšování](./media/howto-auto-grow-storage-portal/3-auto-grow.png)

4. Klikněte na tlačítko **OK** a uložte změny.

5. Oznámení potvrdí, že automatické zvětšování bylo úspěšně povoleno.

    ![Úspěch Azure Database for MySQL – automatické zvětšování](./media/howto-auto-grow-storage-portal/5-auto-grow-success.png)

## <a name="next-steps"></a>Další kroky

Přečtěte si informace o [tom, jak vytvářet výstrahy na metrikách](howto-alert-on-metric.md).
