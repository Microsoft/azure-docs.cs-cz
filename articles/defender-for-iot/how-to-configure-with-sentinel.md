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
ms.openlocfilehash: c5c0f74ed8a5688b20eea4e74f747d3ff6dd0e63
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2020
ms.locfileid: "96339995"
---
# <a name="connect-your-data-from-defender-for-iot-to-azure-sentinel-preview"></a>Připojte data z Defenderu pro IoT do Azure Sentinel (Preview).

Datový konektor Azure Defender pro IoT v Azure Sentinel je momentálně ve verzi Public Preview. Tato funkce se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/en-us/support/legal/preview-supplemental-terms/).

V této příručce se dozvíte, jak připojit Defender pro data IoT do Azure Sentinel.

> [!div class="checklist"]
> * Požadavky
> * Nastavení připojení
> * Zobrazení výstrah Log Analytics

Připojte výstrahy z Defenderu pro IoT a Streamujte je přímo do Azure Sentinel.

Díky většímu začlenění Azure Defenderu pro IoT pomocí Azure Sentinel, prvního cloudového SIEMu a první SIEM s nativním IoT a zabezpečením poskytuje Microsoft jednodušší přístup k sjednocenému zabezpečení napříč IT a průmyslovými sítěmi. V kombinaci se strojovým učením s bránou Azure Sentinel umožňuje tato integrace organizacím rychle detekovat útoky s více fázemi, které jsou často mezi sebou i hranicemi. Kromě toho vám Azure Defender pro integraci IoT s možnostmi orchestrace, automatizace a reakce na zabezpečení Azure Sentinel umožňuje automatizovanou reakci a prevenci pomocí integrovaných playbooky s podporou pro společnosti. 

## <a name="prerequisites"></a>Požadavky

- Musíte mít oprávnění **ke čtení** a **zápisu** v pracovním prostoru.
- V příslušném IoT Hub musí být **povolený** **Defender pro IoT** .
- Musíte mít oprávnění **ke čtení** i **zápisu** na **IoT Hub Azure** , ke kterým se chcete připojit.
- Musíte mít také oprávnění **ke čtení** a **zápisu** ve **skupině prostředků Azure IoT Hub**.


## <a name="connect-to-defender-for-iot"></a>Připojení k programu Defender pro IoT

1. V Azure Sentinel vyberte **datové konektory** a potom klikněte na dlaždici **Defender for IoT** .
1. V dolní části pravého podokna klikněte na tlačítko **otevřít stránku konektoru**.
1. Klikněte na **připojit** a vedle každého předplatného IoT Hub, jejichž výstrahy a výstrahy zařízení chcete streamovat do Azure Sentinel.
    - Pokud není Defender pro IoT v tomto centru povolený, zobrazí se zpráva s upozorněním na povolení. Kliknutím na odkaz **Povolit** spusťte službu a povolte ji.
1. Můžete rozhodnout, jestli chcete, aby upozornění z programu Defender pro IoT automaticky generovala incidenty v Azure Sentinel. V části **vytvořit incidenty** vyberte **Povolit** , pokud chcete, aby pravidlo automaticky vytvořilo incidenty z generovaných výstrah.  Toto pravidlo se dá změnit nebo upravit v části **Analýza**  >  **aktivní** pravidla.

> [!NOTE]
>Po provedení změn připojení může aktualizace seznamu centra trvat 10 sekund nebo déle.

## <a name="using-log-analytics-for-alert-display"></a>Použití Log Analytics pro zobrazení výstrah

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

- Naučte se, jak pomocí funkce Azure Sentinel [získat přehled o vašich datech a potenciálních hrozbách](../sentinel/quickstart-get-visibility.md).

- Informace o [přístupu k datům zabezpečení IoT](how-to-security-data-access.md)