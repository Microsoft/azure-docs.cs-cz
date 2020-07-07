---
title: Konfigurace Sentinel (Preview)
description: Vysvětluje, jak nakonfigurovat Azure Sentinel pro příjem dat z Azure Security Center pro řešení IoT.
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
ms.openlocfilehash: fe8e4c1b08f96e5f6b2fc7649f7a4361616b7c87
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "81311305"
---
# <a name="connect-your-data-from-azure-security-center-for-iot-to-azure-sentinel-preview"></a>Připojení dat z Azure Security Center pro IoT do Azure Sentinel (Preview)

> [!IMPORTANT]
> Azure Security Center pro datový konektor IoT v Azure Sentinel je aktuálně ve verzi Public Preview.
> Tato funkce se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

V této příručce se dozvíte, jak připojit Azure Security Center pro data IoT do Azure Sentinel.

> [!div class="checklist"]
> * Požadavky
> * Nastavení připojení
> * Zobrazení výstrah Log Analytics

Připojte výstrahy z Azure Security Center pro IoT a Streamujte je přímo do Azure Sentinel.

## <a name="prerequisites"></a>Požadavky

- Musíte mít oprávnění **ke čtení** a **zápisu** v pracovním prostoru.
- **Azure Security Center pro IoT** musí být **povolené** na vašich relevantních IoT Hub.
- Musíte mít oprávnění **ke čtení** i **zápisu** na **IoT Hub Azure** , ke kterým se chcete připojit.
- Musíte mít také oprávnění **ke čtení** a **zápisu** ve **skupině prostředků Azure IoT Hub**.

> [!NOTE]
> Aby bylo možné odesílat obecné výstrahy týkající se prostředků Azure, musíte mít ve svém předplatném spuštěnou licenci na úrovni Standard Azure Security Center. U Azure Security Center pro IoT se vyžaduje licencování na úrovni Free, do Azure Sentinel se předají jenom Azure Security Center pro výstrahy související s IoT.

## <a name="connect-to-azure-security-center-for-iot"></a>Připojení k Azure Security Center pro IoT

1. V Azure Sentinel vyberte **datové konektory** a potom klikněte na dlaždici **Azure Security Center pro IoT** .
1. V dolní části pravého podokna klikněte na tlačítko **otevřít stránku konektoru**.
1. Klikněte na **připojit**a vedle každého předplatného IoT Hub, jejichž výstrahy a výstrahy zařízení chcete streamovat do Azure Sentinel.
    - Pokud Azure Security Center pro IoT v tomto centru není povolená, zobrazí se zpráva s upozorněním na povolení. Kliknutím na odkaz **Povolit** spusťte službu a povolte ji.
1. Můžete rozhodnout, jestli chcete, aby upozornění z Azure Security Center pro IoT automaticky generovala incidenty v Azure Sentinel. V části **vytvořit incidenty**vyberte **Povolit** , pokud chcete, aby pravidlo automaticky vytvořilo incidenty z generovaných výstrah.  Toto pravidlo se dá změnit nebo upravit v části **Analýza**  >  **aktivní** pravidla.

> [!NOTE]
>Po provedení změn připojení může aktualizace seznamu centra trvat 10 sekund nebo déle.

## <a name="log-analytics-alert-display"></a>Zobrazení výstrahy Log Analytics

Chcete-li použít příslušné schéma v Log Analytics k zobrazení Azure Security Center pro výstrahy IoT:

1. Otevřete **protokoly**  >  **SecurityInsights**  >  **SecurityAlert**, nebo vyhledejte **SecurityAlert**.
1. Filtr, aby se zobrazily pouze Azure Security Center upozornění vygenerovaných IoT pomocí následujícího filtru KQL:

```kusto
SecurityAlert | where ProductName == "Azure Security Center for IoT"
```

### <a name="service-notes"></a>Poznámky ke službě

Po připojení IoT Hub jsou data centra k dispozici v Azure Sentinel přibližně 15 minut později.

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste zjistili, jak připojit Azure Security Center pro IoT do Azure Sentinel. Další informace o detekci hrozeb a přístupu k datům zabezpečení najdete v následujících článcích:

- Naučte se, jak pomocí funkce Azure Sentinel [získat přehled o vašich datech a potenciálních hrozbách](https://docs.microsoft.com/azure/sentinel/quickstart-get-visibility).

- Informace o [přístupu k datům zabezpečení IoT](how-to-security-data-access.md)
