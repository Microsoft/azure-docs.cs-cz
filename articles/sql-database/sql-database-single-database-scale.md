---
title: Škálování prostředků jednoúčelové databáze
description: Tento článek popisuje, jak škálovat výpočetní prostředky a prostředky úložiště, které jsou k dispozici pro jednu databázi v Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/10/2020
ms.openlocfilehash: 84846e642fa102045b89eb12dbc85b0995867a3e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061599"
---
# <a name="scale-single-database-resources-in-azure-sql-database"></a>Škálování prostředků jedné databáze v Azure SQL Database

Tento článek popisuje, jak škálovat výpočetní prostředky a prostředky úložiště, které jsou k dispozici pro Azure SQL Database v zřízené výpočetní vrstvy. Výpočetní [vrstva bez serveru](sql-database-serverless.md) také poskytuje automatické škálování výpočetních prostředků a účty za sekundu pro použité výpočetní prostředky.

Po počátečním výběru počtu virtuálních jader nebo DTU můžete dynamicky škálovat jednu databázi nahoru nebo dolů na základě skutečného prostředí pomocí [portálu Azure](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server), [Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1), [PowerShellu](/powershell/module/az.sql/set-azsqldatabase), [rozhraní API Azure](/cli/azure/sql/db#az-sql-db-update)nebo rozhraní REST [API](https://docs.microsoft.com/rest/api/sql/databases/update).

Následující video ukazuje dynamicky mění úroveň služby a výpočetní velikost pro zvýšení dostupné DTU pro jednu databázi.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]

> [!IMPORTANT]
> Za určitých okolností může být nutné zmenšit databázi, aby bylo možné uvolnit nevyužité místo. Další informace najdete [v tématu Správa místa v souborech v Azure SQL Database](sql-database-file-space-management.md).

## <a name="impact"></a>Dopad

Změna úrovně služby nebo výpočetní velikost hlavně zahrnuje službu provádějící následující kroky:

1. Vytvoření nové instance výpočetních prostředků pro databázi  

    Nová instance výpočetních prostředků se vytvoří s požadovanou úrovní služby a velikostí výpočetních prostředků. Pro některé kombinace změny velikosti úrovně služby a výpočetní velikosti musí být vytvořena replika databáze v nové výpočetní instanci, která zahrnuje kopírování dat a může silně ovlivnit celkovou latenci. Bez ohledu na to databáze zůstane online během tohoto kroku a připojení i nadále přesměrovány do databáze v původní výpočetní instance.

2. Přepnutí směrování připojení na novou výpočetní instanci

    Existující připojení k databázi v původní výpočetní instanci jsou vynechány. Všechna nová připojení jsou vytvořena k databázi v nové výpočetní instanci. Pro některé kombinace změny velikosti úrovně služby a výpočetní velikosti databázové soubory jsou odpojeny a znovu připojeny během přepínače.  Bez ohledu na to může mít přepínač za následek krátké přerušení služby, pokud databáze není obecně k dispozici po dobu kratší než 30 sekund a často pouze několik sekund. Pokud jsou spuštěny dlouho běžící transakce při přerušení připojení, doba trvání tohoto kroku může trvat déle, aby bylo možné obnovit přerušené transakce. [Zrychlené obnovení databáze](sql-database-accelerated-database-recovery.md) může snížit dopad přerušení dlouho běžící transakce.

> [!IMPORTANT]
> Během žádného kroku pracovního postupu nedojde ke ztrátě dat. Ujistěte se, že jste implementovali některé [logiky opakování](sql-database-connectivity-issues.md) v aplikacích a součástech, které používají Azure SQL Database při změně vrstvy služby.

## <a name="latency"></a>Latence 

Odhadovaná latence pro změnu úrovně služby nebo změnu škálování výpočetní velikosti jedné databáze nebo elastického fondu je parametrizována následujícím způsobem:

