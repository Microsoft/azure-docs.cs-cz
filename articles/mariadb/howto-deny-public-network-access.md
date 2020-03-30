---
title: Odepřít přístup k veřejné síti – portál Azure – databáze Azure pro MariaDB
description: Přečtěte si, jak nakonfigurovat možnost Odepřít přístup k veřejné síti pomocí portálu Azure pro databázi Azure pro MariaDB.
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 87b6033e486e9009573436628d0183c8a022aced
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79375236"
---
# <a name="deny-public-network-access-in-azure-database-for-mariadb-using-azure-portal"></a>Odepření přístupu k veřejné síti v databázi Azure pro MariaDB pomocí portálu Azure

Tento článek popisuje, jak můžete nakonfigurovat Azure Database pro MariaDB server odepřít všechny veřejné konfigurace a povolit pouze připojení prostřednictvím privátní koncové body k dalšímu zvýšení zabezpečení sítě.

## <a name="prerequisites"></a>Požadavky

Chcete-li dokončit tento návod, potřebujete:

* [Databáze Azure pro MariaDB](quickstart-create-MariaDB-server-database-using-azure-portal.md)

## <a name="set-deny-public-network-access"></a>Nastavit odepřít přístup k veřejné síti

Chcete-li nastavit přístup k veřejné síti serveru MariaDB, postupujte takto:

1. Na [webu Azure Portal](https://portal.azure.com/)vyberte stávající azure databázi pro server MariaDB.

1. Na stránce serveru MariaDB v části **Nastavení**klepněte na tlačítko **Zabezpečení připojení** a otevřete stránku konfigurace zabezpečení připojení.

1. V příkazu Odepřít přístup k veřejné síti vyberte **Ano,** chcete-li povolit odepření veřejného přístupu pro server MariaDB.

    ![Databáze Azure pro MariaDB Odepřít přístup k síti](./media/howto-deny-public-network-access/deny-public-network-access.PNG)

1. Kliknutím na **Uložit** uložte změny.

1. Oznámení potvrdí, že nastavení zabezpečení připojení bylo úspěšně povoleno.

    ![Azure Database for MariaDB Deny network access success](./media/howto-deny-public-network-access/deny-public-network-access-success.png)

## <a name="next-steps"></a>Další kroky

Přečtěte [si, jak vytvářet upozornění na metriky](howto-alert-metric.md).