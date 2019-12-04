---
title: Přístup k protokolům auditu-Azure Portal-Azure Database for MySQL
description: Tento článek popisuje, jak nakonfigurovat a přistupovat k protokolům auditu v Azure Database for MySQL z Azure Portal.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: ff1a6c63b6eb99acdef955806a138e3e22b8902a
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74773706"
---
# <a name="configure-and-access-audit-logs-for-azure-database-for-mysql-in-the-azure-portal"></a>Konfigurace a přístup k protokolům auditu pro Azure Database for MySQL v Azure Portal

Z Azure Portal můžete nakonfigurovat [protokoly auditu Azure Database for MySQL](concepts-audit-logs.md) a nastavení diagnostiky.

> [!IMPORTANT]
> Funkce protokolu auditu je momentálně ve verzi Preview.

## <a name="prerequisites"></a>Předpoklady

Pokud chcete projít tento průvodce, budete potřebovat:

- [Server Azure Database for MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="configure-audit-logging"></a>Konfigurace protokolování auditu

Povolte a nakonfigurujte protokolování auditu.

1. Přihlaste se na web [Azure Portal](https://portal.azure.com/).

1. Vyberte server Azure Database for MySQL.

1. V části **Nastavení** na bočním panelu vyberte **parametry serveru**.
    ![Parametry serveru](./media/howto-configure-audit-logs-portal/server-parameters.png)

1. Aktualizujte parametr **audit_log_enabled** na zapnuto.
    ![povolit protokoly auditu](./media/howto-configure-audit-logs-portal/audit-log-enabled.png)

1. Vyberte [typy událostí](concepts-audit-logs.md#configure-audit-logging) , které se mají protokolovat, pomocí aktualizace parametru **audit_log_events** .
    události protokolu auditu ![](./media/howto-configure-audit-logs-portal/audit-log-events.png)

1. Pomocí aktualizace parametru **audit_log_exclude_users** přidejte všechny uživatele MySQL, které chcete vyloučit z protokolování. Zadejte uživatele pomocí svého uživatelského jména MySQL.
    ![vyloučení uživatelů v protokolu auditu](./media/howto-configure-audit-logs-portal/audit-log-exclude-users.png)

1. Po změně parametrů můžete kliknout na **Uložit**. Nebo můžete změny **Zrušit** .
    ![Uložit](./media/howto-configure-audit-logs-portal/save-parameters.png)

## <a name="set-up-diagnostic-logs"></a>Nastavení diagnostických protokolů

1. V části **monitorování** na bočním panelu vyberte **nastavení diagnostiky**.

1. Kliknutím na + Přidat nastavení diagnostiky ![přidat nastavení diagnostiky](./media/howto-configure-audit-logs-portal/add-diagnostic-setting.png)

1. Zadejte název nastavení diagnostiky.

1. Určete, která datová jímka se mají Odeslat protokoly auditu (účet úložiště, centrum událostí a pracovní prostor Log Analytics).

1. Jako typ protokolu vyberte "MySqlAuditLogs".
![konfigurace nastavení diagnostiky](./media/howto-configure-audit-logs-portal/configure-diagnostic-setting.png)

1. Po nakonfigurování datových umyvadel pro přesměrování protokolů auditu na můžete kliknout na **Uložit**.
![Uložit nastavení diagnostiky](./media/howto-configure-audit-logs-portal/save-diagnostic-setting.png)

1. Přihlaste se k protokolům auditu jejich zkoumáním v datových jímkach, které jste nakonfigurovali. Zobrazení protokolů může trvat až 10 minut.

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [protokolech auditu](concepts-audit-logs.md) v Azure Database for MySQL.