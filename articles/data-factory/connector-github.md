---
title: Připojit ke GitHubu
description: Použití GitHubu k zadání odkazů na entity modelu Common data model
author: djpmsft
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/03/2020
ms.author: daperlov
ms.openlocfilehash: 5d555d7bc4d3aae9c016cacbe17b68c30859d99a
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100372276"
---
# <a name="use-github-to-read-common-data-model-entity-references"></a>Použití GitHubu ke čtení odkazů na entity modelu Common data model

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Konektor GitHub v Azure Data Factory slouží pouze k přijetí schématu odkazu na entitu pro formát [modelu Common data model](format-common-data-model.md) v části mapování toku dat.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro propojenou službu GitHubu jsou podporovány následující vlastnosti.

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type musí být nastavená na **GitHub**. | ano
| userName | Uživatelské jméno GitHubu | ano |
| heslo | Heslo GitHubu | ano |

## <a name="next-steps"></a>Další kroky

Vytvoří [zdrojovou datovou sadu](data-flow-source.md) v toku mapování dat.