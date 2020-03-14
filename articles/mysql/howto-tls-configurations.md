---
title: Konfigurace TLS-Azure Portal-Azure Database for MySQL
description: Naučte se nastavit konfiguraci TLS pomocí Azure Portal pro Azure Database for MySQL
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 495fea21c4fca12cd1ec6c1ea45105178d72fe1b
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/14/2020
ms.locfileid: "79375288"
---
# <a name="configuring-tls-settings-in-azure-database-for-mysql-using-azure-portal"></a>Konfigurace nastavení TLS v Azure Database for MySQL pomocí Azure Portal

Tento článek popisuje, jak můžete nakonfigurovat server Azure Database for MySQL tak, aby vynutil připojení k minimální verzi protokolu TLS, aby bylo možné projít a odepřít všechna připojení s nižší verzí protokolu TLS, a tím zvýšit zabezpečení sítě.

Zákazníci teď mají možnost vyhovět verzi TLS pro připojení ke svým Azure Database for MySQL. Zákazníci teď mají možnost nastavit minimální verzi protokolu TLS pro svůj databázový server. Například nastavení minimální verze protokolu TLS na 1,0 znamená, že umožníte klientům připojit se pomocí TLS 1.0, 1.1 a 1,2. Případně můžete tuto hodnotu nastavit na 1,2 znamená, že povolíte pouze klienty připojující se pomocí protokolu TLS 1,2 a všechna příchozí připojení s protokolem TLS 1,0 a TLS 1,1 budou odmítnuty.

## <a name="prerequisites"></a>Předpoklady

K dokončení této příručce s postupy, potřebujete:

* [Azure Database for MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="set-tls-configurations-for-azure-database-for-mysql"></a>Nastavit konfigurace TLS pro Azure Database for MySQL

Pomocí těchto kroků nastavte minimální verzi TLS serveru MySQL:

1. V [Azure Portal](https://portal.azure.com/)vyberte svůj existující Azure Database for MySQL server.

1. Na stránce serveru MySQL v části **Nastavení**klikněte na **zabezpečení připojení** . tím otevřete stránku konfigurace zabezpečení připojení.

1. V **minimální verzi protokolu TLS**vyberte **1,2** pro zamítnutí připojení s protokolem TLS nižším než TLS 1,2 pro váš server MySQL.

    ![Konfigurace Azure Database for MySQL TLS](./media/howto-tls-configurations/setting-tls-value.png)

1. Kliknutím na **Uložit** uložte změny.

1. Oznámení ověří, že nastavení zabezpečení připojení bylo úspěšně povoleno.

    ![Úspěšná konfigurace TLS Azure Database for MySQL](./media/howto-tls-configurations/setting-tls-value-success.png)

## <a name="next-steps"></a>Další kroky

Přečtěte si informace o [tom, jak vytvářet výstrahy na metrikách](howto-alert-on-metric.md).
