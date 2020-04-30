---
title: Architektura monitorování nepřetržitého pacienta v Azure IoT Central | Microsoft Docs
description: Přečtěte si o architektuře řešení monitorování nepřetržitého pacienta.
author: philmea
ms.author: philmea
ms.date: 10/24/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: 92eb4157abb55b7056952d1fb064c7c7d7500335
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "77021692"
---
# <a name="continuous-patient-monitoring-architecture"></a>Architektura průběžného monitorování pacientů



Řešení pro nepřetržité monitorování pacientů se dají sestavit pomocí poskytnuté šablony aplikace a pomocí architektury, která je níže uvedená, jako doprovodné materiály.

>[!div class="mx-imgBorder"] 
>![Architektura CPM](media/cpm-architecture.png)

1. Zdravotnické zařízení komunikující pomocí technologie Bluetooth s nízkou spotřebou (tabulku)
1. Mobilní telefon, který přijímá data v sebranách a odesílá je IoT Central
1. Kontinuální export dat o zdravotním stavu pacientů do Azure API pro FHIR&reg;
1. Strojové učení založené na interoperabilních datech
1. Péče o týmový řídicí panel založený na datech FHIR

## <a name="details"></a>Podrobnosti
Tato část podrobně popisuje jednotlivé části diagramu architektury.

### <a name="ble-medical-devices"></a>Zdravotnické zařízení v/v
Řada lékařských wearables používaných v prostoru IoT pro zdravotnictví je zařízení s nízkou spotřebou Bluetooth. Nemůžou mluvit přímo do cloudu a bude je muset předat přes bránu. Tato architektura navrhuje jako tuto bránu použití aplikace Mobilní telefon.

### <a name="mobile-phone-gateway"></a>Mobilní telefon – brána
Primární funkcí aplikace Mobilní telefon je ingestování dat z lékařských zařízení a jejich sdělování do Azure IoT Central. Kromě toho může aplikace pomáhat s pacienty prostřednictvím nastavení zařízení a toku zřizování a pomáhat jim se zobrazením jejich osobních údajů o stavu. Jiná řešení můžou používat bránu tabletu nebo statickou bránu, pokud je v nemocnici místnosti k dosažení stejného toku komunikace.

### <a name="export-to-azure-api-for-fhirreg"></a>Export do Azure API pro FHIR&reg;
Azure IoT Central je kompatibilní s HIPAA a HITRUST&reg; certifikovaný, ale možná budete chtít odeslat data týkající se zdravotního stavu do rozhraní Azure API pro FHIR. [Azure API pro FHIR](../../healthcare-apis/overview.md) je plně spravované, kompatibilní rozhraní API na bázi standardů pro klinická data o zdravotním stavu, které umožňuje vytvářet nové systémy zapojení s daty o stavu. Umožňuje rychlé výměny dat prostřednictvím rozhraní API FHIR, které zajišťuje spravovaná nabídka PaaS (platforma jako služba) v cloudu. Pomocí funkce průběžného exportu dat IoT Central můžete odesílat data do rozhraní Azure API pro FHIR.

### <a name="machine-learning"></a>Strojové učení
Po agregaci vašich dat a jejich překladu do formátu FHIR můžete vytvářet modely strojového učení, které mohou rozšířit přehledy a umožnit pro svůj tým pro péči inteligentnější rozhodování. Existují různé druhy služeb, které se dají použít k sestavování, výuce a nasazení modelů strojového učení. Další informace o tom, jak používat nabídky strojového učení Azure, najdete v naší [dokumentaci ke službě Machine Learning](../../machine-learning/index.yml).

### <a name="provider-dashboard"></a>Řídicí panel poskytovatele
Data umístěná v rozhraní API Azure pro FHIR se dají použít k vytvoření řídicího panelu pro pacienty nebo můžete přímo integrovat do EMRu, která zajistí, aby týmy vizualizují stav pacienta. Týmy pro péči můžou pomocí tohoto řídicího panelu postarat o pacienty, které potřebují pomoc, a odhalit včasnou varovná znamení zhoršení. Pokud chcete zjistit, jak vytvořit řídicí panel poskytovatele Power BI v reálném čase, postupujte podle pokynů v [Průvodci](howto-health-data-triage.md).

## <a name="next-steps"></a>Další kroky
* [Naučte se nasadit šablonu aplikace pro monitorování nepřetržitého pacienta.](tutorial-continuous-patient-monitoring.md)