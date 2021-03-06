---
title: Odepřít přístup k veřejné síti – Azure Portal-Azure Database for MariaDB
description: Naučte se konfigurovat přístup k veřejné síti pomocí Azure Portal pro Azure Database for MariaDB
author: mksuni
ms.author: sumuth
ms.service: mariadb
ms.topic: how-to
ms.date: 03/10/2020
ms.openlocfilehash: 3117ebfd258c72bb97432871c2ea74d30c52f669
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98665068"
---
# <a name="deny-public-network-access-in-azure-database-for-mariadb-using-azure-portal"></a>Odepřít přístup k veřejné síti v Azure Database for MariaDB pomocí Azure Portal

Tento článek popisuje, jak můžete nakonfigurovat server Azure Database for MariaDB tak, aby odepřel všechny veřejné konfigurace a povoloval jenom připojení prostřednictvím privátních koncových bodů, aby se zvýšilo zabezpečení sítě.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto průvodce budete potřebovat:

* [Azure Database for MariaDB](quickstart-create-MariaDB-server-database-using-azure-portal.md)

## <a name="set-deny-public-network-access"></a>Nastavit přístup k veřejné síti odepřít

Pomocí těchto kroků nastavte MariaDB Server odepřít přístup k veřejné síti:

1. V [Azure Portal](https://portal.azure.com/)vyberte svůj existující Azure Database for MariaDB Server.

1. Na stránce Server MariaDB v části **Nastavení** klikněte na **zabezpečení připojení** . tím otevřete stránku konfigurace zabezpečení připojení.

1. V nástroji odepřít přístup k veřejné síti vyberte **Ano** , pokud chcete povolit přístup pro odepření přístupu pro váš server MariaDB.

    ![Azure Database for MariaDB odepřít přístup k síti](./media/howto-deny-public-network-access/deny-public-network-access.PNG)

1. Kliknutím na **Uložit** uložte změny.

1. Oznámení ověří, že nastavení zabezpečení připojení bylo úspěšně povoleno.

    ![Azure Database for MariaDB odepřít úspěšné přístupy k síti](./media/howto-deny-public-network-access/deny-public-network-access-success.png)

## <a name="next-steps"></a>Další kroky

Přečtěte si informace o [tom, jak vytvářet výstrahy na metrikách](howto-alert-metric.md).