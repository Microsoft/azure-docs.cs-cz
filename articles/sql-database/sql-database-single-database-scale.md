---
title: Škálování izolované databáze prostředků – Azure SQL Database | Dokumentace Microsoftu
description: Tento článek popisuje, jak škálovat výpočetní a úložné prostředky dostupné pro izolované databáze ve službě Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
manager: craigg
ms.date: 04/26/2019
ms.openlocfilehash: 311015aff5ea7020043ad8e43fd987144cdcbf52
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67206740"
---
# <a name="scale-single-database-resources-in-azure-sql-database"></a>Škálování izolované databáze prostředků ve službě Azure SQL Database

Tento článek popisuje, jak škálovat výpočetní a úložné prostředky dostupné pro izolované databáze na úrovni zřízených výpočetních. Alternativně [bez serveru (preview) služba compute úrovně](sql-database-serverless.md) nabízí automatické škálování výpočetních a faktury za sekundu pro výpočetní prostředky, které používá.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Modul Azure PowerShell – Resource Manager je stále podporuje Azure SQL Database, ale všechny budoucí vývoj je Az.Sql modulu. Tyto rutiny najdete v části [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty pro příkazy v modulu Az a moduly AzureRm podstatně totožné.

## <a name="change-compute-size-vcores-or-dtus"></a>Změnit velikost výpočty (jader nebo Dtu)

Po počátečním výběru počet virtuálních jader a počet jednotek Dtu, můžete vertikálně izolovanou databázi směrem nahoru nebo dolů dynamicky na základě aktuálních zkušeností pomocí [webu Azure portal](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server), [příkazů jazyka Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1), [ Prostředí PowerShell](/powershell/module/az.sql/set-azsqldatabase), [rozhraní příkazového řádku Azure](/cli/azure/sql/db#az-sql-db-update), nebo [rozhraní REST API](https://docs.microsoft.com/rest/api/sql/databases/update).

Následující video ukazuje dynamické změny služby vrstvy a vypočítat velikost zvýšit dostupné Dtu pro izolovanou databázi.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]
>

> [!IMPORTANT]
> Za určitých okolností budete muset zmenšit databázi uvolnění nevyužívaného místa. Další informace najdete v tématu [spravovat místo souborů ve službě Azure SQL Database](sql-database-file-space-management.md).

### <a name="impact-of-changing-service-tier-or-rescaling-compute-size"></a>Změna velikost výpočetní služby vrstvě nebo změny měřítka

Změna služby vrstvy nebo výpočtu velikosti u jediné databáze hlavně zahrnuje službu provedením následujících kroků:

1. Vytvořit nové výpočetní instance pro databázi  

    Nové výpočetní instance databáze se vytvoří s úroveň požadované služby a výpočetního prostředí. Pro některé kombinace úroveň služby a změny velikosti výpočetního repliky databáze musí být vytvořeny v nové výpočetní instance, která zahrnuje kopírování dat a důrazně může ovlivnit celkovou latenci. Bez ohledu na to zůstane během tohoto kroku online databáze a připojení nadále přesměrováni do databáze na původní instanci výpočetní prostředky.

2. Přepnout směrování připojení k nové výpočetní instance

    Existující připojení k databázi v původní výpočetní instance se zahodí. Žádná nová připojení jsou vytvořeny v databázi v nové výpočetní instance. Pro některé kombinací úroveň služby a změny velikosti výpočetní prostředky jsou soubory databáze odpojit a znovu připojit během přepínač.  Přepínač bez ohledu na to, může vést ke krátkodobému přerušení služeb, pokud je databáze není k dispozici, obvykle pro méně než 30 sekund a často jenom pár sekund. Pokud jsou dlouho trvající transakce spuštěn, když se zahodí připojení, doba trvání tohoto kroku může trvat déle, aby bylo možné obnovit přerušené transakce. [Obnovení databáze Accelerated](sql-database-accelerated-database-recovery.md) snížit dopad přerušuje se dlouho probíhající transakce.

> [!IMPORTANT]
> Žádná data nejsou ztracena během libovolný krok v pracovním postupu.

### <a name="latency-of-changing-service-tier-or-rescaling-compute-size"></a>Latence Změna velikosti výpočetní služby vrstvě nebo změny měřítka

Odhadované latence změnit úroveň služby nebo změnit velikost výpočetních izolovanou databázi nebo elastický fond je parametrizované následujícím způsobem:

|Úroveň služeb|Základní izolované databáze</br>Standard (S0-S1)|Základní elastického fondu</br>Standard (S2-S12), </br>Hyperškálovatelného </br>Elastický fond nebo izolovanou databázi pro obecné účely|Elastický fond nebo izolovanou databázi Premium nebo pro důležité obchodní informace|
|:---|:---|:---|:---|
|**Základní izolovanou databázi,</br> Standard (S0-S1)**|&bull; &nbsp;Konstantní časovou náročnost nezávislé použitého místa</br>&bull; &nbsp;Obvykle méně než 5 minut|&bull; &nbsp;Latence úměrná místa v databázi použít kvůli kopírování dat</br>&bull; &nbsp;Obvykle, menší než 1 minuta za využité GB|&bull; &nbsp;Latence úměrná místa v databázi použít kvůli kopírování dat</br>&bull; &nbsp;Obvykle, menší než 1 minuta za využité GB|
|**Základní elastického fondu, </br>Standard (S2 S12), </br>Hyperškálovatelného </br>elastický fond nebo izolovanou databázi pro obecné účely**|&bull; &nbsp;Latence úměrná místa v databázi použít kvůli kopírování dat</br>&bull; &nbsp;Obvykle, menší než 1 minuta za využité GB|&bull; &nbsp;Konstantní časovou náročnost nezávislé použitého místa</br>&bull; &nbsp;Obvykle méně než 5 minut|&bull; &nbsp;Latence úměrná místa v databázi použít kvůli kopírování dat</br>&bull; &nbsp;Obvykle, menší než 1 minuta za využité GB|
|**Elastický fond nebo izolovanou databázi Premium nebo pro důležité obchodní informace**|&bull; &nbsp;Latence úměrná místa v databázi použít kvůli kopírování dat</br>&bull; &nbsp;Obvykle, menší než 1 minuta za využité GB|&bull; &nbsp;Latence úměrná místa v databázi použít kvůli kopírování dat</br>&bull; &nbsp;Obvykle, menší než 1 minuta za využité GB|&bull; &nbsp;Latence úměrná místa v databázi použít kvůli kopírování dat</br>&bull; &nbsp;Obvykle, menší než 1 minuta za využité GB|

> [!TIP]
> Monitorování operací v průběhu najdete v tématu: [Správa operací pomocí rozhraní SQL API REST](https://docs.microsoft.com/rest/api/sql/operations/list), [správě operací pomocí rozhraní příkazového řádku](/cli/azure/sql/db/op), [sledování operací s použitím jazyka T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) a tyto dva příkazy Powershellu: [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity) a [Stop-AzSqlDatabaseActivity](/powershell/module/az.sql/stop-azsqldatabaseactivity).

### <a name="cancelling-service-tier-changes-or-compute-rescaling-operations"></a>Ruší se změny úrovně služby nebo výpočetní operace změny měřítka

Úroveň služby změnit nebo výpočetní změny měřítka operace se dá zrušit.

#### <a name="azure-portal"></a>portál Azure

Na kartě s přehledem databáze, přejděte na **oznámení** a kliknutím na dlaždici, která probíhající operace:

![Probíhající operaci](media/sql-database-single-database-scale/ongoing-operations.png)

Pak klikněte na tlačítko s popiskem **zrušit tuto operaci**.

![Zrušte probíhající operaci](media/sql-database-single-database-scale/cancel-ongoing-operation.png)

#### <a name="powershell"></a>PowerShell

Z příkazového řádku Powershellu se nastavit `$ResourceGroupName`, `$ServerName`, a `$DatabaseName`, a pak spusťte následující příkaz:

```PowerShell
$OperationName = (az sql db op list --resource-group $ResourceGroupName --server $ServerName --database $DatabaseName --query "[?state=='InProgress'].name" --out tsv)
if(-not [string]::IsNullOrEmpty($OperationName))
    {
        (az sql db op cancel --resource-group $ResourceGroupName --server $ServerName --database $DatabaseName --name $OperationName)
        "Operation " + $OperationName + " has been canceled"
    }
    else
    {
        "No service tier change or compute rescaling operation found"
    }
```

### <a name="additional-considerations-when-changing-service-tier-or-rescaling-compute-size"></a>Další aspekty při změně služby velikost výpočetní vrstvě nebo změny měřítka

- Pokud provádíte upgrade na vyšší úroveň služby nebo vypočítat velikost, maximální velikost databáze se nezvyšuje, pokud explicitně neurčíte větší velikost (maxsize).
- Na starší verzi databáze, databáze používá prostor musí být menší než maximální povolená velikost cílové úrovni služeb a výpočetního prostředí.
- Při downgradu z verze **Premium** k **standardní** vrstvy, náklady na úložiště platí v případě, že (1) na maximální velikost databáze je podporováno v cílovou velikost výpočetních i (2) je maximální velikost překročí zahrnutou velikost úložiště cíle výpočty velikosti. Například pokud je databáze P1 a maximální velikosti 500 GB downsized na S3, pak náklady na úložiště platí od S3 podporuje maximální velikosti 500 GB a jeho velikost zahrnutého úložiště je pouze 250 GB. Ano velikost dodatečného úložiště je 500 GB – 250 GB = 250 GB. Ceny za dodatečné úložiště, najdete v části [SQL Database – ceny](https://azure.microsoft.com/pricing/details/sql-database/). Pokud se skutečné množství využité je menší než velikost zahrnutého úložiště, pak toho dalších poplatků se lze vyvarovat snížením maximální velikost databáze na objemu zahrnutého v ceně.
- Když upgradujete databázi s [geografickou replikaci](sql-database-geo-replication-portal.md) povolena, upgradovat její sekundární databáze na úrovni požadované služby a vypočítat velikost před upgradem primární databáze (Obecné pokyny pro zajištění nejlepšího výkonu). Při upgradu na jiný, upgrade sekundární databáze nejprve je povinný.
- Při downgradu databáze s [geografickou replikaci](sql-database-geo-replication-portal.md) povolena, downgradovat na úroveň požadovanou službu její primární databáze a vypočítat velikost před snížením sekundární databáze (Obecné pokyny pro zajištění nejlepšího výkonu). Při downgradu na jinou edici, downgradu primární databáze nejprve se vyžaduje.
- Nabídky služeb pro obnovení se u různých úrovní služby liší. Pokud přecházíte na **základní** vrstvy, je zkrátit období uchovávání záloh. Zobrazit [záloh Azure SQL Database](sql-database-automated-backups.md).
- Nové vlastnosti databáze se nepoužijí, dokud nebudou změny dokončeny.

### <a name="billing-during-compute-rescaling"></a>Během změny měřítka výpočetní fakturace

Se vám účtovat každá hodina existence databáze pomocí nejvyšší úroveň služby a vypočítat velikost, která během této hodiny využívalo, bez ohledu na využití nebo na to, jestli byl databáze aktivní kratší dobu než hodinu. Například pokud vytvoření izolované databáze a po pěti minutách ji odstraníte vyúčtování projeví účtovat jedna hodina používání databáze.

## <a name="change-storage-size"></a>Změnit velikost úložiště

### <a name="vcore-based-purchasing-model"></a>Nákupní model založený na virtuálních jádrech

- Úložiště lze zřídit až po limit maximální velikosti 1 GB přírůstcích pomocí. Minimální konfigurovatelné datové úložiště je 5 GB
- Zvýšením nebo snížením jeho maximální velikost pomocí je možné zřídit úložiště pro izolovanou databázi [webu Azure portal](https://portal.azure.com), [příkazů jazyka Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1), [Powershellu](/powershell/module/az.sql/set-azsqldatabase), [Rozhraní příkazového řádku azure](/cli/azure/sql/db#az-sql-db-update), nebo [rozhraní REST API](https://docs.microsoft.com/rest/api/sql/databases/update).
- SQL Database automaticky přiděluje 30 % další úložiště pro soubory protokolů a 32GB za – vCore pro databázi TempDB, ale která nepřekročí 384GB. Databáze TempDB je umístěná na připojené SSD ve všech úrovních služby.
- Ceny úložišť pro izolované databáze je součet množství dat úložiště a protokol úložiště vynásobí jednotkovou cenu úložiště na úrovni služby. Cena databáze tempdb je zahrnutá v ceně – vCore. Podrobnosti o cenách dodatečného úložiště najdete v tématu [SQL Database – ceny](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Za určitých okolností budete muset zmenšit databázi uvolnění nevyužívaného místa. Další informace najdete v tématu [spravovat místo souborů ve službě Azure SQL Database](sql-database-file-space-management.md).

### <a name="dtu-based-purchasing-model"></a>Nákupní model založený na DTU

- Cena za DTU pro izolovanou databázi zahrnuje objem úložiště bez dalších poplatků. Dodatečné úložiště nad rámec objemu zahrnutého v ceně je možné zřídit za poplatek až po limit maximální velikosti, v přírůstcích po 250 GB až 1 TB a potom dokupuje se násobek 256 GB nad rámec 1 TB. Částky zahrnutého úložiště a omezení maximální velikosti najdete v tématu [izolované databáze: Velikosti úložiště a výpočty velikostí](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-compute-sizes).
- Dodatečné úložiště pro izolovanou databázi je možné zřídit zvýšením jeho maximální velikost pomocí webu Azure portal, [příkazů jazyka Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1), [PowerShell](/powershell/module/az.sql/set-azsqldatabase), [rozhraní příkazového řádku Azure](/cli/azure/sql/db#az-sql-db-update), nebo [ Rozhraní REST API](https://docs.microsoft.com/rest/api/sql/databases/update).
- Cena dodatečného úložiště pro izolovanou databázi se velikost dodatečného úložiště vynásobí jednotkovou cenu dodatečné úložiště na úrovni služby. Podrobnosti o cenách dodatečného úložiště najdete v tématu [SQL Database – ceny](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Za určitých okolností budete muset zmenšit databázi uvolnění nevyužívaného místa. Další informace najdete v tématu [spravovat místo souborů ve službě Azure SQL Database](sql-database-file-space-management.md).

## <a name="p11-and-p15-constraints-when-max-size-greater-than-1-tb"></a>P11 a P15 omezení při maximální velikosti větší než 1 TB

Více než 1 TB úložiště na úrovni Premium je aktuálně k dispozici ve všech oblastech s výjimkou: Čína – východ, Čína – sever, Německo – střed, Německo – severovýchod, střed USA – Západ, oblastí pro úlohy ministerstva obrany USA a US Government centrální. V těchto oblastech je úložiště na úrovni Premium omezeno na 1 TB. Následující požadavky a omezení platí pro databáze P11 a P15 s maximální velikostí větší než 1 TB:

- Maximální velikost databáze P11 nebo P15 byl někdy nastavený na hodnotu větší než 1 TB, potom můžete ho jenom obnovit nebo zkopírován do databáze P11 nebo P15.  Databázi lze následně měřítka velikosti různými výpočetními zadaná velikost místa přidělená v okamžiku stala operace není delší než maximální velikost omezení novou velikost výpočetních.
- Pro scénáře aktivní geografickou replikaci:
  - Nastavení relaci geografické replikace: Pokud je primární databáze P11 nebo P15, secondary(ies) musí být také P11 nebo P15; menší velikost výpočetního odmítají jako sekundární databáze, protože nejsou schopný zajistit podporu více než 1 TB.
  - Upgrade primární databázi v relaci geografické replikace: Změna maximální velikosti větší než 1 TB na primární databázi aktivuje stejnou změnu v sekundární databázi. I upgrady musí být úspěšná, aby se změny na primárním se projeví. Platí omezení oblasti pro možnost více než 1 TB. Je-li sekundární v oblasti, která nepodporuje více než 1 TB, není aktualizován primární.
- Použití služby Import/Export pro načítání databáze P11 a P15 s více než 1 TB není podporováno. Použít SqlPackage.exe k [importovat](sql-database-import.md) a [exportovat](sql-database-export.md) data.

## <a name="next-steps"></a>Další postup

Celkové omezení prostředků najdete v tématu [omezení prostředků založený na virtuálních jádrech SQL Database – izolované databáze](sql-database-vcore-resource-limits-single-databases.md) a [omezení prostředků založený na DTU databáze SQL – elastických fondů](sql-database-dtu-resource-limits-single-databases.md).
