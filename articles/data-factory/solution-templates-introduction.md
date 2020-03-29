---
title: Přehled šablon
description: Přečtěte si, jak rychle začít používat předdefinovanou šablonu s Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: daperlov
author: djpmsft
manager: anandsub
ms.custom: seo-lt-2019
ms.date: 01/04/2019
ms.openlocfilehash: f150234ecd3446858e8a6aa7d224eb3ad3d0efd3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74927349"
---
# <a name="templates"></a>Šablony

Šablony jsou předdefinované kanály Azure Data Factory, které vám umožní rychle začít pracovat s Totožnou dat. Šablony jsou užitečné, když jste v Datové továrně noví a chcete rychle začít. Tyto šablony zkracují dobu vývoje pro vytváření projektů integrace dat a tím zvyšují produktivitu vývojářů.

## <a name="create-data-factory-pipelines-from-templates"></a>Vytvoření kanálů datové továrny ze šablon

Můžete začít vytvářet kanál Data Factory ze šablony následujícími dvěma způsoby:

1.  Vyberte **Vytvořit kanál ze šablony** na stránce Přehled a otevřete galerii šablon.

    ![Otevření galerie šablon na stránce Přehled](media/solution-templates-introduction/templates-intro-image1.png)

1.  Na kartě Autor v **+** Průzkumníku prostředků vyberte a pak **Pipeline from template** otevřete galerii šablon.

    ![Otevření galerie šablon na kartě Autor](media/solution-templates-introduction/templates-intro-image2.png)

## <a name="template-gallery"></a>Galerie šablon

![Galerie šablon](media/solution-templates-introduction/templates-intro-image3.png)

### <a name="out-of-the-box-data-factory-templates"></a>Out-out-box šablony data factory

Factory používá šablony Azure Resource Manager pro ukládání šablon kanálu datové továrny. Všechny šablony Správce prostředků spolu se souborem manifestu používaným pro předem povyšovací šablony Data Factory můžete zobrazit v [oficiálním úložišti GitHub Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/templates). Předdefinované šablony poskytované společností Microsoft zahrnují mimo jiné následující položky:

-   Kopírovat šablony:

    -   [Hromadná kopie z databáze](solution-template-bulk-copy-with-control-table.md)
    
    -   [Kopírování nových souborů na základě LastModifiedDate](solution-template-copy-new-files-lastmodifieddate.md)

    -   [Kopírování více kontejnerů souborů mezi úložišti založenými na souborech](solution-template-copy-files-multiple-containers.md)

    -   [Přesunutí souborů](solution-template-move-files.md)

    -   [Rozdílová kopie z databáze](solution-template-delta-copy-with-control-table.md)

    -   Kopírování \<ze\> \<zdroje do cíle\>

        -   [Z Amazonu S3 na Azure Data Lake Store Gen 2](solution-template-migration-s3-azure.md)

        -   Od velkého dotazu Google k úložišti Azure Data Lake Store Gen 2

        -   Z HDF na Azure Data Lake Store Gen 2

        -   Z Netezzy do Úložiště datových jezer Azure Gen 1

        -   Z místního SQL Serveru do Azure SQL Database

        -   Z místního SQL Serveru do Azure SQL Data Warehouse

        -   Od místního řešení Oracle k Azure SQL Data Warehouse

-   Šablony SSIS

    -   Naplánování runtime integrace Azure-SSIS pro spuštění balíčků SSIS

-   Transformovat šablony

    -   [ETL s Datovými cihlami Azure](solution-template-databricks-notebook.md)

### <a name="my-templates"></a>Moje šablony

Kanál můžete uložit také jako šablonu tak, že na kartě Kanál vyberete **Uložit jako šablonu.**

![Uložení kanálu jako šablony](media/solution-templates-introduction/templates-intro-image4.png)

Kanály uložené jako šablony můžete zobrazit v části **Moje šablony** v Galerii šablon. Můžete je také zobrazit v části **Šablony** v Průzkumníku zdrojů.

![Moje šablony](media/solution-templates-introduction/templates-intro-image5.png)

> [!NOTE]
> Chcete-li použít funkci Moje šablony, musíte povolit integraci GIT. Azure DevOps GIT a GitHub jsou podporované.
