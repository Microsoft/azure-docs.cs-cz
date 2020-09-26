---
title: Konfigurace pomalých protokolů dotazů – Azure Portal-Azure Database for MySQL-flexibilní Server
description: Tento článek popisuje, jak nakonfigurovat a přistupovat k protokolům pomalých dotazů v Azure Database for MySQL flexibilní Server z Azure Portal.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: 1a829f4e5f45394d9c5bbed0db9289727b816917
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91315027"
---
# <a name="configure-and-access-slow-query-logs-for-azure-database-for-mysql---flexible-server-using-the-azure-portal"></a>Konfigurace a přístup k protokolům pomalým dotazům pro Azure Database for MySQL-flexibilní Server pomocí Azure Portal

> [!IMPORTANT]
> Azure Database for MySQL – flexibilní Server je momentálně ve verzi Public Preview.

V Azure Portal můžete nakonfigurovat, vypsat a stáhnout Azure Database for MySQL flexibilního [dotazování serveru protokoly](concepts-slow-query-logs.md) .

## <a name="prerequisites"></a>Požadavky
Kroky v tomto článku vyžadují, abyste měli [flexibilní Server](quickstart-create-server-portal.md).

## <a name="configure-logging"></a>Konfigurovat protokolování
Nakonfigurujte přístup k protokolu pomalého dotazu MySQL. 

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

1. Vyberte flexibilní Server.

1. V části **Nastavení** na bočním panelu vyberte **parametry serveru**.
   <!-- :::image type="content" source="./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png" alt-text="Screenshot of Server logs options":::-->

1. Aktualizujte parametr **slow_query_log** na **zapnuto**.

1. Změňte všechny potřebné parametry (např. `long_query_time`, `log_slow_admin_statements`). Další parametry najdete v dokumentaci k [protokolům pomalým dotazům](./concepts-slow-query-logs.md#configure-slow-query-logging) .  

1. Vyberte **Uložit**. 

   <!-- :::image type="content" source="./media/howto-configure-server-logs-in-portal/3-save-discard.png" alt-text="Screenshot of slow query log parameters and save."::: -->

Na stránce **parametry serveru** se můžete vrátit do seznamu protokolů tím, že stránku zavřete.

## <a name="set-up-diagnostics"></a>Nastavení diagnostiky

> [!NOTE]
> Integrace s Azure Monitor nastavení diagnostiky pro přístup k protokolům probíhá nasazování a kompletní funkce budou brzy k dispozici.

Pomalé protokoly dotazů jsou integrované s Azure Monitor nastavení diagnostiky, které vám umožní přesměrovat protokoly do Azure Monitor protokolů, Event Hubs nebo Azure Storage.

1. V části **monitorování** na bočním panelu vyberte **nastavení diagnostiky**  >  **Přidat nastavení diagnostiky**.

   <!--:::image type="content" source="./media/howto-configure-server-logs-in-portal/add-diagnostic-setting.png" alt-text="Screenshot of Diagnostic settings options":::-->

1. Zadejte název nastavení diagnostiky.

1. Určete, která cílová umístění mají Odeslat protokoly pomalých dotazů (účet úložiště, centrum událostí nebo Log Analytics pracovní prostor).

1. Jako typ protokolu vyberte **MySqlSlowLogs** .
    <!--:::image type="content" source="./media/howto-configure-server-logs-in-portal/configure-diagnostic-setting.png" alt-text="Screenshot of Diagnostic settings configuration options":::-->

1. Po nakonfigurování datových umyvadel pro přesměrování protokolů pomalých dotazů na vyberte **Uložit**.
    <!--:::image type="content" source="./media/howto-configure-server-logs-in-portal/save-diagnostic-setting.png" alt-text="Screenshot of Diagnostic settings configuration options, with Save highlighted":::-->

1. Přístup k protokolům pomalým dotazům můžete prozkoumat v datech, která jste nakonfigurovali. Zobrazení protokolů může trvat až 10 minut.

Pokud jste do kanálu Azure Monitor protokoly (Log Analytics), podívejte se na některé [Ukázkové dotazy](concepts-audit-logs.md#analyze-logs-in-azure-monitor-logs) , které můžete použít k analýze. 

## <a name="next-steps"></a>Další kroky
<!-- - See [Access slow query Logs in CLI](howto-configure-server-logs-in-cli.md) to learn how to download slow query logs programmatically.-->
- Další informace o [protokolech pomalých dotazů](concepts-slow-query-logs.md)
- Další informace o definicích parametrů a protokolování MySQL najdete v dokumentaci k MySQL v [protokolech](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html).
- Další informace o [protokolech auditu](concepts-audit-logs.md)
