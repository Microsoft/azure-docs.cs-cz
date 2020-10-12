---
title: Zastavení/spuštění-Azure Portal-Azure Database for PostgreSQL flexibilní Server
description: Tento článek popisuje, jak pomocí Azure Portal zastavit a spustit operace v Azure Database for PostgreSQL.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 4c393e0048a0058ebe0fbf2b0ee65f6ae2e184c5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90935889"
---
# <a name="stopstart-an-azure-database-for-postgresql---flexible-server-preview"></a>Zastavení/spuštění Azure Database for PostgreSQL-flexibilního serveru (verze Preview)

> [!IMPORTANT]
> Azure Database for PostgreSQL – flexibilní Server je momentálně ve verzi Preview.

Tento článek poskytuje podrobné pokyny k zastavení a spuštění flexibilního serveru.

## <a name="pre-requisites"></a>Požadavky

K dokončení tohoto průvodce budete potřebovat:

-   Musíte mít Azure Database for PostgreSQL flexibilní Server.

## <a name="stop-a-running-server"></a>Zastavení běžícího serveru

1.  V [Azure Portal](https://portal.azure.com/)vyberte flexibilní Server, který chcete zastavit.

2.  Na stránce **Přehled** klikněte na tlačítko **zastavit** na panelu nástrojů.

> [!NOTE]
> Po zastavení serveru nejsou u flexibilního serveru k dispozici jiné operace správy.

Všimněte si, že zastavené servery se po sedmi dnech automaticky spustí znovu. Všechny probíhající aktualizace údržby se použijí při příštím spuštění serveru.

## <a name="start-a-stopped-server"></a>Spuštění zastaveného serveru

1.  V [Azure Portal](https://portal.azure.com/)vyberte flexibilní Server, který chcete spustit.

2.  Na stránce **Přehled** klikněte na tlačítko **Start** na panelu nástrojů.

> [!NOTE]
> Po spuštění serveru jsou nyní všechny operace správy k dispozici pro flexibilní Server.

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [možnostech výpočtů a úložiště v Azure Database for PostgreSQL flexibilním serveru](./concepts-compute-storage.md).
