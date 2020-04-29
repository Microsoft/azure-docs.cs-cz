---
title: Připojení SSL – Azure Database for MariaDB
description: Informace o konfiguraci Azure Database for MariaDB a přidružených aplikací pro správné používání připojení SSL
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 36532575645d135a7abe7239798b6f2abc4246f2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "79477064"
---
# <a name="ssl-connectivity-in-azure-database-for-mariadb"></a>Připojení SSL v Azure Database for MariaDB
Azure Database for MariaDB podporuje připojení vašeho databázového serveru k klientským aplikacím pomocí SSL (Secure Sockets Layer) (SSL). Díky vynucování připojení SSL mezi databázovým serverem a klientskými aplikacemi se šifruje datový proud mezi serverem a vaší aplikací, což pomáhá chránit před napadením útočníky, kteří se vydávají za prostředníky.

## <a name="default-settings"></a>Výchozí nastavení
Ve výchozím nastavení by databázová služba měla být nakonfigurovaná tak, aby při připojení k MariaDB vyžadovala připojení SSL.  Doporučujeme, abyste se vyhnuli vypnutí možnosti SSL, kdykoli to bude možné.

Při zřizování nového serveru Azure Database for MariaDB prostřednictvím Azure Portal a CLI je vynucování připojení SSL ve výchozím nastavení povolené.

V Azure Portal se zobrazují připojovací řetězce pro různé programovací jazyky. Tyto připojovací řetězce zahrnují požadované parametry SSL pro připojení k vaší databázi. V Azure Portal vyberte svůj server. V záhlaví **Nastavení** vyberte **připojovací řetězce**. Parametr SSL se liší v závislosti na konektoru, například "SSL = true" nebo "sslmode = vyžadovat" nebo "sslmode = Required" a další variace.

Informace o tom, jak povolit nebo zakázat připojení SSL při vývoji aplikace, najdete v tématu [Postup konfigurace protokolu SSL](howto-configure-ssl.md).

## <a name="next-steps"></a>Další kroky
- Další informace o [pravidlech brány firewall serveru](concepts-firewall-rules.md)
- Přečtěte si, jak [nakonfigurovat SSL](howto-configure-ssl.md).
