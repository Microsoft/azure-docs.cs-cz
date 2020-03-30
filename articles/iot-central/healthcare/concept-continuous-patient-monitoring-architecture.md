---
title: Architektura nepřetržitého monitorování pacientů v Azure IoT Central | Dokumenty společnosti Microsoft
description: Seznamte se s architekturou řešení průběžného monitorování pacientů.
author: philmea
ms.author: philmea
ms.date: 10/24/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: 92eb4157abb55b7056952d1fb064c7c7d7500335
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77021692"
---
# <a name="continuous-patient-monitoring-architecture"></a>Architektura průběžného monitorování pacientů



Řešení průběžného monitorování pacientů lze vytvořit pomocí poskytnuté šablony aplikace a pomocí architektury, která je popsána níže jako vodítko.

>[!div class="mx-imgBorder"] 
>![Architektura CPM](media/cpm-architecture.png)

1. Zdravotnické prostředky komunikující pomocí technologie Bluetooth Low Energy (BLE)
1. Brána mobilního telefonu přijímá ble data a odesílá se do IoT Central
1. Nepřetržitý export dat o stavu pacientů do rozhraní Azure API pro FHIR&reg;
1. Strojové učení založené na interoperabilních datech
1. Řídicí panel týmu péče postavený na datech FHIR

## <a name="details"></a>Podrobnosti
Tato část popisuje každou část diagramu architektury podrobněji.

### <a name="ble-medical-devices"></a>BLE zdravotnické prostředky
Mnoho lékařských nositelných zařízení používaných ve zdravotnictví IoT prostoru jsou Bluetooth low energy zařízení. Nejsou schopni mluvit přímo do cloudu a budou muset projít bránou. Tato architektura navrhuje použití aplikace pro mobilní telefon jako této brány.

### <a name="mobile-phone-gateway"></a>Brána mobilního telefonu
Primární funkcí aplikace pro mobilní telefon je ingestovat data BLE ze zdravotnických prostředků a sdělit je Azure IoT Central. Kromě toho aplikace může pomoci vést pacienty prostřednictvím nastavení zařízení a zřizování toku a pomoci jim zobrazit zobrazení jejich osobních zdravotních údajů. Jiná řešení mohou použít tabletbránu nebo statickou bránu, pokud uvnitř nemocniční místnosti k dosažení stejného komunikačního toku.

### <a name="export-to-azure-api-for-fhirreg"></a>Export do rozhraní Azure API pro FHIR&reg;
Azure IoT Central je kompatibilní s&reg; HIPAA a s certifikací HITRUST, ale můžete taky chtít odesílat data související se stavem pacientů do rozhraní Azure API pro FHIR. [Azure API pro FHIR](../../healthcare-apis/overview.md) je plně spravované rozhraní API kompatibilní se standardy pro klinická zdravotní data, které vám umožní vytvářet nové systémy interakce s vašimi daty o stavu. Umožňuje rychlou výměnu dat prostřednictvím fhir api, podporovaný spravované platformy jako služba (PaaS) nabízí v cloudu. Pomocí funkce průběžného exportu dat ioT central můžete odesílat data do rozhraní Azure API pro FHIR.

### <a name="machine-learning"></a>Strojové učení
Po agregaci dat a jejich překladu do formátu FHIR můžete vytvářet modely strojového učení, které mohou obohatit poznatky a umožnit inteligentnější rozhodování pro váš tým péče. Existují různé druhy služeb, které lze použít k vytváření, trénování a nasazování modelů strojového učení. Další informace o tom, jak používat nabídky strojového učení Azure, najdete v naší [dokumentaci k strojovému učení](../../machine-learning/index.yml).

### <a name="provider-dashboard"></a>Řídicí panel zprostředkovatele
Data umístěná v rozhraní Azure API pro FHIR lze použít k vytvoření řídicího panelu přehledů pacientů nebo mohou být přímo integrována do EMR, aby pomohla týmům péče vizualizovat stav pacienta. Pečovatelské týmy mohou tuto palubní desku použít k péči o pacienty, kteří potřebují pomoc, a odhalit včasné varovné příznaky zhoršení. Pokud se chcete dozvědět, jak vytvořit řídicí panel poskytovatele Power BI v reálném čase, postupujte podle [našeho návodu](howto-health-data-triage.md).

## <a name="next-steps"></a>Další kroky
* [Zjistěte, jak nasadit šablonu aplikace průběžného monitorování pacientů](tutorial-continuous-patient-monitoring.md)