---
title: Odepřít přístup k veřejné síti – Azure Portal-Azure Database for MySQL
description: Naučte se konfigurovat přístup k veřejné síti pomocí Azure Portal pro Azure Database for MySQL
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: b5f93f3a3583900810ca75f925c6a88df9102652
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "79374950"
---
# <a name="deny-public-network-access-in-azure-database-for-mysql-using-azure-portal"></a>Odepřít přístup k veřejné síti v Azure Database for MySQL pomocí Azure Portal

Tento článek popisuje, jak můžete nakonfigurovat server Azure Database for MySQL tak, aby odepřel všechny veřejné konfigurace a povoloval jenom připojení prostřednictvím privátních koncových bodů, aby se zvýšilo zabezpečení sítě.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto průvodce budete potřebovat:

* [Azure Database for MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="set-deny-public-network-access"></a>Nastavit přístup k veřejné síti odepřít

Pomocí těchto kroků nastavte přístup k veřejné síti serveru MySQL:

1. V [Azure Portal](https://portal.azure.com/)vyberte svůj existující Azure Database for MySQL server.

1. Na stránce serveru MySQL v části **Nastavení**klikněte na **zabezpečení připojení** . tím otevřete stránku konfigurace zabezpečení připojení.

1. V nástroji **Odepřít přístup k veřejné síti**vyberte **Ano** , pokud chcete povolit přístup odepřít veřejný přístup pro váš server MySQL.

    ![Azure Database for MySQL odepřít přístup k síti](./media/howto-deny-public-network-access/setting-deny-public-network-access.PNG)

1. Kliknutím na **Uložit** uložte změny.

1. Oznámení ověří, že nastavení zabezpečení připojení bylo úspěšně povoleno.

    ![Azure Database for MySQL odepřít úspěšné přístupy k síti](./media/howto-deny-public-network-access/setting-deny-public-network-access-success.png)

## <a name="next-steps"></a>Další kroky

Přečtěte si informace o [tom, jak vytvářet výstrahy na metrikách](howto-alert-on-metric.md).