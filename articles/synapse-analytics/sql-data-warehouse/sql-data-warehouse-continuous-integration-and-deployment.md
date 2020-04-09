---
title: Průběžná integrace a nasazování
description: Prostředí Database DevOps podnikové třídy pro ukládání dat s integrovanou podporou pro průběžnou integraci a nasazení pomocí Azure Pipelines.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 02/04/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: c5a326c07c8c2b0e5482361060c25c06d25643c1
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80874139"
---
# <a name="continuous-integration-and-deployment-for-data-warehousing"></a>Průběžná integrace a zavádění datových skladů

Tento jednoduchý kurz popisuje, jak integrovat databázový projekt databázových nástrojů SQL Server (SSDT) s Azure DevOps a využít Azure Pipelines k nastavení průběžné integrace a nasazování. Tento kurz je druhým krokem při vytváření průběžné integrace a nasazení kanálu pro ukládání dat.

## <a name="before-you-begin"></a>Než začnete

- Procházení [kurzu integrace správy zdrojového kódu](sql-data-warehouse-source-control-integration.md)

- Nastavení a připojení k Azure DevOps

## <a name="continuous-integration-with-visual-studio-build"></a>Průběžná integrace s sestavením sady Visual Studio

1. Přejděte na Azure Pipelines a vytvořte nový kanál sestavení.

      ![Nový kanál](./media/sql-data-warehouse-continuous-integration-and-deployment/1-new-build-pipeline.png "Nový kanál")

2. Vyberte úložiště zdrojového kódu (Azure Repos Git) a vyberte šablonu aplikace .NET Desktop.

      ![Instalace kanálu](./media/sql-data-warehouse-continuous-integration-and-deployment/2-pipeline-setup.png "Instalace kanálu")

3. Upravte soubor YAML tak, aby používal správný fond vašeho agenta. Soubor YAML by měl vypadat nějak takto:

      ![YAML](./media/sql-data-warehouse-continuous-integration-and-deployment/3-yaml-file.png "YAML")

V tomto okamžiku máte jednoduché prostředí, kde jakékoli vrácení se změnami do hlavní větve úložiště správy zdrojového kódu by mělo automaticky aktivovat úspěšné sestavení aplikace Visual Studio databázového projektu. Ověřte, že automatizace pracuje od konce do konce provedením změny v místním databázovém projektu a vrácením této změny se změnami do hlavní větve.

## <a name="continuous-deployment-with-the-azure-sql-data-warehouse-or-database-deployment-task"></a>Průběžné nasazení s úlohou nasazení Datového skladu Azure SQL (nebo databáze)

1. Přidejte nový úkol pomocí [úlohy nasazení databáze Azure SQL](https://docs.microsoft.com/azure/devops/pipelines/targets/azure-sqldb?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) database a vyplňte požadovaná pole pro připojení k cílovému datovému skladu. Při spuštění této úlohy dacpac generované z předchozího procesu sestavení se nasadí do cílového datového skladu. Můžete také použít [úlohu nasazení Datového skladu Azure SQL](https://marketplace.visualstudio.com/items?itemName=ms-sql-dw.SQLDWDeployment).

      ![Úloha nasazení](./media/sql-data-warehouse-continuous-integration-and-deployment/4-deployment-task.png "Úloha nasazení")

2. Pokud používáte agenta s vlastním hostitelem, ujistěte se, že jste nastavili proměnnou prostředí tak, aby používala správný sqlpackage.exe pro datový sklad SQL. Cesta by měla vypadat nějak takto:

      ![Proměnná prostředí](./media/sql-data-warehouse-continuous-integration-and-deployment/5-environment-variable-preview.png "Proměnná prostředí")

   C:\Program Files (x86)\Microsoft Visual Studio\2019\Preview\Common7\IDE\Extensions\Microsoft\SQLDB\DAC\150  

   Spusťte a ověřte svůj kanál. Můžete provést změny místně a vrácení se změnami změny správy zdrojového kódu, který by měl generovat automatické sestavení a nasazení.

## <a name="next-steps"></a>Další kroky

- Prozkoumejte [architekturu MPP fondu Synapse SQL](massively-parallel-processing-mpp-architecture.md)
- Rychlé [vytvoření fondu SQL](create-data-warehouse-portal.md)
- [Načtení ukázkových dat](load-data-from-azure-blob-storage-using-polybase.md)
- Prozkoumejte [videa](sql-data-warehouse-videos.md)
