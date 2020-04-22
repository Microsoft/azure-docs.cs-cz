---
title: Glosář termínů - IoT Plug and Play Preview | Dokumenty společnosti Microsoft
description: Koncepty - glosář běžných pojmů týkajících se IoT Plug and Play Preview.
author: Philmea
ms.author: philmea
ms.date: 12/23/2019
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: f0c21626c664f2d72b534ebae7f0a257620be07d
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81767063"
---
# <a name="glossary-of-terms-for-iot-plug-and-play-preview"></a>Glosář termínů pro náhled IoT Plug and Play

Definice běžných termínů používaných v článcích IoT Plug and Play.

## <a name="azure-certified-for-iot-portal"></a>Azure certifikovaný pro portál IoT

Na webu [portálu Azure Certified for IoT](https://aka.ms/ACFI) můžete použít k:You for Azure Certified for IoT portal website to:

- Dokončete [certifikační proces](#device-certification) pro [zařízení IoT Plug and Play](#iot-plug-and-play-device).
- Najděte [modely schopností zařízení](#device-capability-model).
- Publikujte model schopností zařízení do [úložiště veřejného modelu](#public-model-repository).

## <a name="azure-cli"></a>Azure CLI

Azure CLI je nástroj příkazového řádku pro různé platformy pro správu prostředků Azure. Rozšíření Azure IoT pro Azure CLI je nástroj příkazového řádku pro interakci s a testování [zařízení IoT Plug and Play](#iot-plug-and-play-device). Rozšíření můžete použít k:

- Připojte se k zařízení IoT Plug and Play.
- Zobrazení [telemetrie,](#telemetry) kterou zařízení odesílá.
- Práce s [vlastnostmi](#properties)zařízení .
- Volání [příkazů](#commands)zařízení .
- Správa [úložišť modelů](#model-repository), [rozhraní](#interface)a modelů [schopností zařízení](#device-capability-model).

## <a name="azure-iot-central"></a>Azure IoT Central

Azure IoT Central je plně spravované řešení jako služba, které usnadňuje připojení, monitorování a správu [vašich zařízení IoT Plug and Play](#iot-plug-and-play-device). [Pomocí modelů schopností zařízení](#device-capability-model) můžete automaticky nakonfigurovat aplikaci IoT Central pro monitorování a správu vašich zařízení.

## <a name="azure-iot-certification-service"></a>Certifikační služba Azure IoT

Certifikační služba Azure IoT spouští sadu certifikačních testů při odeslání [zařízení IoT Plug and Play](#iot-plug-and-play-device) pro certifikaci prostřednictvím [portálu Azure Certified for IoT](#azure-certified-for-iot-portal). Před přidáním zařízení do [katalogu zařízení Certified for IoT](#certified-for-iot-device-catalog)musí být zařízení certifikováno.

## <a name="azure-iot-tools-extension"></a>Rozšíření nástrojů Azure IoT

Azure IoT Tools je kolekce rozšíření v [kódu Visual Studia,](#visual-studio-code) které vám pomůžou pracovat s IoT Hubem a vyvíjet zařízení IoT. Pro vývoj zařízení IoT Plug and Play vám to pomůže:

- Autor [zařízení schopnosti modely](#device-capability-model) a [rozhraní](#interface).
- Publikovat do [úložiště modelu](#model-repository).
- Vygenerujte kód kostry k implementaci aplikace zařízení.

## <a name="azure-iot-explorer-tool"></a>Nástroj Průzkumník Azure IoT

Průzkumník Azure IoT je grafický nástroj, který můžete použít k interakci s a testování [zařízení IoT Plug and Play](#iot-plug-and-play-device). Po instalaci nástroje do místního počítače jej můžete použít k:

- Zobrazení zařízení připojených k [centru IoT hub](#azure-iot-hub).
- Připojte se k zařízení IoT Plug and Play.
- Zobrazení [telemetrie,](#telemetry) kterou zařízení odesílá.
- Práce s [vlastnostmi](#properties)zařízení .
- Volání [příkazů](#commands)zařízení .

## <a name="azure-iot-hub"></a>Azure IoT Hub

IoT Hub je spravovaná služba hostovaná v cloudu, která funguje jako centrum zpráv pro obousměrnou komunikaci mezi aplikací IoT a zařízeními, která spravuje. [Zařízení IoT Plug and Play](#iot-plug-and-play-device) se můžou připojit k centru IoT. Řešení IoT používá centrum IoT pro povolení:

- Zařízení pro odesílání telemetrie do cloudového řešení.
- Cloudové řešení pro správu připojených zařízení.

## <a name="azure-iot-device-sdk"></a>Sada SDK zařízení Azure IoT

Existují sady SDK zařízení pro více jazyků, které můžete použít k vytváření klientských aplikací zařízení IoT Plug and Play. Jedním z požadavků na [certifikaci zařízení](#device-certification) je, že kód klienta zařízení používá jednu z sad SDK zařízení Azure IoT.

## <a name="certified-for-iot-device-catalog"></a>Certifikovaný pro katalog zařízení IoT

[Katalog zařízení Certified for IoT](https://catalog.azureiotsolutions.com/) uvádí zařízení [IoT Plug and Play,](#iot-plug-and-play-device) která prošla certifikačními testy [zařízení.](#device-certification) [Modely schopností zařízení](#device-capability-model) pro zařízení IoT Plug and Play v katalogu a publikované v úložišti veřejného modelu.

## <a name="commands"></a>Příkazy

Příkazy definované v [rozhraní](#interface) představují metody, které lze spustit na [digitální dvojče](#digital-twin). Například příkaz k restartování zařízení.

## <a name="common-interface"></a>Společné rozhraní

Očekává se, že všechna [zařízení IoT Plug and Play](#iot-plug-and-play-device) implementují některá [běžná rozhraní](#interface). Informační rozhraní zařízení například definuje informace o hardwaru a operačním systému o zařízení. [Certifikace zařízení](#device-certification) vyžaduje, aby vaše zařízení implementovalo několik běžných rozhraní. Můžete načíst společné definice rozhraní z úložiště veřejného modelu.

## <a name="company-model-repository"></a>Repozitář firemních modelů

Organizace může použít [úložiště modelu](#model-repository) společnosti jako soukromé úložiště pro modely a [rozhraní](#interface) [schopností zařízení](#device-capability-model) .

## <a name="connection-string"></a>Připojovací řetězec

Připojovací řetězec zapouzdřuje informace potřebné pro připojení ke koncovému bodu. Připojovací řetězec obvykle obsahuje adresu koncového bodu a informace o zabezpečení, ale formáty připojovacího řetězce se v jednotlivých službách liší. Ke službě IoT Hub jsou přidruženy dva typy připojovacího řetězce:

- Připojovací řetězce zařízení umožňují [zařízením IoT Plug and Play](#iot-plug-and-play-device) připojit se ke koncovým bodům orientovaným na zařízení v centru IoT. Kód klienta na zařízení používá připojovací řetězec k navázání zabezpečeného připojení k centru IoT Hub.
- Připojovací řetězce služby IoT Hub umožňují back-endová řešení a nástroje pro bezpečné připojení ke koncovým bodům orientovaným na služby v centru IoT Hub. Tato řešení a nástroje spravují centrum IoT a zařízení k němu připojená.
- Připojovací řetězce repozitáře firemních modelů umožňují back-endová řešení a nástroje pro bezpečné připojení k [úložišti modelů společnosti](#company-model-repository). Tato řešení a nástroje spotřebovávají nebo spravují modely a [rozhraní](#interface) [schopností zařízení](#device-capability-model) v úložišti.

## <a name="device-capability-model"></a>Model schopností zařízení

Model schopností zařízení popisuje [zařízení IoT Plug and Play](#iot-plug-and-play-device) a definuje sadu [rozhraní](#interface) implementovaných zařízením. Model schopností zařízení obvykle odpovídá fyzické zařízení, produkt nebo Skladové položky. K definování modelu schopností zařízení se používá [jazyk digital twin.](#digital-twin-definition-language)

## <a name="device-certification"></a>Certifikace zařízení

Certifikace zařízení je proces certifikace [zařízení IoT Plug and Play,](#iot-plug-and-play-device) aby ho bylo možné přidat do [katalogu zařízení Certified for IoT](#certified-for-iot-device-catalog) a jeho [modelu schopností zařízení](#device-capability-model) a rozhraní přidaných do úložiště [veřejných](#interface) [modelů](#public-model-repository).

## <a name="device-developer"></a>Vývojář zařízení

Vývojář zařízení používá [model schopností zařízení](#device-capability-model), [rozhraní](#interface)a [sadku SDK zařízení Azure IoT](#azure-iot-device-sdk) k implementaci kódu ke spuštění na [zařízení IoT Plug and Play](#iot-plug-and-play-device).

## <a name="device-modeling"></a>Modelování zařízení

[Vývojář zařízení](#device-developer) používá jazyk digital [twin definition](#digital-twin-definition-language) k modelování funkcí [zařízení IoT Plug and Play](#iot-plug-and-play-device). Model lze sdílet pomocí úložiště modelu. Vývojář zařízení může generovat kód kostry zařízení z modelu. [Vývojář řešení](#solution-developer) můžete nakonfigurovat řešení IoT z modelu.

## <a name="device-provisioning-service"></a>Služba zřizování zařízení

[Azure IoT Central](#azure-iot-central) používá službu zřizování zařízení ke správě všech registrací a připojením zařízení. Další informace najdete [v tématu Připojení zařízení v Azure IoT Central](../iot-central/core/concepts-get-connected.md). Službu zřizování zařízení můžete také použít ke správě registrace zařízení a připojení k řešení IoT Hub založenému na IoT Hubu. Další informace najdete [v tématu Zřizování zařízení pomocí služby Azure IoT Hub Device Provisioning Service](../iot-dps/about-iot-dps.md).

## <a name="device-registration"></a>Registrace zařízení

Než se [zařízení IoT Plug and Play](#iot-plug-and-play-device) může připojit k řešení IoT, musí být registrované u řešení. [Azure IoT Central](#azure-iot-central) používá ke správě registrace zařízení [službu Device Provisioning](#device-provisioning-service) Service. Ve vlastním řešení IoT můžete zaregistrovat zařízení s vaším centrem IoT hub na webu Azure Portal nebo programově.

## <a name="device-first"></a>Zařízení na prvním místě

[Azure IoT Central](#azure-iot-central) podporuje scénář registrace a připojení první zařízení. V tomto scénáři [zařízení IoT Plug and Play](#iot-plug-and-play-device) můžete připojit k aplikaci IoT Central bez předchozí registrace. Registrace se stane automaticky, když se zařízení poprvé připojí k aplikaci.

## <a name="digital-twin"></a>Digitální dvojče

Digitální dvojče je model [zařízení IoT Plug and Play](#iot-plug-and-play-device). Digitální dvojče je modelováno pomocí [digitálního twin definition jazyka](#digital-twin-definition-language). Sady [SDK zařízení Azure IoT](#azure-iot-device-sdk) můžete použít k interakci s digitálními dvojčaty za běhu. Můžete například nastavit hodnotu vlastnosti v digitálnídvojče na zařízení a Sada SDK sdělí tuto změnu řešení IoT v cloudu.

## <a name="digital-twin-change-events"></a>Události digitální chodiše

Když je [zařízení IoT Plug and Play](#iot-plug-and-play-device) připojené k centru [IoT ,](#azure-iot-hub)může centrum použít možnost směrování k odesílání oznámení o změnách digitálních dvojčat. Například při každé změně hodnoty [vlastnosti](#properties) na zařízení, IoT Hub můžete odeslat oznámení koncovému bodu, jako je například fronty service bus.

## <a name="digital-twin-definition-language"></a>Jazyk definice digitálního dvojčete

Jazyk pro popis modelů a rozhraní pro [zařízení IoT Plug and Play](#iot-plug-and-play-device). Pomocí [jazyka Digital Twin Definition Language](https://aka.ms/DTDL) můžete popsat možnosti digitálního [dvojčete](#digital-twin) a povolit platformě IoT a řešení IoT využít sémantiku entity.

## <a name="digital-twin-route"></a>Digitální dvojče

Trasa nastavená v [centru IoT](#azure-iot-hub) pro doručování [událostí změny digitálních dvojčat](#digital-twin-change-events) do koncového bodu, jako je například fronta služby Service Bus.

## <a name="interface"></a>Rozhraní

Rozhraní popisuje související funkce, které jsou implementovány [zařízením IoT Plug and Play](#iot-plug-and-play-device) nebo [digitálním dvojčetem](#digital-twin). Rozhraní můžete znovu použít napříč různými [modely schopností zařízení](#device-capability-model).

## <a name="iot-hub-query-language"></a>Dotazovací jazyk centra IoT Hub

Dotazovací jazyk ioT hubu se používá pro více účelů. Jazyk můžete například použít k vyhledávání [zařízení registrovaných](#device-registration) v centru IoT hub nebo k upřesnění chování [směrování digitálního dvojčete.](#digital-twin-route)

## <a name="iot-plug-and-play-device"></a>Zařízení IoT Plug and Play

Zařízení IoT Plug and Play je obvykle malé, samostatné výpočetní zařízení, které shromažďuje data nebo ovládá jiná zařízení a které spouští software nebo firmware, který implementuje [model schopností zařízení](#device-capability-model).  Zařízení IoT Plug and Play může být například zařízení pro monitorování prostředí nebo řadič pro zavlažovací systém inteligentního zemědělství. Můžete napsat cloudové řešení IoT pro příkazy, řízení a příjem dat ze zařízení IoT Plug and Play. Katalog [zařízení S certifikací Azure pro IoT obsahuje](#certified-for-iot-device-catalog) seznamy dostupných zařízení IoT Plug and Play. Každé zařízení IoT Plug and Play v katalogu bylo ověřeno a má [model schopností zařízení](#device-capability-model).

## <a name="microsoft-partner-center"></a>Partnerské centrum Microsoftu

[Centrum partnerů](https://docs.microsoft.com/partner-center/) společnosti Microsoft je místo, kde vaše organizace spravuje svůj vztah mezi koncovými soubory se společností Microsoft. Než budete moci certifikovat zařízení [IoT Plug and Play](#iot-plug-and-play-device) na [portálu Azure Certified for IoT,](#azure-certified-for-iot-portal)budete muset mít účet Microsoft Partner Center.

## <a name="model-discovery"></a>Zjišťování modelů

Když se [zařízení IoT Plug and Play](#iot-plug-and-play-device) připojí k řešení IoT, řešení může zjistit možnosti zařízení vyhledáním modelu schopností [zařízení](#device-capability-model). Zařízení může odeslat svůj model schopností do řešení nebo řešení může najít model schopností zařízení v [úložišti modelu](#model-repository).

## <a name="model-repository"></a>Úložiště modelů

Úložiště modelu ukládá [modely schopností zařízení](#device-capability-model) a [rozhraní](#interface). Existuje jeden [veřejný model úložiště](#public-model-repository). Organizace mohou vytvářet vlastní úložiště organizačních modelů.

## <a name="model-repository-rest-api"></a>Rozhraní REST API úložiště modelu

Rozhraní API pro správu a interakci s úložišti modelů. Pomocí rozhraní API můžete například přidat [modely schopností zařízení](#device-capability-model) a vyhledat modely schopností.

## <a name="properties"></a>Vlastnosti

Vlastnosti jsou datová pole definovaná v [rozhraní,](#interface) které představují určitý stav digitálního dvojčete. Vlastnosti můžete deklarovat jako jen pro čtení nebo zapisovatelné. Vlastnosti jen pro čtení, jako je například sériové číslo, jsou nastaveny kódem spuštěným na samotném [zařízení IoT Plug and Play.](#iot-plug-and-play-device)  Zapisovatelné vlastnosti, jako je prahová hodnota alarmu, se obvykle nastavují z cloudového řešení IoT.

## <a name="public-model-repository"></a>Úložiště veřejných modelů

Existuje jediné úložiště veřejných modelů, které ukládá [modely schopností zařízení](#device-capability-model) a [rozhraní](#interface) pro [certifikovaná zařízení](#device-certification). Úložiště veřejného modelu také ukládá společné definice [rozhraní.](#common-interface)

## <a name="registration-id"></a>ID registrace

ID registrace jednoznačně identifikuje zařízení ve [službě Device Provisioning Service](#device-provisioning-service). Toto ID není stejné jako ID zařízení, které je jedinečným identifikátorem pro zařízení v [centru IoT](#azure-iot-hub)hub .

## <a name="scope-id"></a>ID oboru

Obor ID oboru jednoznačně identifikuje instanci [služby zřizování zařízení.](#device-provisioning-service)

## <a name="shared-access-signature"></a>Sdílený přístupový podpis

Sdílené přístupové podpisy jsou mechanismus ověřování založený na zabezpečených hashe nebo identifikátorech URI SHA-256. Ověřování podpisu sdíleného přístupu má dvě součásti: zásadu sdíleného přístupu a sdílený přístupový podpis (často nazývaný token). [Zařízení IoT Plug and Play](#iot-plug-and-play-device) používá sdílený přístupový podpis k ověření pomocí [služby IoT hub](#azure-iot-hub).

## <a name="solution-developer"></a>Vývojář řešení

Vývojář řešení vytvoří back-end řešení. Vývojář řešení obvykle pracuje s prostředky Azure, jako jsou [úložiště IoT Hub](#azure-iot-hub) a [model ,](#model-repository)nebo pracuje s [IoT Central](#azure-iot-central).

## <a name="telemetry"></a>Telemetrie

Pole telemetrie definovaná v [rozhraní](#interface) představují měření. Tato měření jsou obvykle hodnoty, jako jsou například hodnoty senzorů, které jsou odesílány [zařízením IoT Plug and Play](#iot-plug-and-play-device) jako datový proud.

## <a name="visual-studio-code"></a>Kód visual studia

Visual Studio kód je moderní editor kódu k dispozici pro více platforem. Rozšíření, jako jsou například v balíčku [Nástrojů Azure IoT,](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) umožňují přizpůsobit editor tak, aby podporoval širokou škálu scénářů vývoje.
