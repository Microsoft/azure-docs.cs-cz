---
title: Připojení SSL – Azure Database for MySQL
description: Informace o konfiguraci Azure Database for MySQL a přidružených aplikací pro správné používání připojení SSL
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: d7abed41f3d02382d690f9d7f2a088318632d531
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2020
ms.locfileid: "75902570"
---
# <a name="ssl-connectivity-in-azure-database-for-mysql"></a>Připojení SSL v Azure Database for MySQL
Azure Database for MySQL podporuje připojení vašeho databázového serveru k klientským aplikacím pomocí SSL (Secure Sockets Layer) (SSL). Díky vynucování připojení SSL mezi databázovým serverem a klientskými aplikacemi se šifruje datový proud mezi serverem a vaší aplikací, což pomáhá chránit před napadením útočníky, kteří se vydávají za prostředníky.

## <a name="default-settings"></a>Výchozí nastavení
Ve výchozím nastavení by databázová služba měla být nakonfigurovaná tak, aby při připojování k MySQL vyžadovala připojení SSL.  Doporučujeme, abyste se vyhnuli vypnutí možnosti SSL, kdykoli to bude možné. 

Při zřizování nového serveru Azure Database for MySQL prostřednictvím Azure Portal a CLI je vynucování připojení SSL ve výchozím nastavení povolené. 

V Azure Portal se zobrazují připojovací řetězce pro různé programovací jazyky. Tyto připojovací řetězce zahrnují požadované parametry SSL pro připojení k vaší databázi. V Azure Portal vyberte svůj server. V záhlaví **Nastavení** vyberte **připojovací řetězce**. Parametr SSL se liší v závislosti na konektoru, například "SSL = true" nebo "sslmode = vyžadovat" nebo "sslmode = Required" a další variace.

> [!NOTE]
> V současné době je verze TLS podporovaná pro Azure Database for MySQL TLS 1,0, TLS 1,1, TLS 1,2.

Informace o tom, jak povolit nebo zakázat připojení SSL při vývoji aplikace, najdete v tématu [Postup konfigurace protokolu SSL](howto-configure-ssl.md). 

## <a name="next-steps"></a>Další kroky
[Knihovny připojení pro Azure Database for MySQL](concepts-connection-libraries.md)
