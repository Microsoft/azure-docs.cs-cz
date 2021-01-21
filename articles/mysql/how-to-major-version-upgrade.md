---
title: Upgrade hlavní verze v Azure Database for MySQL – jeden server
description: Tento článek popisuje, jak můžete upgradovat hlavní verzi pro Azure Database for MySQL pro jeden server.
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 1/13/2021
ms.openlocfilehash: b62f4ebc61ac27478788d8b2bae5e4145f87ac8b
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/21/2021
ms.locfileid: "98630196"
---
# <a name="major-version-upgrade-in-azure-database-for-mysql-single-server"></a>Upgrade hlavní verze v Azure Database for MySQL jednom serveru

> [!IMPORTANT]
> Upgrade hlavní verze pro jeden server Azure Database for MySQL je ve verzi Public Preview.

Tento článek popisuje, jak můžete na Azure Database for MySQL jednom serveru upgradovat hlavní verzi MySQL na místě.

Tato funkce umožní zákazníkům provést místní upgrady svých serverů MySQL 5,6 na MySQL 5,7 pomocí tlačítka bez přesunu dat nebo nutnosti změny připojovacího řetězce aplikace.

> [!Note]
> * Upgrade hlavní verze je k dispozici jenom pro upgrade hlavní verze z MySQL 5,6 na MySQL 5,7.<br>
> * Upgrade hlavní verze se ještě na serveru repliky nepodporuje.
> * Server nebude během operace upgradu dostupný. Proto se doporučuje provádět upgrady během plánovaného časového období údržby.

## <a name="perform-major-version-upgrade-from-mysql-56-to-mysql-57-using-azure-portal"></a>Proveďte upgrade hlavní verze z MySQL 5,6 na MySQL 5,7 pomocí Azure Portal

Postupujte podle těchto kroků a proveďte upgrade hlavní verze pro databázi Azure serveru MySQL 5,6 pomocí Azure Portal

