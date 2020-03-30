---
title: Připojení SSL – databáze Azure pro MariaDB
description: Informace pro konfiguraci Azure Database pro MariaDB a přidružené aplikace správně používat připojení SSL
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 36532575645d135a7abe7239798b6f2abc4246f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477064"
---
# <a name="ssl-connectivity-in-azure-database-for-mariadb"></a>Připojení SSL v Azure Database pro MariaDB
Azure Database for MariaDB podporuje připojení databázového serveru ke klientským aplikacím pomocí ssl (Secure Sockets Layer). Díky vynucování připojení SSL mezi databázovým serverem a klientskými aplikacemi se šifruje datový proud mezi serverem a vaší aplikací, což pomáhá chránit před napadením útočníky, kteří se vydávají za prostředníky.

## <a name="default-settings"></a>Výchozí nastavení
Ve výchozím nastavení by měla být databázová služba nakonfigurována tak, aby při připojování k MariaDB vyžadovala připojení SSL.  Doporučujeme, abyste se vyhnuli zakázání možnosti SSL, kdykoli je to možné.

Při zřizování nové databáze Azure pro server MariaDB prostřednictvím portálu Azure a cli, vynucení připojení SSL je povoleno ve výchozím nastavení.

Připojovací řetězce pro různé programovací jazyky jsou zobrazeny na webu Azure Portal. Tyto připojovací řetězce obsahují požadované parametry SSL pro připojení k databázi. Na webu Azure Portal vyberte svůj server. V záhlaví **Nastavení** vyberte **připojovací řetězce**. Parametr SSL se liší v závislosti na konektoru, například "ssl=true" nebo "sslmode=require" nebo "sslmode=required" a dalších variantách.

Informace o povolení nebo zakázání připojení SSL při vývoji aplikace naleznete v najdete v [odkazech Jak nakonfigurovat protokol SSL](howto-configure-ssl.md).

## <a name="next-steps"></a>Další kroky
- Další informace o [pravidlech brány firewall serveru](concepts-firewall-rules.md)
- Přečtěte si, jak [nakonfigurovat protokol SSL](howto-configure-ssl.md).
