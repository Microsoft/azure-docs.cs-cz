---
title: Zastavení/spuštění-Azure Portal-Azure Database for MySQL serveru
description: Tento článek popisuje, jak zastavit/spustit operace v Azure Database for MySQL.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.openlocfilehash: d297d215d4b0edfdd67b603ba4707bf02057ad78
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100516868"
---
# <a name="stopstart-an-azure-database-for-mysql"></a>Zastavení/spuštění Azure Database for MySQL

> [!IMPORTANT]
>  Když **zastavíte** Server, zůstane v tomto stavu po dobu dalších 7 dnů roztažení. Pokud ji během této doby **nespustíte** ručně, server bude automaticky spuštěn na konci 7 dnů. Pokud nepoužíváte server, můžete ho také **zastavit** .

Tento článek poskytuje podrobný postup, jak provést zastavení a spuštění jednoho serveru.

## <a name="prerequisites"></a>Předpoklady

K dokončení tohoto průvodce budete potřebovat:

-   Musíte mít Azure Database for MySQL jeden server.

> [!NOTE]
> Přečtěte si omezení používání [Stop/Start](concepts-servers.md#limitations-of-stopstart-operation) .

## <a name="how-to-stopstart-the-azure-database-for-mysql-using-azure-portal"></a>Postup zastavení nebo spuštění Azure Database for MySQL pomocí Azure Portal

### <a name="stop-a-running-server"></a>Zastavení běžícího serveru

1.  V [Azure Portal](https://portal.azure.com/)vyberte server MySQL, který chcete zastavit.

2.  Na stránce **Přehled** klikněte na tlačítko **zastavit** na panelu nástrojů.

    :::image type="content" source="./media/howto-stop-start-server/mysql-stop-server.png" alt-text="Azure Database for MySQL zastavit server":::

    > [!NOTE]
    > Po zastavení serveru nejsou jiné operace správy pro jeden server k dispozici.

### <a name="start-a-stopped-server"></a>Spuštění zastaveného serveru

1.  V [Azure Portal](https://portal.azure.com/)vyberte jeden server, který chcete spustit.

2.  Na stránce **Přehled** klikněte na tlačítko **Start** na panelu nástrojů.

    :::image type="content" source="./media/howto-stop-start-server/mysql-start-server.png" alt-text="Azure Database for MySQL spustit server":::

    > [!NOTE]
    > Po spuštění serveru jsou nyní všechny operace správy k dispozici pro jeden server.

## <a name="how-to-stopstart-the-azure-database-for-mysql-using-cli"></a>Postup zastavení/spuštění Azure Database for MySQL pomocí rozhraní příkazového řádku

### <a name="stop-a-running-server"></a>Zastavení běžícího serveru

1.  V [Azure Portal](https://portal.azure.com/)vyberte server MySQL, který chcete zastavit.

2.  Na stránce **Přehled** klikněte na tlačítko **zastavit** na panelu nástrojů.

    ```azurecli-interactive
    az mysql server stop --name <server-name> -g <resource-group-name>
    ```
    > [!NOTE]
    > Po zastavení serveru nejsou jiné operace správy pro jeden server k dispozici.

### <a name="start-a-stopped-server"></a>Spuštění zastaveného serveru

1.  V [Azure Portal](https://portal.azure.com/)vyberte jeden server, který chcete spustit.

2.  Na stránce **Přehled** klikněte na tlačítko **Start** na panelu nástrojů.

    ```azurecli-interactive
    az mysql server start --name <server-name> -g <resource-group-name>
    ```
    > [!NOTE]
    > Po spuštění serveru jsou nyní všechny operace správy k dispozici pro jeden server.

## <a name="next-steps"></a>Další kroky
Přečtěte si informace o [tom, jak vytvářet výstrahy na metrikách](howto-alert-on-metric.md).
