---
title: Připojení SSL – Azure Database for MySQL
description: Informace o konfiguraci Azure Database for MySQL a přidružených aplikací pro správné používání připojení SSL
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 7bc3a238ca2ff2861ec6fc65033738e741574321
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/14/2020
ms.locfileid: "79370844"
---
# <a name="ssl-connectivity-in-azure-database-for-mysql"></a>Připojení SSL v Azure Database for MySQL

Azure Database for MySQL podporuje připojení vašeho databázového serveru k klientským aplikacím pomocí SSL (Secure Sockets Layer) (SSL). Díky vynucování připojení SSL mezi databázovým serverem a klientskými aplikacemi se šifruje datový proud mezi serverem a vaší aplikací, což pomáhá chránit před napadením útočníky, kteří se vydávají za prostředníky.

## <a name="ssl-default-settings"></a>Výchozí nastavení SSL

Ve výchozím nastavení by databázová služba měla být nakonfigurovaná tak, aby při připojování k MySQL vyžadovala připojení SSL.  Doporučujeme, abyste se vyhnuli vypnutí možnosti SSL, kdykoli to bude možné.

Při zřizování nového serveru Azure Database for MySQL prostřednictvím Azure Portal a CLI je vynucování připojení SSL ve výchozím nastavení povolené. 

V Azure Portal se zobrazují připojovací řetězce pro různé programovací jazyky. Tyto připojovací řetězce zahrnují požadované parametry SSL pro připojení k vaší databázi. V Azure Portal vyberte svůj server. V záhlaví **Nastavení** vyberte **připojovací řetězce**. Parametr SSL se liší v závislosti na konektoru, například "SSL = true" nebo "sslmode = vyžadovat" nebo "sslmode = Required" a další variace.

Informace o tom, jak povolit nebo zakázat připojení SSL při vývoji aplikace, najdete v tématu [Postup konfigurace protokolu SSL](howto-configure-ssl.md).

## <a name="tls-connectivity-in-azure-database-for-mysql"></a>Připojení TLS v Azure Database for MySQL

Azure Database for MySQL podporuje šifrování pro klienty připojující se k databázovému serveru pomocí protokolu TLS (Transport Layer Security). TLS je průmyslový standardní protokol, který zajišťuje Zabezpečená síťová připojení mezi databázovým serverem a klientskými aplikacemi, což vám umožní dodržovat požadavky na dodržování předpisů.

### <a name="tls-settings"></a>Nastavení TLS

Zákazníci teď mají možnost vymáhat verzi TLS pro klienty připojující se k jejich Azure Database for MySQL. Chcete-li použít možnost TLS, použijte nastavení možnosti **Minimální verze protokolu TLS** . Pro toto nastavení možností jsou povoleny následující hodnoty:

|  Minimální nastavení TLS             | Podporovaná verze TLS                |
|:---------------------------------|-------------------------------------:|
| TLSEnforcementDisabled (výchozí) | Není vyžadován protokol TLS                      |
| TLS1_0                           | TLS 1,0, TLS 1,1, TLS 1,2 a vyšší |
| TLS1_1                           | TLS 1,1, TLS 1,2 a vyšší          |
| TLS1_2                           | TLS verze 1,2 a vyšší           |


Například nastavení minimální verze nastavení TLS na TLS 1,0 znamená, že váš server umožní připojení z klientů pomocí protokolu TLS 1,0, 1,1 a 1,2 +. Případně můžete tuto hodnotu nastavit na 1,2 znamená, že povolíte připojení pouze z klientů pomocí protokolu TLS 1,2 a všechna připojení pomocí protokolu TLS 1,0 a TLS 1,1 budou odmítnuty.

> [!Note] 
> Pro všechny nové servery Azure Database for MySQL výchozí nastavení TLS zakázáno.
>
> Verze TLS podporované nástrojem Azure Database for MySQL jsou v současnosti TLS 1,0, 1,1 a 1,2.

Informace o nastavení nastavení TLS pro Azure Database for MySQL najdete v tématu [Konfigurace nastavení TLS](howto-tls-configurations.md).

## <a name="next-steps"></a>Další kroky

[Knihovny připojení pro Azure Database for MySQL](concepts-connection-libraries.md)
