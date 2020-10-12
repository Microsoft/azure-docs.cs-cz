---
title: Konfigurace parametrů serveru-Azure Portal-Azure Database for PostgreSQL-Single server
description: Tento článek popisuje, jak nakonfigurovat parametry Postgres v Azure Database for PostgreSQL prostřednictvím Azure Portal.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: how-to
ms.date: 02/28/2018
ms.openlocfilehash: e1b40e3116d56e87a2f397350ef2ba5510e04c0c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91707689"
---
# <a name="configure-server-parameters-in-azure-database-for-postgresql---single-server-via-the-azure-portal"></a>Konfigurace parametrů serveru v Azure Database for PostgreSQL-Single server přes Azure Portal 
Pomocí Azure Portal můžete vypsat, zobrazit a aktualizovat parametry konfigurace Azure Database for PostgreSQL serveru.

## <a name="prerequisites"></a>Požadavky
Postup pro krokování tohoto postupu vám pomůže:
- [Server Azure Database for PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="viewing-and-editing-parameters"></a>Zobrazení a úprava parametrů
1. Otevřete web [Azure Portal](https://portal.azure.com).

2. Vyberte svůj server Azure Database for PostgreSQL.

3. V části **Nastavení** vyberte **parametry serveru**. Stránka zobrazuje seznam parametrů, jejich hodnoty a popisy.
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/3-overview-of-parameters.png" alt-text="Stránka s přehledem pro parametry":::

4. Kliknutím na tlačítko **rozevíracího** seznamu zobrazíte možné hodnoty pro parametry výčtového typu, jako je client_min_messages.
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/4-enum-drop-down.png" alt-text="Stránka s přehledem pro parametry":::

5. Vyberte nebo najeďte myší na tlačítko **i** (informace), abyste viděli rozsah možných hodnot číselných parametrů, jako je cpu_index_tuple_cost.
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/4-information-button.png" alt-text="Stránka s přehledem pro parametry":::

6. V případě potřeby pomocí **vyhledávacího pole** upřesněte konkrétní parametr. Hledání je podle názvu a popisu parametrů.
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/5-search.png" alt-text="Stránka s přehledem pro parametry":::

7. Změňte hodnoty parametrů, které chcete upravit. Všechny změny, které provedete v relaci, jsou zvýrazněné fialově. Po změně hodnot můžete vybrat **Uložit**. Nebo můžete změny **Zrušit** .
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/6-save-and-discard-buttons.png" alt-text="Stránka s přehledem pro parametry":::

8. Pokud jste uložili nové hodnoty pro parametry, můžete kdykoli vrátit zpět všechny výchozí hodnoty výběrem možnosti **Obnovit vše na výchozí**.
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/7-reset-to-default-button.png" alt-text="Stránka s přehledem pro parametry":::

## <a name="next-steps"></a>Další kroky
Projdeme si tyto možnosti:
- [Přehled parametrů serveru v Azure Database for PostgreSQL](concepts-servers.md)
- [Konfigurace parametrů pomocí rozhraní příkazového řádku Azure](howto-configure-server-parameters-using-cli.md)
