---
title: Obnovit databázi Azure SQL ze zálohy | Dokumentace Microsoftu
description: Další informace o obnovení bodu v čase, umožňující vrátit zpět Azure SQL Database k dřívějšímu bodu v čase (až po 35 dnů).
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: carlrab
manager: craigg
ms.date: 10/01/2018
ms.openlocfilehash: bdd3f5c5304cec0a562945ffaf412771e15b6031
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2018
ms.locfileid: "48248257"
---
# <a name="recover-an-azure-sql-database-using-automated-database-backups"></a>Obnovit databázi Azure SQL pomocí automatizovaných záloh databáze
Ve výchozím nastavení zálohování SQL Database jsou uložené v geograficky replikovaném blob storage (RA-GRS). Tyto možnosti jsou k dispozici pro databázi pomocí obnovení [automatizovaných záloh databáze](sql-database-automated-backups.md):

* Vytvořte novou databázi na stejném logickém serveru obnovení k určitému bodu v čase do doby uchování. 
* Vytvořte databázi na stejném logickém serveru na čas odstranění pro odstraněnou databázi obnovit.
* Vytvoření nové databáze v libovolné logický server v libovolné oblasti obnovit do bodu poslední zálohy.

Pokud jste nakonfigurovali [zálohovat dlouhodobě uchovávaných](sql-database-long-term-retention.md) můžete také vytvořit novou databázi ze zálohy jakékoli zleva doprava na jakékoli logickém serveru v libovolné oblasti.  

> [!IMPORTANT]
> Během obnovení nelze přepsat stávající databázi.
>

Při použití úrovně služeb Standard nebo Premium, obnovené databáze s sebou nese náklady náklady na úložiště za následujících podmínek: 
- Obnovení P11 – P15 a S4 až S12, P1 – P6, pokud je maximální velikost databáze větší než 500 GB.
- Obnovení P1 – P6 pro S4 až S12, pokud je maximální velikost databáze větší než 250 GB.

Nadbytečné je náklady, protože maximální velikost obnovené databáze je větší než velikost zahrnutého úložiště pro výpočetního prostředí a dodatečné úložiště zřízené nad rámec objemu zahrnutého v se navíc účtuje.  Podrobnosti o cenách dodatečného úložiště, najdete v článku [stránce s cenami SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).  Pokud se skutečné množství využité je menší než velikost zahrnutého úložiště, pak toho dalších poplatků se lze vyvarovat snížením maximální velikost databáze na objemu zahrnutého v ceně.  

> [!NOTE]
> [Automatizované zálohování databáze](sql-database-automated-backups.md) se používají při vytváření [kopírování databáze](sql-database-copy.md). 
>


## <a name="recovery-time"></a>Čas obnovení
Čas obnovení obnovíte databázi pomocí automatizovaných záloh databáze má vliv několika různými faktory: 

* Velikost databáze
* Výpočty velikosti databáze
* Počet zahrnutých protokoly transakcí
* Množství aktivit, které je potřeba znovu přehrát obnovení do bodu obnovení
* Šířka pásma sítě při obnovení do jiné oblasti 
* Počet souběžných obnovení požadavků na zpracování v cílové oblasti. 
  
  Pro velmi velké a aktivní databázi obnovení může trvat několik hodin. Pokud dlouhotrvající výpadku v oblasti, je možné, že existují velké množství geografické obnovení požadavků zpracovávaných jiných oblastech. Pokud existuje velký počet požadavků, může zvýšit dobu obnovení pro databáze v dané oblasti. Většina databázových obnoví dokončeno během 12 hodin.

Pro jedno předplatné, existují určitá omezení pro počet souběžných obnovení požadavků (včetně bodu v čase obnovení, geografické obnovení a obnovení z dlouhodobého uchování zálohy), které se odešle a pokračovat:
|  | **Maximální počet souběžných požadavků zpracovávaných** | **Maximální počet souběžných požadavků právě odesílá** |
| :--- | --: | --: |
|Izolovanou databázi (předplatné)|10|60|
|Elastický fond (na fond)|4|200|
||||

Neexistuje žádná vestavěná funkce hromadné obnovení. [Azure SQL Database: úplné obnovení serveru](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666) skript představuje příklad jednoho způsobu provádění této úlohy.

> [!IMPORTANT]
> Pokud chcete obnovit, pomocí automatizovaného zálohování, musí být členem role Přispěvatel SQL serveru v rámci předplatného nebo být vlastník předplatného – viz [RBAC: vestavěné role](../role-based-access-control/built-in-roles.md). Obnovení můžete provést pomocí webu Azure Portal, prostředí PowerShell nebo rozhraní REST API. Nelze použít jazyk Transact-SQL. 
> 

## <a name="point-in-time-restore"></a>Obnovení k určitému bodu v čase