|Úroveň služeb|Základní jednotná databáze,</br>Standardní (S0-S1)|Základní elastický bazén,</br>standardní (S2-S12), </br>Hyperscale, </br>Univerzální jednotná databáze nebo elastický fond|Premium nebo business critical single database or elastic pool Premium or Business Critical Single database or Elastenou|
|:---|:---|:---|:---|
|**Základní databáze,</br> standardní (S0-S1)**|&bull;&nbsp;Konstantní časová latence nezávislá na použitém prostoru</br>&bull;&nbsp;Obvykle méně než 5 minut|&bull;&nbsp;Latence úměrná databázovému prostoru používanému z důvodu kopírování dat</br>&bull;&nbsp;Obvykle méně než 1 minuta na GB využitého místa|&bull;&nbsp;Latence úměrná databázovému prostoru používanému z důvodu kopírování dat</br>&bull;&nbsp;Obvykle méně než 1 minuta na GB využitého místa|
|**Základní elastický </br>fond, Standard (S2-S12), </br>Hyperscale, </br>Univerzální databáze nebo elastický fond**|&bull;&nbsp;Latence úměrná databázovému prostoru používanému z důvodu kopírování dat</br>&bull;&nbsp;Obvykle méně než 1 minuta na GB využitého místa|&bull;&nbsp;Konstantní časová latence nezávislá na použitém prostoru</br>&bull;&nbsp;Obvykle méně než 5 minut|&bull;&nbsp;Latence úměrná databázovému prostoru používanému z důvodu kopírování dat</br>&bull;&nbsp;Obvykle méně než 1 minuta na GB využitého místa|
|**Premium nebo business critical single database or elastic pool Premium or Business Critical Single database or Elastenou**|&bull;&nbsp;Latence úměrná databázovému prostoru používanému z důvodu kopírování dat</br>&bull;&nbsp;Obvykle méně než 1 minuta na GB využitého místa|&bull;&nbsp;Latence úměrná databázovému prostoru používanému z důvodu kopírování dat</br>&bull;&nbsp;Obvykle méně než 1 minuta na GB využitého místa|&bull;&nbsp;Latence úměrná databázovému prostoru používanému z důvodu kopírování dat</br>&bull;&nbsp;Obvykle méně než 1 minuta na GB využitého místa|

