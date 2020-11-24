---
title: Upgrade hlavní verze – Azure Portal-Azure Database for MySQL-jeden server
description: Tento článek popisuje, jak můžete upgradovat hlavní verzi pro Azure Database for MySQL s jedním serverem pomocí Azure Portal
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 11/16/2020
ms.openlocfilehash: 080d7c09b180d5943216793119718eb6a2add79e
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2020
ms.locfileid: "95753047"
---
# <a name="major-version-upgrade-in-azure-database-for-mysql-single-server-using-the-azure-portal"></a>Upgrade hlavní verze v Azure Database for MySQL jednom serveru pomocí Azure Portal

> [!IMPORTANT]
> Upgrade hlavní verze pro jeden server Azure Database for MySQL je ve verzi Public Preview.

Tento článek popisuje, jak můžete na Azure Database for MySQL jednom serveru upgradovat hlavní verzi MySQL na místě.

Tato funkce umožní zákazníkům provést místní upgrady svých serverů MySQL 5,6 na MySQL 5,7 pomocí tlačítka bez přesunu dat nebo nutnosti změny připojovacího řetězce aplikace.

> [!Note]
> * Upgrade hlavní verze je k dispozici jenom pro upgrade hlavní verze z MySQL 5,6 na MySQL 5,7.<br>
> * Upgrade hlavní verze se ještě na serveru repliky nepodporuje.
> * Server nebude během operace upgradu dostupný. Proto se doporučuje provádět upgrady během plánovaného časového období údržby.

## <a name="prerequisites"></a>Požadavky
K dokončení tohoto průvodce budete potřebovat:
- [Azure Database for MySQL jeden server](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="perform-major-version-upgrade-from-mysql-56-to-mysql-57"></a>Proveďte upgrade hlavní verze z MySQL 5,6 na MySQL 5,7.

Pomocí těchto kroků proveďte upgrade hlavní verze pro vaši databázi Azure serveru MySQL 5,6.

> [!IMPORTANT]
> Doporučujeme nejprve provést upgrade na obnovenou kopii serveru, nikoli přímo upgradovat produkční prostředí. Podívejte [se, jak provést obnovení k bodu v čase](howto-restore-server-portal.md#point-in-time-restore). 

1. V [Azure Portal](https://portal.azure.com/)vyberte svůj existující server Azure Database for MySQL 5,6.

2. Na stránce **Přehled** klikněte na panelu nástrojů na tlačítko **upgradovat** .

3. V části **upgrade** vyberte **OK** a upgradujte Azure Database for MySQL 5,6 Server na 5,7.

    :::image type="content" source="./media/how-to-major-version-upgrade-portal/upgrade.png" alt-text="Azure Database for MySQL – přehled – upgrade":::

4. Oznámení potvrdí, že upgrade proběhl úspěšně.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

**1. Pokud bude tato funkce upgradu GA, protože máme MySQL v 5.6 v našem provozním prostředí, které potřebujeme upgradovat?**

GA této funkce je plánováno před vyřazením MySQL v 5.6. Tato funkce je ale připravená a plně podporovaná v Azure, takže byste ji měli mít jistotu na svém prostředí. Jako osvědčený postup doporučujeme, abyste nejdřív spustili a otestovali na obnovené kopii serveru, abyste mohli odhadnout výpadky během upgradu a před spuštěním testu kompatibility aplikací. Další informace najdete v tématu [Jak provést obnovení](howto-restore-server-portal.md#point-in-time-restore) k určitému bodu v čase a vytvořit tak bod v čase kopie serveru. 

**2. bude to mít za následek výpadky serveru a pokud ano, jak dlouho?**

Ano, během procesu upgradu nebude server k dispozici, proto doporučujeme tuto operaci provést během plánovaného časového období údržby. Odhadované výpadky závisí na velikosti databáze, zřízené velikosti úložiště (v zřízeném IOPs) a na počtu tabulek v databázi. Doba upgradu je přímo úměrná počtu tabulek na serveru. Očekává se, že upgrady serverů základních SKU budou trvat delší dobu, protože se nachází na standardní platformě úložiště. Pro odhad prostojů pro serverové prostředí doporučujeme nejprve provést upgrade na obnovenou kopii serveru.  

**3. znamená to, že na serveru repliky ještě není podporovaná. Co to znamená konkrétní?**

V současné době se upgrade hlavní verze nepodporuje u serveru repliky, což znamená, že byste ho neměli spouštět pro servery, které jsou součástí replikace (buď ze zdrojového serveru, nebo ze serveru repliky). Pokud chcete otestovat upgrade serverů zapojených do replikace předtím, než přidáme podporu repliky pro upgrade, doporučujeme, abyste provedli následující kroky:

1. Během plánované údržby [zastavte replikaci a odstraňte server repliky](howto-read-replicas-portal.md) po zachycení jeho názvu a všech informací o konfiguraci (nastavení brány firewall, konfigurace parametrů serveru, pokud se liší od zdrojového serveru).
2. Proveďte upgrade zdrojového serveru.
3. Zřídí nový server repliky pro čtení se stejným názvem a nastavením konfigurace zaznamenaným v kroku 1. Nový server repliky bude v verzi v 5.7 automaticky po upgradu zdrojového serveru na verzi v 5.7.

**4. k tomu, co se stane, pokud se nerozhodnete upgradovat server MySQL v 5.6 před 5. února 2021?**

Stále můžete pokračovat v používání serveru MySQL v 5.6 jako dříve. Azure na vašem serveru **nikdy** neprovede vynucené upgradu. Omezení popsaná v [Azure Database for MySQL zásady správy verzí](concepts-version-policy.md) se ale použijí.

## <a name="next-steps"></a>Další kroky

Přečtěte si o [Azure Database for MySQLch zásadách správy verzí](concepts-version-policy.md).