Existující databázi můžete obnovit k dřívějšímu bodu v čase jako novou databázi na stejném logickém serveru pomocí webu Azure portal, [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/restore-azurermsqldatabase), nebo [rozhraní REST API](https://msdn.microsoft.com/library/azure/mt163685.aspx). 

> [!TIP]
> Ukázkový skript prostředí PowerShell ukazuje, jak provést obnovení bodu v čase databáze, najdete v části [obnovení databáze SQL pomocí prostředí PowerShell](scripts/sql-database-restore-database-powershell.md).
>

Databázi můžete obnovit pro všechny úrovně služeb a velikosti výpočetní prostředky a jako izolovanou databázi nebo do elastického fondu. Ujistěte se, že máte dostatek prostředků na logickém serveru nebo v elastickém fondu, na který provádíte obnovení databáze. Jakmile budete hotovi, obnovené databáze je normální, plně přístupné, online databáze. Obnovená databáze se účtuje za běžné sazby podle úrovně služeb a jeho velikost výpočetních. Není účtovat poplatky, dokud se nedokončí obnovení databáze.

Obecně obnovení databáze k dřívějšímu bodu pro účely obnovení. Pokud tak učiníte, můžete považovat za náhradu za původní databázi obnovené databáze nebo ho použít k načtení dat z a pak aktualizujte původní databáze. 

* ***Databáze nahrazení:*** Pokud obnovené databáze je určena k nahrazení původní databáze, měli byste ověřit výpočetního prostředí a/nebo úroveň služby jsou vhodné a škálovat databáze v případě potřeby. Můžete přejmenovat původní databáze a poté poskytnout obnovené databáze na původní název pomocí [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-database) v T-SQL. 
* ***Obnovení dat:*** Pokud budete chtít načíst data z obnovené databáze pro obnovení po chybě uživatele nebo aplikace, budete muset psát a spustit skripty pro zotavení potřebná data extrahovat data z obnovené databáze do původní databáze. I když operace obnovení může trvat dlouhou dobu pro dokončení, obnovení databáze je zobrazen v seznamu databází v průběhu procesu obnovení. Pokud během obnovení odstraníte databáze, operace obnovení byla zrušena a se vám neúčtují poplatky pro databáze, která nebyla dokončena, obnovení. 

### <a name="azure-portal"></a>portál Azure

Chcete-li provést obnovení do bodu v čase pomocí webu Azure portal, otevřete stránku pro vaši databázi a klikněte na **obnovení** na panelu nástrojů.

![bod v čase obnovení](./media/sql-database-recovery-using-backups/point-in-time-recovery.png)

## <a name="deleted-database-restore"></a>Obnovení odstraněné databáze
Čas odstranění pro odstraněnou databázi na stejném logickém serveru pomocí webu Azure portal, můžete obnovit odstraněnou databázi [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/restore-azurermsqldatabase), nebo [REST (createMode = obnovit)](https://msdn.microsoft.com/library/azure/mt163685.aspx). Můžete obnovit odstraněnou databázi k dřívějšímu bodu v době uchování pomocí [Powershellu](https://docs.microsoft.com/powershell/module/azurerm.sql/restore-azurermsqldatabase).

> [!Note]
> Obnovení odstraněné databáze není k dispozici ve spravované instanci.

> [!TIP]
> Ukázkový skript prostředí PowerShell ukazuje, jak obnovit odstraněnou databázi, naleznete v tématu [obnovení databáze SQL pomocí prostředí PowerShell](scripts/sql-database-restore-database-powershell.md).
>

> [!IMPORTANT]
> Při odstranění instance serveru Azure SQL Database, jeho databáze se také odstraní a nejde obnovit. Aktuálně není dostupná podpora pro obnovení odstraněného serveru.
> 

### <a name="azure-portal"></a>portál Azure

Obnovení odstraněné databáze během jeho [dobu uchování model založený na DTU](sql-database-service-tiers-dtu.md) nebo [dobu uchování model založený na virtuálních jádrech](sql-database-service-tiers-vcore.md) pomocí webu Azure portal, otevřete stránku pro váš server a v oblasti operace, klikněte na **Odstranila databáze**.

![deleted-database-restore-1](./media/sql-database-recovery-using-backups/deleted-database-restore-1.png)


![deleted-database-restore-2](./media/sql-database-recovery-using-backups/deleted-database-restore-2.png)

## <a name="geo-restore"></a>Geografické obnovení
Obnovení databáze SQL na libovolném serveru v libovolné oblasti Azure z poslední geograficky replikovaného úplné a rozdílové zálohy. Geografické obnovení pomocí geograficky redundantní zálohy jako zdroj a slouží k obnovení databáze, i když je nejsou dostupné kvůli výpadku databáze nebo datového centra. 

> [!Note]
> Geografické obnovení není k dispozici ve spravované instanci.

Geografické obnovení je výchozí možnost zotavení, pokud vaše databáze není k dispozici z důvodu incidentu v oblasti, kde se hostuje databázi. Pokud ve velkém měřítku incidentů v oblasti výsledky v nedostupnost databázovou aplikaci, můžete obnovit databázi z geograficky replikovaných záloh na server v jiné oblasti. Dochází ke zpoždění mezi pořizování rozdílové zálohy a kdy je geograficky replikované do služby Azure blob do jiné oblasti. Toto zpoždění může být až hodinu, proto, pokud dojde k havárii, může trvat jednu hodinu ztrátu. Následující obrázek znázorňuje obnovit databázi z poslední dostupnou zálohou v jiné oblasti.

![Geografické obnovení](./media/sql-database-geo-restore/geo-restore-2.png)

> [!TIP]
> Ukázkový skript prostředí PowerShell ukazuje, jak provést geografické obnovení, najdete v části [obnovení databáze SQL pomocí prostředí PowerShell](scripts/sql-database-restore-database-powershell.md).
> 

Obnovení bodu v čase na typu geo-secondary se momentálně nepodporuje. Obnovení k určitému bodu v čase lze provést pouze u primární databáze. Podrobné informace o použití geografické obnovení k zotavení z výpadku, naleznete v tématu [zotavení z výpadku](sql-database-disaster-recovery.md).

> [!IMPORTANT]
> Obnovení ze záloh je naprosto základní řešení zotavení po havárii, která je k dispozici ve službě SQL Database s cílem bodu nejdelší obnovení (RPO) a doba obnovení odhad (ERT). Pro řešení s využitím malou velikost databáze (například základní úrovně nebo malou velikost databází v elastických fondech tenantů) geografické obnovení je často rozumné řešení zotavení po Havárii pomocí ERT 12 hodin. Řešení pomocí velkých databází a vyžadovat obnovení kratší dobu, měli byste zvážit použití [převzetí služeb při selhání skupiny a aktivní geografickou replikaci](sql-database-geo-replication-overview.md). Aktivní geografická replikace nabízí mnohem nižší cíl bodu obnovení a vložit vyžaduje pouze zahájení převzetí služeb při selhání do průběžně replikovanou sekundární lokality. Další informace o volby obchodní kontinuity podnikových procesů najdete v tématu [přehled kontinuity](sql-database-business-continuity.md).
> 

### <a name="azure-portal"></a>portál Azure

Geografické obnovení a databáze během jeho [dobu uchování model založený na DTU](sql-database-service-tiers-dtu.md) nebo [dobu uchování model založený na virtuálních jádrech](sql-database-service-tiers-vcore.md) pomocí webu Azure portal, otevřete stránku databází SQL a pak klikněte na tlačítko **přidat** . V **výběr zdroje** textového pole, vyberte **zálohování**. Zadejte zálohování, ze kterého se má provést obnovení v oblasti a na serveru podle vašeho výběru. 

## <a name="programmatically-performing-recovery-using-automated-backups"></a>Provádí obnovení pomocí automatizovaného zálohování se prostřednictvím kódu programu
Jak bylo uvedeno výše, kromě webu Azure portal je možné provádět obnovení databáze prostřednictvím kódu programu pomocí Azure Powershellu nebo rozhraní REST API. Následující tabulky popisují sadu příkazů, které jsou k dispozici.

### <a name="powershell"></a>PowerShell
| Rutina | Popis |
| --- | --- |
| [Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase) |Získá jednu nebo více databází. |
| [Get-AzureRMSqlDeletedDatabaseBackup](/powershell/module/azurerm.sql/get-azurermsqldeleteddatabasebackup) | Získá odstraněnou databázi, kterou můžete obnovit. |
| [Get-AzureRmSqlDatabaseGeoBackup](/powershell/module/azurerm.sql/get-azurermsqldatabasegeobackup) |Získá geograficky redundantní zálohu databáze. |
| [Restore-AzureRmSqlDatabase](/powershell/module/azurerm.sql/restore-azurermsqldatabase) |Obnoví databázi SQL. |
|  | |

### <a name="rest-api"></a>REST API
| Rozhraní API | Popis |
| --- | --- |
| [REST (createMode = obnovení)](https://msdn.microsoft.com/library/azure/mt163685.aspx) |Obnoví databázi |
| [Získat vytvořit nebo aktualizovat stav databáze](https://msdn.microsoft.com/library/azure/mt643934.aspx) |Vrátí stav během operace obnovení |
|  | |

## <a name="summary"></a>Souhrn
Automatické zálohování ochrana vaší databáze od uživatele a chyb aplikací a database náhodné odstranění dlouhotrvající výpadků. Tato integrovaná funkce je dostupná pro všechny úrovně služeb a velikostí výpočetních. 

## <a name="next-steps"></a>Další postup
* Přehled zajištění provozní kontinuity podnikání a scénáře, naleznete v tématu [přehled zajištění provozní kontinuity firmy](sql-database-business-continuity.md).
* Další informace o Azure SQL Database, automatické zálohování, naleznete v tématu [automatické zálohování SQL Database](sql-database-automated-backups.md).
* Další informace o dlouhodobé uchovávání dat najdete v tématu [dlouhodobé uchovávání](sql-database-long-term-retention.md).
* Další informace o možnosti rychlejší obnovení najdete v tématu [převzetí služeb při selhání skupiny a aktivní geografickou replikaci](sql-database-geo-replication-overview.md).  
