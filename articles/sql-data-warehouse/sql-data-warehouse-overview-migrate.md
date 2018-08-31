---
title: Migrace řešení do SQL Data Warehouse | Dokumentace Microsoftu
description: Pokyny k migraci pro vaše řešení přináší na platformu Azure SQL Data Warehouse.
services: sql-data-warehouse
author: jrowlandjones
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: jrj
ms.reviewer: igorstan
ms.openlocfilehash: b3e01968c74060bd0dc366609275d63753ad62dd
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2018
ms.locfileid: "43306702"
---
# <a name="migrate-your-solution-to-azure-sql-data-warehouse"></a>Migrace řešení do služby Azure SQL Data Warehouse
Podívejte se, co je součástí existujícího databázového řešení migrace do Azure SQL Data Warehouse. 

## <a name="profile-your-workload"></a>Profil úlohy
Před migrací, mají být, že některé SQL Data Warehouse je to pravé řešení pro vaši úlohu. SQL Data Warehouse je distribuovaný systém navržený tak, aby provádět analýzy na velkých datech.  Migrace do SQL Data Warehouse vyžaduje změny návrhu, které nejsou příliš pevné, abyste pochopili, ale může trvat nějakou dobu provádění. Jestli podnik potřebuje datového skladu podnikové úrovni, je vyplácet. Pokud nepotřebujete výkon služby SQL Data Warehouse, je však cenově výhodnější používat SQL Server nebo databázi SQL Azure.

Zvažte použití SQL Data Warehouse při můžete:
- Máte jeden nebo více terabajty dat
- Plánujete spouštět analýzy velkých objemů dat
- Potřebujete mít možnost škálovat výpočetní výkon a úložiště 
- Chcete snížit náklady pomocí pozastavení výpočetních prostředků, když je nepotřebujete.

Nepoužívejte SQL Data Warehouse pro provozní úlohy (OLTP), které jsou:
- Vysokou frekvencí čtení a zápisy
- Vybere velkého počtu singleton
- Velký objem vloží jeden řádek
- Řádek po řádku zpracování potřebám
- Nekompatibilních formátech (JSON, XML)


## <a name="plan-the-migration"></a>Plánování migrace

Až se rozhodnete k migraci existujícího řešení do SQL Data Warehouse, je důležité mít plán migrace, než začnete. 

Jedním z cílů plánování je zajistit, že vaše data, vaše schémata tabulek a kódu jsou kompatibilní s SQL Data Warehouse. Existují určité rozdíly kompatibility obejít mezi váš aktuální systém a SQL Data Warehouse. Navíc migraci velkého objemu dat do Azure trvá určitou dobu. Pečlivé plánování urychluje získávají se vaše data do Azure. 

Dalším cílem plánování je provést úpravy návrhu Ujistěte se, že vaše řešení využívá výhod vysokého výkonu dotazů, které SQL Data Warehouse je navržené pro poskytování. Návrh datové sklady pro škálování zavádí návrhů, vzory a proto tradiční přístupy nejsou vždy nejlepší. I když některé změny návrhu lze provést po migraci, až po provedení změn v procesu bude později ušetřit čas.

K provedení úspěšné migrace, potřebujete migrovat vaše schémata tabulek, váš kód a data. Pokyny v těchto tématech migrace najdete v tématu:

-  [Migrujte vaše schémata](sql-data-warehouse-migrate-schema.md)
-  [Migrace kódu](sql-data-warehouse-migrate-code.md)
-  [Migrace dat](sql-data-warehouse-migrate-data.md). 

<!--
## Perform the migration


## Deploy the solution


## Validate the migration

-->

## <a name="next-steps"></a>Další postup
CAT (zákaznického poradního týmu) obsahuje také některé skvělé pokyny SQL Data Warehouse, které publikují prostřednictvím blogů.  Podívejte se na jejich článku [přenos dat do služby Azure SQL Data Warehouse v praxi] [ Migrating data to Azure SQL Data Warehouse in practice] další pokyny k migraci.

<!--Image references-->

<!--Article references-->

<!--MSDN references-->

<!--Other Web references-->
[Migrating data to Azure SQL Data Warehouse in practice]: https://blogs.msdn.microsoft.com/sqlcat/2016/08/18/migrating-data-to-azure-sql-data-warehouse-in-practice/
