---
title: Přehled konektoru Azure Data Factory
description: Seznamte se s podporovanými konektory v Data Factory.
services: data-factory
author: linda33wj
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 07/08/2020
ms.author: jingwang
ms.reviewer: craigg
ms.openlocfilehash: d7a872121ca6560b8ede86abc35294ab8c9b0c1b
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/08/2020
ms.locfileid: "86141999"
---
# <a name="azure-data-factory-connector-overview"></a>Přehled konektoru Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory podporují následující úložiště a formáty dat prostřednictvím kopírování, toku dat, vyhledávání, získání metadat a aktivity odstranění. Klikněte na jednotlivá úložiště dat, abyste se seznámili s podporovanými funkcemi a odpovídajícími konfiguracemi v podrobnostech.

## <a name="supported-data-stores"></a>Podporované zdroje dat

[!INCLUDE [Connector overview](../../includes/data-factory-v2-connector-overview.md)]

## <a name="supported-file-formats"></a>Podporované formáty souborů

Azure Data Factory podporuje následující formáty souborů. Nastavení založená na formátu najdete v každém článku.

- [Formát Avro](format-avro.md)
- [Binární formát](format-binary.md)
- [Formát Common Data Modelu](format-common-data-model.md)
- [Formát textu s oddělovači](format-delimited-text.md)
- [Excelový formát](format-excel.md)
- [Formát JSON](format-json.md)
- [Formát ORC](format-orc.md)
- [Formát Parquet](format-parquet.md)

## <a name="next-steps"></a>Další kroky

- [Aktivita kopírování](copy-activity-overview.md)
- [Mapování toku dat](concepts-data-flow-overview.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)
- [Aktivita získání metadat](control-flow-get-metadata-activity.md)
- [Odstranit aktivitu](delete-activity.md)
