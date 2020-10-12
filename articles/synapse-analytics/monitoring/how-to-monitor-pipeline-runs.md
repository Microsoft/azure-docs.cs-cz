---
title: Monitorování spuštění kanálu pomocí synapse studia
description: Pomocí nástroje synapse Studio monitorujte spuštění kanálu pracovního prostoru.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 7676f4aeeb9485ce5e3a702027884bc54d79a863
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87387315"
---
# <a name="use-synapse-studio-to-monitor-your-workspace-pipeline-runs"></a>Použití synapse studia ke sledování spuštění kanálu pracovního prostoru

Pomocí služby Azure synapse Analytics můžete vytvářet komplexní kanály, které mohou automatizovat a orchestrovat přesun dat, transformaci dat a výpočetní aktivity ve vašem řešení. Tyto kanály můžete vytvářet a monitorovat pomocí synapse studia (Preview).

Tento článek vysvětluje, jak monitorovat spuštění vašeho kanálu, což vám umožní sledovat nejnovější stav, problémy a průběh vašich kanálů.

## <a name="access-pipeline-runs-list"></a>Seznam spuštění kanálu přístupu

Pokud chcete zobrazit seznam spuštění kanálu ve vašem pracovním prostoru, nejdřív [otevřete synapse Studio](https://web.azuresynapse.net/) a vyberte svůj pracovní prostor.

![Přihlášení k pracovnímu prostoru](./media/common/login-workspace.png)

Po otevření pracovního prostoru vyberte na levé straně část **monitorování** .

![Výběr centra monitorování](./media/common/left-nav.png)

Vyberte **spuštění kanálu** , abyste zobrazili seznam spuštění kanálu.

![Vybrat spuštění kanálu](./media/how-to-monitor-pipeline-runs/monitor-hub-nav-pipelineruns.png)

## <a name="filter-your-pipeline-runs"></a>Filtrování spuštění kanálu

Seznam spuštěných kanálů můžete filtrovat do těch, které vás zajímají. Filtry v horní části obrazovky umožňují zadat pole, podle kterého chcete filtrovat.

Můžete například filtrovat zobrazení a zobrazit pouze spuštění kanálu pro kanál s názvem "svátek":

![Tlačítko Filtr](./media/common/filter-button.png)

![Vzorový filtr](./media/how-to-monitor-pipeline-runs/filter-example.png)

## <a name="view-details-about-a-specific-pipeline-run"></a>Zobrazit podrobnosti o konkrétním spuštění kanálu

Pokud chcete zobrazit podrobnosti o spuštění kanálu, vyberte spuštění kanálu. Pak zobrazte spuštění aktivit související se spuštěním kanálu. Pokud je kanál stále spuštěný, můžete sledovat jeho průběh. 
  
## <a name="next-steps"></a>Další kroky

Další informace o monitorování aplikací najdete v článku [monitorování Apache Sparkch aplikací](how-to-monitor-spark-applications.md) . 
