---
title: Automaticky zvětší úložiště pomocí webu Azure portal ve službě Azure Database for PostgreSQL – jeden Server
description: Tento článek popisuje, jak můžete zajistit automatické zvětšení úložiště pomocí webu Azure portal ve službě Azure Database pro PostegreSQL – jeden Server
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/29/2019
ms.openlocfilehash: 9f88fe3e8a30dd80c5331bd6c8ea7aec401c6fb9
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2019
ms.locfileid: "66676755"
---
# <a name="auto-grow-storage-using-the-azure-portal-in-azure-database-for-postgresql---single-server"></a>Automaticky zvětší úložiště pomocí webu Azure portal ve službě Azure Database for PostgreSQL – jeden Server
Tento článek popisuje způsob konfigurace serveru Azure Database for postgresql – úložiště serveru růst bez dopadu na zatížení.

Pokud server dosáhne limitu přidělené úložiště, je server označen jako jen pro čtení. Pokud povolíte úložiště automaticky růst, úložiště serveru se zvyšuje s ohledem rostoucí data. Pro servery s méně než 100 GB zřízené úložiště velikost zřízeného úložiště prodloužen o 5 GB, jakmile volný úložný prostor je nižší než delší než 1 GB nebo 10 % zřízeného úložiště. Pro servery s více než 100 GB zřízeného úložiště velikost zřízeného úložiště zvětšit 5 %, pokud volný prostor úložiště je menší než 5 % velikosti zřízeného úložiště. Omezuje maximální velikost úložiště, jak je uvedeno [tady](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers#storage) použít.

## <a name="prerequisites"></a>Požadavky
K dokončení této příručce s postupy, potřebujete:
- [– Azure Database for postgresql – server](quickstart-create-server-database-portal.md)

## <a name="enable-storage-auto-grow"></a>Povolit úložiště automaticky růst 

Postupujte podle těchto pokynů nastavte server úložiště automaticky růst PostgreSQL:

1. V [webu Azure portal](https://portal.azure.com/), vyberte váš server Azure Database for PostgreSQL.

2. Na stránce server PostgreSQL v rámci **nastavení**, klikněte na tlačítko **cenová úroveň** otevřete stránku s cenami úrovně.

3. V **automatického růstu** vyberte **Ano** povolit úložiště automaticky zvětšovat.

    ![Azure Database for PostgreSQL – Settings_Pricing_tier - automatické růstu](./media/howto-auto-grow-storage-portal/3-auto-grow.png)

4. Změny uložíte kliknutím na tlačítko **OK** .

5. Oznámení se potvrdí tento automatický růst bylo úspěšně povoleno.

    ![Azure Database for PostgreSQL – úspěch automatického růstu](./media/howto-auto-grow-storage-portal/5-auto-grow-successful.png)

## <a name="next-steps"></a>Další postup

Další informace o [postupy vytváření upozornění na metriky](howto-alert-on-metric.md).
