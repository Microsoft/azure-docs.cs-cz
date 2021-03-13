---
title: Zápis createOptions pro moduly – Azure IoT Edge | Microsoft Docs
description: Použití createOptions v manifestu nasazení ke konfiguraci modulů za běhu
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/01/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 9ee5536562eb3f2008908a36ff296ef2cfa337ea
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2021
ms.locfileid: "103200622"
---
# <a name="how-to-configure-container-create-options-for-iot-edge-modules"></a>Jak nakonfigurovat možnosti vytváření kontejnerů pro IoT Edge moduly

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Parametr **createOptions** v manifestu nasazení umožňuje konfigurovat kontejnery modulu za běhu. Tento parametr rozbalí vaši kontrolu nad moduly a umožňuje úlohy, jako je povolení nebo omezení přístupu modulu k prostředkům hostitelského zařízení nebo konfigurace sítě.

Moduly IoT Edge jsou v zařízení IoT Edge implementované jako kontejnery kompatibilní s Docker. Docker nabízí mnoho možností pro vytváření kontejnerů a tyto možnosti platí i pro IoT Edge moduly. Další informace najdete v tématu [možnosti vytvoření kontejneru Docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate).

## <a name="format-create-options"></a>Formátování možností vytvoření

Manifest nasazení IoT Edge přijímá možnosti vytváření formátované jako JSON. Například Vezměte v úvahu možnosti vytváření, které jsou automaticky zahrnuty pro každý modul edgeHub:

```json
"createOptions": {
  "HostConfig": {
    "PortBindings": {
      "5671/tcp": [
        {
          "HostPort": "5671"
        }
      ],
      "8883/tcp": [
        {
          "HostPort": "8883"
        }
      ],
      "443/tcp": [
        {
          "HostPort": "443"
        }
      ]
    }
  }
}
```

Tento příklad edgeHub používá parametr **Hostconfig. PortBindings** k mapování vystavených portů na kontejneru na port v hostitelském zařízení.

Pokud používáte rozšíření Azure IoT Tools pro Visual Studio nebo Visual Studio Code, můžete napsat možnosti vytvoření ve formátu JSON v **deployment.template.js** souboru. Poté, když použijete rozšíření k sestavení IoT Edge řešení nebo vygenerování manifestu nasazení, bude stringify JSON pro vás ve formátu, který očekává IoT Edge runtime. Například:

```json
"createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
```

Jedním z tipů pro psaní možností vytváření je použití `docker inspect` příkazu. V rámci procesu vývoje spusťte modul místně pomocí nástroje `docker run <container name>` . Jakmile modul funguje způsobem, který chcete, spusťte `docker inspect <container name>` . Tento příkaz vypíše podrobnosti o modulu ve formátu JSON. Vyhledejte parametry, které jste nakonfigurovali, a zkopírujte kód JSON. Například:

[![Výsledky kontroly edgeHub Docker](./media/how-to-use-create-options/docker-inspect-edgehub-inline-and-expanded.png)](./media/how-to-use-create-options/docker-inspect-edgehub-inline-and-expanded.png#lightbox)

## <a name="common-scenarios"></a>Obvyklé scénáře

Možnosti vytváření kontejnerů umožňují řadu scénářů, ale tady je několik, které se při sestavování IoT Edgech řešení často vyskytují:

* [Udělení přístupu k hostitelskému úložišti pro moduly](how-to-access-host-storage-from-module.md)
* [Mapovat port hostitele na port modulu](#map-host-port-to-module-port)
* [Omezení paměti modulu a využití procesoru](#restrict-module-memory-and-cpu-usage)

### <a name="map-host-port-to-module-port"></a>Mapovat port hostitele na port modulu

Pokud váš modul potřebuje komunikovat se službou mimo IoT Edge řešení a nepoužívá směrování zpráv, musíte port hostitele namapovat na port modulu.

>[!TIP]
>Toto mapování portů není nutné pro komunikaci mezi moduly na stejném zařízení. Pokud modul potřebuje pro dotazování rozhraní API hostovaného v modulu B, může to udělat bez mapování portů. Modul B musí ve svém souboru dockerfileu vystavovat port, například: `EXPOSE 8080` . Pak se modul A může dotazovat rozhraní API pomocí názvu modulu B, například: `http://ModuleB:8080/api` .

Nejdřív se ujistěte, že je port uvnitř modulu vystavený pro naslouchat připojení. To můžete provést pomocí instrukcí [vystavení](https://docs.docker.com/engine/reference/builder/#expose) v souboru Dockerfile. Například, `EXPOSE 8080`. Pokud není zadaný, použije se ve výchozím nastavení protokol TCP, pokud není zadaný, nebo můžete zadat UDP.

Pak pomocí nastavení **PortBindings** ve skupině **Hostconfig** [kontejneru Docker vytvořte možnosti](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) mapování vystaveného portu v modulu na port v hostitelském zařízení. Pokud jste například vystavili port 8080 uvnitř modulu a chcete ho namapovat na port 80 hostitelského zařízení, možnosti vytvoření v template.jssouboru by vypadaly jako v následujícím příkladu:

```json
"createOptions": {
  "HostConfig": {
    "PortBindings": {
      "8080/tcp": [
        {
          "HostPort": "80"
        }
      ]
    }
  }
}
```

Po dokumentí pro manifest nasazení by stejná konfigurace vypadala jako v následujícím příkladu:

```json
"createOptions": "{\"HostConfig\":{\"PortBindings\":{\"8080/tcp\":[{\"HostPort\":\"80\"}]}}}"
```

### <a name="restrict-module-memory-and-cpu-usage"></a>Omezení paměti modulu a využití procesoru

Můžete deklarovat, kolik prostředků hostitele může modul použít. Tento ovládací prvek je užitečný k zajištění toho, že jeden modul nemůže spotřebovat příliš mnoho paměti nebo využití procesoru a zabránit spuštění jiných procesů na zařízení. Tato nastavení můžete spravovat pomocí [možností vytvoření kontejneru Docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) ve skupině **Hostconfig** , včetně:

* **Paměť**: limit paměti v bajtech. Například 268435456 bajtů = 256 MB.
* **MemorySwap**: celkový limit paměti (paměť a swap). Například 536870912 bajtů = 512 MB
* **CpuPeriod**: Délka periody procesoru v mikrosekundách. Výchozí hodnota je 100000, takže například hodnota 25000 omezí kontejner na 25% prostředků procesoru.

V template.jsve formátu by tyto hodnoty vypadaly jako v následujícím příkladu:

```json
"createOptions": {
  "HostConfig": {
    "Memory": 268435456,
    "MemorySwap": 536870912,
    "CpuPeriod": 25000
  }
}
```

Po dokument pro finální manifest nasazení by tyto hodnoty vypadaly jako v následujícím příkladu:

```json
"createOptions":"{\"HostConfig\":{\"Memory\":268435456,\"MemorySwap\":536870912,\"CpuPeriod\":25000}}"
```

## <a name="next-steps"></a>Další kroky

Další příklady vytvoření možností v akci najdete v následujících ukázkách IoT Edge:

* [Custom Vision a Azure IoT Edge na malinu PI 3](https://github.com/Azure-Samples/custom-vision-service-iot-edge-raspberry-pi)
* [Ukázka úložiště objektů blob Azure IoT Edge](https://github.com/Azure-Samples/azure-iotedge-blobstorage-sample)
