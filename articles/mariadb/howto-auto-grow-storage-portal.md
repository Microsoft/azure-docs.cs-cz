---
title: Automatické úložiště růstu – portál Azure – databáze Azure pro MariaDB
description: Tento článek popisuje, jak můžete povolit automatické úložiště pro růst pro Azure Database for MariaDB pomocí portálu Azure Portal
author: ambhatna
ms.author: ambhatna
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 7ec4c7a61725fa02f00bf38048182d640666ae8f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79528893"
---
# <a name="auto-grow-storage-in-azure-database-for-mariadb-using-the-azure-portal"></a>Automatické růst úložiště v Azure Database for MariaDB pomocí portálu Azure
Tento článek popisuje, jak můžete nakonfigurovat Azure Database pro úložiště serveru MariaDB růst bez dopadu na zatížení.

Když server dosáhne limitu přiděleného úložiště, je označen jako jen pro čtení. Pokud však povolíte automatické zvětšování úložiště, úložiště serveru se zvýší tak, aby vyhovovalo rostoucím datům. Pro servery s méně než 100 GB zřízeného úložiště, zřízené úložiště se zvýší o 5 GB, jakmile je bezplatné úložiště nižší než 1 GB nebo 10 % zřízeného úložiště. U serverů s více než 100 GB zřízeného úložiště se zřízené úložiště zvýší o 5 %, když je volný úložný prostor nižší než 5 % zřízené velikosti úložiště. Platí maximální limity úložiště, jak [je uvedeno zde.](https://docs.microsoft.com/azure/mariadb/concepts-pricing-tiers#storage)

## <a name="prerequisites"></a>Požadavky
Chcete-li dokončit tento návod, potřebujete:
- [Databáze Azure pro server MariaDB](./quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="enable-storage-auto-grow"></a>Povolení automatického růstu úložiště 

Chcete-li nastavit automatické zvětšování úložiště serveru MariaDB, postupujte takto:

1. Na [webu Azure Portal](https://portal.azure.com/)vyberte stávající azure databázi pro server MariaDB.

2. Na stránce serveru MariaDB klikněte v části **Nastavení** na **Cenovou úroveň** a otevřete stránku cenové úrovně.

3. V části Automatický růst vyberte **Ano,** chcete-li povolit automatické zvětšování úložiště.

    ![Databáze Azure pro MariaDB – Settings_Pricing_tier – automatický růst](./media/howto-auto-grow-storage-portal/3-auto-grow.png)

4. Klikněte na tlačítko **OK** a uložte změny.

5. Oznámení potvrdí, že automatické zvětšování bylo úspěšně povoleno.

    ![Azure Database for MariaDB – úspěch automatického růstu](./media/howto-auto-grow-storage-portal/5-auto-grow-successful.png)

## <a name="next-steps"></a>Další kroky

Přečtěte [si, jak vytvářet upozornění na metriky](howto-alert-metric.md).
