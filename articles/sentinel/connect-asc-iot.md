---
title: Připojení Azure Security Center pro IoT do Azure Sentinel | Microsoft Docs
description: Naučte se, jak připojit Azure Security Center pro data IoT do Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/18/2020
ms.author: yelevin
ms.openlocfilehash: 3af51110a4c4604444573f62be65077c786db606
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588633"
---
# <a name="connect-your-data-from-azure-security-center-for-iot-to-azure-sentinel"></a>Připojení dat z Azure Security Center pro IoT do Azure Sentinel 


> [!IMPORTANT]
> Azure Security Center pro datový konektor IoT je aktuálně ve verzi Public Preview. Tato funkce se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Použijte Azure Security Center pro IoT Connector ke streamování všech vašich Azure Security Center pro události IoT do Azure Sentinel. 

## <a name="prerequisites"></a>Předpoklady

- Oprávnění **ke čtení** a **zápisu** v pracovním prostoru, na kterém je nasazená Azure Sentinel
- **Azure Security Center pro IoT** musí být **povolené** na vašich relevantních IoT hubech.
- Oprávnění **ke čtení** a **zápisu** na **IoT Hub Azure** , ke kterým se chcete připojit
- Oprávnění **ke čtení** a **zápisu** ve **skupině prostředků Azure IoT Hub**

> [!NOTE]
> I když musíte ve svém předplatném povolit licenci na úrovni **Standard** pro Azure Security Center ke streamování výstrah prostředků IoT do Azure Sentinel, stačí povolit Azure Security Center licence na úrovni **Free** v předplatném, aby se zobrazily Azure Security Center výstrahy IoT v Azure Sentinel. 

## <a name="connect-to-azure-security-center-for-iot"></a>Připojení k Azure Security Center pro IoT

1. V Azure Sentinel vyberte **datové konektory** a potom klikněte na dlaždici **Azure Security Center pro IoT** .
1. V pravém dolním podokně klikněte na možnost **otevřít stránku konektoru**. 
1. Klikněte na **připojit**a vedle každého předplatného IoT Hub, jejichž výstrahy a výstrahy zařízení chcete streamovat do Azure Sentinel. 
    - Pokud v tomto centru není povolená Azure Security Center pro IoT, zobrazí se vám zpráva s upozorněním na **Povolení** . Kliknutím na odkaz **Enable (Povolit** ) spusťte službu. 
1. Můžete rozhodnout, jestli chcete, aby upozornění z Azure Security Center pro IoT automaticky generovala incidenty v Azure Sentinel. V části **vytvořit incidenty**vyberte **Povolit** , pokud chcete výchozí pravidlo pro analýzu povolit automatické vytváření incidentů z výstrah vygenerovaných v připojené službě zabezpečení. Toto pravidlo se dá změnit nebo upravit v části **analýza** > **aktivní** pravidla.

> [!NOTE]
> Může trvat nějakou dobu, než se seznam centra aktualizuje po provedení změn připojení. 

## <a name="log-analytics-alert-display"></a>Zobrazení výstrahy Log Analytics

Chcete-li použít příslušné schéma v Log Analytics k zobrazení Azure Security Center pro výstrahy IoT:

1. Otevřete **protokoly** > **SecurityInsights** > **SecurityAlert**, nebo vyhledejte **SecurityAlert**. 
2. Filtr, aby se zobrazily pouze Azure Security Center upozornění vygenerovaných IoT pomocí následujícího filtru KQL:

```kusto
SecurityAlert | where ProductName == "Azure Security Center for IoT"
``` 

### <a name="service-notes"></a>Poznámky ke službě

Po připojení IoT Hub jsou data centra k dispozici v Azure Sentinel přibližně 15 minut později.


## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste zjistili, jak připojit Azure Security Center pro data IoT do Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Pomocí sešitů](tutorial-monitor-your-data.md) můžete monitorovat data.
