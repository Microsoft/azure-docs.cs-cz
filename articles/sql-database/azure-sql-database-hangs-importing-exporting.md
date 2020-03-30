---
title: Služba importu a exportu trvá dlouhou dobu
description: Import nebo export databáze Azure SQL Database Trvá dlouho
ms.custom: seo-lt-2019
services: sql-database
ms.service: sql-database
ms.topic: troubleshooting
author: v-miegge
ms.author: ramakoni
ms.reviewer: ''
ms.date: 09/27/2019
manager: dcscontentpm
ms.openlocfilehash: cf2d9b218fe63414af2446b8562d3ba187b2d395
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535761"
---
# <a name="azure-sql-database-importexport-service-takes-a-long-time-to-import-or-export-a-database"></a>Import nebo export databáze Azure SQL Database Trvá dlouho

Při použití služby importu a exportu databáze Azure SQL může proces trvat déle, než bylo očekáváno. Tento článek popisuje potenciální příčiny tohoto zpoždění a alternativní metody řešení.

## <a name="azure-sql-database-importexport-service"></a>Služba importu a exportu databáze Azure SQL

Služba Importa a exportu databáze Azure SQL database je webová služba založená na rest, která běží v každém datovém centru Azure. Tato služba se nazývá, když použijete možnost [Importovat databázi](sql-database-import.md#using-azure-portal) nebo [Export](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-the-azure-portal) k přesunutí databáze SQL na webu Azure Portal. Služba poskytuje bezplatné služby řazení požadavků do fronty a výpočetní služby k provádění importů a exportů mezi databází Azure SQL a úložištěm objektů blob Azure.

Operace importu a exportu nepředstavují tradiční zálohování fyzické databáze, ale místo toho logickou zálohu databáze, která používá speciální formát BACPAC. Formát BACPAC umožňuje vyhnout se nutnosti používat fyzický formát, který se může lišit mezi verzemi Microsoft SQL Server a Azure SQL Database. Proto jej můžete bezpečně obnovit databázi do databáze serveru SQL Server a do databáze SQL.

## <a name="what-causes-delays-in-the-process"></a>Co způsobuje zpoždění v procesu?

Služba Importa a exportu databáze Azure SQL database poskytuje omezený počet výpočetních virtuálních počítačů (VM) na oblast pro zpracování operací importu a exportu. Výpočetní virtuální počítače jsou hostované podle oblasti, aby se zajistilo, že import nebo export zabrání zpoždění šířky pásma mezi oblastmi a poplatkům. Pokud je ve stejné oblasti podáno příliš mnoho požadavků současně, může dojít ke značným zpožděním při zpracování operací. Čas potřebný k dokončení požadavků se může lišit od několika sekund až po mnoho hodin.

> [!NOTE]
> Pokud žádost není zpracována do čtyř dnů, služba automaticky zruší požadavek.

## <a name="recommended-solutions"></a>Doporučená řešení

Pokud se exporty databáze používají jenom pro obnovení z náhodného odstranění dat, všechny edice Azure SQL Database poskytují samoobslužné obnovení ze systémově generovaných záloh. Pokud však tyto exporty potřebujete z jiných důvodů a pokud požadujete konzistentně rychlejší nebo předvídatelnější výkon importu a exportu, zvažte následující možnosti:

* [Exportujte do souboru BACPAC pomocí nástroje SQLPackage](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-the-sqlpackage-utility).
* [Exportujte do souboru BACPAC pomocí sql server management studio (SSMS)](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-sql-server-management-studio-ssms).
* Spusťte import bacpac nebo export přímo ve vašem kódu pomocí rozhraní API Microsoft SQL Server Data-Tier Application Framework (DacFx). Další informace naleznete v tématu:
  * [Export aplikace datové vrstvy](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application)
  * [Obor názvů microsoft.SqlServer.Dac](https://docs.microsoft.com/dotnet/api/microsoft.sqlserver.dac)
  * [Stažení DACFx](https://www.microsoft.com/download/details.aspx?id=55713)

## <a name="things-to-consider-when-you-export-or-import-an-azure-sql-database"></a>Co je třeba vzít v úvahu při exportu nebo importu databáze Azure SQL

* Všechny metody popsané v tomto článku používají kvótu databázové transakční jednotky (DTU), což způsobuje omezení službou Azure SQL Database. Můžete [zobrazit statistiky DTU pro databázi na webu Azure Portal](https://docs.microsoft.com/azure/sql-database/sql-database-monitor-tune-overview#sql-database-resource-monitoring). Pokud databáze dosáhla limitů prostředků, [inovujte úroveň služby](https://docs.microsoft.com/azure/sql-database/sql-database-scale-resources) a přidejte další prostředky.
* V ideálním případě byste měli spouštět klientské aplikace (jako je nástroj sqlpackage nebo vlastní aplikace DAC) z virtuálního počítače ve stejné oblasti jako databáze SQL. V opačném případě může dojít k problémům s výkonem související s latencí sítě.
* Export velkých tabulek bez seskupených indexů může být velmi pomalý nebo dokonce způsobit selhání. K tomuto chování dochází, protože tabulka nelze rozdělit a exportovat paralelně. Místo toho musí být exportován v jedné transakci a to způsobuje pomalý výkon a potenciální selhání během exportu, zejména pro velké tabulky.


## <a name="related-documents"></a>Související dokumenty

[Důležité informace při exportu databáze Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-export#considerations-when-exporting-an-azure-sql-database)