> [!IMPORTANT]
> Doporučujeme nejprve provést upgrade na obnovenou kopii serveru, nikoli přímo upgradovat produkční prostředí. Podívejte [se, jak provést obnovení k bodu v čase](howto-restore-server-portal.md#point-in-time-restore).

1. V [Azure Portal](https://portal.azure.com/)vyberte svůj existující server Azure Database for MySQL 5,6.

2. Na stránce **Přehled** klikněte na panelu nástrojů na tlačítko **upgradovat** .

3. V části **upgrade** vyberte **OK** a upgradujte Azure Database for MySQL 5,6 Server na 5,7.

   :::image type="content" source="./media/how-to-major-version-upgrade-portal/upgrade.png" alt-text="Azure Database for MySQL – přehled – upgrade":::

4. Oznámení potvrdí, že upgrade proběhl úspěšně.


## <a name="perform-major-version-upgrade-from-mysql-56-to-mysql-57-using-azure-cli"></a>Provedení upgradu hlavní verze z MySQL 5,6 na MySQL 5,7 pomocí Azure CLI

Postupujte podle těchto kroků, abyste provedli upgrade hlavní verze Azure Database serveru MySQL 5,6 pomocí Azure CLI.

> [!IMPORTANT]
> Doporučujeme nejprve provést upgrade na obnovenou kopii serveru, nikoli přímo upgradovat produkční prostředí. Podívejte [se, jak provést obnovení k bodu v čase](howto-restore-server-cli.md#server-point-in-time-restore).

1. Nainstalujte rozhraní příkazového [řádku Azure CLI pro Windows](/cli/azure/install-azure-cli) nebo použijte Azure cli v [Azure Cloud Shell](../cloud-shell/overview.md) ke spuštění příkazů upgradu. 
 
   Tento upgrade vyžaduje verzi Azure CLI 2.16.0 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná. Spuštěním příkazu az version zjistěte verzi a závislé knihovny, které jsou nainstalované. Pokud chcete upgradovat na nejnovější verzi, spusťte az upgrade.

2. Po přihlášení spusťte příkaz [AZ MySQL server upgrade](https://docs.microsoft.com/cli/azure/mysql/server?view=azure-cli-latest#az_mysql_server_upgrade&preserve-view=true) :
    
   ```azurecli
   az mysql server upgrade --name testsvr --resource-group testgroup --subscription MySubscription --target-server-version 5.7"
   ```
   
   Na příkazovém řádku se zobrazuje zpráva "-Running". Po zobrazení této zprávy už se upgrade verze dokončí.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="when-will-this-upgrade-feature-be-ga-as-we-have-mysql-v56-in-our-production-environment-that-we-need-to-upgrade"></a>Když bude tato funkce upgradu GA, protože máme MySQL v 5.6 v našem provozním prostředí, které potřebujeme upgradovat?

GA této funkce je plánováno před vyřazením MySQL v 5.6. Tato funkce je ale připravená a plně podporovaná v Azure, takže byste ji měli mít jistotu na svém prostředí. Jako osvědčený postup doporučujeme, abyste nejdřív spustili a otestovali na obnovené kopii serveru, abyste mohli odhadnout výpadky během upgradu a před spuštěním testu kompatibility aplikací. Další informace najdete v tématu [Jak provést obnovení](howto-restore-server-portal.md#point-in-time-restore) k určitému bodu v čase a vytvořit tak bod v čase kopie serveru. 

### <a name="will-this-cause-downtime-of-the-server-and-if-so-how-long"></a>Způsobí to výpadky serveru a pokud ano, jak dlouho?

Ano, během procesu upgradu nebude server k dispozici, proto doporučujeme tuto operaci provést během plánovaného časového období údržby. Odhadované výpadky závisí na velikosti databáze, zřízené velikosti úložiště (v zřízeném IOPs) a na počtu tabulek v databázi. Doba upgradu je přímo úměrná počtu tabulek na serveru. Očekává se, že upgrady serverů základních SKU budou trvat delší dobu, protože se nachází na standardní platformě úložiště. Pro odhad prostojů pro serverové prostředí doporučujeme nejprve provést upgrade na obnovenou kopii serveru.  

### <a name="it-is-noted-that-it-is-not-supported-on-replica-server-yet-what-does-that-mean-concrete"></a>Znamená to, že na serveru repliky ještě není podporovaný. Co to znamená konkrétní?

V současné době se upgrade hlavní verze nepodporuje u serveru repliky, což znamená, že byste ho neměli spouštět pro servery, které jsou součástí replikace (buď ze zdrojového serveru, nebo ze serveru repliky). Pokud chcete otestovat upgrade serverů zapojených do replikace předtím, než přidáme podporu repliky pro upgrade, doporučujeme, abyste provedli následující kroky:

1. Během plánované údržby [zastavte replikaci a odstraňte server repliky](howto-read-replicas-portal.md) po zachycení jeho názvu a všech informací o konfiguraci (nastavení brány firewall, konfigurace parametrů serveru, pokud se liší od zdrojového serveru).
2. Proveďte upgrade zdrojového serveru.
3. Zřídí nový server repliky pro čtení se stejným názvem a nastavením konfigurace zaznamenaným v kroku 1. Nový server repliky bude v verzi v 5.7 automaticky po upgradu zdrojového serveru na verzi v 5.7.

### <a name="what-will-happen-if-we-do-not-choose-to-upgrade-our-mysql-v56-server-before-february-5-2021"></a>Co se stane, když se nerozhodneme upgradovat server MySQL v 5.6 před 5. února 2021?

Stále můžete pokračovat v používání serveru MySQL v 5.6 jako dříve. Azure na vašem serveru **nikdy** neprovede vynucené upgradu. Omezení popsaná v [Azure Database for MySQL zásady správy verzí](concepts-version-policy.md) se ale použijí.

## <a name="next-steps"></a>Další kroky

Přečtěte si o [Azure Database for MySQLch zásadách správy verzí](concepts-version-policy.md).
