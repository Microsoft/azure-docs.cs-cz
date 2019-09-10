---
title: Glosář pojmů – IoT technologie Plug and Play Preview | Microsoft Docs
description: Koncepty – Glosář běžných termínů týkajících se IoT technologie Plug and Play ve verzi Preview.
author: ChrisGMsft
ms.author: chrisgre
ms.date: 08/29/2019
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: 7b37912c58312644059f3990b4776514e081a4bb
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2019
ms.locfileid: "70858844"
---
# <a name="glossary-of-terms-for-iot-plug-and-play-preview"></a>Glosář pojmů pro IoT technologie Plug and Play Preview

Definice běžných termínů, jak se používají v článcích technologie Plug and Play IoT.

## <a name="azure-certified-for-iot-portal"></a>Portál Azure Certified for IoT

Web [Azure Certified for IoT Portal](https://aka.ms/ACFI) můžete použít k těmto akcím:

- Dokončete [proces certifikace](#device-certification) pro vaše [zařízení IoT technologie Plug and Play](#iot-plug-and-play-device).
- Najděte [modely schopností zařízení](#device-capability-model).
- Publikujte model schopností zařízení do [úložiště veřejného modelu](#public-model-repository).

## <a name="azure-cli"></a>Azure CLI

Azure CLI je nástroj příkazového řádku pro různé platformy, který slouží ke správě prostředků Azure. Rozšíření Azure IoT pro Azure CLI je nástroj příkazového řádku pro interakci s a testování [zařízení IoT technologie Plug and Play](#iot-plug-and-play-device). Rozšíření můžete použít k těmto akcím:

- Připojte se k zařízení IoT technologie Plug and Play.
- Podívejte se na [telemetrii](#telemetry) , kterou zařízení odesílá.
- Práce s [vlastnostmi](#properties)zařízení
- Zavolejte [příkazy](#commands)zařízení.
- Správa [úložišť modelů](#model-repository), [rozhraní](#interface)a [modelů schopností zařízení](#device-capability-model).

## <a name="azure-iot-central"></a>Azure IoT Central

Azure IoT Central je plně spravované řešení typu software jako služba, které umožňuje snadno připojit, monitorovat a spravovat vaše [zařízení technologie Plug and Play IoT](#iot-plug-and-play-device). [Modely schopností zařízení](#device-capability-model) můžete použít k automatické konfiguraci IoT Central aplikace pro monitorování a správu vašich zařízení.

## <a name="azure-iot-certification-service"></a>Služba certifikace Azure IoT

Certifikační služba Azure IoT spouští sadu ověřovacích testů, když odešlete [zařízení IoT technologie Plug and Play](#iot-plug-and-play-device) pro certifikaci prostřednictvím [portálu Azure Certified for IoT](#azure-certified-for-iot-portal). Než budete moct přidat zařízení do [katalogu zařízení Certified for IoT](#certified-for-iot-device-catalog), musí být zařízení certifikované.

## <a name="azure-iot-tools-extension"></a>Rozšíření Azure IoT Tools

Azure IoT Tools je kolekce rozšíření v sadě [Visual Studio Code](#visual-studio-code) , která vám pomůžou pracovat s IoT Hub a vyvíjet zařízení IoT. Pro IoT technologie Plug and Play vývoj zařízení vám pomůže:

- Vytváření modelů a [rozhraní](#interface) [schopností zařízení](#device-capability-model)
- Publikovat do [úložišť modelů](#model-repository).
- Vygenerujte kostru kódu pro implementaci aplikace zařízení.

## <a name="azure-iot-explorer-tool"></a>Nástroj Azure IoT Explorer

Azure IoT Explorer je grafický nástroj, který můžete použít k interakci a testování [zařízení technologie Plug and Play IoT](#iot-plug-and-play-device). Po instalaci nástroje na místní počítač ho můžete použít k těmto akcím:

- Umožňuje zobrazit zařízení připojená ke službě [IoT Hub](#azure-iot-hub).
- Připojte se k zařízení IoT technologie Plug and Play.
- Podívejte se na [telemetrii](#telemetry) , kterou zařízení odesílá.
- Práce s [vlastnostmi](#properties)zařízení
- Zavolejte [příkazy](#commands)zařízení.

## <a name="azure-iot-hub"></a>Azure IoT Hub

IoT Hub je spravovaná služba hostovaná v cloudu, která funguje jako centrum zpráv pro obousměrnou komunikaci mezi aplikací IoT a zařízeními, která spravuje. [Zařízení iot technologie Plug and Play](#iot-plug-and-play-device) se můžou připojit ke službě IoT Hub. Řešení IoT používá ke zpřístupnění služby IoT Hub:

- Zařízení pro posílání telemetrie do cloudového řešení.
- Cloudové řešení pro správu připojených zařízení.

## <a name="azure-iot-device-sdk"></a>Sada SDK pro zařízení Azure IoT

K dispozici jsou sady SDK pro zařízení, které můžete použít k vytvoření klientských aplikací IoT technologie Plug and Play zařízení. Jednou z požadavků na [certifikaci zařízení](#device-certification) je, že kód klienta zařízení používá jednu ze sad SDK pro zařízení Azure IoT.

## <a name="certified-for-iot-device-catalog"></a>Katalog zařízení Certified for IoT

V [katalogu zařízení s certifikací IoT](https://catalog.azureiotsolutions.com/) jsou uvedena [zařízení IoT technologie Plug and Play](#iot-plug-and-play-device) , která prošla testy [certifikace zařízení](#device-certification) . [Modely schopností zařízení](#device-capability-model) pro zařízení IoT technologie Plug and Play v katalogu a publikované v úložišti veřejného modelu.

## <a name="commands"></a>Příkazy

Příkazy definované v [rozhraní](#interface) představuje metody, které lze provést na základě [digitálního vlákna](#digital-twin). Například příkaz pro restartování zařízení.

## <a name="common-interface"></a>Společné rozhraní

U všech [zařízení IoT technologie Plug and Play](#iot-plug-and-play-device) se očekává implementace některých běžných [rozhraní](#interface). Rozhraní informací o zařízení například definuje informace o hardwaru a operačním systému na zařízení. [Certifikace zařízení](#device-certification) vyžaduje, aby vaše zařízení implementovalo několik společných rozhraní. Z úložiště veřejného modelu můžete načíst společné definice rozhraní.

## <a name="company-model-repository"></a>Úložiště podnikového modelu

Organizace může používat úložiště podnikového [modelu](#model-repository) jako soukromé úložiště pro modely a [rozhraní](#interface) [schopností zařízení](#device-capability-model) .

## <a name="connection-string"></a>Připojovací řetězec

Připojovací řetězec zapouzdřuje informace potřebné pro připojení ke koncovému bodu. Připojovací řetězec obvykle obsahuje adresu koncového bodu a informace o zabezpečení, ale formáty připojovacích řetězců se liší v rámci služeb. Existují dva typy připojovacího řetězce přidruženého ke službě IoT Hub:

- Připojovací řetězce zařízení umožňují [zařízením iot technologie Plug and Play](#iot-plug-and-play-device) připojit se k koncovým bodům připojeným k zařízení ve službě IoT Hub. Klientský kód v zařízení používá připojovací řetězec k navázání zabezpečeného připojení ke službě IoT Hub.
- Připojovací řetězce IoT Hub umožňují zabezpečené připojení k koncovým bodům pro služby v centru IoT. Tato řešení a nástroje spravují centrum IoT a zařízení, která jsou k němu připojená.

## <a name="device-capability-model"></a>Model schopností zařízení

Model schopností zařízení popisuje [zařízení IoT technologie Plug and Play](#iot-plug-and-play-device) a definuje sadu [rozhraní](#interface) implementovaných zařízením. Model schopností zařízení obvykle odpovídá fyzickému zařízení, produktu nebo SKU. K definování modelu schopností zařízení slouží [Jazyk digitálního vlákna s definicí](#digital-twin-definition-language) .

## <a name="device-certification"></a>Certifikace zařízení

Certifikace zařízení je proces certifikace [technologie Plug and Play zařízení IoT](#iot-plug-and-play-device) , aby bylo možné ho přidat do [katalogu zařízení s certifikací pro IoT](#certified-for-iot-device-catalog) a jeho [modelem schopností zařízení](#device-capability-model) a [rozhraní](#interface) přidaných do [veřejného modelu. úložiště](#public-model-repository).

## <a name="device-developer"></a>Vývojář zařízení

Vývojář zařízení používá model, [rozhraní](#interface)a sadu [funkcí](#device-capability-model)pro zařízení [Azure IoT](#azure-iot-device-sdk) k implementaci kódu ke spuštění na [zařízení IoT technologie Plug and Play](#iot-plug-and-play-device).

## <a name="device-modeling"></a>Modelování zařízení

[Vývojář zařízení](#device-developer) používá k modelování schopností [technologie Plug and Play zařízení IoT](#iot-plug-and-play-device) [Jazyk digitálního vlákna s definicemi](#digital-twin-definition-language) . Model se dá sdílet pomocí úložiště modelu. Vývojář zařízení může z modelu vygenerovat kostru kódu zařízení. [Vývojář řešení](#solution-developer) může nakonfigurovat řešení IoT z modelu.

## <a name="device-provisioning-service"></a>Device Provisioning Service

[Azure IoT Central](#azure-iot-central) používá službu Device Provisioning ke správě veškeré registrace a připojení zařízení. Další informace najdete v tématu [připojení zařízení v Azure IoT Central](../iot-central/concepts-connectivity-pnp.md). Službu Device Provisioning můžete použít také ke správě registrace zařízení a připojení k řešení IoT založenému na IoT Hub. Další informace najdete v tématu [zřizování zařízení pomocí Azure IoT Hub Device Provisioning Service](../iot-dps/about-iot-dps.md).

## <a name="device-registration"></a>Registrace zařízení

Předtím, než se [zařízení iot technologie Plug and Play](#iot-plug-and-play-device) může připojit k řešení IoT, musí být zaregistrované v řešení. [Azure IoT Central](#azure-iot-central) používá ke správě registrace zařízení [službu Device Provisioning](#device-provisioning-service) . Ve vlastním řešení IoT můžete zaregistrovat zařízení ve službě IoT Hub v Azure Portal nebo programově.

## <a name="device-first"></a>Zařízení – nejdřív

[Azure IoT Central](#azure-iot-central) podporuje scénář registrace a připojení prvního zařízení. V tomto scénáři se [zařízení IoT technologie Plug and Play](#iot-plug-and-play-device) může připojit k aplikaci IoT Central bez registrace předem. K registraci dochází automaticky, když se zařízení poprvé připojí k aplikaci.

## <a name="digital-twin"></a>Digitální vlákna

Digitální vlákna je model [zařízení IoT technologie Plug and Play](#iot-plug-and-play-device). Digitální vlákna je modelováno pomocí [digitálního vlákna definice jazyka](#digital-twin-definition-language). Sady [SDK pro zařízení Azure IoT](#azure-iot-device-sdk) můžete použít k interakci s digitálními interakcemi v době běhu. Například můžete nastavit hodnotu vlastnosti v poli digitálního vlákna na zařízení a sada SDK oznámí tuto změnu řešení IoT v cloudu.

## <a name="digital-twin-change-events"></a>Události změny digitálního vlákna

Když je [zařízení iot technologie Plug and Play](#iot-plug-and-play-device) připojené ke službě [IoT Hub](#azure-iot-hub), může centrum využít schopnost směrování k posílání oznámení o digitálních prostředcích, které se mění. Například pokaždé, když se v zařízení změní hodnota [vlastnosti](#properties) , IoT Hub může odeslat oznámení na koncový bod, jako je například fronta Service Bus.

## <a name="digital-twin-definition-language"></a>Jazyk digitálního zdvojeného definování

Jazyk popisující modely a rozhraní pro [zařízení IoT technologie Plug and Play](#iot-plug-and-play-device). K popisu možností [digitálního vlákna](#digital-twin) a k tomu, aby mohla platforma IoT a řešení IoT využívat sémantiku této entity, použijte [Jazyk digitálního zdvojeného definování](https://aka.ms/DTDL) .

## <a name="digital-twin-route"></a>Digitální dvojitá trasa

Trasa nastavená ve [IoT Hub](#azure-iot-hub) pro doručování [událostí digitální události změny](#digital-twin-change-events) do a koncového bodu, jako je Service Bus fronta.

## <a name="interface"></a>Rozhraní

Rozhraní popisuje související možnosti, které jsou implementovány [zařízením IoT technologie Plug and Play](#iot-plug-and-play-device) nebo z [digitálního vlákna](#digital-twin). Můžete opakovaně používat rozhraní napříč různými [modely schopností zařízení](#device-capability-model).

## <a name="iot-hub-query-language"></a>Dotazovací jazyk IoT Hub

Dotazovací jazyk IoT Hub slouží k více účelům. Jazyk můžete například použít k vyhledání [zařízení zaregistrovaných](#device-registration) ve službě IoT Hub nebo k upřesnění chování [digitálního zdvojeného směrování](#digital-twin-route) .

## <a name="iot-plug-and-play-device"></a>Zařízení IoT technologie Plug and Play

Zařízení IoT technologie Plug and Play je typicky malé škálované samostatné výpočetní zařízení, které shromažďuje data nebo řídí jiná zařízení a spouští software nebo firmware, který implementuje [model schopností zařízení](#device-capability-model).  Například zařízení IoT technologie Plug and Play může být zařízením pro monitorování prostředí nebo kontroler pro zavlažovací systémy s inteligentním zemědělstvím. Řešení IoT hostované v cloudu můžete napsat pro příkazy, řízení a příjem dat ze zařízení IoT technologie Plug and Play. Seznam dostupných zařízení IoT technologie Plug and Play v [katalogu zařízení Azure Certified for IoT](#certified-for-iot-device-catalog) . Každé zařízení IoT technologie Plug and Play v katalogu bylo ověřeno a má [model schopností zařízení](#device-capability-model).

## <a name="microsoft-partner-center"></a>Partnerské centrum Microsoftu

[Partnerské centrum Microsoftu](https://docs.microsoft.com/partner-center/) je místo, kde vaše organizace spravuje svou ucelenou relaci s Microsoftem. Abyste mohli certifikovat [technologie Plug and Play zařízení IoT](#iot-plug-and-play-device) na [portálu Azure Certified for IoT](#azure-certified-for-iot-portal), potřebujete účet partnerského centra Microsoftu.

## <a name="model-discovery"></a>Zjišťování modelů

Když se [zařízení iot technologie Plug and Play](#iot-plug-and-play-device) připojuje k řešení IoT, může řešení zjistit schopnosti zařízení, a to tak, že najde [model schopností zařízení](#device-capability-model). Zařízení může do řešení odeslat svůj model schopností, nebo řešení může najít model schopností zařízení v [úložišti modelu](#model-repository).

## <a name="model-repository"></a>Úložiště modelu

Úložiště modelu ukládá modely a [rozhraní](#interface) [funkcí zařízení](#device-capability-model) . Existuje jedno [úložiště veřejného modelu](#public-model-repository). Organizace můžou vytvářet svoje vlastní úložiště organizačních modelů.

## <a name="model-repository-rest-api"></a>REST API úložiště modelu

Rozhraní API pro správu úložišť modelů a interakci s nimi Rozhraní API můžete například použít k přidání [modelů schopností zařízení](#device-capability-model) a hledání modelů schopností.

## <a name="properties"></a>Vlastnosti

Vlastnosti jsou datová pole definovaná v [rozhraní](#interface) , které představuje nějaký stav digitálního vlákna. Vlastnosti můžete deklarovat jako jen pro čtení nebo zapisovatelné. Vlastnosti jen pro čtení, například sériové číslo, jsou nastaveny pomocí kódu spuštěného v rámci samotného [zařízení technologie Plug and Play IoT](#iot-plug-and-play-device) .  Vlastnosti s možností zápisu, jako je například prahová hodnota pro alarm, jsou obvykle nastaveny z cloudového řešení IoT.

## <a name="public-model-repository"></a>Úložiště veřejného modelu

Existuje jedno úložiště veřejného modelu, které ukládá [modely schopností zařízení](#device-capability-model) a [rozhraní](#interface) pro [certifikovaná zařízení](#device-certification). Úložiště veřejného modelu také ukládá [společné definice rozhraní](#common-interface) .

## <a name="registration-id"></a>ID registrace

ID registrace jednoznačně identifikuje zařízení ve [službě Device Provisioning](#device-provisioning-service). Toto ID není stejné jako ID zařízení, které je jedinečným identifikátorem zařízení ve službě [IoT Hub](#azure-iot-hub).

## <a name="scope-id"></a>ID oboru

Obor ID oboru jednoznačně identifikuje instanci [služby Device Provisioning](#device-provisioning-service) .

## <a name="shared-access-signature"></a>Sdílený přístupový podpis

Signatury sdíleného přístupu jsou mechanismy ověřování založené na zabezpečených hodnotách hash nebo identifikátorech URI SHA-256. Ověřování pomocí sdíleného přístupového podpisu má dvě komponenty: zásady sdíleného přístupu a sdílený přístupový podpis (často se označuje jako token). [Zařízení iot technologie Plug and Play](#iot-plug-and-play-device) používá ke ověřování pomocí služby [IoT Hub](#azure-iot-hub)sdílený přístupový podpis.

## <a name="solution-developer"></a>Vývojář řešení

Vývojář řešení vytvoří back-end řešení. Vývojář řešení obvykle funguje s prostředky Azure, jako jsou [IoT Hub](#azure-iot-hub) a [úložiště modelů](#model-repository), nebo funguje s [IoT Central](#azure-iot-central).

## <a name="telemetry"></a>Telemetrie

Pole telemetrie definovaná v [rozhraní](#interface) reprezentují měření. Tato měření jsou obvykle hodnoty, jako jsou například čtení senzorů, které jsou odesílány [zařízením IoT technologie Plug and Play](#iot-plug-and-play-device) jako datový proud dat.

## <a name="visual-studio-code"></a>Visual Studio Code

Visual Studio Code je moderní Editor kódu, který je k dispozici pro více platforem. Rozšíření, jako jsou třeba v sadě [nástrojů Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) , umožňují přizpůsobit editor pro podporu široké škály vývojových scénářů.