> [!TIP]
> Chcete-li sledovat probíhající operace, [přečtěte si](https://docs.microsoft.com/rest/api/sql/operations/list)následující informace: Správa operací pomocí rozhraní SQL REST API , Správa operací pomocí rozhraní [PŘÍKAZOVÉHO PŘÍKAZU](/cli/azure/sql/db/op), [Sledování operací pomocí t-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) a těchto dvou příkazů prostředí PowerShell: [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity) a [Stop-AzSqlDatabaseActivity](/powershell/module/az.sql/stop-azsqldatabaseactivity).

## <a name="cancelling-changes"></a>Zrušení změn

Operace změny stupnice úrovně služby nebo operace horizontálního navýšení kapacity výpočetní ch odst.

#### <a name="azure-portal"></a>portál Azure

V okně přehled u databáze přejděte na **Oznámení** a klikněte na dlaždici označující probíhající operaci:

![Probíhající provoz](media/sql-database-single-database-scale/ongoing-operations.png)

Dále klikněte na tlačítko s názvem **Zrušit tuto operaci**.

![Zrušit probíhající operaci](media/sql-database-single-database-scale/cancel-ongoing-operation.png)

#### <a name="powershell"></a>PowerShell

Na příkazovém řádku prostředí `$resourceGroupName` `$serverName`PowerShell `$databaseName`nastavte příkazy , , a , a spusťte následující příkaz:

```azurecli
$operationName = (az sql db op list --resource-group $resourceGroupName --server $serverName --database $databaseName --query "[?state=='InProgress'].name" --out tsv)
if (-not [string]::IsNullOrEmpty($operationName)) {
    (az sql db op cancel --resource-group $resourceGroupName --server $serverName --database $databaseName --name $operationName)
        "Operation " + $operationName + " has been canceled"
}
else {
    "No service tier change or compute rescaling operation found"
}
```

## <a name="additional-considerations"></a>Další aspekty

- Pokud upgradujete na vyšší úroveň služby nebo výpočetní velikost, maximální velikost databáze se nezvětší, pokud explicitně nezadáte větší velikost (maxsize).
- Chcete-li downgrade databáze, databáze používá místo musí být menší než maximální povolená velikost cílové úrovně služby a výpočetní velikost.
- Při downgradu z **úrovně Premium** na úroveň **Standard** se účtuje další náklady na úložiště, pokud je v cílové výpočetní velikosti podporována maximální velikost databáze (1) a (2) maximální velikost překračuje zahrnutou velikost úložiště cílové výpočetní velikosti. Například pokud databáze P1 s maximální velikostí 500 GB je zmenšenna na S3, pak se účtuje další náklady na úložiště, protože S3 podporuje maximální velikost 1 TB a jeho zahrnutá velikost úložiště je pouze 250 GB. Takže další velikost úložiště je 500 GB - 250 GB = 250 GB. Ceny dalšího úložiště najdete v tématu [ceny databáze SQL](https://azure.microsoft.com/pricing/details/sql-database/). Pokud je skutečné množství využitého místa menší než zahrnutá částka úložiště, lze se vyhnout této dodatečné ceně snížením maximální velikosti databáze na zahrnutou částku.
- Při upgradu databáze s [povolenou geografickou replikací](sql-database-geo-replication-portal.md) upgradujte její sekundární databáze na požadovanou úroveň služby a velikost výpočetních prostředků před upgradem primární databáze (obecné pokyny pro nejlepší výkon). Při upgradu na jinou edici je požadavek, aby sekundární databáze byla nejprve upgradována.
- Při downgradu databáze s [povolenou geografickou replikací,](sql-database-geo-replication-portal.md) downgrade jeho primární databáze na požadovanou úroveň služby a výpočetní velikost před downgrading sekundární databáze (obecné pokyny pro nejlepší výkon). Při přechodu na jinou edici je požadavek, aby primární databáze byla nejprve snížena.
- Nabídky služeb pro obnovení se u různých úrovní služby liší. Pokud přehlášujete úroveň **Basic,** je nižší doba uchovávání záloh. Viz [Zálohování databáze Azure SQL](sql-database-automated-backups.md).
- Nové vlastnosti databáze se nepoužijí, dokud nebudou změny dokončeny.

## <a name="billing"></a>Fakturace 

Poplatky se vám účtují za každou hodinu, kdy existuje databáze s použitím nejvyšší úrovně služby + výpočetní velikosti, která byla použita během této hodiny, bez ohledu na využití nebo na to, zda byla databáze aktivní méně než hodinu. Pokud například vytvoříte jednu databázi a o pět minut později ji odstraníte, bude faktura odrážet poplatek za jednu databázovou hodinu.

## <a name="change-storage-size"></a>Změna velikosti úložiště

### <a name="vcore-based-purchasing-model"></a>Nákupní model založený na virtuálních jádrech

- Úložiště lze zřídit až do maximálního limitu velikosti úložiště pomocí přírůstků o velikosti 1 GB. Minimální konfigurovatelné úložiště dat je 1 GB. Viz stránky dokumentace omezení prostředků pro [jednotlivé databáze](sql-database-vcore-resource-limits-single-databases.md) a [elastické fondy](sql-database-vcore-resource-limits-elastic-pools.md) pro maximální limity velikosti úložiště úložiště v jednotlivých cílech služby.
- Úložiště dat pro jednu databázi lze zřídit zvýšením nebo snížením maximální velikosti pomocí [portálu Azure](https://portal.azure.com), [Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1), [PowerShellu](/powershell/module/az.sql/set-azsqldatabase), [rozhraní API Azure](/cli/azure/sql/db#az-sql-db-update)nebo rozhraní REST [API](https://docs.microsoft.com/rest/api/sql/databases/update). Pokud je maximální hodnota velikosti zadána v bajtech, musí být násobkem 1 GB (1073741824 bajtů).
- Množství dat, která mohou být uložena v datových souborech databáze, je omezeno maximální velikostí nakonfigurovaného úložiště dat. Kromě tohoto úložiště sql database automaticky přiděluje 30 % více úložiště, které se má použít pro transakční protokol.
- SQL Database automaticky přiděluje 32 `tempdb` GB na virtuální jádro pro databázi. `tempdb`se nachází v místním úložišti SSD ve všech úrovních služeb.
- Cena úložiště pro jednu databázi nebo elastický fond je součtem částek úložiště dat a transakčních protokolů vynásobených jednotkovou cenou úložiště vrstvy služby. Cena je `tempdb` zahrnuta v ceně. Podrobnosti o ceně úložiště naleznete v tématu [ceny databáze SQL](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Za určitých okolností může být nutné zmenšit databázi, aby bylo možné uvolnit nevyužité místo. Další informace najdete [v tématu Správa místa v souborech v Azure SQL Database](sql-database-file-space-management.md).

### <a name="dtu-based-purchasing-model"></a>Nákupní model založený na DTU

- Cena DTU pro jednu databázi zahrnuje určité množství úložiště bez dodatečných nákladů. Další úložiště nad rámec zahrnuté částky lze zřídit za dodatečné náklady až do maximálního limitu velikosti v přírůstcích po 250 GB až 1 TB a pak v krocích po 256 GB nad 1 TB. Zahrnuté částky úložiště a maximální limity velikosti najdete [v tématu Jedna databáze: Velikosti úložiště a výpočetní velikosti](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-compute-sizes).
- Další úložiště pro jednu databázi lze zřídit zvýšením jeho maximální velikosti pomocí portálu Azure, [Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1), [PowerShell](/powershell/module/az.sql/set-azsqldatabase), [Azure CLI](/cli/azure/sql/db#az-sql-db-update)nebo [rozhraní REST API](https://docs.microsoft.com/rest/api/sql/databases/update).
- Cena dodatečného úložiště pro jednu databázi je částka dodatečného úložiště vynásobená jednotkovou cenou dodatečného úložiště úrovně služby. Podrobnosti o ceně dalšího úložiště naleznete v tématu [ceny databáze SQL](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Za určitých okolností může být nutné zmenšit databázi, aby bylo možné uvolnit nevyužité místo. Další informace najdete [v tématu Správa místa v souborech v Azure SQL Database](sql-database-file-space-management.md).

### <a name="geo-replicated-database"></a>Geograficky replikovaná databáze

Chcete-li změnit velikost databáze replikované sekundární databáze, změňte velikost primární databáze. Tato změna pak bude replikována a implementována také v sekundární databázi. 

## <a name="p11-and-p15-constraints-when-max-size-greater-than-1-tb"></a>Omezení P11 a P15 při maximální velikosti větší než 1 TB

Více než 1 TB úložiště na úrovni Premium je v současné době k dispozici ve všech oblastech kromě: Čína – východ, Čína – sever, Německo – střed, Německo – severovýchod, Západní střed USA, USA DoD a Us Government Central. V těchto oblastech je maximální úložiště na úrovni Premium omezeno na 1 TB. Následující aspekty a omezení platí pro databáze P11 a P15 s maximální velikostí větší než 1 TB:

- Pokud byla maximální velikost databáze P11 nebo P15 někdy nastavena na hodnotu větší než 1 TB, pak ji lze obnovit nebo zkopírovat pouze do databáze P11 nebo P15.  Následně databáze může být přeměřítka na jinou velikost výpočetních za předpokladu, že množství místa přidělené v době operace škálování nepřekročí maximální velikost omezení nové výpočetní velikosti.
- Pro aktivní scénáře geografické replikace:
  - Nastavení geografické replikace vztah: Pokud primární databáze je P11 nebo P15, sekundární (y) musí být také P11 nebo P15; nižší výpočetní velikost jsou odmítnuty jako sekundární, protože nejsou schopny podporovat více než 1 TB.
  - Inovace primární databáze ve vztahu geografické replikace: Změna maximální velikosti na více než 1 TB v primární databázi spustí stejnou změnu v sekundární databázi. Oba upgrady musí být úspěšné, aby se změna primárního efektu projevila. Platí omezení oblasti pro možnost více než 1 TB. Pokud sekundární je v oblasti, která nepodporuje více než 1 TB, primární není inovován.
- Použití služby Import/Export pro načítání databází P11/P15 s více než 1 TB není podporováno. Pomocí nástroje SqlPackage.exe [můžete importovat](sql-database-import.md) a [exportovat](sql-database-export.md) data.

## <a name="next-steps"></a>Další kroky

Celková omezení prostředků naleznete v [tématu LIMITY prostředků založených na virtuálních jádrech SQL Database – jedna databáze](sql-database-vcore-resource-limits-single-databases.md) a limity prostředků založených na [databázi SQL Database – elastické fondy](sql-database-dtu-resource-limits-single-databases.md).
