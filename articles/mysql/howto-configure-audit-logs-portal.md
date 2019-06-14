---
title: Konfigurace a přístup k protokolům auditu pro službu Azure Database for MySQL na webu Azure portal
description: Tento článek popisuje, jak nakonfigurovat a získat přístup k protokolům auditování ve službě Azure Database for MySQL z portálu Azure portal.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: dfa57b3f597ba1c01dc78421cc04074f7b6a858e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67063047"
---
# <a name="configure-and-access-audit-logs-in-the-azure-portal"></a>Konfigurace a přístup k protokolům auditu na portálu Azure portal

Můžete nakonfigurovat [– Azure Database for MySQL – protokoly auditu](concepts-audit-logs.md) a nastavení diagnostiky na webu Azure Portal.

> [!IMPORTANT]
> Funkce protokolu auditu je aktuálně ve verzi preview.

## <a name="prerequisites"></a>Požadavky

Pro jednotlivé kroky v této příručce s postupy, musíte:

- [Azure Database for MySQL serveru](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="configure-audit-logging"></a>Konfigurace protokolování auditu

Povolit a konfigurovat protokolování auditu.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

1. Vyberte váš server Azure Database for MySQL.

1. V části **nastavení** části na bočním panelu, vyberte **parametry serveru**.
    ![Parametry serveru](./media/howto-configure-audit-logs-portal/server-parameters.png)

1. Aktualizace **audit_log_enabled** parametr ON.
    ![Povolení protokolů auditu](./media/howto-configure-audit-logs-portal/audit-log-enabled.png)

1. Vyberte události zaznamenávané aktualizací **audit_log_events** parametru.
    ![Události protokolu auditu](./media/howto-configure-audit-logs-portal/audit-log-events.png)

1. Přidejte všechny uživatele MySQL, které se mají vyloučit z protokolování aktualizací **audit_log_exclude_users** parametru. Určení uživatelů tím, že poskytuje jejich uživatelskému jménu MySQL.
    ![Vyloučit uživatele protokolu auditu](./media/howto-configure-audit-logs-portal/audit-log-exclude-users.png)

1. Jakmile změníte parametry, můžete kliknout na **Uložit**. Nebo můžete **zahodit** provedené změny.
    ![Uložit](./media/howto-configure-audit-logs-portal/save-parameters.png)

## <a name="set-up-diagnostic-logs"></a>Nastavení diagnostických protokolů

1. V části **monitorování** části na bočním panelu, vyberte **nastavení diagnostiky**.

1. Klikněte na "+ přidat nastavení diagnostiky" ![přidejte nastavení diagnostiky](./media/howto-configure-audit-logs-portal/add-diagnostic-setting.png)

1. Zadejte název nastavení diagnostiky.

1. Určete, jaká data jímky k odeslání protokolů auditu (účet úložiště, Centrum událostí, a/nebo pracovní prostor Log Analytics).

1. Vyberte "MySqlAuditLogs" jako typ protokolu.
![Konfigurace nastavení diagnostiky](./media/howto-configure-audit-logs-portal/configure-diagnostic-setting.png)

1. Po konfiguraci datových jímek do kanálu protokoly auditu chcete, můžete kliknout na **Uložit**.
![Uložit nastavení diagnostiky](./media/howto-configure-audit-logs-portal/save-diagnostic-setting.png)

1. Přístup k protokolům auditu, ve kterých je v datové jímky, které jste nakonfigurovali. Může trvat až 10 minut na protokoly, kde se zobrazí.

## <a name="next-steps"></a>Další postup

- Další informace o [protokoly auditu](concepts-audit-logs.md) ve službě Azure Database for MySQL.