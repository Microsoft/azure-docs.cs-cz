---
title: Konfigurace parametrů serveru ve službě Azure Database for PostgreSQL pomocí webu Azure portal
description: Tento článek popisuje postup konfigurace parametrů serveru ve službě Azure Database for PostgreSQL pomocí webu Azure portal.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: 0d0626c48ecebdead604aab93ab0602c698d0d77
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2018
ms.locfileid: "53540533"
---
# <a name="configure-server-parameters-in-azure-portal"></a>Konfigurace parametrů serveru na webu Azure portal
Seznam, zobrazit a aktualizovat parametry konfigurace pro serveru Azure Database for PostgreSQL pomocí webu Azure portal.

## <a name="prerequisites"></a>Požadavky
Pro jednotlivé kroky v této příručce s postupy, které potřebujete:
- [Azure Database for PostgreSQL server](quickstart-create-server-database-portal.md)

## <a name="viewing-and-editing-parameters"></a>Zobrazení a úprava parametrů
1. Otevřete web [Azure Portal](https://portal.azure.com).

2. Vyberte svůj server Azure Database for PostgreSQL.

3. V části **nastavení** vyberte **parametry serveru**. Na stránce se seznamem parametrů, jejich hodnoty a popisy.
![Stránka s přehledem pro parametry](./media/howto-configure-server-parameters-in-portal/3-overview-of-parameters.png)

4. Vyberte **rozevírací seznam** tlačítko, abyste viděli možných hodnot pro parametry Výčtový typ jako client_min_messages.
![Zobrazení výčtu rozevírací seznam](./media/howto-configure-server-parameters-in-portal/4-enum-drop-down.png)

5. Vyberte nebo najeďte myší **můžu** tlačítko (informace o), abyste viděli rozsah možných hodnot pro číselné parametry jako cpu_index_tuple_cost.
![tlačítko informace](./media/howto-configure-server-parameters-in-portal/4-information-button.png)

6. V případě potřeby použijte **vyhledávacího pole** můžete zúžit na určitý parametr. Hledání je na název a popis parametrů.
![Výsledky hledání](./media/howto-configure-server-parameters-in-portal/5-search.png)

7. Změňte hodnoty parametrů, které chcete upravit. Fialově jsou zvýrazněny všechny změny provedené v relaci. Po změně hodnoty, můžete vybrat **Uložit**. Nebo můžete **zahodit** provedené změny.
![Uložit nebo zahodit změny](./media/howto-configure-server-parameters-in-portal/6-save-and-discard-buttons.png)

8. Pokud jste uložili nové hodnoty pro parametry, můžete se vždycky vrátit vše zpět na výchozí hodnoty tak, že vyberete **Resetovat vše na výchozí**.
![Resetovat vše na výchozí](./media/howto-configure-server-parameters-in-portal/7-reset-to-default-button.png)

## <a name="next-steps"></a>Další postup
Další informace o:
- [Přehled parametrů serveru ve službě Azure Database for PostgreSQL](concepts-servers.md)
- [Konfigurace parametrů pomocí Azure CLI](howto-configure-server-parameters-using-cli.md)
