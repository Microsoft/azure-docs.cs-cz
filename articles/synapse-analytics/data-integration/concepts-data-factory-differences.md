---
title: Rozdíly oproti Azure Data Factory
description: Přečtěte si, jak se možnosti integrace dat služby Azure synapse Analytics liší od Azure Data Factory
services: synapse-analytics
author: kromerm
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 12/10/2020
ms.author: makromer
ms.reviewer: jrasnick
ms.openlocfilehash: a8fd0ef006b246e30c02cfb321c72b4e070f54de
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2020
ms.locfileid: "97109143"
---
# <a name="data-integration-in-azure-synapse-analytics-versus-azure-data-factory"></a>Integrace dat ve službě Azure synapse Analytics versus Azure Data Factory

Ve službě Azure synapse Analytics jsou možnosti integrace dat, jako jsou kanály synapse a toky dat, založené na Azure Data Factory. Další informace najdete v tématu [co je Azure Data Factory](../../data-factory/introduction.md).


## <a name="available-features-in-azure-data-factory-and-azure-synapse-analytics"></a>Dostupné funkce v Azure Data Factory a Azure synapse Analytics

Níže najdete níže uvedenou tabulku pro dostupnost funkcí:

| Kategorie                 | Funkce    |  Azure Data Factory  | Azure Synapse Analytics |
| ------------------------ | ---------- | :------------------: | :---------------------: |
| **Integration Runtime**  | Použití SSIS a SSIS Integration Runtime | ✓ | ✗ |
|                          | Podpora Integration Runtime mezi oblastmi (toky dat) | ✓ | ✗ |
|                          | Sdílení Integration Runtime | ✓<br><small>*Dá se sdílet napříč různými objekty pro vytváření dat.* | ✗ |
|                          | Doba do provozu | ✓ | ✗ |
| **Aktivity kanálů** | Aktivita balíčku SSIS | ✓ | ✗ |
|                          | Podpora aktivity Power Query | ✓ | ✓ |
| **Galerie šablon a centrum znalostí** | Šablony řešení | ✓<br><small>*Galerie šablon Azure Data Factory* | ✓<br><small>*Centrum znalostí pro synapse Workspace* |
| **Integrace úložiště GIT** | Integrace GITU | ✓ | ✓ |
| **Monitorování**           | Monitorování úloh Sparku pro tok dat | ✗ | ✓<br><small>*Využijte fondy synapse Spark* |
|                          | Integrace s Azure Monitor | ✓ | ✗ |

> [!Note]
> Hodnota **TTL (Time to Live** ) je Azure Integration Runtimeé nastavení, které umožňuje clusteru Spark *zabývat* po určitou dobu po spuštění toku dat.
>


## <a name="next-steps"></a>Další kroky

Začněte s integrací dat v pracovním prostoru synapse a Naučte se, jak ingestovat [data do účtu Azure Data Lake Storage Gen2](data-integration-data-lake.md).
