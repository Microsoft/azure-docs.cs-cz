---
title: Klasifikace služby SQL Data Warehouse | Dokumentace Microsoftu
description: Pokyny k používání klasifikace ke správě souběžnosti, význam a výpočetní prostředky pro dotazy ve službě Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload management
ms.date: 03/13/2019
ms.author: rortloff
ms.reviewer: jrasnick
ms.openlocfilehash: 888a64de29178834fc47199a033eb6bc62858e57
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2019
ms.locfileid: "59617746"
---
# <a name="sql-data-warehouse-workload-classification-preview"></a>Klasifikace úlohy SQL Data Warehouse (Preview)

Tento článek vysvětluje proces klasifikace úlohy SQL Data Warehouse na příchozí žádosti o přiřazení třídy prostředků a důležitosti.

> [!Note]
> Úloha klasifikace je dostupná ve verzi preview na SQL Data Warehouse Gen2. Klasifikace úlohy správy a význam ve verzi preview je pro sestavení s datem vydání verze z 9. dubna 2019 nebo novější.  Uživatelé byste neměli používat sestavení starší než toto datum pro testování úloh správy.  K určení, zda je sestavení umožňující správu úloh, spustit, vyberte @@version při připojení k vaší instanci SQL Data Warehouse.

## <a name="classification"></a>Klasifikace

> [!Video https://www.youtube.com/embed/QcCRBAhoXpM]

Úlohy správy klasifikace umožňuje úlohy zásady uplatňovat na požadavky prostřednictvím přiřazování [třídy prostředků](resource-classes-for-workload-management.md#what-are-resource-classes) a [význam](sql-data-warehouse-workload-importance.md).

Existuje mnoho způsobů, jak klasifikovat úlohy datových skladů, nejjednodušším a nejběžnějším klasifikace je zatížení a dotazu. Načtení dat pomocí odstranění příkazů insert, update a.  Dotazování dat pomocí vybere. Řešení datového skladu často bude mít zásadu zatížení pro zatížení aktivity, jako je například přiřazení vyšší třídě prostředků s dalšími materiály. Různé úlohy zásady možné aplikovat na dotazy, jako je například nižší ve srovnání s aktivity načíst význam.

Můžete také subclassify zatížení a dotaz úlohy. Dílčí klasifikace v rámci dává větší kontrolu na vašimi úlohami. Například dotaz úlohy se může skládat z datové krychle aktualizuje, dotazy na řídicí panel nebo ad-hoc dotazů. Můžete klasifikovat každý z těchto úloh dotazu pomocí různých prostředků třídy ani nastavení význam. Zatížení mohou také těžit z dílčí klasifikace v rámci. Velké transformace je možné přiřadit do větší třídy prostředků. Větší význam slouží k Ujistěte se, že klíče prodejních dat je zavaděč před data o počasí nebo sociální datového kanálu.

Ne všechny příkazy jsou klasifikovány jako vyžadovat prostředky nebo potřebujete význam k ovlivnění spuštění.  Příkaz DBCC příkazy nejsou klasifikovány příkazů BEGIN, potvrzení a vrácení transakce zpět.

## <a name="classification-process"></a>Proces klasifikace

Klasifikace ve službě SQL Data Warehouse je dosaženo dnes přiřadit uživatele k roli, která má přiřazena pomocí odpovídající prostředek třída [sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql). Možnost charakterizují požadavky nad těchto přihlášení pro třídu prostředků je omezené díky této funkci. Bohatší metodu pro klasifikaci je teď k dispozici [vytvořit ÚLOHU třídění](/sql/t-sql/statements/create-workload-classifier-transact-sql) syntaxe.  Touto syntaxí SQL Data Warehouse uživatelům můžete přiřadit význam a třídu prostředků se požadavky.  

> [!NOTE]
> Klasifikace se vyhodnocuje na základě žádosti. Jinak můžete být klasifikované v jedné relaci více požadavků.

## <a name="classification-precedence"></a>Priorita klasifikace

Jako součást procesu klasifikace priorita je v místo, kde můžete určit, která třída prostředků je přiřazena. Klasifikace na základě uživatele databáze má přednost před členství v roli. Pokud vytvoříte klasifikátor, který mapuje uživatele UserA databáze do třídy prostředků mediumrc. Do třídy prostředků largerc pak připojte databázové role RoleA (z nichž uživatel a je členem). Třídění, která se mapuje RoleA databázové role třídy prostředků largerc bude přednost klasifikátor, který mapuje uživatele databáze pro třídu prostředků mediumrc.

Pokud je uživatel členem více rolí pomocí tříd různých prostředků přiřazen nebo obsažena ve více třídění, uživatel dostane nejvyšší přiřazení třídy prostředků.  Toto chování je konzistentní se stávající chování přiřazení třídy prostředků.

## <a name="system-classifiers"></a>Třídění systému

Úloha klasifikace má třídění zatížení systému. Třídění systému mapování existující členství v rolích třídy prostředků pro přidělení prostředků třídy prostředků s normální důležitostí. Nelze vyřadit, systém Klasifikátory. Zobrazení systému třídění, můžete spustit následující dotaz:

```sql
SELECT * FROM sys.workload_management_workload_classifiers where classifier_id <= 12
```

## <a name="mixing-resource-class-assignments-with-classifiers"></a>Kombinace prostředků třídy přiřazení ke třídění

Třídění systém vytvoří za vás zadejte cestu k snadno migrovat úlohy klasifikace. Použití mapování role třídy prostředků s prioritou klasifikace, může způsobit chybnou k vytvoření nového třídění s důležitostí.

Vezměte v úvahu následující scénář:

•An existující datový sklad má přiřazené DBAUser largerc role třídy prostředků uživatele databáze. S sp_addrolemember bylo provedeno přiřazení třídy prostředků.
•Cílová datový sklad je teď aktualizovaný o správu úloh.
•To otestovat novou syntaxi klasifikace, databázové role DBARole (který DBAUser je členem skupiny), má třídění, vytvořené pro ně mapování mediumrc a vysokou důležitostí.
•When DBAUser přihlásí a spustí dotaz, dotaz se přiřadí largerc. Vzhledem k tomu, že uživatel má přednost před členství v roli.

Pokud chcete zjednodušit Poradce při potížích chybnou, doporučujeme že odebrat mapování role třídy prostředků při vytváření úlohy Klasifikátory.  Následující kód vrátí existující prostředek třídy členství v rolích.  Spustit [sp_droprolemember](/sql/relational-databases/system-stored-procedures/sp-droprolemember-transact-sql) pro každý název člena vrácen z odpovídající třídy prostředků.

```sql
SELECT  r.name AS [Resource Class]
,       m.name AS membername
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r ON rm.role_principal_id = r.principal_id
JOIN    sys.database_principals AS m ON rm.member_principal_id = m.principal_id
WHERE   r.name IN ('mediumrc','largerc','xlargerc','staticrc10','staticrc20','staticrc30','staticrc40','staticrc50','staticrc60','staticrc70','staticrc80');

--for each row returned run
sp_droprolemember ‘[Resource Class]’, membername
```

## <a name="next-steps"></a>Další postup

Další informace o SQL Data Warehouse úloh klasifikace a význam, naleznete v tématu [vytvoření klasifikátoru úlohy](quickstart-create-a-workload-classifier-tsql.md) a [SQL Data Warehouse význam](sql-data-warehouse-workload-importance.md). Zobrazit [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) k zobrazení dotazů a závažnosti přiřazené.
