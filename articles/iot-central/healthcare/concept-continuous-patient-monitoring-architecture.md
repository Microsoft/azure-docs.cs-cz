---
title: Architektura monitorování nepřetržitého pacienta v Azure IoT Central | Microsoft Docs
description: Kurz – Seznamte se s architekturou řešení pro nepřetržité monitorování pacientů.
author: philmea
ms.author: philmea
ms.date: 09/14/2020
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: 3ef4790f9f7a3b3488dade9312203de41be1ff75
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2020
ms.locfileid: "92122147"
---
# <a name="continuous-patient-monitoring-architecture"></a>Architektura průběžného monitorování pacientů

Tento článek popisuje architekturu řešení sestavenou z šablony aplikace pro **monitorování nepřetržitého pacienta** :

Řešení pro průběžné monitorování pacientů se dají vytvořit pomocí poskytnuté šablony aplikace a pomocí architektury, která je níže uvedená, jako doprovodné materiály.

:::image type="content" source="media/cpm-architecture.png" alt-text="Architektura průběžného monitorování pacientů":::

## <a name="details"></a>Podrobnosti

Tato část podrobně popisuje jednotlivé části diagramu architektury:

### <a name="bluetooth-low-energy-ble-medical-devices"></a>Zdravotnické zařízení Bluetooth s nízkou spotřebou (v/v)

K mnoha lékařským wearablesům použitým v řešeních IoT pro zdravotnictví patří i zařízení v oblasti Tato zařízení nemůžou komunikovat přímo do cloudu a potřebují používat bránu k výměně dat s cloudovým řešením. Tato architektura používá jako bránu aplikaci pro mobilní telefony.

### <a name="mobile-phone-gateway"></a>Mobilní telefon – brána

Primární funkcí aplikace Mobilní telefon je shromažďování dat z lékařských zařízení a jejich sdělování IoT Central. Aplikace také nabízí pacienty prostřednictvím nastavení zařízení a umožňuje zobrazit jejich osobní údaje o stavu. Jiná řešení by mohla používat bránu pro tablet nebo statickou bránu v nemocnicovém pokoji. Open Source ukázková mobilní aplikace je k dispozici pro Android a iOS, která slouží jako výchozí bod pro vývoj aplikací. Další informace najdete v tématu [IoT Central mobilní aplikace pro monitorování nepřetržitého pacienta](/samples/iot-for-all/iotc-cpm-sample/iotc-cpm-sample/).

### <a name="export-to-azure-api-for-fhirreg"></a>Export do Azure API pro FHIR&reg;

Azure IoT Central je kompatibilní s HIPAA a HITRUST &reg; Certifikovaný. Data o stavu pacientům můžete také posílat ostatním službám pomocí [rozhraní Azure API pro FHIR](../../healthcare-apis/overview.md). Rozhraní Azure API pro FHIR je standardizované rozhraní API pro klinická data o zdravotním stavu. [Azure IoT Connector pro FHIR](../../healthcare-apis/iot-fhir-portal-quickstart.md) umožňuje používat Azure API pro FHIR jako průběžné exportování dat z IoT Central.

### <a name="machine-learning"></a>Strojové učení

Pomocí modelů strojového učení s FHIR daty můžete vygenerovat přehledy a rozhodování o podpoře pro váš tým pro péči. Další informace najdete v dokumentaci ke [službě Azure Machine Learning](../../machine-learning/index.yml).

### <a name="provider-dashboard"></a>Řídicí panel poskytovatele

Pomocí rozhraní Azure API pro FHIR můžete vytvořit řídicí panel pacient Insights nebo ho integrovat přímo do elektronického lékařského záznamu používaného v rámci týmů. Týmy pro péči o pomoc můžou pomocí řídicího panelu pomáhat pacientům a identifikovat včasné varovné známky zhoršení. Další informace najdete v kurzu [sestavení řídicího panelu poskytovatele Power BI](howto-health-data-triage.md) .

## <a name="next-steps"></a>Další kroky

Navržený další krok se [naučíte, jak nasadit šablonu aplikace pro monitorování nepřetržitého pacienta](tutorial-continuous-patient-monitoring.md).