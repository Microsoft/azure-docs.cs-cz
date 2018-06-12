---
title: Připojení SSL pro databázi Azure pro databázi MySQL
description: Informace pro konfiguraci Azure databáze MySQL a přidružené aplikace odpovídajícím způsobem používat připojení SSL
services: mysql
author: JasonMAnderson
ms.author: janders
editor: jasonwhowell
manager: kfile
ms.service: mysql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: ee7e0ec8524d66ee89cf7b2c4d44b70efa784f8f
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35265057"
---
# <a name="ssl-connectivity-in-azure-database-for-mysql"></a>Připojení SSL ve službě Azure Database pro databázi MySQL
Azure databáze pro databázi MySQL podporuje připojení databázový server pro klientské aplikace pomocí Secure Sockets Layer (SSL). Díky vynucování připojení SSL mezi databázovým serverem a klientskými aplikacemi se šifruje datový proud mezi serverem a vaší aplikací, což pomáhá chránit před napadením útočníky, kteří se vydávají za prostředníky.

## <a name="default-settings"></a>Výchozí nastavení
Ve výchozím nastavení měla by se nakonfigurovat službu databáze vyžadovat připojení SSL při připojování k MySQL.  Doporučujeme, aby se zabránilo zakázání možností protokolu SSL, kdykoli je to možné. 

Při zřizování nové databáze Azure pro server databáze MySQL prostřednictvím portálu Azure a rozhraní příkazového řádku, je ve výchozím nastavení povolena vynucení připojení SSL. 

Připojovací řetězce pro různých programovacích jazyků se zobrazí na portálu Azure. Tyto připojovací řetězce zahrnout požadované parametry protokolu SSL pro připojení k vaší databázi. Na portálu Azure vyberte svůj server. V části **nastavení** záhlaví, vyberte **připojovací řetězce**. Parametr SSL se liší v závislosti na konektoru, například "ssl = true" nebo "sslmode = vyžadují" nebo "sslmode = požadované" a dalších odlišností.

Zjistěte, jak povolit nebo zakázat připojení SSL při vývoji aplikace, najdete v tématu [postup konfigurace protokolu SSL](howto-configure-ssl.md). 

## <a name="next-steps"></a>Další postup
[Knihovny připojení pro databázi Azure pro databázi MySQL](concepts-connection-libraries.md)
