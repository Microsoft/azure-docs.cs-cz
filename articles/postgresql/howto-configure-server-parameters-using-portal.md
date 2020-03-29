---
title: Konfigurace parametrů serveru – portál Azure – databáze Azure pro PostgreSQL – jeden server
description: Tento článek popisuje, jak nakonfigurovat parametry Postgres v Azure Database for PostgreSQL prostřednictvím portálu Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: a9d078fe9aab12b9044733d17a1437801d5130a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74763670"
---
# <a name="configure-server-parameters-in-azure-database-for-postgresql---single-server-via-the-azure-portal"></a>Konfigurace parametrů serveru v Databázi Azure pro PostgreSQL – jeden server prostřednictvím portálu Azure 
Můžete seznam, zobrazit a aktualizovat parametry konfigurace pro Azure Database for PostgreSQL server prostřednictvím portálu Azure.

## <a name="prerequisites"></a>Požadavky
Chcete-li projít tento návod, který potřebujete:
- [Databáze Azure pro postgreSQL server](quickstart-create-server-database-portal.md)

## <a name="viewing-and-editing-parameters"></a>Zobrazení a úpravy parametrů
1. Otevřete [portál Azure](https://portal.azure.com).

2. Vyberte svůj server Azure Database for PostgreSQL.

3. V části **NASTAVENÍ** vyberte **parametry serveru**. Na stránce se zobrazí seznam parametrů, jejich hodnot a popisů.
![Stránka přehledu pro parametry](./media/howto-configure-server-parameters-in-portal/3-overview-of-parameters.png)

4. Výběrem **rozevíracího** tlačítka zobrazíte možné hodnoty parametrů výčtového typu, jako je client_min_messages.
![Rozevírací vypočet](./media/howto-configure-server-parameters-in-portal/4-enum-drop-down.png)

5. Výběrem tlačítka **i** (informace) nebo na něj najedete na tlačítko i(informace) zobrazíte rozsah možných hodnot pro číselné parametry, jako je cpu_index_tuple_cost.
![informační tlačítko](./media/howto-configure-server-parameters-in-portal/4-information-button.png)

6. V případě potřeby se **pomocí vyhledávacího pole** zúžit na konkrétní parametr. Hledání je na název a popis parametrů.
![Výsledky hledání](./media/howto-configure-server-parameters-in-portal/5-search.png)

7. Změňte hodnoty parametrů, které chcete upravit. Všechny změny provedené v relaci jsou zvýrazněny fialovou barvou. Po změně hodnot můžete vybrat **uložit**. Nebo můžete **zahodit** změny.
![Uložit nebo zahodit změny](./media/howto-configure-server-parameters-in-portal/6-save-and-discard-buttons.png)

8. Pokud jste uložili nové hodnoty parametrů, můžete vždy vrátit vše zpět na výchozí hodnoty výběrem **možnosti Obnovit vše na výchozí**.
![Obnovit výchozí hodnotu](./media/howto-configure-server-parameters-in-portal/7-reset-to-default-button.png)

## <a name="next-steps"></a>Další kroky
Projdeme si tyto možnosti:
- [Přehled parametrů serveru v Azure Database pro PostgreSQL](concepts-servers.md)
- [Konfigurace parametrů pomocí rozhraní příkazového řádku Azure](howto-configure-server-parameters-using-cli.md)
