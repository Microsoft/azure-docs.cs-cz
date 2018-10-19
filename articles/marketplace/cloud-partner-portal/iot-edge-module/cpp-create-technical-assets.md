---
title: Vytvoření technických prostředků modulu Azure IoT Edge | Dokumentace Microsoftu
description: Vytvoření technických prostředků pro modul IoT Edge.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: pbutlerm
ms.openlocfilehash: d7f9dfee447cddc771e0e88ca5be04018b51dba2
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2018
ms.locfileid: "49430937"
---
# <a name="prepare-your-iot-edge-module-technical-assets"></a>Příprava modul IoT Edge technické prostředky

Tento článek popisuje požadavky, které vaše IoT Edge module technické prostředky budou muset splnit před publikováním na Azure Marketplace.

## <a name="understanding-iot-edge-modules-and-getting-started"></a>Principy moduly IoT Edge a Začínáme

Modul IoT Edge je kontejner kompatibilní s Dockerem, který provedl ke spuštění na zařízení IoT Edge.

- Další informace o moduly IoT Edge najdete v tématu [moduly pochopit Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-modules).
- Abyste mohli začít s vývojem modul IoT Edge, najdete v článku [požadavky a nástroje pro vývoj modulů IoT Edge](https://docs.microsoft.com/azure/iot-edge/module-development).

## <a name="technical-requirements"></a>Technické požadavky

Aby modul IoT Edge k certifikaci a publikování na webu Azure Marketplace musí být splněny následující technické požadavky.

### <a name="platform-support"></a>Podpora platformy

Modul IoT Edge musí podporovat některý z následujících možností platformu.

#### <a name="tier-1-platforms-supported-by-iot-edge"></a>Vrstva 1 platformách podporovaných službou IoT Edge

Podporují všechny vrstvy 1 platformách podporovaných službou IoT Edge (jak je uvedeno v [podpory Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/support)). Doporučujeme tuto možnost, protože poskytuje lepší uživatelské prostředí. Bude možné předvést mezinárodním moduly, které splňují tato kritéria. Pomocí této možnosti platformy modul musí:

- Zadejte `latest` značku a označení verze (například `1.0.1`), které jsou vytvořené pomocí Githubu manifestu značky [nástroj manifest](https://github.com/estesp/manifest-tool).
- Použití [kartě Marketplace](./cpp-marketplace-tab.md) přidáte odkaz na [kompatibilní IoT Edge certifikovaných zařízení](http://aka.ms/iot-edge-certified). Tento odkaz se překládá na `http://aka.ms/iot-edge-certified`, web, kde můžou zákazníci procházet nebo Hledat certifikovaných zařízení. Tento web se také označuje jako [Azure IoT Edge Certified](https://catalog.azureiotsolutions.com/) katalog zařízení.

#### <a name="a-subset-of-tier-1-platforms-supported-by-iot-edge"></a>Podmnožinu vrstvy 1 platformách podporovaných službou IoT Edge
  
Podporují podmnožinu (alespoň jeden) vrstvy 1 platformách podporovaných službou IoT Edge (jak je uvedeno v [podpory Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/support)). Pomocí této možnosti platformy modul musí:

- Zadejte `latest` značku a značku verze (například `1.0.1`), které jsou vytvořené pomocí Githubu manifestu značky [nástroj manifest](https://github.com/estesp/manifest-tool) Pokud se podporuje více než jednu platformu. Manifestu značky jsou volitelné jenom v případě jedné platformy podporuje.
- Použití [Marketplace kartu](./cpp-marketplace-tab.md) poskytnout odkaz na alespoň jedno zařízení IoT Edge v [Azure IoT Edge Certified](https://catalog.azureiotsolutions.com/) katalog zařízení.

### <a name="device-dimensions"></a>Dimenze zařízení

Dimenze modul IoT Edge (CPU/RAM/úložiště/GPU/atd.) na cílovém zařízení IoT Edge, musí splňovat následující požadavky:

- Modul musí **práce s alespoň jedním certified IoT Edge** zařízení do služby [Azure IoT Edge Certified](https://catalog.azureiotsolutions.com/) katalog zařízení.
- **Minimální hardwarové požadavky** musí být zdokumentována jako poslední odstavce v popisu nabídky (v části [Marketplace kartu](./cpp-marketplace-tab.md)). Pokud se významně liší Volitelně můžete vytvořit seznam také doporučené požadavky na hardware. Na konci vaší popis nabídky přidáte například následující části:

 ```html
    <p><u>Minimum hardware requirements:</u> Linux x64 and arm32  OS, 1GB of RAM, 500 Mb of storage</p>
 ```

### <a name="configuration"></a>Konfigurace

Zahrnuje také výchozí nastavení konfigurace provést nasazení do zařízení IoT Edge tak přímočaré jako je to možné. Kontejner může také zahrnovat SDK modul IoT Edge k umožnění komunikace s edgeHub a centrem IoT.

#### <a name="default-configuration"></a>Výchozí konfigurace

Moduly IoT Edge musí být možné spustit ve výchozím nastavení součástí [SKU kartu na portálu Cloud Partner](./cpp-skus-tab.md). K dispozici jsou následující výchozí nastavení:

- Výchozí **trasy**
- Výchozí **požadované vlastnosti dvojčete**
- Výchozí **proměnné prostředí**
- Výchozí **CreateOptions field**

Ve scénáři, kde parametr vyžadované pro výchozí hodnotu nemá smysl (například IP adresa serveru zákazníka) přidejte parametr jako výchozí hodnotu. Tato hodnota je uzavřen v závorkách a velkými písmeny. V tomto příkladu byste nastavili následující výchozí hodnota proměnné prostředí:

```
    ServerIPAddress = <MY_SERVER_IP_ADDRESS>
```

#### <a name="configuration-documentation"></a>Dokumentace ke službě konfigurace

Všechna nastavení konfigurace modulu IoT Edge musí být zdokumentována jasně (jak používat jeho trasy, dvojčete požadované vlastnosti, proměnné prostředí, CreateOptions field atd.) Zadejte odkaz na dokumentaci nebo v dokumentaci k musí být součástí popisu nabídka/sku.

### <a name="tags-and-versioning"></a>Značky a správy verzí

Zákazníci musí být schopen snadno nasadit modul a automaticky získávat aktualizace z webu marketplace (v případě developer.) Také musí být schopné používat a zablokovat přesnou verzi, které jsou otestované (v případě produkčního prostředí.)

Očekávání těchto zákazníků a publikovat na webu Marketplace, moduly IoT Edge, musí splňovat následující požadavky:

- Zahrnout manifest `latest` značky, který odkazuje na nejnovější verzi na všech podporovaných platformách.
- Verze značky musí být ve tvaru X.Y.Z, kde X, Y a jsou celá čísla.
- Značka "verze", jako je třeba zahrnout `1.0.1`, který odkazuje na konkrétní verzi na všech podporovaných platformách.
- Neaktualizovat značky "verze", jako je třeba `1.0.1`, protože jsou neměnné.

>[!Note]
>Volitelně, Správa verzí může obsahovat značky "vrácení verze", jako `2.0` a `1.0`. Tento atribut podporuje Správa více hlavních verzí paralelně.

### <a name="telemetry"></a>Telemetrická data

Moduly pomocí sady SDK modulu IoT musíte nastavit modul jedinečný identifikátor `PublisherId.OfferId.SkuId` pro účely telemetrie. Jedinečný identifikátor umožňuje identifikovat počet instancí modulu, které běží na Azure Marketplace.

 Pomocí následujících metod ze sady SDK pro modul IoT nastavte `ProductInfo` na tento identifikátor:

- [C\#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.productinfo?view=azure-dotnet#Microsoft_Azure_Devices_Client_DeviceClient_ProductInfo) 
- [C](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Python](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device._product_info?view=azure-java-stable)

Pro moduly, které nepoužívají sady SDK modulu IoT méně přesné insights jsou k dispozici prostřednictvím portál Cloud Partner, jako je počet souborů ke stažení.

### <a name="security"></a>Zabezpečení

Moduly IoT Edge musí požádat o nejméně privilegovaný přístup k hostiteli, jako je to možné. [Privilegovaný moduly](https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities) třeba se jim vyhnout.

### <a name="module-iot-sdk"></a>Modul IoT SDK

Včetně sady SDK modulu IoT není předpokladem k certifikaci. Včetně sady SDK modulu IoT může poskytovat lepší výkon. Chcete-li například podpora směrování nebo odesílání zpráv do cloudu.

Sady SDK modulu IoT je požadováno pro získání telemetrických dat o počet spuštěných instancí modulu.


## <a name="recertification-process"></a>Vydal procesu

<!-- Add legal time windows--> Partneři dostanete oznámení pokaždé, když existuje k narušující změně ovlivňuje jejich modulů, jako například:

- Matice podpory úrovně 1 os/arch nepodporuje IoT Edge
- Modul IoT SDK
- Modul Runtime IoT Edge
- Pokyny pro certifikaci modul IoT Edge

Partneři muset aktualizovat své moduly a znovu certifikovali je pomocí nástroje portál partnerů cloudu.

## <a name="host-your-iot-edge-module-in-an-azure-container-registry"></a>Hostování modulu IoT Edge ve službě Azure Container Registry

Nahrát modul IoT Edge na portál Cloud Partner, musíte nejprve v hostiteli [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) (ACR). V modulu musí obsahovat všechny značky, které chcete publikovat, včetně obrázku značky odkazuje značku manifestu.


## <a name="next-steps"></a>Další postup

- [Vytvoření nabídky modul IoT Edge](./cpp-create-offer.md)
