---
title: Automaticky zvětší úložiště ve službě Azure Database for MySQL pomocí webu Azure portal
description: Tento článek popisuje, jak můžete zajistit automatické zvětší úložiště pro službu Azure Database for MySQL pomocí webu Azure portal
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: conceptual
ms.date: 5/29/2019
ms.openlocfilehash: 5343475f38dd5389d6b0e266ff7167925cd38d71
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66676785"
---
# <a name="auto-grow-storage-in-azure-database-for-mysql-using-the-azure-portal"></a>Automaticky zvětší úložiště ve službě Azure Database for MySQL pomocí webu Azure portal
Tento článek popisuje způsob konfigurace serveru Azure Database for MySQL server úložiště rozšířit bez dopadu na zatížení.

Pokud server dosáhne limitu přidělené úložiště, je server označen jako jen pro čtení. Pokud povolíte úložiště automaticky růst, úložiště serveru se zvyšuje s ohledem rostoucí data. Pro servery s méně než 100 GB zřízené úložiště velikost zřízeného úložiště prodloužen o 5 GB, jakmile volný úložný prostor je nižší než delší než 1 GB nebo 10 % zřízeného úložiště. Pro servery s více než 100 GB zřízeného úložiště velikost zřízeného úložiště zvětšit 5 %, pokud volný prostor úložiště je menší než 5 % velikosti zřízeného úložiště. Omezuje maximální velikost úložiště, jak je uvedeno [tady](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers#storage) použít.

## <a name="prerequisites"></a>Požadavky
K dokončení této příručce s postupy, potřebujete:
- [– Azure Database for MySQL serveru](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="enable-storage-auto-grow"></a>Povolit úložiště automaticky růst 

Postupujte podle těchto pokynů nastavte MySQL server úložiště automaticky růst:

1. V [webu Azure portal](https://portal.azure.com/), vyberte váš server Azure Database for MySQL.

2. Na stránce server MySQL v rámci **nastavení** záhlaví, klikněte na tlačítko **cenová úroveň** otevřete stránku cenová úroveň.

3. V části automatického růstu vyberte **Ano** povolit úložiště automaticky zvětšovat.

    ![Azure Database for MySQL – Settings_Pricing_tier - automatické růstu](./media/howto-auto-grow-storage-portal/3-auto-grow.png)

4. Změny uložíte kliknutím na tlačítko **OK** .

5. Oznámení se potvrdí tento automatický růst bylo úspěšně povoleno.

    ![Azure Database for MySQL – úspěch automatického růstu](./media/howto-auto-grow-storage-portal/5-auto-grow-success.png)

## <a name="next-steps"></a>Další postup

Další informace o [postupy vytváření upozornění na metriky](howto-alert-on-metric.md).
