---
title: Zastavení/spuštění-Azure Portal-Azure Database for PostgreSQL flexibilní Server
description: Tento článek popisuje, jak pomocí Azure Portal zastavit a spustit operace v Azure Database for PostgreSQL.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: e28243f5919c355e6ad0d4998ff8388a56e3d0ca
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105607364"
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
