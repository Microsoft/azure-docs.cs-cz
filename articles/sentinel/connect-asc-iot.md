---
title: Připojit Azure Defender pro IoT do Azure Sentinel | Microsoft Docs
description: Naučte se, jak propojit Azure Defender (dřív Azure Security Center) pro data IoT do Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/07/2020
ms.author: yelevin
ms.openlocfilehash: 822d0c742bbd54b5bab0c69e82652743584a0696
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89659609"
---
# <a name="connect-your-data-from-azure-defender-formerly-azure-security-center-for-iot-to-azure-sentinel"></a>Připojení dat z Azure Defenderu (dříve Azure Security Center) pro IoT do Azure Sentinel 


> [!IMPORTANT]
> Datový konektor Azure Defender pro IoT je momentálně ve verzi Public Preview. Tato funkce se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Použijte Azure Defender pro IoT Connector k streamování všech událostí Azure Defenderu pro IoT do Azure Sentinel. 

## <a name="prerequisites"></a>Požadavky

- Oprávnění **ke čtení** a **zápisu** v pracovním prostoru, na kterém je nasazená Azure Sentinel
- V příslušném IoT Hub musí být **povolený** **Azure Defender pro IoT** .
- Oprávnění **ke čtení** a **zápisu** na **IoT Hub Azure** , ke kterým se chcete připojit
- Oprávnění **ke čtení** a **zápisu** ve **skupině prostředků Azure IoT Hub**

## <a name="connect-to-azure-defender-for-iot"></a>Připojení k Azure Defenderu pro IoT

1. V Azure Sentinel vyberte **datové konektory** a pak v galerii vyberte **Azure Defender pro IoT** (Azure Security Center pro IoT).
1. V pravém dolním podokně klikněte na možnost **otevřít stránku konektoru**. 
1. Klikněte na **připojit**a vedle každého předplatného IoT Hub, jejichž výstrahy a výstrahy zařízení chcete streamovat do Azure Sentinel. 
    - Pokud Azure Defender pro IoT není v tomto centru povolený, zobrazí se vám zpráva s upozorněním na **Povolení** . Kliknutím na odkaz **Enable (Povolit** ) spusťte službu. 
1. Můžete rozhodnout, jestli chcete, aby upozornění z Azure Defenderu pro IoT automaticky generovala incidenty v Azure Sentinel. V části **vytvořit incidenty**vyberte **Povolit** , pokud chcete výchozí pravidlo pro analýzu povolit automatické vytváření incidentů z výstrah vygenerovaných v připojené službě zabezpečení. Toto pravidlo se dá změnit nebo upravit v části **Analýza**  >  **aktivní** pravidla.

> [!NOTE]
> Může trvat nějakou dobu, než se seznam centra aktualizuje po provedení změn připojení. 

## <a name="log-analytics-alert-display"></a>Zobrazení výstrahy Log Analytics

Pokud chcete použít příslušné schéma v Log Analytics k zobrazení výstrah Azure Defenderu pro IoT:

1. Otevřete **protokoly**  >  **SecurityInsights**  >  **SecurityAlert**, nebo vyhledejte **SecurityAlert**. 
2. Filtr – Pokud chcete zobrazit jenom výstrahy vygenerované službou IoT pomocí následujícího filtru KQL, podívejte se jenom na Azure Defender:

```kusto
SecurityAlert | where ProductName == "Azure Defender for IoT"
``` 

### <a name="service-notes"></a>Poznámky ke službě

Po připojení IoT Hub jsou data centra k dispozici v Azure Sentinel přibližně 15 minut později.


## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste zjistili, jak připojit Azure Defender pro data IoT do Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Pomocí sešitů](tutorial-monitor-your-data.md) můžete monitorovat data.
