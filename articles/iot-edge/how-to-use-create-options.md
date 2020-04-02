---
title: Napište možnosti vytvoření modulů – Azure IoT Edge | Dokumenty společnosti Microsoft
description: Použití funkce createOptions v manifestu nasazení ke konfiguraci modulů za běhu
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/01/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: c07e161042a497a232cbd5e3f11128893a095381
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80550341"
---
# <a name="how-to-configure-container-create-options-for-iot-edge-modules"></a>Konfigurace možností vytvoření kontejneru pro moduly IoT Edge

Parametr **createOptions** v manifestu nasazení umožňuje konfigurovat kontejnery modulů za běhu. Tento parametr rozšiřuje vaši kontrolu nad moduly a umožňuje úlohy, jako je povolení nebo omezení přístupu modulu k prostředkům hostitelského zařízení nebo konfigurace sítě.

Moduly IoT Edge se implementují jako kontejnery kompatibilní s Dockerem na vašem zařízení IoT Edge. Docker nabízí mnoho možností pro vytváření kontejnerů a tyto možnosti platí i pro moduly IoT Edge. Další informace naleznete v tématu [Možnosti vytváření kontejnerů Dockeru](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate).

## <a name="format-create-options"></a>Formátování možností vytvoření

Manifest nasazení IoT Edge přijímá možnosti vytvoření formátované jako JSON. Vezměte například možnosti vytvoření, které jsou automaticky zahrnuty pro každý modul edgeHub:

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

Tento příklad edgeHub používá **Parametr HostConfig.PortBindings** k mapování exponovaných portů v kontejneru na port na hostitelském zařízení.

Pokud používáte rozšíření nástroje Azure IoT pro Visual Studio nebo Visual Studio Code, můžete napsat možnosti vytvoření ve formátu JSON v souboru **deployment.template.json.** Potom při použití rozšíření k sestavení řešení IoT Edge nebo generování manifestu nasazení, bude stringify JSON pro vás ve formátu, který očekává runtime IoT Edge. Například:

```json
"createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
```

Jeden tip pro psaní možnosti `docker inspect` vytvoření je použít příkaz. Jako součást procesu vývoje spusťte `docker run <container name>`modul místně pomocí . Jakmile budete mít modul pracuje tak, `docker inspect <container name>`jak chcete, spusťte . Tento příkaz vyvodí podrobnosti modulu ve formátu JSON. Najděte parametry, které jste nakonfigurovali, a zkopírujte JSON. Například:

[![Výsledky docker zkontrolovat](./media/how-to-use-create-options/docker-inspect-edgehub-inline-and-expanded.png) edgeHub](./media/how-to-use-create-options/docker-inspect-edgehub-inline-and-expanded.png#lightbox)

## <a name="common-scenarios"></a>Obvyklé scénáře

Možnosti vytvoření kontejneru umožňují mnoho scénářů, ale tady jsou některé, které se objevují nejčastěji při vytváření řešení IoT Edge:

* [Poskytněte modulům přístup k hostitelskému úložišti](how-to-access-host-storage-from-module.md)
* [Mapování portu hostitele na port modulu](#map-host-port-to-module-port)
* [Omezení využití paměti modulu a procesoru](#restrict-module-memory-and-cpu-usage)

### <a name="map-host-port-to-module-port"></a>Mapování portu hostitele na port modulu

Pokud váš modul potřebuje komunikovat se službou mimo řešení IoT Edge a nepoužívá směrování zpráv k tomu, pak je třeba mapovat port hostitele na port modulu.

>[!TIP]
>Toto mapování portů není vyžadováno pro komunikaci mezi modulemi na stejném zařízení. Pokud modul A potřebuje dotaz na rozhraní API hostované v modulu B, může tak učinit bez mapování portů. Modul B musí vystavit port v jeho dockerfile, například: `EXPOSE 8080`. Pak modul A může dotaz API pomocí názvu modulu B, například: `http://ModuleB:8080/api`.

Nejprve se ujistěte, že port uvnitř modulu je vystaven naslouchat připojení. Můžete to provést pomocí [expose](https://docs.docker.com/engine/reference/builder/#expose) instrukce v souboru dockerfile. Například, `EXPOSE 8080`. Příkaz vystavit výchozí protokol TCP, pokud není zadán, nebo můžete zadat UDP.

Potom použijte **portbindings** nastavení ve skupině **HostConfig** [kontejneru Docker vytvořit možnosti](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) mapovat exponovaný port v modulu na port na hostitelském zařízení. Pokud jste například vystavili port 8080 uvnitř modulu a chcete jej namapovat na port 80 hostitelského zařízení, budou možnosti vytvoření v souboru template.json vypadat jako následující příklad:

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

Jakmile stringified pro manifest nasazení, stejná konfigurace bude vypadat jako v následujícím příkladu:

```json
"createOptions": "{\"HostConfig\":{\"PortBindings\":{\"8080/tcp\":[{\"HostPort\":\"80\"}]}}}"
```

### <a name="restrict-module-memory-and-cpu-usage"></a>Omezení využití paměti modulu a procesoru

Můžete deklarovat, kolik prostředků hostitele modul může použít. Tento ovládací prvek je užitečné zajistit, že jeden modul nemůže spotřebovat příliš mnoho paměti nebo využití procesoru a zabránit spuštění jiných procesů v zařízení. Tato nastavení můžete spravovat pomocí [možností vytváření kontejnerů Dockeru](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) ve skupině **HostConfig,** včetně:

* **Paměť**: Limit paměti v bajtech. Například 268435456 bajtů = 256 MB.
* **MemorySwap:** Celkový limit paměti (paměť + swap). Například 536870912 bajtů = 512 MB
* **CpuPeriod**: Délka období procesoru v mikrosekundách. Výchozí hodnota je 100000, takže například hodnota 25000 omezuje kontejner na 25 % prostředků procesoru.

Ve formátu template.json budou tyto hodnoty vypadat jako v následujícím příkladu:

```json
"createOptions": {
  "HostConfig": {
    "Memory": 268435456,
    "MemorySwap": 536870912,
    "CpuPeriod": 25000
  }
}
```

Po stringified pro konečné nasazení manifestu, tyto hodnoty by vypadat v následujícím příkladu:

```json
"createOptions":"{\"HostConfig\":{\"Memory\":268435456,\"MemorySwap\":536870912,\"CpuPeriod\":25000}}"
```

## <a name="next-steps"></a>Další kroky

Další příklady možností vytvoření v akci najdete v následujících ukázkách ioT edge:

* [Vlastní vize a Azure IoT Edge na Raspberry Pi 3](https://github.com/Azure-Samples/Custom-vision-service-iot-edge-raspberry-pi)
* [Ukázka úložiště objektů blob Azure IoT Edge](https://github.com/Azure-Samples/azure-iotedge-blobstorage-sample)
