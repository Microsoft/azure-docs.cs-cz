---
title: Vytvoření technických prostředků modulu Azure IoT Edge | Azure Marketplace
description: Vytvořte technické prostředky pro modul IoT Edge.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: b36d03695c107054549fba716f4cc27ecb7bba26
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80983479"
---
# <a name="prepare-your-iot-edge-module-technical-assets"></a>Příprava technických prostředků modulu IoT Edge

>[!Important]
>dubna 2020 začneme přesouvat správu nabídek modulu IoT Edge do Partnerského centra. Po migraci vytvoříte a spravujete nabídky v Centru partnerů. Postupujte podle pokynů v [části Vytvoření nabídky modulu IoT Edge](https://aka.ms/AzureIoTTechAsset) pro správu migrovaných nabídek.

Tento článek popisuje požadavky, které vaše technické prostředky modulu IoT Edge musí splňovat před publikováním na Azure Marketplace.

## <a name="understanding-iot-edge-modules-and-getting-started"></a>Principy modulů IoT Edge a začínáme

Modul IoT Edge je kontejner kompatibilní s Dockerem, který se používá ke spuštění na zařízení IoT Edge.

- Další informace o modulech IoT Edge najdete [v tématu Principy modulů Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-modules).
- Chcete-li začít s vývojem modulů IoT Edge, podívejte se [na požadavky a nástroje pro vývoj modulů IoT Edge](https://docs.microsoft.com/azure/iot-edge/module-development).

## <a name="technical-requirements"></a>Technické požadavky

Aby měl být váš modul IoT Edge certifikovaný a publikovaný na Azure Marketplace, musí být splněny následující technické požadavky.

### <a name="platform-support"></a>Podpora platformy

Váš modul IoT Edge musí podporovat jednu z následujících možností platformy.

#### <a name="tier-1-platforms-supported-by-iot-edge"></a>Platformy Tier 1 podporované IoT Edge

Podporujte všechny platformy Tier 1 podporované IoT Edge (jak je zaznamenáno v [podpoře Azure IoT Edge).](https://docs.microsoft.com/azure/iot-edge/support) Tuto možnost doporučujeme, protože poskytuje lepší zákaznickou zkušenost. Budou představeny moduly splňující tato kritéria. Modul používající tuto možnost platformy musí:

- Zadejte `latest` značku a značku verze `1.0.1`(například ), což jsou značky manifestu vytvořené pomocí [nástroje manifestu](https://github.com/estesp/manifest-tool)GitHub .
- Na [kartě Marketplace](./cpp-marketplace-tab.md) přidejte odkaz na [kompatibilní zařízení s certifikací IoT Edge](https://aka.ms/iot-edge-certified). Tento odkaz se `https://aka.ms/iot-edge-certified`překládá na web , na kterém mohou zákazníci procházet nebo vyhledávat certifikovaná zařízení. Tento web se také označuje jako katalog zařízení [s certifikací Azure IoT Edge.](https://catalog.azureiotsolutions.com/)

#### <a name="a-subset-of-tier-1-platforms-supported-by-iot-edge"></a>Podmnožina platforem Tier 1 podporovaná IoT Edge
  
Podporujte podmnožinu (alespoň jednu) platforem Tier 1 podporovaných ioT Edge (jak je zaznamenáno v [podpoře Azure IoT Edge).](https://docs.microsoft.com/azure/iot-edge/support) Modul používající tuto možnost platformy musí:

- Zadejte `latest` značku a značku verze `1.0.1`(například), které jsou značky manifestu vytvořené pomocí [nástroje manifestu](https://github.com/estesp/manifest-tool) GitHub, pokud je podporováno více než jedna platforma. Značky manifestu jsou volitelné pouze v případě, že je podporována jedna platforma.
- Karta [Marketplace](./cpp-marketplace-tab.md) slouží k poskytnutí odkazu alespoň na jedno zařízení IoT Edge v katalogu zařízení [s certifikací Azure IoT Edge.](https://catalog.azureiotsolutions.com/)

### <a name="device-dimensions"></a>Rozměry zařízení

Rozměry modulu IoT Edge (CPU/RAM/Storage/GPU/etc.) na cílených zařízeních IoT Edge musí splňovat následující požadavky:

- Modul musí fungovat alespoň s jedním zařízením **certifikovaným pro IoT Edge** v katalogu zařízení [s certifikací Azure IoT Edge.](https://catalog.azureiotsolutions.com/)
- **Minimální požadavky na hardware** musí být dokumentovány jako poslední odstavec v popisu nabídky (na [kartě Marketplace](./cpp-marketplace-tab.md)). Volitelně můžete také uvést doporučené požadavky na hardware, pokud se výrazně liší. Na konec popisu nabídky například přidejte následující oddíl:

  ```html
    <p><u>Minimum hardware requirements:</u> Linux x64 and arm32  OS, 1GB of RAM, 500 Mb of storage</p>
  ```

### <a name="configuration"></a>Konfigurace

Obsahuje také výchozí nastavení konfigurace, aby nasazení zařízení IoT Edge bylo co nejpřímější. Kontejner může také obsahovat IoT Edge Module SDK povolit komunikaci s edgeHub a IoT Hub.

#### <a name="default-configuration"></a>Výchozí konfigurace

Moduly IoT Edge musí být možné začít s výchozím nastavením, které je k dispozici na [kartě Skladové položky na portálu cloudových partnerů](./cpp-skus-tab.md). K dispozici jsou následující výchozí nastavení:

- Výchozí **trasy**
- Výchozí **požadované vlastnosti dvojčete**
- Výchozí **proměnné prostředí**
- Výchozí **možnosti vytvoření**

Ve scénáři, kde parametr požadovaný pro výchozí hodnotu nedává smysl (například IP adresa serveru zákazníka), přidáte parametr jako výchozí hodnotu. Tato hodnota je uzavřena v závorkách a velkými písmeny. V tomto příkladu byste nastavili následující výchozí proměnnou prostředí:

```
    ServerIPAddress = <MY_SERVER_IP_ADDRESS>
```

#### <a name="configuration-documentation"></a>Konfigurační dokumentace

Všechna nastavení konfigurace modulu IoT Edge musí být jasně zdokumentována (jak používat jeho trasy, twin požadované vlastnosti, proměnné prostředí, createOptions a tak dále.) Poskytněte odkaz na dokumentaci nebo musí být součástí popisu nabídky/sku.

### <a name="tags-and-versioning"></a>Značky a správa verzí

Zákazníci musí být schopni snadno nasadit modul a automaticky získat aktualizace z tržiště (ve scénáři pro vývojáře.) Musí být také schopni používat a zmrazit přesnou verzi, kterou testovali (ve scénáři výroby).)

Aby moduly IoT Edge splnily očekávání těchto zákazníků a byly publikovány na trhu, musí splňovat následující požadavky:

- Zahrňte značku manifestu, `latest` která odkazuje na nejnovější verzi na všech podporovaných platformách.
- Značky verze musí být ve tvaru X.Y.Z, kde X, Y a Z jsou celá čísla.
- Zahrňte značku "verze", například `1.0.1`, která odkazuje na konkrétní verzi na všech podporovaných platformách.
- Neaktualizujte značky "verze", jako `1.0.1`je , protože musí být neměnné.

>[!Note]
>Volitelně může správa verzí obsahovat značky `2.0` "rolling version", například a `1.0`. To podporuje udržování více hlavních verzí paralelně.

### <a name="telemetry"></a>Telemetrie

Moduly používající sadu SDK modulu IoT `PublisherId.OfferId.SkuId` musí nastavit jedinečný identifikátor modulu pro účely telemetrie. Jedinečný identifikátor umožňuje Azure Marketplace k identifikaci počtu instancí modulu, které jsou spuštěny.

 Pomocí následujících metod z sad SDK modulů `ProductInfo` IoT nastavte tento identifikátor:

- [C\#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.productinfo?view=azure-dotnet#Microsoft_Azure_Devices_Client_DeviceClient_ProductInfo) 
- [C](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Python](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.productinfo?view=azure-java-stable)

Pro moduly, které nepoužívají sdk moduli IoT, jsou k dispozici méně přesné přehledy prostřednictvím portálu partnerů cloudu, jako je počet stažení.

### <a name="security"></a>Zabezpečení

Moduly IoT Edge musí požádat o nejméně privilegovaný přístup k hostiteli, jak je to možné. Je třeba se vyhnout [privilegovaným modulům.](https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities)

### <a name="module-iot-sdk"></a>Modul IoT SDK

Zahrnutí sady SDK modulu IoT není předpokladem pro certifikaci. Včetně sady SDK modulu IoT však může poskytnout lepší uživatelské prostředí. Například pro podporu směrování nebo odesílání zpráv do cloudu.

Sada IoT Module SDK je vyžadována k získání telemetrických dat o počtu spuštěných instancí modulu.


## <a name="recertification-process"></a>Proces recertifikace

<!-- Add legal time windows-->
Partneři budou upozorněni vždy, když dojde k porušení změny, která ovlivňuje jejich moduly, například:

- Matice podpory os/arch úrovně 1 podporovaná IoT Edge
- Sada SDK modulu IoT
- Doba běhu na okraji IoT
- Pokyny pro certifikaci modulů IoT Edge

Partneři budou muset aktualizovat své moduly a znovu je certifikovat pomocí nástroje Portál partnerů cloudu.

## <a name="host-your-iot-edge-module-in-an-azure-container-registry"></a>Hostování modulu IoT Edge v registru kontejnerů Azure

Chcete-li nahrát modul IoT Edge na portál pro partnery cloudu, musíte ho nejprve hostovat v [registru kontejnerů Azure](https://azure.microsoft.com/services/container-registry/) (ACR). Modul musí obsahovat všechny značky, které chcete publikovat, včetně značek obrázků, na které odkazuje značka manifestu.


## <a name="next-steps"></a>Další kroky

- [Vytvořte si nabídku modulů IoT Edge](./cpp-create-offer.md)
