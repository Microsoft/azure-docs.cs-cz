---
title: Připojení SSL pro službu Azure Database pro MariaDB
description: Informace pro konfiguraci – Azure Database pro MariaDB a přidružené aplikace pro správné použití připojení SSL
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 15bb28846b3409dd31bcdf8d42990facc94fd06d
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2018
ms.locfileid: "53539598"
---
# <a name="ssl-connectivity-in-azure-database-for-mariadb"></a>Připojení SSL ve službě Azure Database pro MariaDB
Azure Database pro MariaDB podporuje připojení databázový server pro klientské aplikace pomocí vrstvy SSL (Secure Sockets). Díky vynucování připojení SSL mezi databázovým serverem a klientskými aplikacemi se šifruje datový proud mezi serverem a vaší aplikací, což pomáhá chránit před napadením útočníky, kteří se vydávají za prostředníky.

## <a name="default-settings"></a>Výchozí nastavení
Ve výchozím nastavení databázová služba musí být nakonfigurovaný tak, aby vyžadovala připojení SSL při připojování k MariaDB.  Doporučujeme, aby bylo zabráněno zakázání možnost SSL, kdykoli je to možné.

Při zřizování novou službu Azure Database pro MariaDB server na webu Azure portal a rozhraní příkazového řádku, je standardně povolená vynucení připojení SSL.

Připojovací řetězce pro různé programovací jazyky jsou uvedeny na webu Azure Portal. Tyto řetězce připojení obsahovat požadované parametry protokolu SSL pro připojení k vaší databázi. Na webu Azure Portal vyberte svůj server. V části **nastavení** záhlaví, vyberte **připojovací řetězce**. Parametr SSL se liší v závislosti na konektoru, třeba "ssl = true" nebo "sslmode = vyžadují" nebo "sslmode = požadované" a další varianty konfigurací.

Zjistěte, jak povolit nebo zakázat připojení protokolem SSL při vývoji aplikací, najdete v tématu [konfigurace SSL](howto-configure-ssl.md).

## <a name="next-steps"></a>Další postup
- Další informace o [pravidla brány firewall serveru](concepts-firewall-rules.md)
- Zjistěte, jak [konfigurace protokolu SSL](howto-configure-ssl.md).
