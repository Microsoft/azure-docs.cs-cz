---
title: Upgrade hlavní verze v Azure Database for MySQL – jeden server
description: Tento článek popisuje, jak můžete upgradovat hlavní verzi pro Azure Database for MySQL pro jeden server.
author: Bashar-MSFT
ms.author: bahusse
ms.service: mysql
ms.topic: how-to
ms.date: 1/28/2021
ms.openlocfilehash: 13cf315291cdf788951e352e430976851b30ce0c
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102216713"
---
# <a name="major-version-upgrade-in-azure-database-for-mysql-single-server"></a>Upgrade hlavní verze v Azure Database for MySQL jednom serveru

> [!NOTE]
> Tento článek obsahuje odkazy na _podřízený_ termín, termín, který už Microsoft nepoužívá. Po odebrání termínu ze softwaru ho odebereme z tohoto článku.
>

> [!IMPORTANT]
> Upgrade hlavní verze pro jeden server Azure Database for MySQL je ve verzi Public Preview.

Tento článek popisuje, jak můžete na Azure Database for MySQL jednom serveru upgradovat hlavní verzi MySQL na místě.

Tato funkce umožní zákazníkům provést místní upgrady svých serverů MySQL 5,6 na MySQL 5,7 pomocí tlačítka bez přesunu dat nebo nutnosti změny připojovacího řetězce aplikace.

