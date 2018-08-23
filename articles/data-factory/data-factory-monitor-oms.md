---
title: Monitorování pomocí OMS pro vytváření dat Azure | Dokumentace Microsoftu
description: Zjistěte, jak monitorovat objekt pro vytváření dat Azure pomocí směrování dat pro Operations Management Suite (OMS) pro účely analýzy.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/20/2018
ms.author: douglasl
ms.openlocfilehash: fb5da75d0e42dd0700cad008d348ff846d602409
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2018
ms.locfileid: "42054668"
---
# <a name="monitor-azure-data-factory-with-operations-management-suite-oms"></a>Monitorování pro vytváření dat Azure pomocí Operations Management Suite (OMS)

Integrace služby Azure Data Factory prostřednictvím služby Azure Monitor můžete použít pro data trasy pro Operations Management Suite (OMS). Tato integrace je užitečná v následujících scénářích:

1.  Chcete vytváření složitých dotazů na celou řadu metrik, jenž je publikována serverem služby Data Factory k OMS. Můžete také vytvořit vlastní oznámení na tyto dotazy přes OMS.

2.  Chcete sledovat v rámci datové továrny. Směrovat data z několika továren s dat do jednoho pracovního prostoru OMS.

Pro zavedení sedm po minutách a ukázku této funkce z následujícího videa:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Monitor-Data-Factory-pipelines-using-Operations-Management-Suite-OMS/player]

## <a name="get-started"></a>Začínáme

### <a name="configure-diagnostic-settings-and-oms-workspace"></a>Konfigurace nastavení diagnostiky a pracovní prostor OMS.

Povolení diagnostických nastavení pro službu data factory.

1.  Vyberte **Azure Monitor** -> **nastavení diagnostiky** -> vyberte služby data factory -> zapnout na diagnostiky.

    ![monitorování oms-image1.png](media/data-factory-monitor-oms/monitor-oms-image1.png)

2.  Zadejte nastavení diagnostiky, včetně konfigurace pracovního prostoru OMS.

    ![monitorování oms-image2.png](media/data-factory-monitor-oms/monitor-oms-image2.png)

### <a name="install-azure-data-factory-analytics-oms-pack-from-azure-marketplace"></a>Nainstalujte sadu Azure Data Factory Analytics OMS z Azure Marketplace

![monitorování oms-image3.png](media/data-factory-monitor-oms/monitor-oms-image3.png)

![monitorování oms-image4.png](media/data-factory-monitor-oms/monitor-oms-image4.png)

Klikněte na tlačítko **vytvořit** a vyberte pracovní prostor OMS a nastavení pracovního prostoru OMS.

![monitorování oms-image5.png](media/data-factory-monitor-oms/monitor-oms-image5.png)

## <a name="monitor-azure-data-factory-metrics-using-oms"></a>Monitorujte metriky objektů pro vytváření dat Azure pomocí OMS

Instalace **Azure Data Factory Analytics** OMS pack vytvoří výchozí sadu zobrazení, která umožňuje následující metriky:

- Spuštění kanálu ADF spuštění-1) pomocí služby Data Factory

- Spuštění aktivit spuštění ADF-2) pomocí služby Data Factory

- Spuštění aktivační události spuštění ADF-3) pomocí služby Data Factory

- Chyby kanálu ADF chyby-1) prvních 10 službou Data Factory

- Spuštění aktivit prvních 10 ADF chyby-2) pomocí služby Data Factory

- Chyby ADF-3) prvních 10 aktivační událost chyby službou Data Factory

- Spuštění aktivit ADF statistiky-1) podle typu

- Statistiky ADF-2) spuštění aktivační události podle typu

- Spuštění kanálu ADF statistiky – 3) maximální doba trvání

![monitorování oms-image6.png](media/data-factory-monitor-oms/monitor-oms-image6.png)

![monitorování oms-image7.png](media/data-factory-monitor-oms/monitor-oms-image7.png)

Můžete vizualizovat metriky výše, podívejte se na dotazy za tyto metriky, upravit dotazy, vytvářet výstrahy a tak dále.

![monitorování oms-image8.png](media/data-factory-monitor-oms/monitor-oms-image8.png)

## <a name="next-steps"></a>Další postup

Zobrazit [monitorování a Správa kanálů prostřednictvím kódu programu](https://docs.microsoft.com/en-us/azure/data-factory/monitor-programmatically) Další informace o monitorování a Správa kanálů pomocí spouštění skriptů.
