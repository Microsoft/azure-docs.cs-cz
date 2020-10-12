---
title: Přehled konektoru Azure Data Factory
description: Seznamte se s podporovanými konektory v Data Factory.
services: data-factory
author: linda33wj
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/28/2020
ms.author: jingwang
ms.reviewer: craigg
ms.openlocfilehash: 9b346c24b6a363e66e56f6c16b70058df3864c88
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/12/2020
ms.locfileid: "91945820"
---
# <a name="azure-data-factory-connector-overview"></a>Přehled konektoru Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory podporuje následující úložiště a formáty dat prostřednictvím kopírování, toku dat, vyhledávání, získávání metadat a odstraňování aktivit. Klikněte na jednotlivá úložiště dat, abyste se seznámili s podporovanými funkcemi a odpovídajícími konfiguracemi v podrobnostech.

## <a name="supported-data-stores"></a>Podporované zdroje dat

[!INCLUDE [Connector overview](../../includes/data-factory-v2-connector-overview.md)]

## <a name="integrate-with-more-data-stores"></a>Integrace s dalšími úložišti dat

Azure Data Factory může dosáhnout širší sady úložišť dat než výše uvedená výše. Pokud potřebujete přesunout data do nebo z úložiště dat, které není v seznamu Azure Data Factory integrovaný konektor, tady jsou některé rozšiřitelné možnosti:
- Pro databáze a datový sklad, obvykle můžete najít odpovídající ovladač ODBC, pomocí kterého můžete použít [obecný konektor ODBC](connector-odbc.md).
- Pro SaaS aplikace:
    - Pokud poskytuje rozhraní API RESTful, můžete použít [obecný konektor REST](connector-rest.md).
    - Pokud má kanál OData, můžete použít [obecný konektor OData](connector-odata.md).
    - Pokud poskytuje rozhraní API protokolu SOAP, můžete použít [obecný konektor http](connector-http.md).
    - Pokud má ovladač ODBC ovladač, můžete použít [obecný konektor ODBC](connector-odbc.md).
- Pro ostatní uživatele zkontrolujte, jestli můžete načíst data nebo je vystavovat jako jakákoli úložiště s AUTOMATICKÝm zápisem dat, např. Azure Blob/File/FTP/SFTP/a atd. Vlastní mechanismus načítání dat můžete vyvolat prostřednictvím [funkce Azure](control-flow-azure-function-activity.md), [vlastní aktivity](transform-data-using-dotnet-custom-activity.md), [datacihly](transform-data-databricks-notebook.md) / [HDInsight](transform-data-using-hadoop-hive.md), [aktivity webu](control-flow-web-activity.md)atd.

## <a name="supported-file-formats"></a>Podporované formáty souborů

Azure Data Factory podporuje následující formáty souborů. Nastavení založená na formátu najdete v každém článku.

- [Formát Avro](format-avro.md)
- [Binární formát](format-binary.md)
- [Formát Common Data Modelu](format-common-data-model.md)
- [Formát textu s oddělovači](format-delimited-text.md)
- [Rozdílový formát](format-delta.md)
- [Excelový formát](format-excel.md)
- [Formát JSON](format-json.md)
- [Formát ORC](format-orc.md)
- [Formát Parquet](format-parquet.md)
- [Formát XML](format-xml.md)

## <a name="next-steps"></a>Další kroky

- [Aktivita kopírování](copy-activity-overview.md)
- [Mapování toku dat](concepts-data-flow-overview.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)
- [Aktivita získání metadat](control-flow-get-metadata-activity.md)
- [Odstranit aktivitu](delete-activity.md)
