---
title: Konfigurace TLS-Azure Portal-Azure Database for PostgreSQL – jeden server
description: Naučte se nastavit konfiguraci TLS pomocí Azure Portal pro Azure Database for PostgreSQL jeden server.
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 049b30689c86881ce89d4fbcd54175ee1ee5497e
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/14/2020
ms.locfileid: "79375106"
---
# <a name="configuring-tls-settings-in-azure-database-for-postgresql---single-server-using-azure-portal"></a>Konfigurace nastavení TLS v Azure Database for PostgreSQL-Single server pomocí Azure Portal

Tento článek popisuje, jak můžete nakonfigurovat Azure Database for PostgreSQL jeden server, abyste vynutili minimální verzi protokolu TLS pro všechna příchozí připojení, což pomáhá zlepšit zabezpečení sítě.

Zákazníci teď mají možnost vyhovět verzi protokolu TLS pro připojení k jejich Azure Database for PostgreSQLmu jednomu serveru. Zákazníci teď mají možnost nastavit minimální verzi protokolu TLS pro svůj databázový server. Například nastavení minimální verze nastavení TLS na TLS 1,0 znamená, že váš server umožní připojení z klientů pomocí protokolu TLS 1,0, 1,1 a 1,2 +. Případně můžete tuto hodnotu nastavit na 1,2 znamená, že povolíte připojení pouze z klientů pomocí protokolu TLS 1,2 a všechna připojení pomocí protokolu TLS 1,0 a TLS 1,1 budou odmítnuty.

## <a name="prerequisites"></a>Předpoklady

K dokončení této příručce s postupy, potřebujete:

* [Azure Database for PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="set-tls-configurations-for-azure-database-for-postgresql---single-server"></a>Nastavení konfigurace TLS pro Azure Database for PostgreSQL – jeden server

Postupujte podle těchto kroků a nastavte minimální verzi protokolu TLS pro PostgreSQL jeden server:

1. V [Azure Portal](https://portal.azure.com/)vyberte svůj existující Azure Database for PostgreSQL jeden server.

1.  Na stránce Azure Database for PostgreSQL-Single server klikněte v části **Nastavení**na **zabezpečení připojení** a otevřete stránku konfigurace zabezpečení připojení.

1. V **minimální verzi protokolu TLS**vyberte **1,2** pro zamítnutí připojení s protokolem TLS nižším než TLS 1,2 pro váš PostgreSQL Server.

    ![Azure Database for PostgreSQL konfigurace TLS s jedním serverem](./media/howto-tls-configurations/setting-tls-value.png)

1. Kliknutím na **Uložit** uložte změny.

1. Oznámení ověří, že nastavení zabezpečení připojení bylo úspěšně povoleno.

    ![Azure Database for PostgreSQL – úspěšná konfigurace TLS na jednom serveru](./media/howto-tls-configurations/setting-tls-value-success.png)

## <a name="next-steps"></a>Další kroky

Přečtěte si informace o [tom, jak vytvářet výstrahy na metrikách](howto-alert-on-metric.md).
