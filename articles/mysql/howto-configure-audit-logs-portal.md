---
title: Protokoly auditu přístupu – portál Azure – databáze Azure pro MySQL
description: Tento článek popisuje, jak nakonfigurovat a přistupovat k protokolům auditu v Azure Database for MySQL z webu Azure Portal.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 188ef3a1b9777c37f8557a69e19887638a973611
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062527"
---
# <a name="configure-and-access-audit-logs-for-azure-database-for-mysql-in-the-azure-portal"></a>Konfigurace a přístup k protokolům auditu pro Azure Database for MySQL na webu Azure Portal

Můžete nakonfigurovat [Azure Database pro protokoly auditu MySQL](concepts-audit-logs.md) a nastavení diagnostiky z webu Azure Portal.

> [!IMPORTANT]
> Funkce protokolu auditu je nyní ve verzi Preview.

## <a name="prerequisites"></a>Požadavky

Chcete-li projít tento návod, co potřebujete:

- [Databáze Azure pro server MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="configure-audit-logging"></a>Konfigurace protokolování auditu

Povolte a nakonfigurujte protokolování auditu.

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).

1. Vyberte databázi Azure pro server MySQL.

1. V části **Nastavení** na postranním panelu vyberte **parametry serveru**.
    ![Parametry serveru](./media/howto-configure-audit-logs-portal/server-parameters.png)

1. Aktualizujte **parametr audit_log_enabled** na ZAPNUTO.
    ![Povolení protokolů auditování](./media/howto-configure-audit-logs-portal/audit-log-enabled.png)

1. Vyberte [typy událostí,](concepts-audit-logs.md#configure-audit-logging) které mají být zaznamenány, aktualizací parametru **audit_log_events.**
    ![Události protokolu auditu](./media/howto-configure-audit-logs-portal/audit-log-events.png)

1. Přidejte všechny uživatele MySQL, kteří budou vyloučeni z protokolování aktualizací **parametru audit_log_exclude_users.** Zadejte uživatele zadáním jejich uživatelského jména MySQL.
    ![Protokol auditu vylučuje uživatele](./media/howto-configure-audit-logs-portal/audit-log-exclude-users.png)

1. Po změně parametrů můžete klepnout na tlačítko **Uložit**. Nebo můžete **zahodit** změny.
    ![Uložení](./media/howto-configure-audit-logs-portal/save-parameters.png)

## <a name="set-up-diagnostic-logs"></a>Nastavení diagnostických protokolů

1. V části **Monitorování** na postranním panelu vyberte **Nastavení diagnostiky**.

1. Klikněte na "+ ![Přidat diagnostické nastavení" Přidat diagnostické nastavení](./media/howto-configure-audit-logs-portal/add-diagnostic-setting.png)

1. Zadejte název diagnostického nastavení.

1. Určete, které jímky dat k odeslání protokolů auditu (účet úložiště, centrum událostí nebo pracovní prostor Log Analytics).

1. Jako typ protokolu vyberte "MySqlAuditLogs".
![Konfigurace nastavení diagnostiky](./media/howto-configure-audit-logs-portal/configure-diagnostic-setting.png)

1. Po konfiguraci jímek dat tak, aby byly protokoly auditu na kanál, můžete klepnout na tlačítko **Uložit**.
![Uložit diagnostické nastavení](./media/howto-configure-audit-logs-portal/save-diagnostic-setting.png)

1. Získejte přístup k protokolům auditu jejich prozkoumáním v datových propadech, které jste nakonfigurovali. Může trvat až 10 minut, než se protokoly zobrazí.

## <a name="next-steps"></a>Další kroky

- Další informace o [protokolech auditování](concepts-audit-logs.md) v Azure Database for MySQL.