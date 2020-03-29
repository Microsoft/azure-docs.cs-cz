---
title: Automatické úložiště růstu – portál Azure – Databáze Azure pro MySQL
description: Tento článek popisuje, jak můžete povolit automatické úložiště pro růst pro Azure Database for MySQL pomocí portálu Azure Portal
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: ddbcb0143a4f84d9e0f3dc2fedf780fca6637692
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062518"
---
# <a name="auto-grow-storage-in-azure-database-for-mysql-using-the-azure-portal"></a>Automatické rozšiřování úložiště v Azure Database for MySQL pomocí portálu Azure
Tento článek popisuje, jak můžete nakonfigurovat Azure Database pro úložiště serveru MySQL růst bez dopadu na zatížení.

Když server dosáhne limitu přiděleného úložiště, je označen jako jen pro čtení. Pokud však povolíte automatické zvětšování úložiště, úložiště serveru se zvýší tak, aby vyhovovalo rostoucím datům. Pro servery s méně než 100 GB zřízeného úložiště, zřízené úložiště se zvýší o 5 GB, jakmile je bezplatné úložiště nižší než 1 GB nebo 10 % zřízeného úložiště. U serverů s více než 100 GB zřízeného úložiště se zřízené úložiště zvýší o 5 %, když je volný úložný prostor nižší než 5 % zřízené velikosti úložiště. Platí maximální limity úložiště, jak [je uvedeno zde.](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers#storage)

## <a name="prerequisites"></a>Požadavky
Chcete-li dokončit tento návod, potřebujete:
- [Databáze Azure pro server MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="enable-storage-auto-grow"></a>Povolení automatického růstu úložiště 

Chcete-li nastavit automatické zvětšování úložiště serveru MySQL, postupujte takto:

1. Na [webu Azure Portal](https://portal.azure.com/)vyberte existující Azure Database for MySQL server.

2. Na stránce MySQL server v části **Nastavení** klikněte na **Cenová úroveň** a otevřete stránku Cenová úroveň.

3. V části Automatický růst vyberte **Ano,** chcete-li povolit automatické zvětšování úložiště.

    ![Databáze Azure pro MySQL – Settings_Pricing_tier – automatický růst](./media/howto-auto-grow-storage-portal/3-auto-grow.png)

4. Klikněte na tlačítko **OK** a uložte změny.

5. Oznámení potvrdí, že automatické zvětšování bylo úspěšně povoleno.

    ![Azure Database for MySQL – úspěch automatického růstu](./media/howto-auto-grow-storage-portal/5-auto-grow-success.png)

## <a name="next-steps"></a>Další kroky

Přečtěte [si, jak vytvářet upozornění na metriky](howto-alert-on-metric.md).