> [!Note]
> * Upgrade hlavní verze je k dispozici jenom pro upgrade hlavní verze z MySQL 5,6 na MySQL 5,7.
> * Server nebude během operace upgradu dostupný. Proto se doporučuje provádět upgrady během plánovaného časového období údržby. Při [provádění minimálního výpadku hlavní verze z mysql 5,6 na mysql 5,7 pomocí repliky pro čtení](#perform-minimal-downtime-major-version-upgrade-from-mysql-56-to-mysql-57-using-read-replicas) můžete uvažovat.

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

2. Po přihlášení spusťte příkaz [AZ MySQL server upgrade](/cli/azure/mysql/server#az_mysql_server_upgrade) :

   ```azurecli
   az mysql server upgrade --name testsvr --resource-group testgroup --subscription MySubscription --target-server-version 5.7"
   ```
   
   Na příkazovém řádku se zobrazuje zpráva "-Running". Po zobrazení této zprávy už se upgrade verze dokončí.

## <a name="perform-major-version-upgrade-from-mysql-56-to-mysql-57-on-read-replica-using-azure-portal"></a>Proveďte upgrade hlavní verze z MySQL 5,6 na MySQL 5,7 v replice pro čtení pomocí Azure Portal

1. V [Azure Portal](https://portal.azure.com/)vyberte existující server repliky Azure Database for MySQL 5,6 pro čtení.

2. Na stránce **Přehled** klikněte na panelu nástrojů na tlačítko **upgradovat** .

3. V části **upgrade** vyberte **OK** , pokud chcete upgradovat Azure Database for MySQL 5,6 čtení serveru repliky na Server 5,7.

   :::image type="content" source="./media/how-to-major-version-upgrade-portal/upgrade.png" alt-text="Azure Database for MySQL – přehled – upgrade":::

4. Oznámení potvrdí, že upgrade proběhl úspěšně.

5. Na stránce **Přehled** ověřte, že verze serveru repliky pro čtení Azure Database for MySQL je 5,7.

6. Teď přejdete na primární server a [proveďte upgrade hlavní verze](#perform-major-version-upgrade-from-mysql-56-to-mysql-57-using-azure-portal) .

## <a name="perform-minimal-downtime-major-version-upgrade-from-mysql-56-to-mysql-57-using-read-replicas"></a>Provedení minimálního výpadku hlavní verze z MySQL 5,6 na MySQL 5,7 pomocí replik pro čtení

Upgrade hlavní verze s minimálními výpadky můžete provést z databáze MySQL 5,6 na MySQL 5,7 pomocí replik pro čtení. Je třeba nejprve upgradovat repliku čtení serveru na 5,7 a později převzít služby při selhání, aby odkazovaly na repliku pro čtení a načetli si ji nový primární.

1. V [Azure Portal](https://portal.azure.com/)vyberte stávající Azure Database for MySQL 5,6.

2. Vytvořte z primárního serveru [repliku pro čtení](./concepts-read-replicas.md#create-a-replica) .

3. [Upgradujte repliku pro čtení](#perform-major-version-upgrade-from-mysql-56-to-mysql-57-on-read-replica-using-azure-portal) na verzi 5,7.

4. Jakmile ověříte, že na serveru repliky běží verze 5,7, zastavte aplikaci, aby se nepřipojovala k primárnímu serveru.
 
5. Zkontrolujte stav replikace a ujistěte se, že je replika zachytila s primárním, takže všechna data jsou synchronizovaná a zajistěte, aby v primárním stavu nebyly žádné nové operace.

   Voláním [`show slave status`](https://dev.mysql.com/doc/refman/5.7/en/show-slave-status.html) příkazu na serveru repliky zobrazte stav replikace.

   ```sql
   SHOW SLAVE STATUS\G
   ```

   Pokud je stav `Slave_IO_Running` a v `Slave_SQL_Running` hodnotě "Ano" a hodnota `Seconds_Behind_Master` je "0", replikace funguje dobře. `Seconds_Behind_Master` Určuje, jak pozdě je replika. Pokud hodnota není "0", znamená to, že replika zpracovává aktualizace. Jakmile ověříte, `Seconds_Behind_Master` že je "0", je bezpečné zastavit replikaci.

6. Povýšit repliku pro čtení na primární [zastavením replikace](./howto-read-replicas-portal.md#stop-replication-to-a-replica-server).

7. Nasměrujte svoji aplikaci na novou primární (předchozí repliku), na které běží Server 5,7. Každý server má jedinečný připojovací řetězec. Aktualizujte svou aplikaci tak, aby odkazovala na (bývalé) repliku místo zdroje.

> [!Note]
> V tomto scénáři dojde k výpadkům během kroků 4, 5 a 6.


## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="when-will-this-upgrade-feature-be-ga-as-we-have-mysql-v56-in-our-production-environment-that-we-need-to-upgrade"></a>Když bude tato funkce upgradu GA, protože máme MySQL v 5.6 v našem provozním prostředí, které potřebujeme upgradovat?

GA této funkce je plánováno před vyřazením MySQL v 5.6. Tato funkce je ale připravená a plně podporovaná v Azure, takže byste ji měli mít jistotu na svém prostředí. Jako osvědčený postup doporučujeme, abyste nejdřív spustili a otestovali na obnovené kopii serveru, abyste mohli odhadnout výpadky během upgradu a před spuštěním testu kompatibility aplikací. Další informace najdete v tématu [Jak provést obnovení](howto-restore-server-portal.md#point-in-time-restore) k určitému bodu v čase a vytvořit tak bod v čase kopie serveru. 

### <a name="will-this-cause-downtime-of-the-server-and-if-so-how-long"></a>Způsobí to výpadky serveru a pokud ano, jak dlouho?

Ano, během procesu upgradu nebude server k dispozici, proto doporučujeme tuto operaci provést během plánovaného časového období údržby. Odhadované výpadky závisí na velikosti databáze, zřízené velikosti úložiště (v zřízeném IOPs) a na počtu tabulek v databázi. Doba upgradu je přímo úměrná počtu tabulek na serveru. Očekává se, že upgrady serverů základních SKU budou trvat delší dobu, protože se nachází na standardní platformě úložiště. Pro odhad prostojů pro serverové prostředí doporučujeme nejprve provést upgrade na obnovenou kopii serveru. Zvažte možnost provést [minimální výpadky hlavní verze z mysql 5,6 na mysql 5,7 pomocí repliky pro čtení.](#perform-minimal-downtime-major-version-upgrade-from-mysql-56-to-mysql-57-using-read-replicas)

### <a name="what-will-happen-if-we-do-not-choose-to-upgrade-our-mysql-v56-server-before-february-5-2021"></a>Co se stane, když se nerozhodneme upgradovat server MySQL v 5.6 před 5. února 2021?

Stále můžete pokračovat v používání serveru MySQL v 5.6 jako dříve. Azure na vašem serveru **nikdy** neprovede vynucené upgradu. Omezení popsaná v [Azure Database for MySQL zásady správy verzí](concepts-version-policy.md) se ale použijí.

## <a name="next-steps"></a>Další kroky

Přečtěte si o [Azure Database for MySQLch zásadách správy verzí](concepts-version-policy.md).