---
title: Obnovit databázi Azure SQL ze zálohy | Dokumentace Microsoftu
description: Další informace o obnovení bodu v čase, umožňující vrátit zpět Azure SQL Database k dřívějšímu bodu v čase (až po 35 dnů).
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 8d2cac7635b9d97561b3cebf517c95855407cbe3
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55462779"
---
# <a name="recover-an-azure-sql-database-using-automated-database-backups"></a>Obnovit databázi Azure SQL pomocí automatizovaných záloh databáze

Ve výchozím nastavení zálohování SQL Database jsou uložené v geograficky replikovaném blob storage (RA-GRS). Tyto možnosti jsou k dispozici pro databázi pomocí obnovení [automatizovaných záloh databáze](sql-database-automated-backups.md):

- Vytvořte novou databázi na stejný server SQL Database obnovení k určitému bodu v čase do doby uchování.
- Vytvořte databázi na stejný server SQL Database na čas odstranění pro odstraněnou databázi obnovit.
- Vytvořte novou databázi na všechny databáze SQL serveru ve stejné oblasti obnovit do bodu poslední zálohy.
- Vytvořte novou databázi na libovolném serveru SQL Database v kterékoli jiné oblasti obnovit do bodu nejnovější replikovaných záloh.

Pokud jste nakonfigurovali [zálohovat dlouhodobě uchovávaných](sql-database-long-term-retention.md) můžete také vytvořit novou databázi ze zálohy jakékoli zleva doprava na libovolném serveru SQL Database v libovolné oblasti.

> [!IMPORTANT]
> Během obnovení nelze přepsat stávající databázi.

Při použití úrovně služeb Standard nebo Premium, obnovené databáze s sebou nese náklady náklady na úložiště za následujících podmínek:

- Obnovení P11 – P15 a S4 až S12, P1 – P6, pokud je maximální velikost databáze větší než 500 GB.
- Obnovení P1 – P6 pro S4 až S12, pokud je maximální velikost databáze větší než 250 GB.

