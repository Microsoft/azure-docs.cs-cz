---
title: Připojení Centra zabezpečení Azure pro IoT k Azure Sentinelu | Dokumenty společnosti Microsoft
description: Zjistěte, jak propojit azure security center pro data IoT k Azure Sentinelu.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588633"
---
# <a name="connect-your-data-from-azure-security-center-for-iot-to-azure-sentinel"></a>Připojení dat z Azure Security Center pro IoT k Azure Sentinelu 


> [!IMPORTANT]
> Datový konektor Azure Security Center pro IoT je aktuálně ve verzi Public Preview. Tato funkce je k dispozici bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pomocí konektoru Azure Security Center for IoT můžete streamovat všechny události Centra zabezpečení Azure pro IoT do Azure Sentinelu. 

## <a name="prerequisites"></a>Požadavky

- **Oprávnění ke čtení** a **zápisu** v pracovním prostoru, do kterého se nasazuje Azure Sentinel
- **Azure Security Center pro IoT** musí být **povolené** na příslušných službách Služby IoT Hub
- **Oprávnění ke čtení** a **zápisu** v centru **Azure IoT Hub,** který chcete připojit
- **Oprávnění ke čtení** a **zápisu** ve **skupině prostředků služby Azure IoT Hub**

> [!NOTE]
> I když musíte povolit licenci úrovně Azure Security Center **Standard** na vašem předplatném k streamování výstrah prostředků IoT do Azure Sentinelu, stačí povolit licenci na **bezplatnou** úroveň Azure Security Center ve vašem předplatném, abyste mohli zobrazit výstrahy Azure Security Center for IoT v Azure Sentinelu. 

## <a name="connect-to-azure-security-center-for-iot"></a>Připojení k Centru zabezpečení Azure pro IoT

1. V Azure Sentinelu vyberte **datové konektory** a klikněte na dlaždici **Azure Security Center for IoT.**
1. V pravém dolním podokně klikněte na **Otevřít stránku konektoru**. 
1. Vedle každého předplatného služby IoT Hub, jehož výstrahy a upozornění na zařízení, které chcete streamovat do Azure Sentinelu, klikněte na **Připojit**. 
    - Pokud Azure Security Center pro IoT není povolena v tomto centru, zobrazí se upozornění **Povolit.** Kliknutím na odkaz **Povolit** spusťte službu. 
1. Můžete se rozhodnout, jestli chcete, aby výstrahy z Azure Security Center pro IoT automaticky generovat incidenty v Azure Sentinelu. V části **Vytvořit incidenty**vyberte **Povolit,** chcete-li povolit výchozí analytické pravidlo pro automatické vytváření incidentů z výstrah generovaných v připojené službě zabezpečení. Toto pravidlo lze změnit nebo upravit v části Pravidla **služby Analytics** > **Active.**

> [!NOTE]
> Může chvíli trvat, než se seznam rozbočovačů po provedení změn připojení aktualizuje. 

## <a name="log-analytics-alert-display"></a>Zobrazení výstrahy Log Analytics

Použití příslušného schématu v Log Analytics k zobrazení výstrah Azure Security Center for IoT:

1. **Scházejte protokoly** > **SecurityInsights** > **SecurityAlert**nebo vyhledejte **službu SecurityAlert**. 
2. Filtrem zobrazíte jenom Azure Security Center for IoT generované výstrahy pomocí následujícího filtru kql:

```kusto
SecurityAlert | where ProductName == "Azure Security Center for IoT"
``` 

### <a name="service-notes"></a>Servisní poznámky

Po připojení služby IoT Hub jsou data centra dostupná v Azure Sentinelu přibližně o 15 minut později.


## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste se dozvěděli, jak propojit Azure Security Center pro data IoT s Azure Sentinel. Další informace o Azure Sentinelu najdete v následujících článcích:
- Přečtěte [si, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začínáme [s detekcí hrozeb pomocí Azure Sentinelu](tutorial-detect-threats-built-in.md).
- Ke sledování dat [použijte sešity.](tutorial-monitor-your-data.md)
