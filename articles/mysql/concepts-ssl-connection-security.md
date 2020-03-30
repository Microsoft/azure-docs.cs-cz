---
title: Připojení SSL – Azure Database for MySQL
description: Informace pro konfiguraci Azure Database pro MySQL a přidružené aplikace správně používat připojení SSL
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 6a12ef851823ab5eff2b11905d05be1950c82ef0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79474267"
---
# <a name="ssl-connectivity-in-azure-database-for-mysql"></a>Připojení SSL v Azure Database for MySQL

Azure Database for MySQL podporuje připojení databázového serveru ke klientským aplikacím pomocí ssl (Secure Sockets Layer). Díky vynucování připojení SSL mezi databázovým serverem a klientskými aplikacemi se šifruje datový proud mezi serverem a vaší aplikací, což pomáhá chránit před napadením útočníky, kteří se vydávají za prostředníky.

## <a name="ssl-default-settings"></a>Výchozí nastavení protokolu SSL

Ve výchozím nastavení by měla být databázová služba nakonfigurována tak, aby při připojování k MySQL vyžadovala připojení SSL.  Doporučujeme, abyste se vyhnuli zakázání možnosti SSL, kdykoli je to možné.

Při zřizování nové databáze Azure pro mySQL server prostřednictvím portálu Azure a CLI je vynucení připojení SSL ve výchozím nastavení povoleno. 

Připojovací řetězce pro různé programovací jazyky jsou zobrazeny na webu Azure Portal. Tyto připojovací řetězce obsahují požadované parametry SSL pro připojení k databázi. Na webu Azure Portal vyberte svůj server. V záhlaví **Nastavení** vyberte **připojovací řetězce**. Parametr SSL se liší v závislosti na konektoru, například "ssl=true" nebo "sslmode=require" nebo "sslmode=required" a dalších variantách.

Informace o povolení nebo zakázání připojení SSL při vývoji aplikace naleznete v najdete v [odkazech Jak nakonfigurovat protokol SSL](howto-configure-ssl.md).

## <a name="next-steps"></a>Další kroky

[Knihovny připojení pro Azure Database for MySQL](concepts-connection-libraries.md)
