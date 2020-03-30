---
title: Technologie a řešení Azure Internet věcí (IoT)
description: Popisuje kolekci technologií a služeb, které můžete použít k vytvoření řešení Azure IoT.
author: dominicbetts
ms.service: iot-fundamentals
services: iot-fundamentals
ms.topic: overview
ms.date: 01/15/2020
ms.author: dobett
ms.openlocfilehash: 0b04b5170c13f6f6c3fd74976461f03e4367060a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77046067"
---
# <a name="azure-technologies-and-services-for-creating-iot-solutions"></a>Technologie a služby Azure pro vytváření řešení IoT

Technologie a služby Azure IoT vám poskytují možnosti pro vytvoření široké škály řešení IoT, která umožňují digitální transformaci pro vaši organizaci. Můžete například provést následující věci:

- Pomocí [Azure IoT Central](https://apps.azureiotcentral.com), spravované aplikační platformy IoT, můžete vytvořit a nasadit zabezpečené řešení IoT na podnikové úrovni. IoT Central obsahuje kolekci šablon aplikací specifických pro dané odvětví, jako je maloobchod a zdravotní péče, aby urychlil proces vývoje řešení.
- Rozšiřte základnu open source kódu pro [akcelerátor řešení](https://www.azureiotsolutions.com) Azure IoT a implementujte běžný scénář IoT, jako je vzdálené monitorování nebo prediktivní údržba.
- Pomocí služeb platformy Azure IoT, jako je [Azure IoT Hub](../iot-hub/about-iot-hub.md) a [sady SDK zařízení Azure IoT,](../iot-hub/iot-hub-devguide-sdks.md) můžete vytvořit vlastní řešení IoT od začátku.

![Technologie, služby a řešení Azure IoT](./media/iot-services-and-technologies/iot-technologies-services.png)

## <a name="azure-iot-central"></a>Azure IoT Central

[Aplikační platforma IoT Central](https://apps.azureiotcentral.com) snižuje zátěž a náklady spojené s vývojem, správou a údržbou řešení IoT na podnikové úrovni. Přizpůsobitelné webové uživatelské rozhraní ioT Central umožňuje monitorovat podmínky zařízení, vytvářet pravidla a spravovat miliony zařízení a jejich dat během celého životního cyklu. Povrch rozhraní API v rámci IoT Central vám poskytuje programový přístup ke konfiguraci a interakci s řešením IoT.

Azure IoT Central je plně spravovaná aplikační platforma, kterou můžete použít k vytvoření vlastních řešení IoT. IoT Central používá šablony aplikací k vytváření řešení. Existují šablony pro obecná řešení a pro konkrétní odvětví, jako je energetika, zdravotnictví, státní správa a maloobchod. Šablony aplikací IoT Central umožňují nasadit aplikaci IoT Central během několika minut, kterou pak můžete přizpůsobit pomocí motivů, řídicích panelů a zobrazení.

Vyberte si zařízení z [katalogu zařízení Azure Certified for IoT,](https://catalog.azureiotsolutions.com) abyste se rychle připojili k vašemu řešení. Pomocí webového uživatelského uživatelského uživatelského nastavení IoT Central můžete monitorovat a spravovat svá zařízení, aby byla v pořádku a připojená. Pomocí konektorů a rozhraní API integrujete aplikaci IoT Central s jinými obchodními aplikacemi.

Jako plně spravovaná aplikační platforma má IoT Central jednoduchý a předvídatelný cenový model.

## <a name="azure-iot-solution-accelerators"></a>Akcelerátory řešení Azure IoT

[Akcelerátory řešení Azure IoT](https://www.azureiotsolutions.com) jsou kolekcí přizpůsobitelných řešení na podnikové úrovni. Tato řešení můžete nasadit tak, jak jsou, nebo vyvinout vlastní řešení IoT pomocí open source zdrojového kódu Jazyka Java nebo .NET.

Akcelerátory řešení Azure IoT poskytují vysokou úroveň kontroly nad vaším řešením IoT. Akcelerátory řešení zahrnují předem připravená řešení pro běžné scénáře IoT, které můžete nasadit do předplatného Azure během několika minut. Scénáře zahrnují:

  - Vzdálené monitorování
  - Propojená továrna
  - Prediktivní údržba
  - Simulace zařízení

Základ open source kódu pro všechny akcelerátory řešení je k dispozici na GitHubu. Stáhněte si kód a přizpůsobte akcelerátor řešení tak, aby vyhovoval vašim specifickým požadavkům na IoT.

Akcelerátory řešení používají služby Azure, jako je Azure IoT Hub a Azure Storage, které musíte spravovat ve svém předplatném Azure.

## <a name="custom-solutions"></a>Vlastní řešení

Chcete-li vytvořit řešení IoT od začátku nebo rozšířit řešení vytvořené pomocí IoT Central nebo akcelerátoru řešení, použijte jednu nebo více následujících technologií a služeb Azure IoT:

### <a name="devices"></a>Zařízení

Vyvíjejte svá zařízení IoT pomocí jedné ze [sad Azure IoT Starter Kit](https://catalog.azureiotsolutions.com/kits) nebo si vyberte zařízení, které chcete použít z [katalogu zařízení Azure Certified for IoT](https://catalog.azureiotsolutions.com). Implementujte vložený kód pomocí sad [SDK](../iot-hub/iot-hub-devguide-sdks.md)s otevřeným zdrojovým kódem . Sady SDK zařízení podporují více operačních systémů, jako je Linux, Windows a operační systémy v reálném čase. Existují sady SDK pro více programovacích jazyků, například [C](https://github.com/Azure/azure-iot-sdk-c), [Node.js](https://github.com/Azure/azure-iot-sdk-node), [Java](https://github.com/Azure/azure-iot-sdk-java), [.NET](https://github.com/Azure/azure-iot-sdk-csharp)a [Python](https://github.com/Azure/azure-iot-sdk-python).

Pomocí služby [IoT Plug and Play Preview](../iot-pnp/overview-iot-plug-and-play.md) můžete dále zjednodušit způsob vytváření vloženého kódu pro vaše zařízení. IoT Plug and Play umožňuje vývojářům řešení integrovat zařízení s jejich řešeními bez psaní vloženého kódu. Jádrem IoT Plug and Play je _schéma modelu schopností zařízení,_ které popisuje možnosti zařízení. Pomocí modelu schopností zařízení můžete vygenerovat kód vestavěného zařízení a nakonfigurovat cloudové řešení, jako je například aplikace IoT Central.

[Azure IoT Edge](../iot-edge/about-iot-edge.md) vám umožní převést část úlohy IoT z cloudových služeb Azure do vašich zařízení. IoT Edge může snížit latenci ve vašem řešení, snížit množství dat, která vaše zařízení vyměňují s cloudem, a povolit off-line scénáře. Můžete spravovat zařízení IoT Edge z IoT Central a některé akcelerátory řešení.

[Azure Sphere](https://docs.microsoft.com/azure-sphere/product-overview/what-is-azure-sphere) je zabezpečená aplikační platforma na vysoké úrovni s integrovanými komunikačními a bezpečnostními funkcemi pro zařízení připojená k internetu. Zahrnuje zabezpečenou jednotku mikrokontroléru, vlastní operační systém založený na Linuxu a cloudovou bezpečnostní službu, která poskytuje nepřetržité obnovitelné zabezpečení.

### <a name="cloud-connectivity"></a>Připojení ke cloudu

Služba [Azure IoT Hub](../iot-hub/about-iot-hub.md) umožňuje spolehlivou a zabezpečenou obousměrnou komunikaci mezi miliony zařízení IoT a cloudovým řešením. [Služba zřizování zařízení Služby Azure IoT Hub](../iot-dps/about-iot-dps.md) je pomocná služba pro službu IoT Hub. Služba poskytuje nulové dotykové ovládání zařízení do správného centra IoT hub, které vyžaduje lidský zásah. Tyto funkce umožňují zákazníkům zřídit miliony zařízení bezpečným a škálovatelným způsobem.

IoT Hub je základní součástí akcelerátorů řešení a můžete ho použít ke splnění výzev implementace IoT, jako jsou:

* Správa a připojení zařízení velkého rozsahu
* Ingestování telemetrie velkého rozsahu
* Příkazy a ovládání zařízení
* Vynucení zabezpečení zařízení

### <a name="bridging-the-gap-between-the-physical-and-digital-worlds"></a>Překlenutí propasti mezi fyzickým a digitálním světem

[Azure Digital Twins](../digital-twins/about-digital-twins.md) je služba IoT, která umožňuje modelovat fyzické prostředí. Používá graf prostorové inteligence k modelování vztahů mezi lidmi, prostory a zařízeními. Korelacedat napříč digitálním a fyzickým světem můžete vytvářet kontextově uvědomělá řešení.

Iot Central používá digitální dvojčata k synchronizaci zařízení a dat v reálném světě s digitálními modely, které uživatelům umožňují sledovat a spravovat tato připojená zařízení.

### <a name="data-and-analytics"></a>Data a analýza

Zařízení IoT obvykle generují velké množství dat časových řad, jako jsou například údaje o teplotě ze senzorů. [Azure Time Series Insights](../time-series-insights/time-series-insights-overview.md) se můžou připojit k centru IoT, číst telemetrický proud z vašich zařízení, ukládat tato data a umožnit vám je dotazovat a vizualizovat.

[Azure Maps](/azure/azure-maps) je kolekce geoprostorových služeb, které využívají čerstvá data mapování k poskytování přesného geografického kontextu webovým a mobilním aplikacím. K vytváření aplikací můžete použít rozhraní REST API, webový ovládací prvek JavaScript nebo sadku SDK pro Android.

## <a name="next-steps"></a>Další kroky

Pro praktický zážitek vyzkoušejte jeden z rychlých startů:

- [Vytvoření aplikace Azure IoT Central](../iot-central/core/quick-deploy-iot-central.md)
- [Odeslání telemetrie ze zařízení do služby IoT hub](../iot-hub/quickstart-send-telemetry-cli.md)
- [Vyzkoušení cloudového řešení pro vzdálené monitorování](../iot-accelerators/quickstart-remote-monitoring-deploy.md)
