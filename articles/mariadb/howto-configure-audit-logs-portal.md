---
title: Přístup k protokolům auditu-Azure Portal-Azure Database for MariaDB
description: Tento článek popisuje, jak nakonfigurovat a přistupovat k protokolům auditu v Azure Database for MariaDB z Azure Portal.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 6/24/2020
ms.openlocfilehash: f1f3cda4ed9cdac6f6e03e88601b0a35d5c53faf
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94533442"
---
# <a name="configure-and-access-audit-logs-in-the-azure-portal"></a>Konfigurace a přístup k protokolům auditu v Azure Portal

Z Azure Portal můžete nakonfigurovat [protokoly auditu Azure Database for MariaDB](concepts-audit-logs.md) a nastavení diagnostiky.

## <a name="prerequisites"></a>Požadavky

Pokud chcete projít tento průvodce, budete potřebovat:

- [Server Azure Database for MariaDB](quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="configure-audit-logging"></a>Konfigurace protokolování auditu

>[!IMPORTANT]
> Doporučujeme pouze protokolovat typy událostí a uživatele požadované pro vaše účely auditování, aby se zajistilo, že výkon serveru nebude velmi ovlivněn.

Povolte a nakonfigurujte protokolování auditu.

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).

1. Vyberte server Azure Database for MariaDB.

1. V části **Nastavení** na bočním panelu vyberte **parametry serveru**.
    ![Parametry serveru](./media/howto-configure-audit-logs-portal/server-parameters.png)

1. Aktualizujte parametr **audit_log_enabled** na zapnuto.
    ![Povolit protokoly auditu](./media/howto-configure-audit-logs-portal/audit-log-enabled.png)

1. Vyberte [typy událostí](concepts-audit-logs.md#configure-audit-logging) , které se mají protokolovat, pomocí aktualizace parametru **audit_log_events** .
    ![Události protokolu auditu](./media/howto-configure-audit-logs-portal/audit-log-events.png)

1. Pomocí aktualizace parametru **audit_log_exclude_users** přidejte všechny uživatele MariaDB, kteří budou vyloučení z protokolování. Určete uživatele zadáním svého uživatelského jména MariaDB.
    ![Vyloučení uživatelů v protokolu auditu](./media/howto-configure-audit-logs-portal/audit-log-exclude-users.png)

1. Po změně parametrů můžete kliknout na **Uložit**. Nebo můžete změny **Zrušit** .
    ![Uložení](./media/howto-configure-audit-logs-portal/save-parameters.png)

## <a name="set-up-diagnostic-logs"></a>Nastavení diagnostických protokolů

1. V části **monitorování** na bočním panelu vyberte **nastavení diagnostiky**.

1. Klikněte na + Přidat nastavení diagnostiky ![ Přidat nastavení diagnostiky.](./media/howto-configure-audit-logs-portal/add-diagnostic-setting.png)

1. Zadejte název nastavení diagnostiky.

1. Určete, která datová jímka se mají Odeslat protokoly auditu (účet úložiště, centrum událostí a pracovní prostor Log Analytics).

1. Jako typ protokolu vyberte "MySqlAuditLogs".
![Konfigurace nastavení diagnostiky](./media/howto-configure-audit-logs-portal/configure-diagnostic-setting.png)

1. Po nakonfigurování datových umyvadel pro přesměrování protokolů auditu na můžete kliknout na **Uložit**.
![Uložit nastavení diagnostiky](./media/howto-configure-audit-logs-portal/save-diagnostic-setting.png)

1. Přihlaste se k protokolům auditu jejich zkoumáním v datových jímkach, které jste nakonfigurovali. Zobrazení protokolů může trvat až 10 minut.

## <a name="next-steps"></a>Další kroky

- Další informace o [protokolech auditu](concepts-audit-logs.md) v Azure Database for MariaDB
- Naučte se konfigurovat protokoly auditu v [Azure CLI](howto-configure-audit-logs-cli.md) .