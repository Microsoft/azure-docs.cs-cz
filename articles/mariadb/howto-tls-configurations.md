---
title: Konfigurace TLS-Azure Portal-Azure Database for MariaDB
description: Naučte se nastavit konfiguraci TLS pomocí Azure Portal pro Azure Database for MariaDB
author: mksuni
ms.author: sumuth
ms.service: mariadb
ms.topic: how-to
ms.date: 06/02/2020
ms.openlocfilehash: 55a664ceb3524a0c545e037dcba7a2af90034ad4
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/03/2020
ms.locfileid: "93241980"
---
# <a name="configuring-tls-settings-in-azure-database-for-mariadb-using-azure-portal"></a>Konfigurace nastavení TLS v Azure Database for MariaDB pomocí Azure Portal

Tento článek popisuje, jak můžete nakonfigurovat server Azure Database for MariaDB tak, aby vynutil minimální verzi TLS pro připojení k nástroji a odepřel všechna připojení s nižší verzí TLS, než je nakonfigurovaná minimální verze protokolu TLS, a tím rozšiřuje zabezpečení sítě.

Verzi TLS můžete vynutili pro připojení k jejich Azure Database for MariaDB nastavením minimální verze protokolu TLS pro svůj databázový server. Například nastavení minimální verze nastavení TLS na TLS 1,0 znamená, že váš server umožní připojení z klientů pomocí protokolu TLS 1,0, 1,1 a 1,2 +. Další možností je nastavení na 1,2 znamená, že povolíte připojení jenom z klientů pomocí protokolu TLS 1.2 + a všechna připojení k TLS 1,0 a TLS 1,1 budou odmítnutá.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto průvodce budete potřebovat:

* [Azure Database for MariaDB](quickstart-create-mariaDB-server-database-using-azure-portal.md)

## <a name="set-tls-configurations-for-azure-database-for-mariadb"></a>Nastavit konfigurace TLS pro Azure Database for MariaDB

Při nastavování minimální verze protokolu TLS serveru MariaDB použijte následující postup:

1. V [Azure Portal](https://portal.azure.com/)vyberte svůj existující Azure Database for MariaDB Server.

1. Na stránce Server MariaDB v části **Nastavení** klikněte na **zabezpečení připojení** . tím otevřete stránku konfigurace zabezpečení připojení.

1. V **minimální verzi protokolu TLS** vyberte **1,2** pro zamítnutí připojení s protokolem TLS nižším než TLS 1,2 pro váš server MariaDB.

    ![Konfigurace Azure Database for MariaDB TLS](./media/howto-tls-configurations/tls-configurations.png)

1. Kliknutím na **Uložit** uložte změny.

1. Oznámení ověří, že nastavení zabezpečení připojení bylo úspěšně povoleno.

    ![Úspěšná konfigurace TLS Azure Database for MariaDB](./media/howto-tls-configurations/tls-configurations-success.png)

## <a name="next-steps"></a>Další kroky

Informace o [tom, jak vytvářet výstrahy na metrikách](howto-alert-metric.md)