---
title: Monitorování kanálu spouští Azure synapse Studio (Preview).
description: Pomocí Azure synapse studia monitorujte spuštění kanálu pracovního prostoru.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 712dc62e29229f03dec12afdf18edbf55667dbdf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81430783"
---
# <a name="use-azure-synapse-studio-to-monitor-your-workspace-pipeline-runs"></a>Použití Azure synapse studia ke sledování spuštění kanálu pracovního prostoru

Pomocí služby Azure synapse Analytics můžete vytvářet komplexní kanály, které mohou automatizovat a orchestrovat přesun dat, transformaci dat a výpočetní aktivity ve vašem řešení. Tyto kanály můžete vytvářet a monitorovat pomocí Azure synapse studia (Preview).

Tento článek vysvětluje, jak monitorovat spuštění vašeho kanálu, což vám umožní sledovat nejnovější stav, problémy a průběh vašich kanálů.

## <a name="access-the-list-of-pipeline-runs"></a>Přístup k seznamu spuštění kanálu

Pokud chcete zobrazit seznam spuštění kanálu ve vašem pracovním prostoru, nejdřív [otevřete Azure synapse Studio](https://web.azuresynapse.net/) a vyberte svůj pracovní prostor.

![Přihlášení k pracovnímu prostoru](./media/common/login-workspace.png)

Po otevření pracovního prostoru vyberte na levé straně část **monitorování** .

![Výběr centra monitorování](./media/common/left-nav.png)

Vyberte **spuštění kanálu** , abyste zobrazili seznam spuštění kanálu.

![Vybrat spuštění kanálu](./media/how-to-monitor-pipeline-runs/monitor-hub-nav-pipelineruns.png)

## <a name="filtering-your-pipeline-runs"></a>Filtrování spuštění kanálu

Seznam spuštěných kanálů můžete filtrovat do těch, které vás zajímají. Filtry v horní části obrazovky umožňují zadat pole, podle kterého chcete filtrovat.

Můžete například filtrovat zobrazení a zobrazit pouze spuštění kanálu pro kanál s názvem "svátek":

![Tlačítko Filtr](./media/common/filter-button.png)

![Vzorový filtr](./media/how-to-monitor-pipeline-runs/filter-example.png)

## <a name="viewing-details-about-a-specific-pipeline-run"></a>Zobrazení podrobností o konkrétním spuštění kanálu

Pokud chcete zobrazit podrobnosti o spuštění kanálu, vyberte spuštění kanálu. Pak zobrazte spuštění aktivit související se spuštěním kanálu. Pokud je kanál stále spuštěný, můžete sledovat jeho průběh. 
  
## <a name="next-steps"></a>Další kroky

Další informace o monitorování aplikací najdete v článku [monitorování Apache Sparkch aplikací](how-to-monitor-spark-applications.md) . 
