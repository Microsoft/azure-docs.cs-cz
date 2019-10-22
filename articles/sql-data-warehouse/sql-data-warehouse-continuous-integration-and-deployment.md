---
title: Průběžná integrace a nasazování | Microsoft Docs
description: DevOps prostředí databáze podnikové třídy pro SQL Data Warehouse s integrovanou podporou pro průběžnou integraci a nasazování pomocí Azure Pipelines.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: overview
ms.subservice: integration
ms.date: 08/28/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 7afb616fffaf01b1efa4f5d5a4af9dddd9683781
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2019
ms.locfileid: "70143146"
---
# <a name="continuous-integration-and-deployment-for-azure-sql-data-warehouse"></a>Průběžná integrace a nasazování pro Azure SQL Data Warehouse

V tomto jednoduchém kurzu se naučíte integrovat projekt databáze SSDT (SQL Server Data Tools) do Azure DevOps a využít Azure Pipelines k nastavení průběžné integrace a nasazování. Tento kurz je druhým krokem při sestavování kanálu průběžné integrace a nasazování pomocí SQL Data Warehouse. 

## <a name="before-you-begin"></a>Než začnete

- Projděte si [kurz integrace správy zdrojového kódu](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-source-control-integration)

- Vytvoření agenta s místním [hostováním](https://docs.microsoft.com/azure/devops/pipelines/agents/agents?view=azure-devops#install) , který má nainstalované služby SSDT preview (16,3 Preview 2 a novější) pro SQL Data Warehouse (Preview)

- Nastavení a připojení k Azure DevOps

  > [!NOTE]
  > SSDT je aktuálně ve verzi Preview, kde budete potřebovat využít samoobslužného agenta. Agenti s hostováním Microsoftu budou aktualizováni během několika následujících měsíců.

## <a name="continuous-integration-with-visual-studio-build"></a>Průběžná integrace se sestavením sady Visual Studio

1. Přejít na Azure Pipelines a vytvořit nový kanál sestavení

      ![Nový kanál](media/sql-data-warehouse-continuous-integration-and-deployment/1-new-build-pipeline.png "Nový kanál")

2. Vyberte úložiště zdrojového kódu (Azure Repos Git) a vyberte šablonu desktopové aplikace .NET.

      ![Nastavení kanálu](media/sql-data-warehouse-continuous-integration-and-deployment/2-pipeline-setup.png "Nastavení kanálu") 

3. Upravte soubor YAML tak, aby používal správný fond vašeho agenta. Váš soubor YAML by měl vypadat přibližně takto:

      ![YAML](media/sql-data-warehouse-continuous-integration-and-deployment/3-yaml-file.png "YAML")

V tomto okamžiku máte jednoduché prostředí, kde jakékoli vrácení se změnami do hlavní větve úložiště správy zdrojových kódů by mělo automaticky aktivovat úspěšné sestavení databázového projektu sady Visual Studio. Ověřte, že automatizace pracuje na konci, tím, že provedete změnu v projektu místní databáze a zkontrolujete, že se změní na hlavní větev.


## <a name="continuous-deployment-with-the-azure-sql-database-deployment-task"></a>Průběžné nasazování pomocí úlohy nasazení Azure SQL Database

1. Přidejte nový úkol pomocí [úlohy nasazení Azure SQL Database](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/sql-azure-dacpac-deployment?view=azure-devops) a vyplňte požadovaná pole pro připojení k cílovému datovému skladu. Při spuštění této úlohy je DACPAC vygenerovaný z předchozího procesu sestavení nasazen do cílového datového skladu.

      ![Úloha nasazení](media/sql-data-warehouse-continuous-integration-and-deployment/4-deployment-task.png "Úloha nasazení")

2. Při použití agenta s vlastním hostováním se ujistěte, že jste nastavili proměnnou prostředí tak, aby pro SQL Data Warehouse používala správný soubor SqlPackage. exe. Cesta by měla vypadat přibližně takto:

      ![Proměnná prostředí](media/sql-data-warehouse-continuous-integration-and-deployment/5-environment-variable-preview.png "Proměnná prostředí")

   C:\Program Files (x86) \Microsoft Visual Studio\2019\Preview\Common7\IDE\Extensions\Microsoft\SQLDB\DAC\150  

   Spusťte a ověřte svůj kanál. Můžete provést změny místně a vrátit se změnami do správy zdrojového kódu, který by měl generovat automatické sestavení a nasazení.

## <a name="next-steps"></a>Další kroky

- Prozkoumat [architekturu Azure SQL Data Warehouse](/azure/sql-data-warehouse/massively-parallel-processing-mpp-architecture)
- Rychlé [vytvoření SQL Data Warehouse][create a SQL Data Warehouse]
- [Načíst ukázková data][load sample data].
- Prozkoumat [videa](/azure/sql-data-warehouse/sql-data-warehouse-videos)



<!--Image references-->

[1]: ./media/sql-data-warehouse-overview-what-is/dwarchitecture.png

<!--Article references-->
[Create a support ticket]: ./sql-data-warehouse-get-started-create-support-ticket.md
[load sample data]: ./sql-data-warehouse-load-sample-databases.md
[create a SQL Data Warehouse]: ./sql-data-warehouse-get-started-provision.md
[Migration documentation]: ./sql-data-warehouse-overview-migrate.md
[SQL Data Warehouse solution partners]: ./sql-data-warehouse-partner-business-intelligence.md
[Integrated tools overview]: ./sql-data-warehouse-overview-integrate.md
[Backup and restore overview]: ./sql-data-warehouse-restore-database-overview.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md

<!--MSDN references-->

<!--Other Web references-->
[Blogs]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Customer Advisory Team blogs]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Feature requests]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[MSDN forum]: https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureSQLDataWarehouse
[Stack Overflow forum]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Videos]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[SLA for SQL Data Warehouse]: https://azure.microsoft.com/support/legal/sla/sql-data-warehouse/v1_0/
[Volume Licensing]: https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=37
[Service Level Agreements]: https://azure.microsoft.com/support/legal/sla/
