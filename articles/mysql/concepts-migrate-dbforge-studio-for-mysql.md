---
title: Připojení k Azure Database for MySQL pomocí dbForge studia pro MySQL
description: Tento článek ukazuje, jak se připojit k serveru Azure Database for MySQL přes dbForge Studio pro MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: 5138e542d1cc744a23fa1017dfec1f24b2ec1a5b
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102107447"
---
# <a name="connect-to-azure-database-for-mysql-using-dbforge-studio-for-mysql"></a>Připojení k Azure Database for MySQL pomocí dbForge studia pro MySQL

Připojení k Azure Database for MySQL pomocí dbForge studia pro MySQL:

1. V nabídce databáze vyberte nové připojení.

2. Zadejte název hostitele a přihlašovací údaje pro přihlášení.

3. Kliknutím na tlačítko Test připojení zkontrolujte konfiguraci.

:::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/azure-connection-1.png" alt-text="Připojení Azure":::

## <a name="migrate-a-database-using-the-backup-and-restore-functionality"></a>Migrace databáze pomocí funkce zálohování a obnovení

Studio umožňuje migrovat databáze do Azure mnoha způsoby, podle toho, jaké jsou možnosti, které závisí výhradně na vašich potřebách. Pokud potřebujete přesunout celou databázi, je vhodné použít funkce zálohování a obnovení. V tomto příkladu migrujeme databázi *sakila* , která se nachází na serveru MySQL, a Azure Database for MySQL. Logika za procesem migrace pomocí funkce zálohování a obnovení nástroje dbForge Studio pro MySQL je vytvořit zálohu databáze MySQL a pak ji obnovit v Azure Database for MySQL.

### <a name="back-up-the-database"></a>Zálohování databáze

1. V nabídce databáze přejděte na příkaz zálohování a obnovení a pak vyberte možnost zálohovat databázi. Zobrazí se Průvodce zálohováním databáze.

2. Na kartě zálohovaný obsah v Průvodci zálohováním databáze vyberte objekty databáze, které chcete zálohovat.

3. Na kartě Možnosti nakonfigurujte proces zálohování tak, aby vyhovoval vašim požadavkům.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/back-up-wizard-options.png" alt-text="Možnosti Průvodce zálohováním":::

4. Dále zadejte chování při zpracování chyb a možnosti protokolování.

5. Vyberte zálohování.

### <a name="restore-the-database"></a>Obnovení databáze

1. Připojte se k Azure pro databázi MySQL, jak je popsáno výše.

2. Klikněte pravým tlačítkem myši na tělo Průzkumníka databáze, přejděte na příkaz zálohování a obnovení a pak vyberte možnost obnovit databázi.

3. V Průvodci obnovením databáze, který se otevře, vyberte soubor se zálohou databáze.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/restore-step-1.png" alt-text="Obnovit krok":::

4. Vyberte obnovit.

5. Podívejte se na výsledek.

## <a name="migrate-a-database-using-the-copy-databases-functionality"></a>Migrace databáze pomocí funkce kopírování databází

Funkce kopírování databází se podobá zálohování a obnovení, s tím rozdílem, že k migraci databáze nepotřebujete dva kroky. A co je více, funkce umožňuje přenášet dvě nebo více databází v rámci jednoho přechodu. Funkce kopírování databází je k dispozici pouze v edici Enterprise systému dbForge Studio pro MySQL.
V tomto příkladu migrujeme *world_x* databázi ze serveru MySQL do Azure Database for MySQL.
Migrace databáze pomocí funkce kopírování databází:

1. V nabídce databáze vyberte Kopírovat databáze. 

2. Na kartě kopírovat databáze, která se zobrazí, zadejte zdrojové a cílové připojení a vyberte databáze, které chcete migrovat. Zadáte připojení Azure MySQL a vyberete databázi *world_x* . Vyberte zelenou šipku pro zahájení procesu.

3. Podívejte se na výsledek.

V důsledku naší migrace databáze se databáze *world_x* ve službě Azure MySQL úspěšně zobrazila.

:::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/copy-databases-result.png" alt-text="Výsledek kopírování databází":::

## <a name="migrate-a-database-using-schema-and-data-compare-tools"></a>Migrace databáze pomocí nástrojů pro porovnání schématu a dat

dbForge Studio for MySQL obsahuje několik nástrojů, které umožňují migrovat databáze MySQL, schémata MySQL and\or data do Azure. Výběr funkcí závisí na vašich potřebách a požadavcích vašeho projektu. Pokud potřebujete selektivně přesunout databázi, to znamená migrovat některé tabulky MySQL do Azure, je nejlepší použít funkce pro porovnání schématu a dat.
V tomto příkladu migrujeme *světová* databáze, která se nachází na serveru MySQL, a Azure Database for MySQL. Logika za procesem migrace s využitím funkcí porovnání schématu a dat v dbForge studiu pro MySQL je vytvoření prázdné databáze v Azure Database for MySQL, jejich následné synchronizaci s požadovanou databází MySQL pomocí nástroje porovnání schématu a pak pomocí nástroje pro porovnání dat. Tímto způsobem jsou schémata a data MySQL přesně přesunuty do Azure.

### <a name="connect-to-azure-database-for-mysql-and-create-an-empty-database"></a>Připojení k Azure Database for MySQL a vytvoření prázdné databáze

Připojte se k Azure Database for MySQL a vytvořte prázdnou databázi.

### <a name="step-2-schema-synchronization"></a>Krok 2. Synchronizace schématu

1. V nabídce porovnání vyberte nové porovnání schématu.
Zobrazí se Průvodce vytvořením nového schématu porovnání.

2. Vyberte zdroj a cíl a pak zadejte možnosti porovnání schématu. Vyberte porovnat.

3. V mřížce výsledků porovnání, které se zobrazí, vyberte objekty pro synchronizaci. Výběrem zelené tlačítko se šipkou otevřete Průvodce synchronizací schématu.

4. Projděte si kroky průvodce Konfigurace synchronizace. Kliknutím na tlačítko synchronizovat nasaďte změny.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/schema-sync-wizard.png" alt-text="Průvodce synchronizací schématu":::

### <a name="data-comparison"></a>Porovnání dat

1. V nabídce porovnání vyberte nové porovnání dat. Zobrazí se Průvodce vytvořením nového porovnání dat.

2. Vyberte zdroj a cíl, zadejte možnosti porovnání dat a v případě potřeby mapování změňte. Vyberte porovnat.

3. V mřížce výsledků porovnání, které se zobrazí, vyberte objekty pro synchronizaci. Výběrem zelené tlačítko se šipkou otevřete Průvodce synchronizací dat.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/data-comp-result.png" alt-text="Výsledek datové kompozice":::

4. Projděte si kroky průvodce Konfigurace synchronizace. Kliknutím na tlačítko synchronizovat nasaďte změny.

5. Podívejte se na výsledek.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/data-sync-result.png" alt-text="Výsledek synchronizace dat":::

## <a name="summary"></a>Souhrn

Současné době více firem přesouvá své databáze na Azure Database for MySQL, protože tuto databázovou službu je možné snadno nastavit, spravovat a škálovat. Tato migrace nemusí být bolestivý. dbForge Studio pro MySQL Boasts Immaculate nástroje pro migraci, které můžou významně usnadnit proces. Studio umožňuje snadno nakonfigurovanou, uloženou, upravenou, automatizovanou a plánovanou přenos databází.

## <a name="next-steps"></a>Další kroky
- [Přehled MySQL](overview.md)