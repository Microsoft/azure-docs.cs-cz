---
title: Přehled šablon
description: Naučte se používat předem definovanou šablonu, abyste mohli rychle začít pracovat s Azure Data Factory.
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
ms.openlocfilehash: 515de0fed987f412adb060a3ba58978f0400ae0d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89441948"
---
# <a name="templates"></a>Šablony

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Šablony jsou předdefinované Azure Data Factory kanály, které vám umožní rychle začít pracovat s Data Factory. Šablony jsou užitečné v případě, že jste Data Factory a chcete rychle začít. Tyto šablony omezují dobu vývoje pro vytváření projektů pro integraci dat, což zlepšuje produktivitu vývojářů.

## <a name="create-data-factory-pipelines-from-templates"></a>Vytváření kanálů Data Factory z šablon

Můžete začít vytvářet Data Factory kanál ze šablony následujícími dvěma způsoby:

1.  Vyberte **vytvořit kanál z šablony** na stránce Přehled a otevřete galerii šablon.

    ![Otevřít galerii šablon ze stránky přehled](media/solution-templates-introduction/templates-intro-image1.png)

1.  Na kartě autor v Průzkumník prostředků vyberte možnost **+** , potom **kanál ze šablony** a otevřete galerii šablon.

    ![Otevřete galerii šablon z karty autor.](media/solution-templates-introduction/templates-intro-image2.png)

## <a name="template-gallery"></a>Galerie šablon

![Galerie šablon](media/solution-templates-introduction/templates-intro-image3.png)

### <a name="out-of-the-box-data-factory-templates"></a>Data Factory šablon

Data Factory používá šablony Azure Resource Manager pro ukládání šablon kanálu služby Data Factory. V [oficiálním úložišti Azure Data Factory GitHubu](https://github.com/Azure/Azure-DataFactory/tree/master/templates)můžete zobrazit všechny šablony Správce prostředků společně se souborem manifestu použitým pro Data Factory šablony. Předdefinované šablony poskytované společností Microsoft zahrnují, ale nejsou omezeny na následující položky:

-   Kopírovat šablony:

    -   [Hromadné kopírování z databáze](solution-template-bulk-copy-with-control-table.md)
    
    -   [Kopírování nových souborů na základě LastModifiedDate](solution-template-copy-new-files-lastmodifieddate.md)

    -   [Kopírovat více kontejnerů souborů mezi úložišti na základě souborů](solution-template-copy-files-multiple-containers.md)

    -   [Přesunutí souborů](solution-template-move-files.md)

    -   [Rozdílová kopie z databáze](solution-template-delta-copy-with-control-table.md)

    -   Kopírovat z \<source\> do \<destination\>

        -   [Z Amazon S3 do Azure Data Lake Store Gen 2](solution-template-migration-s3-azure.md)

        -   Od Google Big Query až po Azure Data Lake Store Gen 2

        -   Od HDF po Azure Data Lake Store Gen 2

        -   Od Netezza po Azure Data Lake Store Gen 1

        -   Z SQL Server místně do Azure SQL Database

        -   Z SQL Server v místním prostředí do služby Azure synapse Analytics (dřív SQL Data Warehouse)

        -   Od Oracle místně po Azure synapse Analytics (dříve SQL Data Warehouse)

-   Šablony SSIS

    -   Naplánovat Azure-SSIS Integration Runtime pro spouštění balíčků SSIS

-   Transformace šablon

    -   [ETL s Azure Databricks](solution-template-databricks-notebook.md)

### <a name="my-templates"></a>Moje šablony

Kanál můžete uložit také jako šablonu, a to tak, že na kartě kanálu vyberete **Uložit jako šablonu** .

![Uložení kanálu jako šablony](media/solution-templates-introduction/templates-intro-image4.png)

Kanály uložené jako šablony můžete zobrazit v části **Moje šablony** v galerii šablon. Můžete je také zobrazit v části **šablony** v Průzkumník prostředků.

![Moje šablony](media/solution-templates-introduction/templates-intro-image5.png)

> [!NOTE]
> Chcete-li použít funkci Moje šablony, je nutné povolit integraci GIT. Podporují se Azure DevOps GIT i GitHub.
