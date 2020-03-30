---
title: Monitorování prostředku Azure pomocí Azure Monitoru
description: Zjistěte, jak shromažďovat a analyzovat data pro prostředek Azure v Azure Monitoru.
ms. subservice: logs
ms.topic: quickstart
author: bwren
ms.author: bwren
ms.date: 12/15/2019
ms.openlocfilehash: c4b80e62d3800392b847a411dfc66c3278e72bba
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77661860"
---
# <a name="quickstart-monitor-an-azure-resource-with-azure-monitor"></a>Úvodní příručka: Monitorování prostředku Azure pomocí Azure Monitoru
[Azure Monitor](../overview.md) začne shromažďovat data z prostředků Azure v okamžiku, kdy jsou vytvořeny. Tento rychlý start poskytuje stručný návod dat, která se automaticky shromáždí pro prostředek a jak ho zobrazit na webu Azure Portal pro konkrétní prostředek. Později můžete přidat konfiguraci pro shromažďování dalších dat a můžete přejít do nabídky Azure Monitor použít stejné nástroje pro přístup k datům shromážděným pro všechny prostředky ve vašem předplatném.

Podrobnější popis dat monitorování shromážděných z prostředků Azure najdete v [tématu Monitorování prostředků Azure pomocí Azure Monitoru](../insights/monitor-azure-resource.md).


## <a name="sign-in-to-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k [https://portal.azure.com](https://portal.azure.com)portálu Azure na adrese . 


## <a name="overview-page"></a>Stránka s přehledem
Mnoho služeb bude zahrnovat data monitorování na stránce **Přehled** jako rychlý pohled na jejich provoz. To bude obvykle založené na podmnožině metrik platformy uložené v metrikách Azure Monitor.

1. Vyhledejte prostředek Azure ve vašem předplatném.
2. Přejděte na stránku **Přehled** a poznamenejte si, zda se zobrazují údaje o výkonu. Tato data budou poskytována službou Azure Monitor. Níže uvedený příklad je stránka **Přehled** pro účet úložiště Azure a uvidíte, že se zobrazuje více metrik.

    ![Stránka s přehledem](media/quick-monitor-azure-resource/overview.png)

3. Kliknutím na některý z grafů otevřete data v průzkumníku metrik, který je popsán níže.

## <a name="view-the-activity-log"></a>Zobrazení protokolu aktivit
Protokol aktivit poskytuje přehled o operacích na každém prostředku Azure v předplatném. To bude zahrnovat takové informace, jako když je zdroj vytvořen nebo změněn, při spuštění úlohy nebo při určité operaci.

1. V horní části nabídky zdroje vyberte **protokol aktivit**.
2. Aktuální filtr je nastaven na události související s vaším zdrojem. Pokud nevidíte žádné události, zkuste změnit **Časový rozsah,** abyste zvýšili časový rozsah.

    ![Protokol aktivit](media/quick-monitor-azure-resource/activity-log-resource.png)

4. Pokud chcete zobrazit události z jiných prostředků v předplatném, změňte kritéria ve filtru nebo dokonce odeberte vlastnosti filtru.

    ![Protokol aktivit](media/quick-monitor-azure-resource/activity-log-all.png)



## <a name="view-metrics"></a>Zobrazit metriky
Metriky jsou číselné hodnoty, které popisují některé aspekty vašeho prostředku v určitém čase. Azure Monitor automaticky shromažďuje metriky platformy v jednominutových intervalech ze všech prostředků Azure. Tyto metriky můžete zobrazit pomocí průzkumníka metrik.

1. V části **Monitorování** v nabídce zdroje vyberte **Metriky**. Tím se otevře průzkumník metrik s oborem nastaveným na váš prostředek.
2. Kliknutím na **Přidat metriku** přidáte metriku do grafu.
   
   ![Průzkumník metrik](media/quick-monitor-azure-resource/metrics-explorer-01.png)
   
4. V rozevíracím seznamu vyberte **metriku** a potom vyberte **agregaci**. To definuje, jak budou shromážděné hodnoty vzorkovány v každém časovém intervalu.

    ![Průzkumník metrik](media/quick-monitor-azure-resource/metrics-explorer-02.png)

5. Kliknutím na **Přidat metriku** přidáte do grafu další kombinace metrik a agregace.

    ![Průzkumník metrik](media/quick-monitor-azure-resource/metrics-explorer-03.png)



## <a name="next-steps"></a>Další kroky
V tomto rychlém startu jste si prohlíželi protokol aktivit a metriky pro prostředek Azure, které se automaticky shromažďují pomocí Azure Monitoru. Protokoly prostředků poskytují přehled o podrobné operaci prostředku, ale musí být nakonfigurovány, aby mohly být shromažďovány. Pokračujte v kurzu pro shromažďování protokolů prostředků do pracovního prostoru Analýzy protokolů, kde je lze analyzovat pomocí dotazů protokolu.

> [!div class="nextstepaction"]
> [Shromažďování a analýza protokolů prostředků pomocí Azure Monitoru](tutorial-resource-logs.md)
