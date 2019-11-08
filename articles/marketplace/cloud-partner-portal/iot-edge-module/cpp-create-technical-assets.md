---
title: Vytváření technických prostředků pro Azure IoT Edge modulu | Azure Marketplace
description: Vytvořte technické prostředky pro modul IoT Edge.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: pabutler
ms.openlocfilehash: 57bc2f789836a7d3453004cdacc59029c4b24129
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827626"
---
# <a name="prepare-your-iot-edge-module-technical-assets"></a>Příprava technických prostředků IoT Edge modulu

Tento článek popisuje požadavky, které musí váš technický Asset IoT Edge před publikováním na Azure Marketplace splnit.

## <a name="understanding-iot-edge-modules-and-getting-started"></a>Porozumění modulům IoT Edge a Začínáme

Modul IoT Edge je kontejner kompatibilní s Docker, který je vytvořený pro spuštění na IoT Edgem zařízení.

- Další informace o IoT Edgech modulech najdete v tématu [principy Azure IoT Edgech modulů](https://docs.microsoft.com/azure/iot-edge/iot-edge-modules).
- Chcete-li začít s vývojem modulu IoT Edge, přečtěte si téma [požadavky a nástroje pro vývoj IoT Edgech modulů](https://docs.microsoft.com/azure/iot-edge/module-development).

## <a name="technical-requirements"></a>Technické požadavky

Aby bylo možné modul IoT Edge v Azure Marketplace certifikovat a publikovat, musí být splněné následující technické požadavky.

### <a name="platform-support"></a>Podpora platformy

Váš modul IoT Edge musí podporovat jednu z následujících možností platformy.

#### <a name="tier-1-platforms-supported-by-iot-edge"></a>Platformy vrstvy 1 podporované nástrojem IoT Edge

Podpora všech platforem vrstvy 1 podporovaných nástrojem IoT Edge (jak je uvedeno v [Azure IoT Edge podpory](https://docs.microsoft.com/azure/iot-edge/support)). Tuto možnost doporučujeme, protože poskytuje lepší prostředí pro zákazníky. Budou se prezentovat moduly, které splňují tato kritéria. Modul používající tuto možnost platformy musí:

- Zadejte značku `latest` a značku verze (například `1.0.1`), které jsou značky manifestu sestavené pomocí [nástroje GitHub Manifest-Tool](https://github.com/estesp/manifest-tool).
- Pomocí [karty tržiště](./cpp-marketplace-tab.md) přidejte odkaz na [kompatibilní IoT Edge certifikovaných zařízení](https://aka.ms/iot-edge-certified). Tento odkaz řeší `https://aka.ms/iot-edge-certified`web, na kterém můžou zákazníci procházet nebo Hledat certifikovaná zařízení. Tento web je taky známý jako katalog zařízení [Azure IoT Edge Certified](https://catalog.azureiotsolutions.com/) .

#### <a name="a-subset-of-tier-1-platforms-supported-by-iot-edge"></a>Podmnožina platforem vrstvy 1, kterou podporuje IoT Edge
  
Podpora podmnožiny (nejméně jedné) platforem vrstvy 1 podporované službou IoT Edge (jak je uvedeno v [Azure IoT Edge podpoře](https://docs.microsoft.com/azure/iot-edge/support)). Modul používající tuto možnost platformy musí:

- Poskytněte značku `latest` a značku verze (například `1.0.1`), které jsou značky manifestu sestavené s [manifestem](https://github.com/estesp/manifest-tool) GitHubu, pokud je podporovaná víc než jedna platforma. Značky manifestu jsou volitelné jenom v případě, že je podporovaná jedna platforma.
- Na [kartě Marketplace](./cpp-marketplace-tab.md) můžete zadat odkaz na alespoň jedno zařízení IoT Edge v katalogu [Azure IoT Edge certifikovaných](https://catalog.azureiotsolutions.com/) zařízení.

### <a name="device-dimensions"></a>Rozměry zařízení

Dimenze modulu IoT Edge (CPU/RAM/Storage/GPU/atd.) na cílových IoT Edgech zařízeních musí splňovat následující požadavky:

- Modul musí **fungovat s minimálně jedním IoT Edge certifikovaným** zařízením v katalogu zařízení [Azure IoT Edge Certified](https://catalog.azureiotsolutions.com/) .
- **Minimální požadavky na hardware** musí být zdokumentovány jako poslední odstavec v popisu nabídky (na [kartě Marketplace](./cpp-marketplace-tab.md)). V případě potřeby můžete také zobrazit seznam doporučených požadavků na hardware, pokud se výrazně liší. Přidejte například následující část na konec popisu nabídky:

  ```html
    <p><u>Minimum hardware requirements:</u> Linux x64 and arm32  OS, 1GB of RAM, 500 Mb of storage</p>
  ```

### <a name="configuration"></a>Konfigurace

Obsahuje taky výchozí nastavení konfigurace, které umožňuje nasazení IoT Edge zařízení jako přímého navýšení. Kontejner může také zahrnovat sadu SDK IoT Edge modulu, která umožňuje komunikaci s edgeHub a IoT Hub.

#### <a name="default-configuration"></a>Výchozí konfigurace

IoT Edge moduly musí být schopné začít s výchozím nastavením, které jsou k dispozici na [kartě SKU portálu Cloud Partner](./cpp-skus-tab.md). K dispozici jsou následující výchozí nastavení:

- Výchozí **trasy**
- Výchozí **požadované vlastnosti**
- Výchozí **proměnné prostředí**
- Výchozí **createOptions**

V případě, že parametr vyžadovaný pro výchozí hodnotu nemá smysl (například IP adresa serveru zákazníka), přidáte parametr jako výchozí hodnotu. Tato hodnota je uzavřená v závorkách a velkými písmeny. V tomto příkladu jste nastavili následující výchozí proměnnou prostředí:

```
    ServerIPAddress = <MY_SERVER_IP_ADDRESS>
```

#### <a name="configuration-documentation"></a>Dokumentace ke konfiguraci

Všechna nastavení konfigurace modulu IoT Edge musí být jasně zdokumentována (jak používat jeho trasy, vlákna s více instancemi, proměnné prostředí, createOptions atd.) Zadejte odkaz na svou dokumentaci nebo dokumentaci musí být součástí popisu nabídky/SKU.

### <a name="tags-and-versioning"></a>Značky a správa verzí

Zákazníci musí být schopni snadno nasadit modul a automaticky získávat aktualizace z webu Marketplace (ve scénáři vývoje). Také musí být schopni používat a zablokovat přesnou verzi, kterou testoval (v produkčním scénáři).

Aby bylo možné splnit očekávání těchto zákazníků a publikovat je na webu Marketplace, IoT Edge moduly musí splňovat následující požadavky:

- Zahrňte `latest` značku manifestu, která odkazuje na nejnovější verzi na všech podporovaných platformách.
- Značky verze musí mít formát X. Y. Z, kde X, Y a Z jsou celá čísla.
- Přidejte značku Version, například `1.0.1`, která odkazuje na konkrétní verzi na všech podporovaných platformách.
- Neaktualizujte značky Version, jako je `1.0.1`, protože musí být neměnné.

>[!Note]
>Volitelně může správa verzí zahrnovat značky "kumulovaná verze", například `2.0` a `1.0`. To podporuje souběžné udržování více hlavních verzí.

### <a name="telemetry"></a>Telemetrická data

Moduly, které používají sadu SDK modulu IoT, musí pro účely telemetrie nastavit jedinečný identifikátor modulu `PublisherId.OfferId.SkuId`. Jedinečný identifikátor umožňuje Azure Marketplace identifikovat počet instancí modulů, které jsou spuštěny.

 Pomocí následujících metod v sadách SDK služby IoT nastavte `ProductInfo` na tento identifikátor:

- [C\#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.productinfo?view=azure-dotnet#Microsoft_Azure_Devices_Client_DeviceClient_ProductInfo) 
- [C](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Python](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.productinfo?view=azure-java-stable)

V případě modulů, které nepoužívají sadu SDK služby IoT, jsou k dispozici méně přesné přehledy prostřednictvím portál partnerů cloudu, jako je například počet souborů ke stažení.

### <a name="security"></a>Zabezpečení

IoT Edge moduly musí požádat o co možná nejnižší privilegovaný přístup k hostiteli. [Privilegované moduly](https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities) by se měly vyhnout.

### <a name="module-iot-sdk"></a>Sada SDK IoT pro modul

Zahrnutí sady SDK služby IoT není předpokladem pro certifikaci. Nicméně, včetně sady SDK modulu IoT, můžou poskytovat lepší uživatelské prostředí. Například pro podporu směrování nebo posílání zpráv do cloudu.

Sada SDK modulu IoT je nutná k získání dat telemetrie o počtu spuštěných instancí modulu.


## <a name="recertification-process"></a>Proces recertifikace

<!-- Add legal time windows-->
Partner dostane oznámení vždy, když dojde k zásadní změně, která má vliv na jejich moduly, například:

- Matice podpory pro operační systém nebo na archu úrovně 1 podporovaná IoT Edge
- Sada SDK pro modul IoT
- Modul runtime IoT Edge
- Pokyny pro certifikaci IoT Edge modulu

Partneři budou muset aktualizovat své moduly a znovu je certifikovat pomocí nástroje portál partnerů cloudu.

## <a name="host-your-iot-edge-module-in-an-azure-container-registry"></a>Hostování modulu IoT Edge v Azure Container Registry

Pokud chcete nahrát modul IoT Edge do portál partnerů cloudu, musíte ho nejdřív hostovat v [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) (ACR). Modul musí obsahovat všechny značky, které chcete publikovat, včetně značek obrázků, na které odkazuje značka manifestu.


## <a name="next-steps"></a>Další kroky

- [Vytvoření nabídky modulu IoT Edge](./cpp-create-offer.md)
