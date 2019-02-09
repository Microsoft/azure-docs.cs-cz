---
title: Přehled šablon pro Azure Data Factory | Dokumentace Microsoftu
description: Další informace o použití předem definované šablony, abyste mohli rychle začít s Azure Data Factory.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: douglasl
ms.reviewer: douglasl
ms.openlocfilehash: c3571aec55d5050df01f740a163db18e9c6c1095
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2019
ms.locfileid: "55967248"
---
# <a name="templates"></a>Šablony

Šablony jsou předdefinované kanálů Azure Data Factory, které umožňují rychle začít s Data Factory. Šablony jsou užitečné, pokud jste do služby Data Factory začínáte a chcete rychle začít. Tyto šablony tím zkrátit dobu vývoje pro vytváření projekty integrace dat a vylepšení produktivity při vývoji.

## <a name="create-data-factory-pipelines-from-templates"></a>Vytváření kanálů Data Factory ze šablon

Můžete začít vytváření kanálu služby Data Factory ze šablony těmito dvěma způsoby:

1.  Vyberte **vytvořit kanál ze šablony** na stránce Přehled otevřete Galerii šablon.

    ![Otevřít galerii šablon z stránka s přehledem](media/solution-templates-introduction/templates-intro-image1.png)

1.  Na kartě Autor v Průzkumníku prostředků, vyberte **+**, pak **kanál ze šablony** otevřete Galerii šablon.

    ![Otevřít galerii šablon z karty autora](media/solution-templates-introduction/templates-intro-image2.png)

## <a name="template-gallery"></a>Galerie šablon

![Galerie šablon](media/solution-templates-introduction/templates-intro-image3.png)

### <a name="out-of-the-box-data-factory-templates"></a>Z šablony služby Data Factory pole

Data Factory používá pro ukládání šablon kanálu data factory šablon Azure Resource Manageru. Zobrazí se všechny šablon Resource Manageru, spolu s používat pro šablony služby Data Factory pole, v souboru manifestu [oficiální úložiště Azure Data Factory na Githubu](https://github.com/Azure/Azure-DataFactory/tree/master/templates). Předdefinované šablony, které poskytuje Microsoft zahrnout, avšak nejsou omezeny na následující položky:

-   Kopírování šablony:

    -   [Hromadné kopírování z databáze](solution-template-bulk-copy-with-control-table.md)

    -   [Kopírování více kontejnerů souborů mezi souborové úložiště](solution-template-copy-files-multiple-containers.md)

    -   [Rozdílové kopírování z databáze](solution-template-delta-copy-with-control-table.md)

    -   Zkopírujte z \<zdroj\> k \<cíl\>

        -   Z Amazonu S3 do Azure Data Lake Store Gen 2

        -   Z Google Big dotazu do Azure Data Lake Store Gen 2

        -   Z HDF do Azure Data Lake Store Gen 2

        -   Z Netezza do 1. generace Azure Data Lake Store

        -   Z SQL serveru v místním prostředí do Azure SQL Database

        -   Z SQL serveru v místním prostředí do Azure SQL Data Warehouse

        -   Od Oraclu v místním prostředí do Azure SQL Data Warehouse

-   Šablony služby SSIS

    -   Plánování prostředí Azure-SSIS IR pro spouštění balíčků služby SSIS

-   Transformace šablon

    -   [ETL pomocí Azure Databricks](solution-template-databricks-notebook.md)

### <a name="my-templates"></a>Moje šablony

Kanál můžete také uložit jako šablonu tak, že vyberete **uložit jako šablonu** na kartu kanálu.

![Uložit jako šablonu kanálu](media/solution-templates-introduction/templates-intro-image4.png)

Můžete zobrazit kanály uložena jako šablony v **šablony** oddíl Galerie šablon. Můžete také zobrazit, je **šablony** části v Průzkumníku prostředků.

![Moje šablony](media/solution-templates-introduction/templates-intro-image5.png)

> [!NOTE]
> Jak používat funkci šablony, budete muset povolit integrace GITU. Azure DevOps GIT a Githubu se podporují.
