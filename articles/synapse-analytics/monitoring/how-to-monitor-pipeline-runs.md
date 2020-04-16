---
title: Kanál monitorování běží Azure Synapse Studio (preview)
description: Azure Synapse Studio slouží ke sledování spuštění kanálu pracovního prostoru.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 712dc62e29229f03dec12afdf18edbf55667dbdf
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430783"
---
# <a name="use-azure-synapse-studio-to-monitor-your-workspace-pipeline-runs"></a>Použití Azure Synapse Studio ke sledování spuštění kanálu pracovního prostoru

S Azure Synapse Analytics můžete vytvářet složité kanály, které můžou automatizovat a organizovat přesun dat, transformaci dat a výpočetní aktivity v rámci vašeho řešení. Tyto kanály můžete vytvářet a monitorovat pomocí Azure Synapse Studio (preview).

Tento článek vysvětluje, jak sledovat spuštění kanálu, což vám umožní sledovat nejnovější stav, problémy a průběh vašich kanálů.

## <a name="access-the-list-of-pipeline-runs"></a>Přístup k seznamu spuštění kanálu

Chcete-li zobrazit seznam spuštění kanálu ve vašem pracovním prostoru, [nejprve otevřete Azure Synapse Studio](https://web.azuresynapse.net/) a vyberte svůj pracovní prostor.

![Přihlášení do pracovního prostoru](./media/common/login-workspace.png)

Po otevření pracovního prostoru vyberte část **Monitor** vlevo.

![Vybrat centrum monitoru](./media/common/left-nav.png)

Chcete-li zobrazit seznam spuštění kanálu, vyberte **možnost Spuštění** kanálu.

![Vybrat spuštění kanálu](./media/how-to-monitor-pipeline-runs/monitor-hub-nav-pipelineruns.png)

## <a name="filtering-your-pipeline-runs"></a>Filtrování spuštění kanálu

Seznam spuštění kanálu můžete filtrovat na ty, které vás zajímají. Filtry v horní části obrazovky umožňují určit pole, na které chcete filtrovat.

Můžete například filtrovat zobrazení a zobrazit pouze spuštění kanálu pro kanál s názvem "dovolená":

![Tlačítko Filtrovat](./media/common/filter-button.png)

![Ukázkový filtr](./media/how-to-monitor-pipeline-runs/filter-example.png)

## <a name="viewing-details-about-a-specific-pipeline-run"></a>Zobrazení podrobností o konkrétním spuštění kanálu

Chcete-li zobrazit podrobnosti o spuštění kanálu, vyberte spuštění kanálu. Potom zobrazte spuštění aktivity přidružené ke spuštění kanálu. Pokud je kanál stále spuštěn, můžete sledovat průběh. 
  
## <a name="next-steps"></a>Další kroky

Další informace o monitorování aplikací najdete v článku [Monitor Apache Spark aplikace.](how-to-monitor-spark-applications.md) 
