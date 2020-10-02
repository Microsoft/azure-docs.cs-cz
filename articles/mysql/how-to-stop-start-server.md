---
title: Zastavení/spuštění-Azure Portal-Azure Database for MySQL serveru
description: Tento článek popisuje, jak zastavit/spustit operace v Azure Database for MySQL.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.openlocfilehash: 706748018c9f32ab2300b290c6929d344b82e0b0
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2020
ms.locfileid: "91653090"
---
# <a name="stopstart-an-azure-database-for-mysql"></a>Zastavení/spuštění Azure Database for MySQL

> [!IMPORTANT]
> Funkce Stop/Start pro Azure Database for MySQL je aktuálně ve verzi Public Preview.

Tento článek poskytuje podrobný postup, jak provést zastavení a spuštění jednoho serveru.

## <a name="prerequisites"></a>Požadované součásti

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

    :::image type="content" source="./media/howto-stop-start-server/mysql-start-server.png" alt-text="Azure Database for MySQL zastavit server":::

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
