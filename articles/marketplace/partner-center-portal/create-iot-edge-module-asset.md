---
title: Příprava technických prostředků modulu IoT Edge – Azure Marketplace
description: Seznamte se s požadavky na technickou a konfiguraci, které Internet věcí (IoT) Edge pro modul na hraničních zařízeních musí splnit, aby je bylo možné publikovat do Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 04/03/2020
ms.openlocfilehash: aca27b89a3b92b410fa560c8b4bd7eb3d4e0a935
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "93346770"
---
# <a name="prepare-your-iot-edge-module-technical-assets"></a>Příprava technických prostředků IoT Edge modulu

Tento článek popisuje požadavky, které musí váš modul pro Internet věcí (IoT) Edge před publikováním v Azure Marketplace splnit.

## <a name="get-started"></a>Začínáme

Modul IoT Edge je kontejner kompatibilní s Docker, který běží na zařízení IoT Edge.

- Další informace o IoT Edgech modulech najdete v tématu [principy Azure IoT Edgech modulů](../../iot-edge/iot-edge-modules.md).
- Chcete-li začít s vývojem modulu IoT Edge, přečtěte si téma [vývoj vlastních modulů IoT Edge](../../iot-edge/module-development.md).

## <a name="technical-requirements"></a>Technické požadavky

Váš IoT Edge modul musí splňovat následující technické požadavky pro certifikaci a publikování v Azure Marketplace.

### <a name="platform-support"></a>Podpora platformy

Váš modul IoT Edge musí podporovat jednu z následujících možností platformy:

#### <a name="tier-1-platforms-supported-by-iot-edge"></a>Platformy vrstvy 1 podporované nástrojem IoT Edge

Váš modul musí podporovat všechny platformy vrstvy 1 podporované aplikací IoT Edge (jak je uvedeno v [Azure IoT Edge podpoře](../../iot-edge/support.md)). Tuto možnost doporučujeme, protože poskytuje lepší prostředí pro zákazníky. Budou se prezentovat moduly, které splňují tato kritéria. Modul používající tuto možnost platformy musí:

