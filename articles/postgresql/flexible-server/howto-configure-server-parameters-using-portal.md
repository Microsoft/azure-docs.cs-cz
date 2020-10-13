---
title: Konfigurace parametrů serveru-Azure Portal-Azure Database for PostgreSQL-flexibilní Server
description: Tento článek popisuje, jak nakonfigurovat parametry Postgres v Azure Database for PostgreSQL flexibilním serveru prostřednictvím Azure Portal.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 4f945c2c7fffb143bdb8324a330775fb072b25c1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90935871"
---
# <a name="configure-server-parameters-in-azure-database-for-postgresql---flexible-server-via-the-azure-portal"></a>Konfigurace parametrů serveru v Azure Database for PostgreSQL-flexibilním serveru prostřednictvím Azure Portal 

Pomocí Azure Portal můžete vypsat, zobrazit a aktualizovat parametry konfigurace Azure Database for PostgreSQL flexibilního serveru.

## <a name="prerequisites"></a>Požadavky

Postup pro krokování tohoto postupu vám pomůže:
- [Azure Database for PostgreSQL flexibilní Server](quickstart-create-server-portal.md)

## <a name="viewing-and-editing-parameters"></a>Zobrazení a úprava parametrů

1. Otevřete web [Azure Portal](https://portal.azure.com).

2. Vyberte flexibilní Server.

3. V části **Nastavení** vyberte **parametry serveru**. Stránka zobrazuje seznam parametrů, jejich hodnoty a popisy.
![Stránka s přehledem pro parametry](./media/howto-configure-server-parameters-in-portal/3-overview-of-parameters.png)

4. Kliknutím na tlačítko **rozevíracího** seznamu zobrazíte možné hodnoty pro parametry výčtového typu, jako je client_min_messages.
![Výčet rozevíracích seznamu](./media/howto-configure-server-parameters-in-portal/4-enum-drop-down.png)

5. Vyberte nebo najeďte myší na tlačítko **i** (informace), abyste viděli rozsah možných hodnot číselných parametrů, jako je cpu_index_tuple_cost.
![Informační tlačítko](./media/howto-configure-server-parameters-in-portal/4-information-button.png)

6. V případě potřeby pomocí **vyhledávacího pole** upřesněte konkrétní parametr. Hledání je podle názvu a popisu parametrů.
![Výsledky hledání](./media/howto-configure-server-parameters-in-portal/5-search.png)

7. Změňte hodnoty parametrů, které chcete upravit. Všechny změny, které provedete v relaci, jsou zvýrazněné fialově. Po změně hodnot můžete vybrat **Uložit**. Nebo můžete změny **Zrušit** .
![Uložit nebo zahodit změny](./media/howto-configure-server-parameters-in-portal/6-save-and-discard-buttons.png)

8. Pokud jste uložili nové hodnoty pro parametry, můžete kdykoli vrátit zpět všechny výchozí hodnoty výběrem možnosti **Obnovit vše na výchozí**.
![Obnovit výchozí](./media/howto-configure-server-parameters-in-portal/7-reset-to-default-button.png)

## <a name="next-steps"></a>Další kroky

Projdeme si tyto možnosti:

- [Přehled parametrů serveru v Azure Database for PostgreSQL](concepts-servers.md)
- [Konfigurace parametrů pomocí rozhraní příkazového řádku Azure](howto-configure-server-parameters-using-cli.md)
