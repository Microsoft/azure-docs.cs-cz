---
title: Průběžná integrace a nasazování
description: DevOps možnosti databáze na podnikové úrovni pro datové sklady s integrovanou podporou pro průběžnou integraci a nasazování pomocí Azure Pipelines.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: overview
ms.subservice: integration
ms.date: 02/04/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: cb28afc84c423c5c5321a8f948e82f82031888a0
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/29/2020
ms.locfileid: "78195936"
---
# <a name="continuous-integration-and-deployment-for-data-warehousing"></a>Průběžná integrace a nasazování pro datové sklady

V tomto jednoduchém kurzu se naučíte integrovat projekt databáze SSDT (SQL Server Data Tools) do Azure DevOps a využít Azure Pipelines k nastavení průběžné integrace a nasazování. Tento kurz je druhým krokem při sestavování kanálu průběžné integrace a nasazování pro datové sklady. 

## <a name="before-you-begin"></a>Než začnete

- Projděte si [kurz integrace správy zdrojového kódu](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-source-control-integration)

- Nastavení a připojení k Azure DevOps


## <a name="continuous-integration-with-visual-studio-build"></a>Průběžná integrace se sestavením sady Visual Studio

1. Přejděte na Azure Pipelines a vytvořte nový kanál sestavení.

      ![Nový kanál](media/sql-data-warehouse-continuous-integration-and-deployment/1-new-build-pipeline.png "Nový kanál")

2. Vyberte úložiště zdrojového kódu (Azure Repos Git) a vyberte šablonu desktopové aplikace .NET.

      ![Nastavení kanálu](media/sql-data-warehouse-continuous-integration-and-deployment/2-pipeline-setup.png "Nastavení kanálu") 

3. Upravte soubor YAML tak, aby používal správný fond vašeho agenta. Váš soubor YAML by měl vypadat přibližně takto:

      ![YAML](media/sql-data-warehouse-continuous-integration-and-deployment/3-yaml-file.png "YAML")

V tomto okamžiku máte jednoduché prostředí, kde jakékoli vrácení se změnami do hlavní větve úložiště správy zdrojových kódů by mělo automaticky aktivovat úspěšné sestavení databázového projektu sady Visual Studio. Ověřte, že automatizace pracuje na konci, tím, že provedete změnu v projektu místní databáze a zkontrolujete, že se změní na hlavní větev.


## <a name="continuous-deployment-with-the-azure-sql-data-warehouse-or-database-deployment-task"></a>Průběžné nasazování s úlohou nasazení Azure SQL Data Warehouse (nebo databáze)

1. Přidejte nový úkol pomocí [úlohy nasazení Azure SQL Database](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/sql-azure-dacpac-deployment?view=azure-devops) a vyplňte požadovaná pole pro připojení k cílovému datovému skladu. Při spuštění této úlohy je DACPAC vygenerovaný z předchozího procesu sestavení nasazen do cílového datového skladu. Můžete také použít [úlohu nasazení Azure SQL Data Warehouse](https://marketplace.visualstudio.com/items?itemName=ms-sql-dw.SQLDWDeployment). 

      ![Úloha nasazení](media/sql-data-warehouse-continuous-integration-and-deployment/4-deployment-task.png "Úloha nasazení")

2. Pokud používáte samoobslužného agenta, ujistěte se, že jste nastavili proměnnou prostředí tak, aby pro SQL Data Warehouse používala správný soubor SqlPackage. exe. Cesta by měla vypadat přibližně takto:

      ![Proměnná prostředí](media/sql-data-warehouse-continuous-integration-and-deployment/5-environment-variable-preview.png "Proměnná prostředí")

   C:\Program Files (x86) \Microsoft Visual Studio\2019\Preview\Common7\IDE\Extensions\Microsoft\SQLDB\DAC\150  

   Spusťte a ověřte svůj kanál. Můžete provést změny místně a vrátit se změnami do správy zdrojového kódu, který by měl generovat automatické sestavení a nasazení.

## <a name="next-steps"></a>Další kroky

- Prozkoumat [architekturu MPP SQL Analytics](massively-parallel-processing-mpp-architecture.md)
- Rychlé [Vytvoření fondu SQL](create-data-warehouse-portal.md)
- [Načíst ukázková data](sql-data-warehouse-load-sample-databases.md)
- Prozkoumat [videa](/azure/sql-data-warehouse/sql-data-warehouse-videos)
