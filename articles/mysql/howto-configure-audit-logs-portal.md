---
title: Přístup k protokolům auditu-Azure Portal-Azure Database for MySQL
description: Tento článek popisuje, jak nakonfigurovat a přistupovat k protokolům auditu v Azure Database for MySQL z Azure Portal.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 9/29/2020
ms.openlocfilehash: a73a3c77310c0e19792758c0586975e14cfaebf8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94541653"
---
# <a name="configure-and-access-audit-logs-for-azure-database-for-mysql-in-the-azure-portal"></a>Konfigurace a přístup k protokolům auditu pro Azure Database for MySQL v Azure Portal

Z Azure Portal můžete nakonfigurovat [protokoly auditu Azure Database for MySQL](concepts-audit-logs.md) a nastavení diagnostiky.

## <a name="prerequisites"></a>Požadavky

Pokud chcete projít tento průvodce, budete potřebovat:

- [Server Azure Database for MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="configure-audit-logging"></a>Konfigurace protokolování auditu

>[!IMPORTANT]
> Doporučujeme pouze protokolovat typy událostí a uživatele požadované pro vaše účely auditování, aby se zajistilo, že výkon serveru nebude velmi ovlivněn.

Povolte a nakonfigurujte protokolování auditu.

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).

1. Vyberte server Azure Database for MySQL.

1. V části **Nastavení** na bočním panelu vyberte **parametry serveru**.
    :::image type="content" source="./media/howto-configure-audit-logs-portal/server-parameters.png" alt-text="Parametry serveru":::

1. Aktualizujte parametr **audit_log_enabled** na zapnuto.
    :::image type="content" source="./media/howto-configure-audit-logs-portal/audit-log-enabled.png" alt-text="Povolit protokoly auditu":::

1. Vyberte [typy událostí](concepts-audit-logs.md#configure-audit-logging) , které se mají protokolovat, pomocí aktualizace parametru **audit_log_events** .
    :::image type="content" source="./media/howto-configure-audit-logs-portal/audit-log-events.png" alt-text="Události protokolu auditu":::

1. Pomocí aktualizace parametrů **audit_log_exclude_users** a **audit_log_include_users** přidejte všechny uživatele MySQL, které chcete zahrnout do protokolování, nebo je z něj vyloučit. Zadejte uživatele pomocí svého uživatelského jména MySQL.
    :::image type="content" source="./media/howto-configure-audit-logs-portal/audit-log-exclude-users.png" alt-text="Vyloučení uživatelů v protokolu auditu":::

1. Po změně parametrů můžete kliknout na **Uložit**. Nebo můžete změny **Zrušit** .
    :::image type="content" source="./media/howto-configure-audit-logs-portal/save-parameters.png" alt-text="Uložit":::

## <a name="set-up-diagnostic-logs"></a>Nastavení diagnostických protokolů

1. V části **monitorování** na bočním panelu vyberte **nastavení diagnostiky**.

1. Klikněte na + Přidat nastavení diagnostiky :::image type="content" source="./media/howto-configure-audit-logs-portal/add-diagnostic-setting.png" alt-text="Přidat nastavení diagnostiky"::: .

1. Zadejte název nastavení diagnostiky.

1. Určete, která datová jímka se mají Odeslat protokoly auditu (účet úložiště, centrum událostí a pracovní prostor Log Analytics).

1. Jako typ protokolu vyberte "MySqlAuditLogs".
:::image type="content" source="./media/howto-configure-audit-logs-portal/configure-diagnostic-setting.png" alt-text="Konfigurace nastavení diagnostiky":::

1. Po nakonfigurování datových umyvadel pro přesměrování protokolů auditu na můžete kliknout na **Uložit**.
:::image type="content" source="./media/howto-configure-audit-logs-portal/save-diagnostic-setting.png" alt-text="Uložit nastavení diagnostiky":::

1. Přihlaste se k protokolům auditu jejich zkoumáním v datových jímkach, které jste nakonfigurovali. Zobrazení protokolů může trvat až 10 minut.

## <a name="next-steps"></a>Další kroky

- Další informace o [protokolech auditu](concepts-audit-logs.md) v Azure Database for MySQL
- Naučte se konfigurovat protokoly auditu v [Azure CLI](howto-configure-audit-logs-cli.md) .