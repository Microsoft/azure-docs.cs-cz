---
title: Konfigurace TLS-Azure Portal-Azure Database for MariaDB
description: Naučte se nastavit konfiguraci TLS pomocí Azure Portal pro Azure Database for MariaDB
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 063fc82131a66012c7329f590c0fdf2ce4d4da79
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/14/2020
ms.locfileid: "79375002"
---
# <a name="configuring-tls-settings-in-azure-database-for-mariadb-using-azure-portal"></a>Konfigurace nastavení TLS v Azure Database for MariaDB pomocí Azure Portal

Tento článek popisuje, jak můžete nakonfigurovat server Azure Database for MariaDB tak, aby vynutil připojení k minimální verzi protokolu TLS, aby bylo možné projít a odepřít všechna připojení s nižší verzí protokolu TLS, a tím zvýšit zabezpečení sítě.

Zákazníci teď mají možnost vyhovět verzi TLS pro připojení ke svým Azure Database for MariaDB nastavením minimální verze protokolu TLS pro svůj databázový server. Například nastavení minimální verze nastavení TLS na TLS 1,0 znamená, že váš server umožní připojení z klientů pomocí protokolu TLS 1,0, 1,1 a 1,2 +. Případně můžete tuto hodnotu nastavit na 1,2 znamená, že povolíte připojení pouze z klientů pomocí protokolu TLS 1,2 a všechna připojení pomocí protokolu TLS 1,0 a TLS 1,1 budou odmítnuty.

## <a name="prerequisites"></a>Předpoklady

K dokončení této příručce s postupy, potřebujete:

* [Azure Database for MariaDB](quickstart-create-mariaDB-server-database-using-azure-portal.md)

## <a name="set-tls-configurations-for-azure-database-for-mariadb"></a>Nastavit konfigurace TLS pro Azure Database for MariaDB

Při nastavování minimální verze protokolu TLS serveru MariaDB použijte následující postup:

1. V [Azure Portal](https://portal.azure.com/)vyberte svůj existující Azure Database for MariaDB Server.

1. Na stránce Server MariaDB v části **Nastavení**klikněte na **zabezpečení připojení** . tím otevřete stránku konfigurace zabezpečení připojení.

1. V **minimální verzi protokolu TLS**vyberte **1,2** pro zamítnutí připojení s protokolem TLS nižším než TLS 1,2 pro váš server MariaDB.

    ![Konfigurace Azure Database for MariaDB TLS](./media/howto-tls-configurations/tls-configurations.png)

1. Kliknutím na **Uložit** uložte změny.

1. Oznámení ověří, že nastavení zabezpečení připojení bylo úspěšně povoleno.

    ![Úspěšná konfigurace TLS Azure Database for MariaDB](./media/howto-tls-configurations/tls-configurations-success.png)

## <a name="next-steps"></a>Další kroky

Přečtěte si informace o [tom, jak vytvářet výstrahy na metrikách](howto-alert-metric.md).
