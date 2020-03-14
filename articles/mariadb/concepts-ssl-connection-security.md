---
title: Připojení SSL – Azure Database for MariaDB
description: Informace o konfiguraci Azure Database for MariaDB a přidružených aplikací pro správné používání připojení SSL
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: c03a56176a6e2cc995e74017b60747541fc843bb
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/14/2020
ms.locfileid: "79371626"
---
# <a name="ssl-connectivity-in-azure-database-for-mariadb"></a>Připojení SSL v Azure Database for MariaDB
Azure Database for MariaDB podporuje připojení vašeho databázového serveru k klientským aplikacím pomocí SSL (Secure Sockets Layer) (SSL). Díky vynucování připojení SSL mezi databázovým serverem a klientskými aplikacemi se šifruje datový proud mezi serverem a vaší aplikací, což pomáhá chránit před napadením útočníky, kteří se vydávají za prostředníky.

## <a name="default-settings"></a>Výchozí nastavení
Ve výchozím nastavení by databázová služba měla být nakonfigurovaná tak, aby při připojení k MariaDB vyžadovala připojení SSL.  Doporučujeme, abyste se vyhnuli vypnutí možnosti SSL, kdykoli to bude možné.

Při zřizování nového serveru Azure Database for MariaDB prostřednictvím Azure Portal a CLI je vynucování připojení SSL ve výchozím nastavení povolené.

V Azure Portal se zobrazují připojovací řetězce pro různé programovací jazyky. Tyto připojovací řetězce zahrnují požadované parametry SSL pro připojení k vaší databázi. V Azure Portal vyberte svůj server. V záhlaví **Nastavení** vyberte **připojovací řetězce**. Parametr SSL se liší v závislosti na konektoru, například "SSL = true" nebo "sslmode = vyžadovat" nebo "sslmode = Required" a další variace.

Informace o tom, jak povolit nebo zakázat připojení SSL při vývoji aplikace, najdete v tématu [Postup konfigurace protokolu SSL](howto-configure-ssl.md).

## <a name="tls-connectivity-in-azure-database-for-mariadb"></a>Připojení TLS v Azure Database for MariaDB

Azure Database for MariaDB podporuje šifrování pro klienty připojující se k databázovému serveru pomocí protokolu TLS (Transport Layer Security). TLS je průmyslový standardní protokol, který zajišťuje Zabezpečená síťová připojení mezi databázovým serverem a klientskými aplikacemi, což vám umožní dodržovat požadavky na dodržování předpisů.

### <a name="tls-settings"></a>Nastavení TLS

Azure Database for MariaDB poskytuje možnost vyhovět verzi TLS pro připojení klienta. Chcete-li použít možnost TLS, použijte nastavení možnosti **Minimální verze protokolu TLS** . Pro toto nastavení možností jsou povoleny následující hodnoty:

|  Minimální nastavení TLS             | Podporovaná verze TLS                |
|:---------------------------------|-------------------------------------:|
| TLSEnforcementDisabled (výchozí) | Není vyžadován protokol TLS                      |
| TLS1_0                           | TLS 1,0, TLS 1,1, TLS 1,2 a vyšší |
| TLS1_1                           | TLS 1,1, TLS 1,2 a vyšší          |
| TLS1_2                           | TLS verze 1,2 a vyšší           |


Například nastavení minimální verze nastavení TLS na TLS 1,0 znamená, že váš server umožní připojení z klientů pomocí protokolu TLS 1,0, 1,1 a 1,2 +. Případně můžete tuto hodnotu nastavit na 1,2 znamená, že povolíte připojení pouze z klientů pomocí protokolu TLS 1,2 a všechna připojení pomocí protokolu TLS 1,0 a TLS 1,1 budou odmítnuty.

> [!Note] 
> Pro všechny nové servery Azure Database for MariaDB výchozí nastavení TLS zakázáno.
>
> V současné době jsou verze TLS podporované byAzure databází pro MariaDB jsou TLS 1,0, 1,1 a 1,2.

Informace o nastavení nastavení TLS pro Azure Database for MariaDB najdete v tématu [Konfigurace nastavení TLS](howto-tls-configurations.md).

## <a name="next-steps"></a>Další kroky
- Další informace o [pravidlech brány firewall serveru](concepts-firewall-rules.md)
- Přečtěte si, jak [nakonfigurovat SSL](howto-configure-ssl.md).
- Přečtěte si, jak [nakonfigurovat TLS](howto-tls-configurations.md).