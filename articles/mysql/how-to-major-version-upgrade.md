---
title: Upgrade hlavní verze – Azure Portal-Azure Database for MySQL-jeden server
description: Tento článek popisuje, jak můžete upgradovat hlavní verzi pro Azure Database for MySQL s jedním serverem pomocí Azure Portal
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 11/16/2020
ms.openlocfilehash: 4dd4729589e429cb1b028b183fdfd144617d1d1b
ms.sourcegitcommit: 03c0a713f602e671b278f5a6101c54c75d87658d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2020
ms.locfileid: "94920640"
---
# <a name="major-version-upgrade-in-azure-database-for-mysql-single-server-using-the-azure-portal"></a>Upgrade hlavní verze v Azure Database for MySQL jednom serveru pomocí Azure Portal

> [!IMPORTANT]
> Upgrade hlavní verze pro jeden server Azure Database for MySQL je ve verzi Public Preview.

Tento článek popisuje, jak můžete na Azure Database for MySQL jednom serveru upgradovat hlavní verzi MySQL na místě.

Tato funkce umožní zákazníkům provést místní upgrady svých serverů MySQL 5,6 na MySQL 5,7 pomocí tlačítka bez přesunu dat nebo nutnosti změny připojovacího řetězce aplikace.

> [!Note]
> * Upgrade hlavní verze je k dispozici jenom pro upgrade hlavní verze z MySQL 5,6 na MySQL 5,7.<br>
> * Upgrade hlavní verze se ještě na serveru repliky nepodporuje.

## <a name="prerequisites"></a>Požadavky
K dokončení tohoto průvodce budete potřebovat:
- [Azure Database for MySQL jeden server](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="perform-major-version-upgrade-from-mysql-56-to-mysql-57"></a>Proveďte upgrade hlavní verze z MySQL 5,6 na MySQL 5,7.

Pomocí těchto kroků proveďte upgrade hlavní verze pro vaši databázi Azure serveru MySQL 5,6.

> [!IMPORTANT]
> Doporučujeme nejprve provést upgrade na obnovenou kopii serveru, nikoli přímo upgradovat produkční prostředí. Podívejte [se, jak provést obnovení k bodu v čase](howto-restore-server-portal.md#point-in-time-restore).

1. V [Azure Portal](https://portal.azure.com/)vyberte svůj existující server Azure Database for MySQL 5,6.

2. Na stránce **Přehled** klikněte na panelu nástrojů na tlačítko **upgradovat** .

3. V části **upgrade** vyberte **OK** a upgradujte Azure Database for MySQL 5,6 Server na 5,7.

    :::image type="content" source="./media/how-to-major-version-upgrade-portal/upgrade.png" alt-text="Azure Database for MySQL – přehled – upgrade":::

4. Oznámení potvrdí, že upgrade proběhl úspěšně.

## <a name="next-steps"></a>Další kroky

Přečtěte si o [Azure Database for MySQLch zásadách správy verzí](concepts-version-policy.md).