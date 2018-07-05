---
title: Protokolů serveru ve službě Azure Database for PostgreSQL
description: Tento článek popisuje, databáze Azure pro PostgreSQL generuje dotaz a chybových protokolů a jak uchování protokolu konfigurace.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: bcca8ce8d11482dd8517992297b7e8a5b94ac8b1
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37435486"
---
# <a name="server-logs-in-azure-database-for-postgresql"></a>Protokolů serveru ve službě Azure Database for PostgreSQL 
Azure Database for PostgreSQL generuje dotaz a chybových protokolů. Přístup k protokolům transakce však není podporován. Dotaz a chybových protokolů umožňuje zjistit, řešit a opravit chyby v konfiguraci a neoptimální výkonu. Další informace najdete v tématu [generování sestav a protokolování chyb](https://www.postgresql.org/docs/9.6/static/runtime-config-logging.html).

## <a name="access-server-logs"></a>Přístup k protokolům serveru
Seznam a stáhnout protokoly chyb serveru Azure PostgreSQL pomocí webu Azure portal, můžete [rozhraní příkazového řádku Azure](howto-configure-server-logs-using-cli.md)a rozhraní Azure REST API.

## <a name="log-retention"></a>Uchovávání protokolů
Můžete nastavit dobu uchování, systémové protokoly, pomocí **protokolu\_uchování\_období** parametr přidružené k serveru. Jednotka pro tento parametr je dny. Výchozí hodnota je 3 dny. Maximální hodnota je 7 dní. Server musí mít dostatek přidělené úložiště tak, aby obsahovala zachované protokoly.
Soubory protokolů otočit každou hodinu nebo velikost 100 MB, podle toho, co nastane dřív.

## <a name="configure-logging-for-azure-postgresql-server"></a>Nakonfigurujte si protokolování pro server Azure PostgreSQL
Dotaz protokolování a protokolování chyb můžete povolit pro váš server. Protokoly chyb může obsahovat informace o automatické takový, připojení a kontrolní body.

Můžete povolit protokolování dotazu pro vaši instanci databáze PostgreSQL nastavením dva parametry serveru: `log_statement` a `log_min_duration_statement`.

**Protokolu\_příkaz** parametr řídí, jaké příkazy SQL jsou protokolovány. Doporučujeme nastavit tento parametr na ***všechny*** protokolovat všechny příkazy; výchozí hodnota je žádné.

**Protokolu\_min\_doba trvání\_příkaz** parametr nastaví limit v milisekundách příkazu má být zaznamenána. Protokolují se všechny příkazy SQL, které trvají déle než nastavení parametru. Tento parametr je zakázané a odečte 1. (-1) ve výchozím nastavení. Povolení tento parametr může být užitečné při sledování neoptimalizované dotazy ve svých aplikacích.

**Protokolu\_min\_zprávy** umožňuje, můžete řídit, která úrovně zprávy se zapisují do protokolu serveru. Výchozí hodnota je upozornění. 

Další informace o těchto nastaveních najdete v tématu [generování sestav a protokolování chyb](https://www.postgresql.org/docs/9.6/static/runtime-config-logging.html) dokumentaci. Zejména konfigurace databáze Azure pro parametry serveru PostgreSQL, naleznete v tématu [přizpůsobení parametrů konfigurace serveru pomocí Azure CLI](howto-configure-server-parameters-using-cli.md).

## <a name="next-steps"></a>Další postup
- Pokud chcete získat přístup k protokolům pomocí rozhraní příkazového řádku Azure CLI, přečtěte si téma [server protokoly konfigurace a přístup pomocí rozhraní příkazového řádku Azure](howto-configure-server-logs-using-cli.md).
- Další informace o parametrech serveru najdete v tématu [přizpůsobení parametrů konfigurace serveru pomocí Azure CLI](howto-configure-server-parameters-using-cli.md).
