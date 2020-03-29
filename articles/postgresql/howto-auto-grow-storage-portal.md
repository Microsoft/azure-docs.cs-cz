---
title: Automatické úložiště růstu – portál Azure – databáze Azure pro PostgreSQL – jeden server
description: Tento článek popisuje, jak můžete nakonfigurovat automatické růst úložiště pomocí portálu Azure v Azure Database for PostgreSQL – jeden server
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/29/2019
ms.openlocfilehash: 5e4f9d68d02edf456394d4ce10b7b6af5f8643d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74769230"
---
# <a name="auto-grow-storage-using-the-azure-portal-in-azure-database-for-postgresql---single-server"></a>Automatické rozšiřování úložiště pomocí portálu Azure v Azure Database for PostgreSQL – jeden server
Tento článek popisuje, jak můžete nakonfigurovat Azure Database pro úložiště serveru PostgreSQL růst bez dopadu na zatížení.

Když server dosáhne limitu přiděleného úložiště, je označen jako jen pro čtení. Pokud však povolíte automatické zvětšování úložiště, úložiště serveru se zvýší tak, aby vyhovovalo rostoucím datům. Pro servery s méně než 100 GB zřízeného úložiště, zřízené úložiště se zvýší o 5 GB, jakmile je bezplatné úložiště nižší než 1 GB nebo 10 % zřízeného úložiště. U serverů s více než 100 GB zřízeného úložiště se zřízené úložiště zvýší o 5 %, když je volný úložný prostor nižší než 5 % zřízené velikosti úložiště. Platí maximální limity úložiště, jak [je uvedeno zde.](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers#storage)

## <a name="prerequisites"></a>Požadavky
Chcete-li dokončit tento návod, potřebujete:
- [Databáze Azure pro postgreSQL server](quickstart-create-server-database-portal.md)

## <a name="enable-storage-auto-grow"></a>Povolení automatického růstu úložiště 

Chcete-li nastavit automatické zvětšování úložiště serveru PostgreSQL, postupujte takto:

1. Na [webu Azure Portal](https://portal.azure.com/)vyberte stávající azure databázi pro postgreSQL server.

2. Na stránce PostgreSQL server v části **Nastavení**klikněte na **Cenová úroveň** a otevřete stránku cenové úrovně.

3. V části Automatický růst vyberte **Ano,** **chcete-li** povolit automatické zvětšování úložiště.

    ![Databáze Azure pro PostgreSQL – Settings_Pricing_tier – automatický růst](./media/howto-auto-grow-storage-portal/3-auto-grow.png)

4. Klikněte na tlačítko **OK** a uložte změny.

5. Oznámení potvrdí, že automatické zvětšování bylo úspěšně povoleno.

    ![Databáze Azure pro PostgreSQL – úspěch automatického růstu](./media/howto-auto-grow-storage-portal/5-auto-grow-successful.png)

## <a name="next-steps"></a>Další kroky

Přečtěte [si, jak vytvářet upozornění na metriky](howto-alert-on-metric.md).