Nadbytečné je náklady, protože maximální velikost obnovené databáze je větší než velikost zahrnutého úložiště pro výpočetního prostředí a dodatečné úložiště zřízené nad rámec objemu zahrnutého v se navíc účtuje. Podrobnosti o cenách dodatečného úložiště, najdete v článku [stránce s cenami SQL Database](https://azure.microsoft.com/pricing/details/sql-database/). Pokud se skutečné množství využité je menší než velikost zahrnutého úložiště, pak toho dalších poplatků se lze vyvarovat snížením maximální velikost databáze na objemu zahrnutého v ceně.

> [!NOTE]
> [Automatizované zálohování databáze](sql-database-automated-backups.md) se používají při vytváření [kopírování databáze](sql-database-copy.md).

## <a name="recovery-time"></a>Čas obnovení

Čas obnovení obnovíte databázi pomocí automatizovaných záloh databáze má vliv několika různými faktory:

- Velikost databáze
- Výpočty velikosti databáze
- Počet zahrnutých protokoly transakcí
- Množství aktivit, které je potřeba znovu přehrát obnovení do bodu obnovení
- Šířka pásma sítě při obnovení do jiné oblasti
- Počet souběžných obnovení požadavků na zpracování v cílové oblasti

Pro velmi velké a aktivní databázi obnovení může trvat několik hodin. Pokud dlouhotrvající výpadku v oblasti, je možné, že existují velké množství geografické obnovení požadavků zpracovávaných jiných oblastech. Pokud existuje velký počet požadavků, může zvýšit dobu obnovení pro databáze v dané oblasti. Většina databázových obnoví dokončí méně než 12 hodin.

Pro jedno předplatné, existují určitá omezení pro počet souběžných obnovení požadavků (včetně bodu v čase obnovení, geografické obnovení a obnovení z dlouhodobého uchování zálohy), které se odešle a pokračovat:

| | **Maximální počet souběžných požadavků zpracovávaných** | **Maximální počet souběžných požadavků právě odesílá** |
| :--- | --: | --: |
|Izolovanou databázi (předplatné)|10|60|
|Elastický fond (na fond)|4|200|
||||

Neexistuje žádná vestavěná funkce hromadné obnovení. [Azure SQL Database: Úplné obnovení serveru](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666) skript představuje příklad jednoho způsobu provádění této úlohy.

> [!IMPORTANT]
> Pokud chcete obnovit, pomocí automatizovaného zálohování, musí být členem role Přispěvatel SQL serveru v rámci předplatného nebo být vlastník předplatného – viz [RBAC: Předdefinované role](../role-based-access-control/built-in-roles.md). Obnovení můžete provést pomocí webu Azure Portal, prostředí PowerShell nebo rozhraní REST API. Nelze použít jazyk Transact-SQL.

## <a name="point-in-time-restore"></a>Obnovení k určitému bodu v čase

Dokáže obnovit samostatnou ve fondu, nebo instanci databáze k dřívějšímu bodu v čase jako novou databázi na stejném serveru pomocí webu Azure portal, [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/restore-azurermsqldatabase), nebo [rozhraní REST API](https://docs.microsoft.com/rest/api/sql/databases). Databázi můžete obnovit na všech úrovních služby nebo vypočítat velikost. Ujistěte se, že máte dostatek prostředků na serveru, na který provádíte obnovení databáze. Jakmile budete hotovi, obnovené databáze je normální, plně přístupné, online databáze. Obnovená databáze se účtuje za běžné sazby podle úrovně služeb a jeho velikost výpočetních. Není účtovat poplatky, dokud se nedokončí obnovení databáze.

Obecně obnovení databáze k dřívějšímu bodu pro účely obnovení. Pokud tak učiníte, můžete považovat za náhradu za původní databázi obnovené databáze nebo ho použít k načtení dat z a pak aktualizujte původní databáze.

- **Nahrazení databáze**

  Pokud obnovené databáze je určena k nahrazení původní databáze, měli byste ověřit výpočetního prostředí a/nebo úrovně služby jsou vhodné a škálování databáze v případě potřeby. Můžete přejmenovat původní databáze a poté poskytnout obnovené databáze na původní název pomocí [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-database) v T-SQL.

- **Obnovení dat**

  Pokud budete chtít načíst data z obnovené databáze pro obnovení po chybě uživatele nebo aplikace, musíte pro zápis a spouštění skriptů obnovení potřebná data extrahovat data z obnovené databáze do původní databáze. I když operace obnovení může trvat dlouhou dobu pro dokončení, obnovení databáze je zobrazen v seznamu databází v průběhu procesu obnovení. Pokud během obnovení odstraníte databáze, operace obnovení byla zrušena a se vám neúčtují poplatky pro databáze, která nebyla dokončena, obnovení.

Obnovit samostatnou ve fondu, nebo instanci databáze do bodu v čase pomocí webu Azure portal, otevřete stránku pro vaši databázi a klikněte na tlačítko **obnovení** na panelu nástrojů.

![point-in-time-restore](./media/sql-database-recovery-using-backups/point-in-time-recovery.png)

> [!IMPORTANT]
> Programová obnova databáze ze zálohy, naleznete v tématu [pomocí provádí programově obnovení automatizované zálohování](sql-database-recovery-using-backups.md#programmatically-performing-recovery-using-automated-backups)

## <a name="deleted-database-restore"></a>Obnovení odstraněné databáze

Čas odstranění pro odstraněnou databázi na stejný server SQL Database pomocí webu Azure portal, můžete obnovit odstraněnou databázi [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/restore-azurermsqldatabase), nebo [REST (createMode = obnovit)](https://docs.microsoft.com/rest/api/sql/databases/createorupdate). Je možné [obnovení odstraněné databáze na Managed Instance pomocí prostředí PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2019/01/21/recreate-dropped-database-on-azure-sql-managed-instance). Můžete obnovit odstraněnou databázi k dřívějšímu bodu v době uchování pomocí [Powershellu](https://docs.microsoft.com/powershell/module/azurerm.sql/restore-azurermsqldatabase).

> [!TIP]
> Ukázkový skript prostředí PowerShell ukazuje, jak obnovit odstraněnou databázi, naleznete v tématu [obnovení databáze SQL pomocí prostředí PowerShell](scripts/sql-database-restore-database-powershell.md).
> [!IMPORTANT]
> Při odstranění instance serveru Azure SQL Database, jeho databáze se také odstraní a nejde obnovit. Aktuálně není dostupná podpora pro obnovení odstraněného serveru.

### <a name="deleted-database-restore-using-the-azure-portal"></a>Obnovení odstraněné databáze pomocí webu Azure portal

Obnovení odstraněné databáze pomocí webu Azure portal během jeho [dobu uchování model založený na DTU](sql-database-service-tiers-dtu.md) nebo [dobu uchování model založený na virtuálních jádrech](sql-database-service-tiers-vcore.md) pomocí webu Azure portal otevřete stránku pro váš server a v Operace oblast, klikněte na tlačítko **odstranila databáze**.

![deleted-database-restore-1](./media/sql-database-recovery-using-backups/deleted-database-restore-1.png)

![deleted-database-restore-2](./media/sql-database-recovery-using-backups/deleted-database-restore-2.png)

> [!IMPORTANT]
> Obnovení odstraněné databáze prostřednictvím kódu programu, najdete v článku [pomocí provádí programově obnovení automatizované zálohování](sql-database-recovery-using-backups.md#programmatically-performing-recovery-using-automated-backups)

## <a name="geo-restore"></a>Geografické obnovení

Obnovení databáze SQL na libovolném serveru v libovolné oblasti Azure z poslední geograficky replikovaných záloh. Geografické obnovení pomocí geograficky redundantní zálohy jako zdroj a slouží k obnovení databáze, i když je nejsou dostupné kvůli výpadku databáze nebo datového centra.

> [!Note]
> Geografické obnovení není k dispozici ve spravované instanci.

Geografické obnovení je výchozí možnost zotavení, pokud vaše databáze není k dispozici z důvodu incidentu v oblasti, kde se hostuje databázi. Pokud ve velkém měřítku incidentů v oblasti výsledky v nedostupnost databázovou aplikaci, můžete obnovit databázi z geograficky replikovaných záloh na server v jiné oblasti. Dochází ke zpoždění mezi pořizování zálohy a kdy je geograficky replikované do služby Azure blob do jiné oblasti. Toto zpoždění může být až hodinu, proto, pokud dojde k havárii, může trvat jednu hodinu ztrátu. Následující obrázek znázorňuje obnovit databázi z poslední dostupnou zálohou v jiné oblasti.

![geo-restore](./media/sql-database-geo-restore/geo-restore-2.png)

> [!TIP]
> Ukázkový skript prostředí PowerShell ukazuje, jak provést geografické obnovení, najdete v části [obnovení databáze SQL pomocí prostředí PowerShell](scripts/sql-database-restore-database-powershell.md).

Obnovení bodu v čase na typu geo-secondary se momentálně nepodporuje. Obnovení k určitému bodu v čase lze provést pouze u primární databáze. Podrobné informace o použití geografické obnovení k zotavení z výpadku, naleznete v tématu [zotavení z výpadku](sql-database-disaster-recovery.md).

> [!IMPORTANT]
> Obnovení ze záloh je naprosto základní řešení zotavení po havárii, která je k dispozici ve službě SQL Database s cílem bodu nejdelší obnovení (RPO) a doba obnovení odhad (ERT). Pro řešení s využitím malou velikost databáze (například základní úrovně nebo malou velikost databází v elastických fondech tenantů) geografické obnovení je často rozumné řešení zotavení po Havárii pomocí ERT až 12 hodin (obvykle mnohem menší). Řešení pomocí velkých databází a vyžadovat obnovení kratší dobu, měli byste zvážit použití [aktivní geografickou replikaci](sql-database-active-geo-replication.md) nebo [-automatické převzetí služeb při selhání skupiny](sql-database-auto-failover-group.md). Aktivní geografická replikace nabízí mnohem nižší cíl bodu obnovení a vložit vyžaduje pouze zahájení převzetí služeb při selhání do průběžně replikovanou sekundární lokality. Automatické převzetí služeb při selhání skupiny umožňují automatické převzetí služeb při selhání pro skupinu databází. Další informace o volby obchodní kontinuity podnikových procesů najdete v tématu [přehled kontinuity](sql-database-business-continuity.md).

### <a name="geo-restore-using-the-azure-portal"></a>Geografické obnovení prostřednictvím webu Azure portal

Geografické obnovení a databáze během jeho [dobu uchování model založený na DTU](sql-database-service-tiers-dtu.md) nebo [dobu uchování model založený na virtuálních jádrech](sql-database-service-tiers-vcore.md) pomocí webu Azure portal, otevřete stránku databází SQL a pak klikněte na tlačítko **přidat** . V **výběr zdroje** textového pole, vyberte **zálohování**. Zadejte zálohování, ze kterého se má provést obnovení v oblasti a na serveru podle vašeho výběru.

## <a name="programmatically-performing-recovery-using-automated-backups"></a>Provádí obnovení pomocí automatizovaného zálohování se prostřednictvím kódu programu

Jak bylo uvedeno výše, kromě webu Azure portal je možné provádět obnovení databáze prostřednictvím kódu programu pomocí Azure Powershellu nebo rozhraní REST API. Následující tabulky popisují sadu příkazů, které jsou k dispozici.

### <a name="powershell"></a>PowerShell

- K obnovení samostatné nebo databázi ve fondu, naleznete v tématu [Restore-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/module/azurerm.sql/restore-azurermsqldatabase)

  | Rutina | Popis |
  | --- | --- |
  | [Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase) |Získá jednu nebo více databází. |
  | [Get-AzureRMSqlDeletedDatabaseBackup](/powershell/module/azurerm.sql/get-azurermsqldeleteddatabasebackup) | Získá odstraněnou databázi, kterou můžete obnovit. |
  | [Get-AzureRmSqlDatabaseGeoBackup](/powershell/module/azurerm.sql/get-azurermsqldatabasegeobackup) |Získá geograficky redundantní zálohu databáze. |
  | [Restore-AzureRmSqlDatabase](/powershell/module/azurerm.sql/restore-azurermsqldatabase) |Obnoví databázi SQL. |

  > [!TIP]
  > Ukázkový skript prostředí PowerShell ukazuje, jak provést obnovení bodu v čase databáze, najdete v části [obnovení databáze SQL pomocí prostředí PowerShell](scripts/sql-database-restore-database-powershell.md).

- Chcete-li obnovit databázi Managed Instance, naleznete v tématu [v daném okamžiku obnovení databáze na spravované instanci Azure SQL pomocí prostředí AzureRm.Sql PowerShell knihovny](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/28/point-in-time-restore-of-a-database-on-azure-sql-managed-instance-using-azurerm-sql-powershell-library/)

### <a name="rest-api"></a>REST API

Chcete-li obnovit samostatné nebo databázi ve fondu pomocí rozhraní REST API:

| Rozhraní API | Popis |
| --- | --- |
| [REST (createMode = obnovení)](https://docs.microsoft.com/rest/api/sql/databases) |Obnoví databázi |
| [Získat vytvořit nebo aktualizovat stav databáze](https://docs.microsoft.com/rest/api/sql/operations) |Vrátí stav během operace obnovení |

### <a name="azure-cli"></a>Azure CLI

K obnovení samostatné nebo databázi ve fondu pomocí Azure CLI, najdete v článku [az sql db restore](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-restore).

## <a name="summary"></a>Souhrn

Automatické zálohování ochrana vaší databáze od uživatele a chyb aplikací a database náhodné odstranění dlouhotrvající výpadků. Tato integrovaná funkce je dostupná pro všechny úrovně služeb a velikostí výpočetních.

## <a name="next-steps"></a>Další postup

- Přehled zajištění provozní kontinuity podnikání a scénáře, naleznete v tématu [přehled zajištění provozní kontinuity firmy](sql-database-business-continuity.md).
- Další informace o Azure SQL Database, automatické zálohování, naleznete v tématu [automatické zálohování SQL Database](sql-database-automated-backups.md).
- Další informace o dlouhodobé uchovávání dat najdete v tématu [dlouhodobé uchovávání](sql-database-long-term-retention.md).
- Další informace o možnosti rychlejší obnovení najdete v tématu [aktivní geografickou replikaci](sql-database-active-geo-replication.md) nebo [-automatické převzetí služeb při selhání skupiny](sql-database-auto-failover-group.md).
