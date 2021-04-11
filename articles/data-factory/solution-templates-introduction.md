---
title: Přehled šablon
description: Naučte se používat předem definovanou šablonu, abyste mohli rychle začít pracovat s Azure Data Factory.
ms.service: data-factory
ms.topic: conceptual
ms.author: weetok
author: dcstwh
ms.custom: seo-lt-2019
ms.date: 01/04/2019
ms.openlocfilehash: 511c8851a39d283bd69215216f5ec4a4e77653d1
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2021
ms.locfileid: "106219803"
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

        -   Z SQL Server v místním prostředí do Azure synapse Analytics

        -   Od Oracle místně po Azure synapse Analytics

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
