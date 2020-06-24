---
title: Připojit ke GitHubu
description: Použití GitHubu k zadání odkazů na entity modelu Common data model
author: djpmsft
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/03/2020
ms.author: daperlov
ms.openlocfilehash: 0e17580524d6a67934aed83c6f745583b92e2422
ms.sourcegitcommit: 99d016949595c818fdee920754618d22ffa1cd49
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/15/2020
ms.locfileid: "84771032"
---
# <a name="use-github-to-read-common-data-model-entity-references"></a>Použití GitHubu ke čtení odkazů na entity modelu Common data model

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Konektor GitHub v Azure Data Factory slouží pouze k přijetí schématu odkazu na entitu pro formát [modelu Common data model](format-common-data-model.md) v části mapování toku dat.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro propojenou službu GitHubu jsou podporovány následující vlastnosti.

| Vlastnost | Popis | Vyžadováno |
|:--- |:--- |:--- |
| typ | Vlastnost Type musí být nastavená na **GitHub**. | ano
| userName | Uživatelské jméno GitHubu | ano |
| heslo | Heslo GitHubu | ano |

## <a name="next-steps"></a>Další kroky

Vytvoří [zdrojovou datovou sadu](data-flow-source.md) v toku mapování dat.