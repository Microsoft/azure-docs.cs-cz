---
title: Odepřít přístup k veřejné síti – Azure Portal-Azure Database for MySQL
description: Naučte se konfigurovat přístup k veřejné síti pomocí Azure Portal pro Azure Database for MySQL
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: how-to
ms.date: 03/10/2020
ms.openlocfilehash: eb5c4e4c4dfb73b2f7c9dc9f2629296790790885
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90896132"
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

    :::image type="content" source="./media/howto-deny-public-network-access/setting-deny-public-network-access.PNG" alt-text="Azure Database for MySQL odepřít přístup k síti":::

1. Kliknutím na **Uložit** uložte změny.

1. Oznámení ověří, že nastavení zabezpečení připojení bylo úspěšně povoleno.

    :::image type="content" source="./media/howto-deny-public-network-access/setting-deny-public-network-access-success.png" alt-text="Azure Database for MySQL odepřít úspěšné přístupy k síti":::

## <a name="next-steps"></a>Další kroky

Přečtěte si informace o [tom, jak vytvářet výstrahy na metrikách](howto-alert-on-metric.md).