---
title: Konfigurace protokolů auditu-Azure Portal-Azure Database for MySQL-flexibilní Server
description: Tento článek popisuje, jak nakonfigurovat a přistupovat k protokolům auditu v Azure Database for MySQL flexibilní Server z Azure Portal.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 9/29/2020
ms.openlocfilehash: cadd384bc7d3222cbc5d1179271f7b4624c9224a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91565822"
---
# <a name="configure-and-access-audit-logs-for-azure-database-for-mysql---flexible-server-using-the-azure-portal"></a>Konfigurace a přístup k protokolům auditu pro Azure Database for MySQL flexibilní Server pomocí Azure Portal

> [!IMPORTANT]
> Azure Database for MySQL – flexibilní Server je momentálně ve verzi Public Preview.

Z Azure Portal můžete nakonfigurovat Azure Database for MySQL flexibilní [protokoly auditu](concepts-audit-logs.md) serveru a nastavení diagnostiky.

## <a name="prerequisites"></a>Požadavky
Kroky v tomto článku vyžadují, abyste měli [flexibilní Server](quickstart-create-server-portal.md).

## <a name="configure-audit-logging"></a>Konfigurace protokolování auditu

>[!IMPORTANT]
> Doporučujeme pouze protokolovat typy událostí a uživatele požadované pro vaše účely auditování, aby se zajistilo, že výkon serveru nebude velmi ovlivněn.

Povolte a nakonfigurujte protokolování auditu.

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com/).

1. Vyberte flexibilní Server.

1. V části **Nastavení** na bočním panelu vyberte **parametry serveru**.
    :::image type="content" source="./media/how-to-configure-audit-logs-portal/server-parameters.png" alt-text="Parametry serveru":::

1. Aktualizujte parametr **audit_log_enabled** na zapnuto.
    :::image type="content" source="./media/how-to-configure-audit-logs-portal/audit-log-enabled.png" alt-text="Parametry serveru":::

1. Vyberte [typy událostí](concepts-audit-logs.md#configure-audit-logging) , které se mají protokolovat, pomocí aktualizace parametru **audit_log_events** .
    :::image type="content" source="./media/how-to-configure-audit-logs-portal/audit-log-events.png" alt-text="Parametry serveru":::

1. Pomocí aktualizace parametrů **audit_log_exclude_users** a **audit_log_include_users** přidejte všechny uživatele MySQL, které chcete zahrnout do protokolování, nebo je z něj vyloučit. Zadejte uživatele pomocí svého uživatelského jména MySQL.
    :::image type="content" source="./media/how-to-configure-audit-logs-portal/audit-log-exclude-users.png" alt-text="Parametry serveru":::

1. Po změně parametrů můžete kliknout na **Uložit**. Nebo můžete změny **Zrušit** .
    :::image type="content" source="./media/how-to-configure-audit-logs-portal/save-parameters.png" alt-text="Parametry serveru":::

## <a name="set-up-diagnostics"></a>Nastavení diagnostiky

Protokoly auditu jsou integrované s nastavením diagnostiky Azure Monitor, aby bylo možné přesměrovat protokoly do Azure Monitor protokolů, Event Hubs nebo Azure Storage.

1. V části **monitorování** na bočním panelu vyberte **nastavení diagnostiky**.

1. Klikněte na + Přidat nastavení diagnostiky :::image type="content" source="./media/how-to-configure-audit-logs-portal/add-diagnostic-setting.png" alt-text="Parametry serveru"::: .

1. Zadejte název nastavení diagnostiky.

1. Určete, která cílová umístění mají Odeslat protokoly auditu (účet úložiště, centrum událostí a pracovní prostor Log Analytics).

1. Jako typ protokolu vyberte **MySqlAuditLogs** .
    :::image type="content" source="./media/how-to-configure-audit-logs-portal/configure-diagnostic-setting.png" alt-text="Parametry serveru":::

1. Po nakonfigurování datových umyvadel pro přesměrování protokolů auditu na můžete kliknout na **Uložit**.
    :::image type="content" source="./media/how-to-configure-audit-logs-portal/save-diagnostic-setting.png" alt-text="Parametry serveru":::

1. Přihlaste se k protokolům auditu jejich zkoumáním v datových jímkach, které jste nakonfigurovali. Zobrazení protokolů může trvat až 10 minut.

Pokud jste nahlásili protokoly auditu do Azure Monitor protokolů (Log Analytics), přečtěte si několik [ukázkových dotazů](concepts-audit-logs.md#analyze-logs-in-azure-monitor-logs) , které můžete použít k analýze.  

## <a name="next-steps"></a>Další kroky

- Další informace o [protokolech auditu](concepts-audit-logs.md)
- Další informace o [protokolech pomalých dotazů](concepts-slow-query-logs.md)
<!-- - Learn how to configure audit logs in the [Azure CLI](howto-configure-audit-logs-cli.md)-->