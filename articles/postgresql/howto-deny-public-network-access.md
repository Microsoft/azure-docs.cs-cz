---
title: Odepřít přístup k veřejné síti – portál Azure – databáze Azure pro PostgreSQL – jeden server
description: Přečtěte si, jak nakonfigurovat možnost Odepřít přístup k veřejné síti pomocí portálu Azure pro váš server Azure Database for PostgreSQL Single
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 4dff2321414721dbd415b468e59aea0ab4b3acee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79375119"
---
# <a name="deny-public-network-access-in-azure-database-for-postgresql-single-server-using-azure-portal"></a>Odepření přístupu k veřejné síti v databázi Azure pro server PostgreSQL pomocí portálu Azure

Tento článek popisuje, jak můžete nakonfigurovat azure databáze pro postgreSQL jeden server odepřít všechny veřejné konfigurace a povolit pouze připojení prostřednictvím privátní koncové body k dalšímu zvýšení zabezpečení sítě.

## <a name="prerequisites"></a>Požadavky

Chcete-li dokončit tento návod, potřebujete:

* [Databáze Azure pro server PostgreSQL Single](quickstart-create-PostgreSQL Single server-server-database-using-azure-portal.md)

## <a name="set-deny-public-network-access"></a>Nastavit odepřít přístup k veřejné síti

Chcete-li nastavit přístup k veřejné síti postgreSQL single server, postupujte takto:

1. Na [webu Azure Portal](https://portal.azure.com/)vyberte stávající databázi Azure pro server PostgreSQL Single.

1. Na stránce PostgreSQL Single server v části **Nastavení**klepnutím na tlačítko **Zabezpečení připojení** otevřete stránku konfigurace zabezpečení připojení.

1. V **příkazu Odepřít přístup k veřejné síti**vyberte Možnost **Ano,** chcete-li povolit odepření veřejného přístupu pro váš server PostgreSQL Single.

    ![Databáze Azure pro server PostgreSQL Single Odepřít přístup k síti](./media/howto-deny-public-network-access/deny-public-network-access.PNG)

1. Kliknutím na **Uložit** uložte změny.

1. Oznámení potvrdí, že nastavení zabezpečení připojení bylo úspěšně povoleno.

    ![Databáze Azure pro server PostgreSQL Single Odepřít úspěšný přístup k síti](./media/howto-deny-public-network-access/deny-public-network-access-success.png)

## <a name="next-steps"></a>Další kroky

Přečtěte [si, jak vytvářet upozornění na metriky](howto-alert-on-metric.md).