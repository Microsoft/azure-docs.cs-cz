---
title: Zastavení/spuštění-Azure Portal-Azure Database for MySQL flexibilní Server
description: Tento článek popisuje, jak pomocí Azure Portal zastavit a spustit operace v Azure Database for MySQL.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: how-to
ms.date: 09/29/2020
ms.openlocfilehash: e3e08ae9bbf00e1c9a44e6ba913cac1d842928b7
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2020
ms.locfileid: "91567473"
---
# <a name="stopstart-an-azure-database-for-mysql---flexible-server-preview"></a>Zastavení/spuštění Azure Database for MySQL-flexibilního serveru (verze Preview)

> [!IMPORTANT]
> Azure Database for MySQL – flexibilní Server je momentálně ve verzi Public Preview.

Tento článek poskytuje podrobný postup, jak provést zastavení a spuštění flexibilního serveru.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto průvodce budete potřebovat:

-   Musíte mít Azure Database for MySQL flexibilní Server.

## <a name="stop-a-running-server"></a>Zastavení běžícího serveru

1.  V [Azure Portal](https://portal.azure.com/)vyberte flexibilní Server, který chcete zastavit.

2.  Na stránce **Přehled** klikněte na tlačítko **zastavit** na panelu nástrojů.
    
    :::image type="content" source="media/how-to-stop-start-server-portal/stop-server.png" alt-text="Zastavení flexibilního serveru."::: 

3.  Kliknutím na **Ano** potvrďte zastavení serveru.

    :::image type="content" source="media/how-to-stop-start-server-portal/confirm-stop.png" alt-text="Zastavení flexibilního serveru."::: 

> [!NOTE]
> Po zastavení serveru nejsou pro flexibilní Server k dispozici jiné operace správy.

## <a name="start-a-stopped-server"></a>Spuštění zastaveného serveru

1.  V [Azure Portal](https://portal.azure.com/)vyberte flexibilní Server, který chcete spustit.

2.  Na stránce **Přehled** klikněte na tlačítko **Start** na panelu nástrojů.

    :::image type="content" source="media/how-to-stop-start-server-portal/start-server.png" alt-text="Zastavení flexibilního serveru.":::  

> [!NOTE]
> Po spuštění serveru jsou nyní všechny operace správy k dispozici pro flexibilní Server.

## <a name="next-steps"></a>Další kroky
- Další informace o [sítích v Azure Database for MySQL flexibilním serveru](./concepts-networking.md)
- [Vytvářejte a spravujte Azure Database for MySQL flexibilní serverovou virtuální síť pomocí Azure Portal](./how-to-manage-virtual-network-portal.md).

