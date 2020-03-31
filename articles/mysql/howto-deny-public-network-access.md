---
title: Odepřít přístup k veřejné síti – portál Azure – databáze Azure pro MySQL
description: Přečtěte si, jak nakonfigurovat možnost Odepřít přístup k veřejné síti pomocí portálu Azure pro databázi Azure pro MySQL.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: b5f93f3a3583900810ca75f925c6a88df9102652
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79374950"
---
# <a name="deny-public-network-access-in-azure-database-for-mysql-using-azure-portal"></a>Odepření přístupu k veřejné síti v Azure Database for MySQL pomocí portálu Azure

Tento článek popisuje, jak můžete nakonfigurovat Azure Database for MySQL server odepřít všechny veřejné konfigurace a povolit pouze připojení prostřednictvím privátní koncové body k dalšímu zvýšení zabezpečení sítě.

## <a name="prerequisites"></a>Požadavky

Chcete-li dokončit tento návod, potřebujete:

* [Databáze Azure pro MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="set-deny-public-network-access"></a>Nastavit odepřít přístup k veřejné síti

Chcete-li nastavit přístup k veřejné síti serveru MySQL, postupujte takto:

1. Na [webu Azure Portal](https://portal.azure.com/)vyberte existující Azure Database for MySQL server.

1. Na stránce MySQL server v části **Nastavení**klepnutím na tlačítko **Zabezpečení připojení** otevřete stránku konfigurace zabezpečení připojení.

1. V **příkazu Odepřít přístup k veřejné síti**vyberte Možnost **Ano,** chcete-li povolit odepření veřejného přístupu pro váš server MySQL.

    ![Azure Database for MySQL Odepřít přístup k síti](./media/howto-deny-public-network-access/setting-deny-public-network-access.PNG)

1. Kliknutím na **Uložit** uložte změny.

1. Oznámení potvrdí, že nastavení zabezpečení připojení bylo úspěšně povoleno.

    ![Azure Database for MySQL Deny , úspěch přístupu k síti](./media/howto-deny-public-network-access/setting-deny-public-network-access-success.png)

## <a name="next-steps"></a>Další kroky

Přečtěte [si, jak vytvářet upozornění na metriky](howto-alert-on-metric.md).