---
title: Konfigurace Azure Sentinel pro Defender pro IoT (Preview)
description: Vysvětluje, jak nakonfigurovat Azure Sentinel pro příjem dat z vašeho programu Defender pro řešení IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: 71147352c5b75195ed0dff2b05acc5315f3183cc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90936471"
---
# <a name="connect-your-data-from-defender-for-iot-to-azure-sentinel-preview"></a>Připojte data z Defenderu pro IoT do Azure Sentinel (Preview).

Azure Security Center pro datový konektor IoT v Azure Sentinel je aktuálně ve verzi Public Preview. Tato funkce se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/en-us/support/legal/preview-supplemental-terms/).

V této příručce se dozvíte, jak připojit Defender pro data IoT do Azure Sentinel.

> [!div class="checklist"]
> * Požadavky
> * Nastavení připojení
> * Zobrazení výstrah Log Analytics

Připojte výstrahy z Defenderu pro IoT a Streamujte je přímo do Azure Sentinel.

## <a name="prerequisites"></a>Požadavky

- Musíte mít oprávnění **ke čtení** a **zápisu** v pracovním prostoru.
- V příslušném IoT Hub musí být **povolený** **Defender pro IoT** .
- Musíte mít oprávnění **ke čtení** i **zápisu** na **IoT Hub Azure** , ke kterým se chcete připojit.
- Musíte mít také oprávnění **ke čtení** a **zápisu** ve **skupině prostředků Azure IoT Hub**.

> [!NOTE]
> Aby bylo možné odesílat obecné výstrahy týkající se prostředků Azure, musíte mít ve svém předplatném spuštěnou licenci na úrovni Standard Azure Security Center. V případě, že je pro Defender pro IoT potřeba licencování na úrovni Free, do Azure Sentinel se přepošle jenom Defender pro výstrahy související s IoT.

## <a name="connect-to-defender-for-iot"></a>Připojení k programu Defender pro IoT

1. V Azure Sentinel vyberte **datové konektory** a potom klikněte na dlaždici **Defender for IoT** .
1. V dolní části pravého podokna klikněte na tlačítko **otevřít stránku konektoru**.
1. Klikněte na **připojit**a vedle každého předplatného IoT Hub, jejichž výstrahy a výstrahy zařízení chcete streamovat do Azure Sentinel.
    - Pokud není Defender pro IoT v tomto centru povolený, zobrazí se zpráva s upozorněním na povolení. Kliknutím na odkaz **Povolit** spusťte službu a povolte ji.
1. Můžete rozhodnout, jestli chcete, aby upozornění z programu Defender pro IoT automaticky generovala incidenty v Azure Sentinel. V části **vytvořit incidenty**vyberte **Povolit** , pokud chcete, aby pravidlo automaticky vytvořilo incidenty z generovaných výstrah.  Toto pravidlo se dá změnit nebo upravit v části **Analýza**  >  **aktivní** pravidla.

> [!NOTE]
>Po provedení změn připojení může aktualizace seznamu centra trvat 10 sekund nebo déle.

## <a name="log-analytics-alert-display"></a>Zobrazení výstrahy Log Analytics

Chcete-li použít příslušné schéma v Log Analytics k zobrazení výstrah programu Defender pro IoT:

1. Otevřete **protokoly**  >  **SecurityInsights**  >  **SecurityAlert**, nebo vyhledejte **SecurityAlert**.
1. Filtr pro zobrazení upozornění vygenerovaných v rámci IoT pomocí následujícího filtru KQL:

```kusto
SecurityAlert | where ProductName == "Defender for IoT"
```

### <a name="service-notes"></a>Poznámky ke službě

Po připojení IoT Hub jsou data centra k dispozici v Azure Sentinel přibližně 15 minut později.

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste zjistili, jak připojit Defender pro IoT k Azure Sentinel. Další informace o detekci hrozeb a přístupu k datům zabezpečení najdete v následujících článcích:

- Naučte se, jak pomocí funkce Azure Sentinel [získat přehled o vašich datech a potenciálních hrozbách](https://docs.microsoft.com/azure/sentinel/quickstart-get-visibility).

- Informace o [přístupu k datům zabezpečení IoT](how-to-security-data-access.md)
