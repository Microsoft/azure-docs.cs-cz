---
title: Monitorování pro vytváření dat Azure s OMS | Microsoft Docs
description: Naučte se monitorovat objekt pro vytváření dat Azure pomocí dat směrování k Operations Management Suite (OMS) pro analýzu.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: douglasl
ms.openlocfilehash: 4275a4ddcee51d58949b5bd83e4a898cb3dbb389
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/21/2018
ms.locfileid: "36304420"
---
# <a name="monitor-azure-data-factory-with-operations-management-suite-oms"></a>Monitorování pro vytváření dat Azure pomocí služby Operations Management Suite (OMS)

Azure Data Factory integrace s monitorováním Azure slouží k data trasy k Operations Management Suite (OMS). Tato integrace je užitečné v následujících scénářích:

1.  Chcete zapsat složitých dotazů na širokou škálu metriky, které se publikuje na serveru služby Data Factory k OMS. Můžete také vytvořit vlastní oznámení na tyto dotazy prostřednictvím OMS.

2.  Chcete sledovat v rámci datové továrny. Data z více objektů pro vytváření dat může směrovat do jednoho pracovního prostoru OMS.

## <a name="get-started"></a>Začínáme

### <a name="configure-diagnostic-settings-and-oms-workspace"></a>Konfigurace nastavení pro diagnostiku a pracovním prostorem OMS

Povolte nastavení diagnostiky pro datovou továrnu.

1.  Vyberte **Azure monitorování** -> **nastavení diagnostiky** -> vyberte objekt pro vytváření dat na Diagnostika -> zapnout.

    ![monitorování. oms image1.png](media/data-factory-monitor-oms/monitor-oms-image1.png)

2.  Zadejte nastavení pro diagnostiku včetně konfigurace pracovním prostorem OMS.

    ![monitorování. oms image2.png](media/data-factory-monitor-oms/monitor-oms-image2.png)

### <a name="install-azure-data-factory-analytics-oms-pack-from-azure-marketplace"></a>Nainstalujte Azure Data Factory Analytics OMS pack z Azure Marketplace

![monitorování. oms image3.png](media/data-factory-monitor-oms/monitor-oms-image3.png)

![monitorování. oms image4.png](media/data-factory-monitor-oms/monitor-oms-image4.png)

Klikněte na tlačítko **vytvořit** a vyberte pracovní prostor OMS a nastavení pracovní prostor OMS.

![monitorování. oms image5.png](media/data-factory-monitor-oms/monitor-oms-image5.png)

## <a name="monitor-azure-data-factory-metrics-using-oms"></a>Monitorovat metriky objekt pro vytváření dat Azure pomocí OMS

Instalace **Azure Data Factory Analytics** OMS pack vytvoří výchozí sadu zobrazení, která umožňuje následující metriky:

- Spustí kanálu spustí ADF-1) pomocí služby Data Factory

- Spustí aktivita běží ADF-2) službou Data Factory

- Spustí ADF-3) spustí aktivační službou Data Factory

- Chyby kanálu prvních 10 ADF chyby-1) pomocí služby Data Factory

- Běh aktivit prvních 10 chyby ADF-2) pomocí služby Data Factory

- Chyby ADF-3) prvních 10 aktivační událost chyby službou Data Factory

- Běh aktivit statistiky ADF-1) podle typu

- Spustí aktivační událost statistiky ADF-2) podle typu

- Kanál maximální statistiky ADF-3) spustí doba trvání

![monitorování. oms image6.png](media/data-factory-monitor-oms/monitor-oms-image6.png)

![monitorování. oms image7.png](media/data-factory-monitor-oms/monitor-oms-image7.png)

Můžete vizualizovat metriku výše, podívejte se na dotazy za tyto metriky, upravit dotazy, vytvářet výstrahy a tak dále.

![monitorování. oms image8.png](media/data-factory-monitor-oms/monitor-oms-image8.png)

## <a name="next-steps"></a>Další postup

V tématu [monitorování a Správa kanálů prostřednictvím kódu programu](https://docs.microsoft.com/en-us/azure/data-factory/monitor-programmatically) Další informace o monitorování a Správa kanálů pomocí spouštění skriptů.
