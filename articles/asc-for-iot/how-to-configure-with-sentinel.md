---
title: Konfigurace sentinelu (náhled)
description: Vysvětluje, jak nakonfigurovat Azure Sentinel tak, aby přijímala data z vašeho řešení Azure Security Center for IoT.
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
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311305"
---
# <a name="connect-your-data-from-azure-security-center-for-iot-to-azure-sentinel-preview"></a>Připojení dat z Azure Security Center pro IoT k Azure Sentinelu (preview)

> [!IMPORTANT]
> Datový konektor Azure Security Center for IoT v Azure Sentinelu je momentálně ve verzi Public Preview.
> Tato funkce je k dispozici bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

V této příručce se dozvíte, jak připojit data Azure Security Center pro IoT k Azure Sentinelu.

> [!div class="checklist"]
> * Požadavky
> * Nastavení připojení
> * Zobrazení výstrahy Log Analytics

Připojte výstrahy z Azure Security Center pro IoT a streamujte je přímo do Azure Sentinelu.

## <a name="prerequisites"></a>Požadavky

- Musíte mít oprávnění **ke čtení** a **zápisu** v pracovním prostoru.
- **Azure Security Center pro IoT** musí být **povolené** na příslušných službách Služby IoT Hub.
- Musíte mít oprávnění **ke čtení** i **zápisu** v centru **Azure IoT Hub,** které chcete připojit.
- Musíte mít také oprávnění **ke čtení** a **zápisu** ve **skupině prostředků Služby Azure IoT Hub**.

> [!NOTE]
> Abyste mohli odesílat obecné výstrahy prostředků Azure, musíte mít ve vašem předplatném spuštěné licencování úrovně Azure Security Center Standard. S bezplatnými licencemi úrovně, které jsou vyžadovány pro Azure Security Center pro IoT, budou do Azure Sentinelu předávány jenom výstrahy související s Azure Security Center pro IoT.

## <a name="connect-to-azure-security-center-for-iot"></a>Připojení k Centru zabezpečení Azure pro IoT

1. V Azure Sentinelu vyberte **datové konektory** a klikněte na dlaždici **Azure Security Center for IoT.**
1. V dolní části pravého podokna klikněte na **Otevřít stránku konektoru**.
1. Vedle každého předplatného služby IoT Hub, jehož výstrahy a upozornění na zařízení, které chcete streamovat do Azure Sentinelu, klikněte na **Připojit**.
    - Pokud Azure Security Center pro IoT není povolena v tomto centru, zobrazí se upozornění Povolit. Klepnutím na odkaz **Povolit** spusťte a povolte službu.
1. Můžete se rozhodnout, jestli chcete, aby výstrahy z Azure Security Center pro IoT automaticky generovat incidenty v Azure Sentinelu. V části **Vytvořit incidenty**vyberte **Povolit,** chcete-li pravidlo povolit automatické vytváření incidentů z generovaných výstrah.  Toto pravidlo lze změnit nebo upravit v části Pravidla **služby Analytics** > **Active.**

> [!NOTE]
>Aktualizace seznamu rozbočovačů po provedení změn připojení může trvat 10 sekund nebo déle.

## <a name="log-analytics-alert-display"></a>Zobrazení výstrahy Log Analytics

Použití příslušného schématu v Log Analytics k zobrazení výstrah Azure Security Center for IoT:

1. **Scházejte protokoly** > **SecurityInsights** > **SecurityAlert**nebo vyhledejte **službu SecurityAlert**.
1. Filtrem zobrazíte jenom Azure Security Center for IoT generované výstrahy pomocí následujícího filtru kql:

```kusto
SecurityAlert | where ProductName == "Azure Security Center for IoT"
```

### <a name="service-notes"></a>Servisní poznámky

Po připojení služby IoT Hub jsou data centra dostupná v Azure Sentinelu přibližně o 15 minut později.

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste se dozvěděli, jak připojit Azure Security Center pro IoT k Azure Sentinelu. Další informace o zjišťování hrozeb a přístupu k bezpečnostním datům naleznete v následujících článcích:

- Přečtěte si, jak pomocí Azure Sentinelu [získat přehled o vašich datech a potenciálních hrozbách](https://docs.microsoft.com/azure/sentinel/quickstart-get-visibility).

- Přečtěte si, jak získat přístup k [datům zabezpečení IoT](how-to-security-data-access.md)
