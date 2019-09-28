---
title: Konfigurace a přístup k protokolům auditu – Azure Database for MySQL
description: Tento článek popisuje, jak nakonfigurovat a přistupovat k protokolům auditu v Azure Database for MySQL z Azure Portal.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 06/24/2019
ms.openlocfilehash: 2a2d2a697f0e41fb296c61c01909a814678f8277
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350407"
---
# <a name="configure-and-access-audit-logs-for-azure-database-for-mysql-in-the-azure-portal"></a>Konfigurace a přístup k protokolům auditu pro Azure Database for MySQL v Azure Portal

Z Azure Portal můžete nakonfigurovat [protokoly auditu Azure Database for MySQL](concepts-audit-logs.md) a nastavení diagnostiky.

> [!IMPORTANT]
> Funkce protokolu auditu je momentálně ve verzi Preview.

## <a name="prerequisites"></a>Požadavky

Pokud chcete projít tento průvodce, budete potřebovat:

- [Server Azure Database for MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="configure-audit-logging"></a>Konfigurace protokolování auditu

Povolte a nakonfigurujte protokolování auditu.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

1. Vyberte váš server Azure Database for MySQL.

1. V části **Nastavení** na bočním panelu vyberte **parametry serveru**.
    ![Parametry serveru](./media/howto-configure-audit-logs-portal/server-parameters.png)

1. Aktualizujte parametr **audit_log_enabled** na zapnuto.
    @no__t – protokoly auditu 0Enable @ no__t-1

1. Vyberte [typy událostí](concepts-audit-logs.md#configure-audit-logging) , které se mají protokolovat, pomocí aktualizace parametru **audit_log_events** .
    @no__t – události protokolu 0Audit @ no__t-1

1. Pomocí aktualizace parametru **audit_log_exclude_users** přidejte všechny uživatele MySQL, které se mají vyloučit z protokolování. Zadejte uživatele pomocí svého uživatelského jména MySQL.
    @no__t – protokol 0Audit vyloučení uživatelů @ no__t-1

1. Po změně parametrů můžete kliknout na **Uložit**. Nebo můžete změny **Zrušit** .
    ![Uložit](./media/howto-configure-audit-logs-portal/save-parameters.png)

## <a name="set-up-diagnostic-logs"></a>Nastavení diagnostických protokolů

1. V části **monitorování** na bočním panelu vyberte **nastavení diagnostiky**.

1. Klikněte na + Přidat nastavení diagnostiky ![Add nastavení diagnostiky @ no__t-1.

1. Zadejte název nastavení diagnostiky.

1. Určete, která datová jímka se mají Odeslat protokoly auditu (účet úložiště, centrum událostí a pracovní prostor Log Analytics).

1. Jako typ protokolu vyberte "MySqlAuditLogs".
![Configure nastavení diagnostiky @ no__t-1

1. Po nakonfigurování datových umyvadel pro přesměrování protokolů auditu na můžete kliknout na **Uložit**.
![Save nastavení diagnostiky @ no__t-1

1. Přihlaste se k protokolům auditu jejich zkoumáním v datových jímkach, které jste nakonfigurovali. Zobrazení protokolů může trvat až 10 minut.

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [protokolech auditu](concepts-audit-logs.md) v Azure Database for MySQL.