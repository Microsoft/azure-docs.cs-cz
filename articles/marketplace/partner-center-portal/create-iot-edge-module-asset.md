---
title: Příprava technických prostředků modulu IoT Edge – Azure Marketplace
description: Seznamte se s technickými a konfiguračními požadavky, které musí splňovat vaše technické prostředky modulu Edge (Internet věcí( IoT), než je budete moci publikovat na Azure Marketplace.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/03/2020
ms.openlocfilehash: 2c0cd47acbd4639ff5eff2af78dcebdfc26270a7
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81730701"
---
# <a name="prepare-your-iot-edge-module-technical-assets"></a>Příprava technických prostředků modulu IoT Edge

> [!IMPORTANT]
> Přesouváme správu vašich nabídek modulů IoT Edge z cloudového partnerského portálu do Partnerského centra. Dokud se vaše nabídky nemigrují, postupujte podle pokynů v [části Příprava technických prostředků modulu IoT Edge](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/iot-edge-module/cpp-create-technical-assets) pro portál partnerů cloudu pro správu nabídek.

Tento článek popisuje požadavky, které musí splňovat vaše technické prostředky modulu Edge (Internet věcí (IoT) před publikováním na Azure Marketplace.

## <a name="get-started"></a>Začínáme

Modul IoT Edge je kontejner kompatibilní s Dockerem, který běží na zařízení IoT Edge.

- Další informace o modulech IoT Edge najdete [v tématu Principy modulů Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-modules).
- Pokud chcete začít s vývojem modulů IoT Edge, přečtěte si informace [o vývoji vlastních modulů IoT Edge](https://docs.microsoft.com/azure/iot-edge/module-development).

## <a name="technical-requirements"></a>Technické požadavky

Váš modul IoT Edge musí splňovat následující technické požadavky, aby mohl být certifikován a publikován na Azure Marketplace.

### <a name="platform-support"></a>Podpora platformy

Modul IoT Edge musí podporovat jednu z následujících možností platformy:

#### <a name="tier-1-platforms-supported-by-iot-edge"></a>Platformy Tier 1 podporované IoT Edge

Váš modul musí podporovat všechny platformy Tier 1 podporované IoT Edge (jak je zaznamenáno v [podpoře Azure IoT Edge).](https://docs.microsoft.com/azure/iot-edge/support) Tuto možnost doporučujeme, protože poskytuje lepší zákaznickou zkušenost. Moduly, které splňují tato kritéria, budou představeny. Modul používající tuto možnost platformy musí:

- Zadejte nejnovější značku a značku verze (například 1.0.1), což jsou značky manifestu vytvořené pomocí [nástroje GitHub Manifest- Tool](https://github.com/estesp/manifest-tool).

- Pomocí karty výpis nabídky v [Centru partnerů](https://partner.microsoft.com/dashboard/commercial-marketplace) přidejte odkaz v části **Užitečné odkazy** do [katalogu zařízení s certifikací Azure IoT Edge](https://catalog.azureiotsolutions.com/alldevices?filters={%2218%22:[%221%22]}/).

#### <a name="a-subset-of-tier-1-platforms-supported-by-iot-edge"></a>Podmnožina platforem Tier 1 podporovaná IoT Edge

Váš modul musí podporovat podmnožinu (alespoň jednu) platforem Tier 1 podporovaných ioT Edge (jak je zaznamenáno v [podpoře Azure IoT Edge).](https://docs.microsoft.com/azure/iot-edge/support) Modul používající tuto možnost platformy musí:

- Zadejte nejnovější značku a značku verze (například 1.0.1), které jsou značky manifestu vytvořené pomocí [nástroje manifestu](https://github.com/estesp/manifest-tool) GitHub, pokud je podporována více než jedna platforma. Značky manifestu jsou volitelné pouze v případě, že je podporována jedna platforma.
- Pomocí karty výpis nabídky v [Centru partnerů](https://partner.microsoft.com/dashboard/commercial-marketplace) můžete přidat odkaz v části **Užitečné odkazy** alespoň na jedno zařízení IoT Edge z [katalogu zařízení s certifikací Azure IoT Edge](https://catalog.azureiotsolutions.com/).

:::image type="content" source="media/iot-edge-module-technical-assets-offer-listing.png" alt-text="Toto je obrázek sekce Nabídka výpis v rámci Partnerského centra":::

### <a name="device-dimensions"></a>Rozměry zařízení

Dimenze modulu IoT Edge (například CPU, RAM, úložiště a GPU) na cílených zařízeních IoT Edge musí splňovat následující požadavky:

- Modul musí pracovat alespoň s jedním zařízením IoT Edge z [katalogu zařízení Azure IoT Edge Certified](https://catalog.azureiotsolutions.com/).

- Minimální požadavky na hardware musí být v popisu nabídky zdokumentovány jako poslední odstavec (na kartě výpis nabídky v [Partnerském centru).](https://partner.microsoft.com/dashboard/commercial-marketplace) Volitelně můžete také uvést doporučené požadavky na hardware, pokud se výrazně liší. Na konec popisu nabídky například přidejte následující oddíl:

Zkopírujte tento text HTML nebo použijte odpovídající funkce rtf v okně úprav.

```html
<p><u>Minimum hardware requirements:</u> Linux x64 and arm32 OS, 1GB of RAM, 500 Mb of storage</p>
```

### <a name="configuration"></a>Konfigurace

Váš modul musí obsahovat výchozí nastavení konfigurace, aby nasazení zařízení IoT Edge bylo co nejjednodušší. Tyto informace lze uvést na stránce **Technická konfigurace** plánu v [Centru partnerů](https://partner.microsoft.com/dashboard/commercial-marketplace). Kontejner může také obsahovat sdk modulu IoT Edge, který umožňuje komunikaci s hraničním centrem a službou IoT Hub.

#### <a name="default-configuration"></a>Výchozí konfigurace

Moduly IoT Edge musí být možné začít s výchozím nastavením, které je k dispozici na stránce **Technická konfigurace** plánu v [Centru partnerů](https://partner.microsoft.com/dashboard/commercial-marketplace). K dispozici jsou následující výchozí nastavení:

- Výchozí **trasy**
- Výchozí **modul twin požadované vlastnosti**
- Výchozí **proměnné prostředí**
- Výchozí **možnosti vytvoření kontejneru**

Ve scénáři, kde parametr, který je požadován pro výchozí hodnotu nemá smysl (například IP adresa serveru zákazníka), přidejte parametr jako výchozí hodnotu. Tato hodnota je velká písmena a uzavřena v závorkách. V tomto příkladu byste nastavili následující výchozí proměnnou prostředí:

```
ServerIPAddress = <MY_SERVER_IP_ADDRESS>
```

#### <a name="configuration-documentation"></a>Konfigurační dokumentace

Všechna nastavení konfigurace modulu IoT Edge musí být jasně zdokumentována. Například je nutné dokumentovat, jak používat jeho trasy, twin požadované vlastnosti, proměnné prostředí, createOptions a tak dále. Musíte buď poskytnout odkaz na dokumentaci, nebo ji učinit součástí vaší nabídky nebo popisu plánu. Tyto informace můžete uvést na stránce **Nabídka a** **Plán** v [Centru partnerů](https://partner.microsoft.com/dashboard/commercial-marketplace).

#### <a name="tags-and-versioning"></a>Značky a správa verzí

Zákazníci musí být schopni snadno nasadit modul a automaticky získat aktualizace z trhu (ve scénáři pro vývojáře). Musí být také schopni používat a zmrazit přesnou verzi, kterou testovali (ve scénáři výroby).

Aby moduly IoT Edge splnily tato očekávání zákazníků a byly publikovány na trhu, musí splňovat následující požadavky.

- Zahrňte nejnovější značku manifestu, která odkazuje na nejnovější verzi na všech podporovaných platformách.
- Vytvořte značky verze ve tvaru X.Y.Z, kde X, Y a Z jsou celá čísla.
- Zahrňte značku "verze", například 1.0.1, která odkazuje na konkrétní verzi na všech podporovaných platformách.
- Neaktualizujte značky "verze", například 1.0.1, protože nesmí být změněny.

> [!NOTE]
> Volitelně může správa verzí obsahovat značky "rolling version", například 2.0 a 1.0. To podporuje udržování více hlavních verzí paralelně.

### <a name="telemetry"></a>Telemetrie

Moduly používající sadu SDK modulu IoT musí pro účely telemetrie nastavit jedinečný identifikátor modulu na PublisherId.OfferId.SkuId. Jedinečný identifikátor pomáhá Azure Marketplace identifikovat počet instancí modulů, které jsou spuštěny.

Pomocí jedné z následujících metod z sad SDK modulu IoT nastavte informace o produktu na tento identifikátor:

- [C #](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.productinfo?view=azure-dotnet#Microsoft_Azure_Devices_Client_DeviceClient_ProductInfo)
- [C](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Python](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.productinfo?view=azure-java-stable)

Pro moduly, které nepoužívají sdk moduli IoT, jsou k dispozici méně přesné přehledy prostřednictvím Centra partnerů, jako je například počet stažení.

### <a name="security"></a>Zabezpečení

Moduly IoT Edge se musí vyhnout [privilegovaným modulům](https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities). Místo toho požádejte o nejméně privilegovaný přístup k hostiteli, jak je to možné.

### <a name="module-iot-sdk"></a>Modul IoT SDK

Zahrnutí sady SDK modulu IoT není předpokladem pro certifikaci. Včetně sady SDK modulu IoT však může poskytnout lepší uživatelské prostředí. Například pro podporu směrování nebo odesílání zpráv do cloudu.

Sada IoT Module SDK je vyžadována k získání telemetrických dat o počtu instancí modulu, které jsou spuštěny.

## <a name="recertification-process"></a>Proces recertifikace

Partneři jsou upozorněni vždy, když dojde k porušení změny, která ovlivňuje jejich moduly, například:

- Matice podpory operačního systému Tier 1/arch podporovaná ioT edge
- Sada SDK modulu IoT
- Modul runtime IoT Edge
- Pokyny pro certifikaci modulů IoT Edge

Partneři musí aktualizovat a znovu certifikovat své nabídky opětovným publikováním v [Centru partnerů](https://partner.microsoft.com/dashboard/commercial-marketplace).

Vaše nabídka bude také znovu certifikována, pokud ji aktualizujete, například přidání nových značek obrázků.

## <a name="host-module-in-azure-container-registry"></a>Modul hostitele v registru kontejnerů Azure

Pokud chcete nahrát modul IoT Edge na Azure Marketplace, musíte ho nejdřív hostovat v [registru kontejnerů Azure](https://azure.microsoft.com/services/container-registry/) (ACR). Modul musí obsahovat všechny značky, které chcete publikovat, včetně značek obrázků, na které odkazuje značka manifestu. Další informace naleznete v kurzu [Vytvoření registru kontejnerů Azure a nabízení image kontejneru](https://docs.microsoft.com/azure/container-instances/container-instances-tutorial-prepare-acr).

## <a name="next-steps"></a>Další kroky

- [Vytvoření nabídky modulu IoT Edge](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-iot-edge-module-creation)