- Poskytněte nejnovější značku a značku verze (například 1.0.1), které jsou značky manifestu sestavené pomocí [nástroje GitHub Manifest-Tool](https://github.com/estesp/manifest-tool).

- Pomocí karty seznam nabídek v [partnerském centru](https://partner.microsoft.com/dashboard/commercial-marketplace) můžete přidat odkaz v části **užitečné odkazy** do [katalogu zařízení Azure IoT Edge Certified](https://catalog.azureiotsolutions.com/alldevices?filters={%2218%22:[%221%22]}/).

#### <a name="a-subset-of-tier-1-platforms-supported-by-iot-edge"></a>Podmnožina platforem vrstvy 1, kterou podporuje IoT Edge

Váš modul musí podporovat podmnožinu (alespoň jednu) platforem vrstvy 1, kterou podporuje IoT Edge (jak je uvedeno v [Azure IoT Edge podpoře](../../iot-edge/support.md)). Modul používající tuto možnost platformy musí:

- Poskytněte nejnovější značku a značku verze (například 1.0.1), které jsou značky manifestu sestavené s [manifestem](https://github.com/estesp/manifest-tool) GitHubu, pokud je podporovaná víc než jedna platforma. Značky manifestu jsou volitelné jenom v případě, že je podporovaná jedna platforma.
- V [partnerském centru](https://partner.microsoft.com/dashboard/commercial-marketplace) použijte kartu seznam nabídek a přidejte odkaz v části **užitečné odkazy** na alespoň jeden IoT Edge zařízení z [katalogu zařízení Azure IoT Edge Certified](https://catalog.azureiotsolutions.com/).

:::image type="content" source="media/iot-edge-module-technical-assets-offer-listing.png" alt-text="Toto je obrázek části Seznam nabídek v rámci partnerského centra.":::

### <a name="device-dimensions"></a>Rozměry zařízení

Dimenze modulu IoT Edge (například CPU, paměť RAM, úložiště a GPU) na cílových IoT Edgech zařízeních musí splňovat následující požadavky:

- Modul musí spolupracovat s aspoň jedním zařízením IoT Edge z [katalogu zařízení Azure IoT Edge Certified](https://catalog.azureiotsolutions.com/).

- Minimální požadavky na hardware musí být zdokumentovány jako poslední odstavec v popisu nabídky (na kartě seznam nabídek v [partnerském centru](https://partner.microsoft.com/dashboard/commercial-marketplace)). V případě potřeby můžete také zobrazit seznam doporučených požadavků na hardware, pokud se výrazně liší. Přidejte například následující část na konec popisu nabídky:

Zkopírujte tento text HTML nebo použijte odpovídající funkce formátovaného textu v okně pro úpravy.

```html
<p><u>Minimum hardware requirements:</u> Linux x64 and arm32 OS, 1GB of RAM, 500 Mb of storage</p>
```

### <a name="configuration"></a>Konfigurace

Váš modul musí zahrnovat výchozí nastavení konfigurace, aby bylo možné co nejjednodušší nasazení IoT Edge zařízení. Tyto informace se dají zadat na stránce **Technická konfigurace** pro plán v [partnerském centru](https://partner.microsoft.com/dashboard/commercial-marketplace). Kontejner může také zahrnovat sadu SDK IoT Edge modulu, aby bylo možné komunikovat s centrem Edge a IoT Hub.

#### <a name="default-configuration"></a>Výchozí konfigurace

IoT Edge moduly musí být schopné začít s výchozími nastaveními, která jsou k dispozici na stránce **Technická konfigurace** pro plán v [partnerském centru](https://partner.microsoft.com/dashboard/commercial-marketplace). K dispozici jsou následující výchozí nastavení:

- Výchozí **trasy**
- Výchozí **modul – požadované vlastnosti vlákna**
- Výchozí **proměnné prostředí**
- **Možnosti vytvoření výchozího kontejneru**

V případě, že parametr, který je požadován pro výchozí hodnotu, nesmyslí (například IP adresa serveru zákazníka), přidejte jako výchozí hodnotu parametr. Tato hodnota je velká písmena a uzavřená v závorkách. V tomto příkladu jste nastavili následující výchozí proměnnou prostředí:

```
ServerIPAddress = <MY_SERVER_IP_ADDRESS>
```

#### <a name="configuration-documentation"></a>Dokumentace ke konfiguraci

Všechna nastavení konfigurace modulu IoT Edge musí být jasně zdokumentovaná. Například je třeba zdokumentovat, jak používat své trasy, vlákna s požadovanou vlastností, proměnné prostředí, createOptions a tak dále. Musíte buď zadat odkaz na svou dokumentaci, nebo ho nastavit jako součást popisu nabídky nebo plánu. Tyto informace můžete zadat na stránce seznam **nabídek** a **seznam plánů** v [partnerském centru](https://partner.microsoft.com/dashboard/commercial-marketplace).

#### <a name="tags-and-versioning"></a>Značky a správa verzí

Zákazníci musí být schopni snadno nasadit modul a automaticky získávat aktualizace z webu Marketplace (ve scénáři pro vývojáře). Také musí být schopni používat a zablokovat přesnou verzi, kterou testoval (v produkčním scénáři).

Aby bylo možné splnit tyto očekávání zákazníků a publikovat je na webu Marketplace, IoT Edge moduly musí splňovat následující požadavky.

- Zahrňte nejnovější značku manifestu, která odkazuje na nejnovější verzi na všech podporovaných platformách.
- Značku verze ve formátu X. Y. Z, kde X, Y a Z jsou celá čísla.
- Přidejte značku "Version", například 1.0.1, která odkazuje na konkrétní verzi na všech podporovaných platformách.
- Neaktualizujte značky "Version", například 1.0.1, protože nesmí být změněny.

> [!NOTE]
> Volitelně může správa verzí zahrnovat značky "kumulovaná verze", například 2,0 a 1,0. To podporuje souběžné udržování více hlavních verzí.

### <a name="telemetry"></a>Telemetrie

Moduly používající sadu SDK modulu IoT musí pro účely telemetrie nastavit jedinečný identifikátor modulu na PublisherId. hodnotami OfferId. SkuId. Jedinečný identifikátor pomáhá Azure Marketplace identifikaci počtu instancí modulů, které jsou spuštěny.

Použijte jednu z následujících metod ze sady SDK modulu IoT a nastavte ProductInfo na tento identifikátor:

- [C#](/dotnet/api/microsoft.azure.devices.client.deviceclient.productinfo#Microsoft_Azure_Devices_Client_DeviceClient_ProductInfo)
- [R](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Python](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Java](/java/api/com.microsoft.azure.sdk.iot.device.productinfo)

V případě modulů, které nepoužívají sadu SDK služby IoT, jsou k dispozici méně přesné přehledy prostřednictvím partnerského centra, jako je například počet souborů ke stažení.

### <a name="security"></a>Zabezpečení

Moduly IoT Edge se musí vyhnout [privilegovaným modulům](https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities). Místo toho se postará o co nejmenší privilegovaný přístup k hostiteli.

### <a name="module-iot-sdk"></a>Sada SDK IoT pro modul

Zahrnutí sady SDK služby IoT není předpokladem pro certifikaci. Nicméně, včetně sady SDK modulu IoT, můžou poskytovat lepší uživatelské prostředí. Například pro podporu směrování nebo posílání zpráv do cloudu.

Sada SDK modulu IoT je nutná k získání dat telemetrie o počtu instancí modulů, které jsou spuštěny.

## <a name="recertification-process"></a>Proces recertifikace

Partneři jsou informováni vždy, když dojde k zásadní změně, která má vliv na jejich moduly, například:

- Matice podpory pro operační systém nebo na archu úrovně 1 podporovaná IoT Edge
- Sada SDK pro modul IoT
- Modul runtime IoT Edge
- Pokyny pro certifikaci IoT Edge modulu

Partneři musí aktualizovat a znovu certifikovat své nabídky jejich opětovným publikováním v [partnerském centru](https://partner.microsoft.com/dashboard/commercial-marketplace).

Vaše nabídka se taky ověří, pokud ji aktualizujete, třeba přidáním nových značek obrázku.

## <a name="host-module-in-azure-container-registry"></a>Modul hostitele v Azure Container Registry

Pokud chcete nahrát modul IoT Edge do Azure Marketplace, musíte ho nejdřív hostovat v [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) (ACR). Modul musí obsahovat všechny značky, které chcete publikovat, včetně značek obrázků, na které odkazuje značka manifestu. Další informace najdete v kurzu [Vytvoření služby Azure Container registry a vložení image kontejneru](../../container-instances/container-instances-tutorial-prepare-acr.md).

## <a name="next-steps"></a>Další kroky

- [Vytvoření nabídky modulu IoT Edge](azure-iot-edge-module-creation.md)