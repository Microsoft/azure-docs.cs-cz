---
title: Architektura monitorování nepřetržitého pacienta v Azure IoT Central | Microsoft Docs
description: Přečtěte si o architektuře řešení monitorování nepřetržitého pacienta.
author: philmea
ms.author: philmea
ms.date: 09/14/2020
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: bd0d2f3368f6a2c39b3a9e95c577d85dfe0d87d7
ms.sourcegitcommit: 1fe5127fb5c3f43761f479078251242ae5688386
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2020
ms.locfileid: "90068063"
---
# <a name="continuous-patient-monitoring-architecture"></a>Architektura průběžného monitorování pacientů

Tento článek popisuje architekturu řešení sestavenou z šablony aplikace pro **monitorování nepřetržitého pacienta** :

:::image type="content" source="media/cpm-architecture.png" alt-text="Architektura průběžného monitorování pacientů":::

## <a name="details"></a>Podrobnosti

Tato část podrobně popisuje jednotlivé části diagramu architektury:

### <a name="bluetooth-low-energy-ble-medical-devices"></a>Zdravotnické zařízení Bluetooth s nízkou spotřebou (v/v)

K mnoha lékařským wearablesům použitým v řešeních IoT pro zdravotnictví patří i zařízení v oblasti Tato zařízení nemůžou komunikovat přímo do cloudu a potřebují používat bránu k výměně dat s cloudovým řešením. Tato architektura používá jako bránu aplikaci pro mobilní telefony.

### <a name="mobile-phone-gateway"></a>Mobilní telefon – brána

Primární funkcí aplikace Mobilní telefon je shromažďování dat z lékařských zařízení a jejich sdělování IoT Central. Aplikace také nabízí pacienty prostřednictvím nastavení zařízení a umožňuje zobrazit jejich osobní údaje o stavu. Jiná řešení by mohla používat bránu pro tablet nebo statickou bránu v nemocnicovém pokoji. Open Source ukázková mobilní aplikace je k dispozici pro Android a iOS, která slouží jako výchozí bod pro vývoj aplikací. Další informace najdete v tématu [IoT Central mobilní aplikace pro monitorování nepřetržitého pacienta](https://docs.microsoft.com/samples/iot-for-all/iotc-cpm-sample/iotc-cpm-sample/).

### <a name="export-to-azure-api-for-fhirreg"></a>Export do Azure API pro FHIR&reg;

Azure IoT Central je kompatibilní s HIPAA a HITRUST &reg; Certifikovaný. Data o stavu pacientům můžete také posílat ostatním službám pomocí [rozhraní Azure API pro FHIR](../../healthcare-apis/overview.md). Rozhraní Azure API pro FHIR je standardizované rozhraní API pro klinická data o zdravotním stavu. [Azure IoT Connector pro FHIR](https://docs.microsoft.com/azure/healthcare-apis/iot-fhir-portal-quickstart) umožňuje používat Azure API pro FHIR jako průběžné exportování dat z IoT Central.

### <a name="machine-learning"></a>Strojové učení

Pomocí modelů strojového učení s FHIR daty můžete vygenerovat přehledy a rozhodování o podpoře pro váš tým pro péči. Další informace najdete v dokumentaci ke [službě Azure Machine Learning](../../machine-learning/index.yml).

### <a name="provider-dashboard"></a>Řídicí panel poskytovatele

Pomocí rozhraní Azure API pro FHIR můžete vytvořit řídicí panel pacient Insights nebo ho integrovat přímo do elektronického lékařského záznamu používaného v rámci týmů. Týmy pro péči o pomoc můžou pomocí řídicího panelu pomáhat pacientům a identifikovat včasné varovné známky zhoršení. Další informace najdete v kurzu [sestavení řídicího panelu poskytovatele Power BI](howto-health-data-triage.md) .

## <a name="next-steps"></a>Další kroky

Navržený další krok se [naučíte, jak nasadit šablonu aplikace pro monitorování nepřetržitého pacienta](tutorial-continuous-patient-monitoring.md).
