---
title: Zastavení/spuštění-Azure Portal-Azure Database for MySQL serveru
description: Tento článek popisuje, jak zastavit/spustit operace v Azure Database for MySQL.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.openlocfilehash: f09b6d48e8a98b0995c882769d6c978996324dad
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91342268"
---
# <a name="stopstart-an-azure-database-for-mysql"></a>Zastavení/spuštění Azure Database for MySQL

> [!IMPORTANT]
> Funkce Stop/Start pro Azure Database for MySQL je aktuálně ve verzi Public Preview.

Tento článek poskytuje podrobný postup, jak provést zastavení a spuštění flexibilního serveru.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto průvodce budete potřebovat:

-   Musíte mít Azure Database for MySQL flexibilní Server.

> [!NOTE]
> Přečtěte si omezení používání [Stop/Start](concepts-servers.md#limitations-of-stopstart-operation) .

## <a name="how-to-stopstart-the-azure-database-for-mysql-using-azure-portal"></a>Postup zastavení nebo spuštění Azure Database for MySQL pomocí Azure Portal

### <a name="stop-a-running-server"></a>Zastavení běžícího serveru

1.  V [Azure Portal](https://portal.azure.com/)vyberte server MySQL, který chcete zastavit.

2.  Na stránce **Přehled** klikněte na tlačítko **zastavit** na panelu nástrojů.

    :::image type="content" source="./media/howto-stop-start-server/mysql-stop-server.png" alt-text="Azure Database for MySQL zastavit server":::

    > [!NOTE]
    > Po zastavení serveru nejsou pro flexibilní Server k dispozici jiné operace správy.

### <a name="start-a-stopped-server"></a>Spuštění zastaveného serveru

1.  V [Azure Portal](https://portal.azure.com/)vyberte flexibilní Server, který chcete spustit.

2.  Na stránce **Přehled** klikněte na tlačítko **Start** na panelu nástrojů.

    :::image type="content" source="./media/howto-stop-start-server/mysql-start-server.png" alt-text="Azure Database for MySQL spustit server":::

    > [!NOTE]
    > Po spuštění serveru jsou nyní všechny operace správy k dispozici pro flexibilní Server.

## <a name="how-to-stopstart-the-azure-database-for-mysql-using-cli"></a>Postup zastavení/spuštění Azure Database for MySQL pomocí rozhraní příkazového řádku

### <a name="stop-a-running-server"></a>Zastavení běžícího serveru

1.  V [Azure Portal](https://portal.azure.com/)vyberte server MySQL, který chcete zastavit.

2.  Na stránce **Přehled** klikněte na tlačítko **zastavit** na panelu nástrojů.

    ```azurecli-interactive
    az mysql server stop --name <server-name> -g <resource-group-name>
    ```
    > [!NOTE]
    > Po zastavení serveru nejsou pro flexibilní Server k dispozici jiné operace správy.

### <a name="start-a-stopped-server"></a>Spuštění zastaveného serveru

1.  V [Azure Portal](https://portal.azure.com/)vyberte flexibilní Server, který chcete spustit.

2.  Na stránce **Přehled** klikněte na tlačítko **Start** na panelu nástrojů.

    ```azurecli-interactive
    az mysql server start --name <server-name> -g <resource-group-name>
    ```
    > [!NOTE]
    > Po spuštění serveru jsou nyní všechny operace správy k dispozici pro flexibilní Server.

## <a name="next-steps"></a>Další kroky
Přečtěte si informace o [tom, jak vytvářet výstrahy na metrikách](howto-alert-on-metric.md).
