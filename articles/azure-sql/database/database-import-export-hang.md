---
title: Import a export databáze trvá dlouhou dobu.
description: Import nebo export služby SQL Managed instance spravované instance Azure SQL Database a Azure SQL Database trvá dlouhou dobu.
ms.custom: seo-lt-2019, sqldbrb=1
services: sql-database
ms.service: sql-db-mi
ms.subservice: data-movement
ms.topic: troubleshooting
author: v-miegge
ms.author: ramakoni
ms.reviewer: ''
ms.date: 09/27/2019
ms.openlocfilehash: f98cfcd49806061a969a9227f9ade05f70ce79ff
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85982306"
---
# <a name="azure-sql-database-and-managed-instance-importexport-service-takes-a-long-time-to-import-or-export-a-database"></a>Import nebo export databáze služby Import/export pro Azure SQL Database a spravovanou instanci trvá dlouhou dobu.

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Když použijete službu import/export, proces může trvat déle, než se čekalo. Tento článek popisuje možné příčiny tohoto zpoždění a alternativní alternativní řešení.

## <a name="azure-sql-database-importexport-service"></a>Azure SQL Database služba import/export

Služba Azure SQL Database import/export je webová služba založená na REST, která běží v každém datovém centru Azure. Tato služba se volá, když použijete možnost [importovat databázi](database-import.md#using-azure-portal) nebo [exportovat](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-the-azure-portal) k přesunu databáze v Azure Portal. Služba poskytuje služby a výpočetní služby pro bezplatné žádosti o provádění importů a exportů mezi Azure SQL Database a úložištěm objektů BLOB v Azure.

Operace importu a exportu nepředstavuje tradiční fyzickou zálohu databáze, ale místo logické zálohy databáze, která používá speciální formát BACPAC. Formát BACPAC vám umožňuje vyhnout se nutnosti používat fyzický formát, který se může lišit mezi verzemi Microsoft SQL Server, Azure SQL Database a spravované instance Azure SQL.

## <a name="what-causes-delays-in-the-process"></a>Co způsobí zpoždění v procesu?

Služba Azure SQL Database import/export poskytuje omezený počet výpočetních virtuálních počítačů (VM) na oblast pro zpracování operací importu a exportu. Výpočetní virtuální počítače jsou hostované na oblast, aby se zajistilo, že při importu nebo exportu se vyhnete zpoždění a účtování šířky pásma mezi oblastmi. Pokud se ve stejné oblasti provedou příliš mnoho žádostí současně, můžou se při zpracování operací objevit významné prodlevy. Čas potřebný k dokončení požadavků se může lišit od několika sekund až po spoustu hodin.

> [!NOTE]
> Pokud žádost není zpracována do čtyř dnů, služba tuto žádost automaticky zruší.

## <a name="recommended-solutions"></a>Doporučená řešení

Pokud jsou vaše databáze exportovány pouze pro obnovení před náhodným odstraněním dat, všechny Azure SQL Database edice poskytují možnost samoobslužného obnovení ze záloh generovaných systémem. Pokud ale potřebujete tyto exporty z jiných důvodů, a pokud potřebujete konzistentně rychlejší nebo více předvídatelný výkon importu/exportu, vezměte v úvahu následující možnosti:

* [Exportujte soubor BacPac pomocí nástroje SQLPackage](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-the-sqlpackage-utility).
* [Exportujte soubor BacPac pomocí SQL Server Management Studio (SSMS)](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-sql-server-management-studio-ssms).
* Spusťte import nebo export BACPAC přímo ve vašem kódu pomocí rozhraní API Data-Tier aplikační architektury (DacFx) Microsoft SQL Server. Další informace najdete v těchto tématech:
  * [Export aplikace na datové vrstvě](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application)
  * [Obor názvů Microsoft. SqlServer. DAC](https://docs.microsoft.com/dotnet/api/microsoft.sqlserver.dac)
  * [Stáhnout DACFx](https://www.microsoft.com/download/details.aspx?id=55713)

## <a name="things-to-consider-when-you-export-or-import-a-database"></a>Věci, které je potřeba vzít v úvahu při exportu nebo importu databáze

* Všechny metody popsané v tomto článku používají kvótu jednotky databáze (DTU), která způsobuje omezení služby Azure SQL Database. [Na Azure Portal můžete zobrazit statistiky DTU pro databázi](https://docs.microsoft.com/azure/sql-database/sql-database-monitor-tune-overview#sql-database-resource-monitoring). Pokud databáze dosáhla svých omezení prostředků, [upgradujte vrstvu služby](https://docs.microsoft.com/azure/sql-database/sql-database-scale-resources) a přidejte další prostředky.
* V ideálním případě byste měli spustit klientské aplikace (jako je například nástroj SqlPackage nebo vlastní aplikace DAC) z virtuálního počítače ve stejné oblasti jako vaše databáze. V opačném případě může docházet k problémům s výkonem souvisejícím s latencí sítě.
* Export rozsáhlých tabulek bez clusterovaných indexů může být velmi pomalý nebo dokonce způsobit selhání. K tomuto chování dochází, protože tabulku nelze rozdělit a exportovat paralelně. Místo toho je nutné jej exportovat v rámci jedné transakce, což způsobí pomalý výkon a potenciální chybu při exportu, zejména pro velké tabulky.


## <a name="related-documents"></a>Související dokumenty

[Pokyny k exportu databáze](https://docs.microsoft.com/azure/sql-database/sql-database-export#considerations-when-exporting-an-azure-sql-database)
