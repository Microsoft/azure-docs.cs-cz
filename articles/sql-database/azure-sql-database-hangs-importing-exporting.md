---
title: Import nebo export databáze služby Azure SQL Database import/export trvá dlouhou dobu. | Microsoft Docs
description: Import nebo export databáze Azure SQL Database službě Import/export trvá dlouhou dobu.
services: sql-database
ms.service: sql-database
ms.topic: troubleshooting
author: v-miegge
ms.author: ramakoni
ms.reviewer: ''
ms.date: 09/27/2019
ms.openlocfilehash: ed5f3d1cd505270eb91c9cfbd6fb5c38b908f33d
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2019
ms.locfileid: "71974458"
---
# <a name="azure-sql-database-importexport-service-takes-a-long-time-to-import-or-export-a-database"></a>Import nebo export databáze Azure SQL Database službě Import/export trvá dlouhou dobu.

Pokud používáte Azure SQL Database službu import/export, můžete si všimnout, že v některých případech může dokončení procesu trvat delší dobu. V tomto článku najdete další informace o možných příčinách Thee zpoždění a alternativních metod, které můžete použít k řešení těchto problémů.

## <a name="azure-sql-database-importexport-service"></a>Azure SQL Database služba import/export

Služba Azure SQL Database import/export je webová služba založená na REST, která běží v každém Microsoft Azure datovém centru. Toto je služba, která se volá při použití možností [Import databáze](https://docs.microsoft.com/azure/sql-database/sql-database-import#import-from-a-bacpac-file-in-the-azure-portal) nebo [exportu](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-the-azure-portal) k přesunu databáze SQL v portál Microsoft Azure. Služba poskytuje bezplatnou službu Řízení front žádostí a bezplatnou výpočetní službu pro provádění importu a exportu z Microsoft Azure SQL Database pro Microsoft Azure úložiště binárních rozsáhlých objektů (BLOB).

Operace importu a exportu nejsou tradiční fyzickou zálohou databáze, ale s logickou zálohou databáze, která používá speciální formát BACPAC. Tento formát logických BACPAC vám umožňuje vyhnout se nutnosti používat fyzický formát, který se může lišit mezi verzemi SQL Server a SQL Database. Proto jej můžete použít k bezpečnému obnovení databáze do databáze SQL a do databáze SQL Server.

## <a name="what-causes-the-process-to-take-a-long-time"></a>Co způsobí, že proces trvá delší dobu

Služba Azure SQL Database import/export poskytuje omezený počet výpočetních virtuálních počítačů (VM) na oblast pro zpracování operací importu a exportu. Výpočetní virtuální počítač je hostovaný na oblast, aby se zajistilo, že při importu nebo exportu se vyhnete zpoždění a účtování šířky pásma mezi oblastmi. Pokud se tedy ve stejné oblasti v jednom okamžiku nachází příliš mnoho požadavků, dojde při zpracování operací k výraznému zpoždění. Čas potřebný k dokončení požadavků se může lišit od několika sekund až po spoustu hodin.

> [!NOTE]
> Pokud žádost není zpracována do čtyř dnů, služba tuto žádost automaticky zruší.

## <a name="recommended-solutions"></a>Doporučená řešení

Pokud jsou vaše databáze exportovány jenom pro obnovení při náhodném odstranění dat, všechny edice Azure SQL Server Database poskytují možnost samoobslužného obnovení ze systémových záloh generovaných systémem. Pokud ale potřebujete tyto exporty z jiných důvodů, a pokud budete potřebovat konzistentně rychlejší nebo větší výkon při importu nebo exportu, měli byste zvážit následující možnosti:

* [Export do souboru BACPAC pomocí nástroje SQLPackage](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-the-sqlpackage-utility)
* [Export do souboru BACPAC pomocí SQL Server Management Studio (SSMS)](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-sql-server-management-studio-ssms)
* Spusťte import nebo export BACPAC přímo ve vašem kódu pomocí rozhraní API Microsoft® SQL Server® rozhraní DacFx (data-vrstva Application Framework) API. Další informace najdete v
  * [Export aplikace na datové vrstvě](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application)
  * [Obor názvů Microsoft. SqlServer. DAC](https://docs.microsoft.com/dotnet/api/microsoft.sqlserver.dac)
  * [Stáhnout DACFx](https://www.microsoft.com/download/details.aspx?id=55713)

## <a name="considerations-when-exporting-or-importing-an-azure-sql-database"></a>Předpoklady při exportu nebo importu databáze SQL Azure

* Všechny metody popsané v tomto článku využívají kvótu DTU, což má za následek omezení služby Azure SQLDB. [Na Azure Portal můžete zobrazit statistiky DTU pro databázi](https://docs.microsoft.com/azure/sql-database/sql-database-monitor-tune-overview#monitor-database-performance). Pokud databáze přesáhne omezení prostředků, [upgradujte vrstvu služby](https://docs.microsoft.com/azure/sql-database/sql-database-scale-resources) a přidejte další prostředky.
* Klientské aplikace (jako je SqlPackage Utility nebo vlastní aplikace DAC) by se v ideálním případě měly spouštět z virtuálního počítače ve stejné oblasti jako vaše databáze SQL nebo jinak může dojít k problémům s výkonem kvůli latenci sítě.
* Export rozsáhlých tabulek bez clusterovaných indexů může být velmi pomalý nebo dokonce způsobí selhání. Je to proto, že tabulku nelze rozdělit a exportovat paralelně a je třeba ji exportovat v rámci jedné transakce, což způsobí zpomalení a potenciální chyby při exportu, zejména pro velké tabulky. 


## <a name="related-documents"></a>Související dokumenty

[Předpoklady při exportu databáze SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-export#considerations-when-exporting-an-azure-sql-database)
