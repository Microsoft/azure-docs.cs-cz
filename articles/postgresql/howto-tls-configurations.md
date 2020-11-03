---
title: Konfigurace TLS-Azure Portal-Azure Database for PostgreSQL – jeden server
description: Naučte se nastavit konfiguraci TLS pomocí Azure Portal pro Azure Database for PostgreSQL jeden server.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 06/02/2020
ms.openlocfilehash: 25be6b3c4e3172fc8ee14b97fd890b5948c284ba
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/03/2020
ms.locfileid: "93242360"
---
# <a name="configuring-tls-settings-in-azure-database-for-postgresql-single---server-using-azure-portal"></a>Konfigurace nastavení TLS v Azure Database for PostgreSQL jednom serveru pomocí Azure Portal

Tento článek popisuje, jak můžete nakonfigurovat Azure Database for PostgreSQL k prosazování minimální verze protokolu TLS povolené pro připojení a odepřít všechna připojení s nižší verzí TLS, než je nakonfigurovaná minimální verze protokolu TLS, a tím zvýšit zabezpečení sítě.

Verzi TLS můžete vynutili pro připojení k jejich Azure Database for PostgreSQL. Zákazníci teď mají možnost nastavit minimální verzi protokolu TLS pro svůj databázový server. Například nastavení minimální verze nastavení TLS na TLS 1,0 znamená, že váš server umožní připojení z klientů pomocí protokolu TLS 1,0, 1,1 a 1,2 +. Místo toho se nastavení minimální verze protokolu TLS na 1,2 + znamená, že povolíte připojení jenom z klientů pomocí protokolu TLS 1,2 a všechna připojení k TLS 1,0 a TLS 1,1 budou odmítnutá.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto průvodce budete potřebovat:

* [Azure Database for PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="set-tls-configurations-for-azure-database-for-postgresql---single-server"></a>Nastavení konfigurace TLS pro Azure Database for PostgreSQL – jeden server

Pomocí těchto kroků nastavte minimální verzi protokolu TLS pro PostgreSQL:

1. V [Azure Portal](https://portal.azure.com/)vyberte existující Azure Database for PostgreSQL.

1.  Na stránce Azure Database for PostgreSQL-Single server klikněte v části **Nastavení** na **zabezpečení připojení** a otevřete stránku konfigurace zabezpečení připojení.

1. V **minimální verzi protokolu TLS** vyberte **1,2** pro zamítnutí připojení s protokolem TLS nižším než TLS 1,2 pro váš PostgreSQL Server.

    :::image type="content" source="./media/howto-tls-configurations/setting-tls-value.png" alt-text="Azure Database for PostgreSQL konfigurace TLS s jedním serverem":::

1. Kliknutím na **Uložit** uložte změny.

1. Oznámení ověří, že nastavení zabezpečení připojení bylo úspěšně povoleno.

    :::image type="content" source="./media/howto-tls-configurations/setting-tls-value-success.png" alt-text="Azure Database for PostgreSQL konfigurace TLS s jedním serverem":::

## <a name="next-steps"></a>Další kroky

Informace o [tom, jak vytvářet výstrahy na metrikách](howto-alert-on-metric.md)