---
title: Azure Security Center pro příručku IoT pro konfiguraci s konfigurací Azure Sentinel (Preview) | Microsoft Docs
description: Tato příručka vysvětluje, jak nakonfigurovat Azure Sentinel pro příjem dat z Azure Security Center pro řešení IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/18/2020
ms.author: mlottner
ms.openlocfilehash: f6e7eddd6ddbcec61c3d8d173891cbc9abaaf08f
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2020
ms.locfileid: "77463177"
---
> [!IMPORTANT]
> Azure Security Center pro datový konektor IoT v Azure Sentinel je aktuálně ve verzi Public Preview.
> Tato funkce se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

# <a name="connect-your-data-from-azure-security-center-for-iot-to-azure-sentinel-preview"></a>Připojení dat z Azure Security Center pro IoT do Azure Sentinel (Preview) 

V této příručce se dozvíte, jak připojit Azure Security Center pro data IoT do Azure Sentinel.  

> [!div class="checklist"]
> * Předpoklady 
> * Nastavení připojení
> * Zobrazení výstrah Log Analytics 

Připojte výstrahy z Azure Security Center pro IoT a Streamujte je přímo do Azure Sentinel.

## <a name="prerequisites"></a>Předpoklady

- Musíte mít oprávnění **ke čtení** a **zápisu** v pracovním prostoru.
- **Azure Security Center pro IoT** musí být **povolené** na vašich relevantních IoT Hub.
- Musíte mít oprávnění **ke čtení** i **zápisu** na **IoT Hub Azure** , ke kterým se chcete připojit.
- Musíte mít také oprávnění **ke čtení** a **zápisu** ve **skupině prostředků Azure IoT Hub**.

> [!NOTE]
> Aby bylo možné odesílat obecné výstrahy týkající se prostředků Azure, musíte mít ve svém předplatném spuštěnou licenci na úrovni Standard Azure Security Center. U Azure Security Center pro IoT se vyžaduje licencování na úrovni Free, do Azure Sentinel se předají jenom Azure Security Center pro výstrahy související s IoT. 

## <a name="connect-to-azure-security-center-for-iot"></a>Připojení k Azure Security Center pro IoT

1. V Azure Sentinel vyberte **datové konektory** a potom klikněte na dlaždici **Azure Security Center pro IoT** .
1. V pravém dolním podokně klikněte na možnost **otevřít stránku konektoru**. 
1. Klikněte na **připojit**a vedle každého předplatného IoT Hub, jejichž výstrahy a výstrahy zařízení chcete streamovat do Azure Sentinel. 
    - Pokud v tomto centru není povolená Azure Security Center pro IoT, zobrazí se vám zpráva s upozorněním na povolení. Kliknutím na odkaz **Enable (Povolit** ) spusťte službu. 
1. Můžete rozhodnout, jestli chcete, aby upozornění z Azure Security Center pro IoT automaticky generovala incidenty v Azure Sentinel. V části **vytvořit incidenty**vyberte **Povolit** , pokud chcete výchozí pravidlo pro analýzu povolit automatické vytváření incidentů z výstrah vygenerovaných v připojené službě zabezpečení. Toto pravidlo se dá změnit nebo upravit v části **analýza** > **aktivní** pravidla.

> [!NOTE]
>Po provedení změn připojení může aktualizace seznamu centra trvat 10 sekund nebo déle. 

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

V tomto dokumentu jste zjistili, jak připojit Azure Security Center pro IoT do Azure Sentinel. Další informace o detekci hrozeb a přístupu k datům zabezpečení najdete v následujících článcích:

- Naučte se, jak pomocí funkce Azure Sentinel [získat přehled o vašich datech a potenciálních hrozbách](https://docs.microsoft.com/azure/sentinel/quickstart-get-visibility).

- Informace o [přístupu k datům zabezpečení IoT](how-to-security-data-access.md)