---
title: Konfigurace vydavatele Microsoft OPCe
description: V tomto kurzu se naučíte konfigurovat vydavatele OPC v samostatném režimu.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: 4d4f9c90fd96365216480164f29f08fad92eb9d0
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104787666"
---
# <a name="tutorial-configure-the-opc-publisher"></a>Kurz: Konfigurace vydavatele OPC

V tomto kurzu najdete informace o konfiguraci OPC vydavatele. Ke konfiguraci je možné použít několik rozhraní.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Konfigurace vydavatele OPC prostřednictvím konfiguračního souboru
> * Konfigurace vydavatele OPC pomocí argumentů příkazového řádku
> * Konfigurace vydavatele OPC prostřednictvím IoT Hub přímých metod
> * Konfigurace vydavatele OPC prostřednictvím cloudové služby s doprovodnou mikroslužbou REST

## <a name="configuring-security"></a>Konfigurace zabezpečení

IoT Edge poskytuje vydavateli OPC s konfigurací zabezpečení pro automatické přistupování k IoT Hub. Vydavatel OPC může také běžet jako samostatný kontejner Docker zadáním připojovacího řetězce zařízení pro přístup k IoT Hub přes `dc` parametr příkazového řádku. Je možné vytvořit zařízení pro IoT Hub a jeho připojovací řetězec načíst prostřednictvím Azure Portal.

Pro přístup k prostředkům s povoleným OPC UA jsou certifikáty X. 509 a jejich přidružené privátní klíče používány v OPC UA. Tato metoda se nazývá ověřování aplikací OPC UA a navíc k ověřování uživatelů OPC UA. OPC Publisher používá úložiště certifikátů založené na systému souborů ke správě všech certifikátů aplikací. Při spuštění OPC Publisher kontroluje, jestli existuje certifikát, který může použít v těchto úložištích certifikátů, a vytvoří nový certifikát podepsaný svým držitelem a nový přidružený privátní klíč, pokud není žádný. Certifikáty podepsané svým držitelem poskytují slabé ověřování, protože nejsou podepsány důvěryhodnou certifikační autoritou, ale aby bylo možné šifrování povolit, musí být alespoň komunikace s prostředkem OPC UA.

V konfiguračním souboru je povoleno zabezpečení prostřednictvím `"UseSecurity": true,` příznaku. Nejbezpečnější dostupný koncový bod na serverech OPC UA, ke kterému se má Vydavatel OPC připojit, se automaticky vybere.
Ve výchozím nastavení používá Vydavatel OPC anonymní ověřování uživatelů (kromě ověření aplikace popsané výše). Vydavatel OPC ale taky podporuje ověřování uživatelů pomocí uživatelského jména a hesla. Dá se zadat pomocí konfiguračního rozhraní REST API (popsané níže) nebo konfiguračního souboru takto:
```
"OpcAuthenticationMode": "UsernamePassword",
"OpcAuthenticationUsername": "usr",
"OpcAuthenticationPassword": "pwd",
```
Kromě toho Vydavatel OPC verze 2,5 a níže šifruje uživatelské jméno a heslo v konfiguračním souboru. Verze 2,6 a vyšší podporuje pouze uživatelské jméno a heslo ve formátu prostého textu. Tato funkce bude vylepšena v příští verzi aplikace OPC Publisher.

Aby bylo možné zachovat konfiguraci zabezpečení OPC vydavatele v rámci restartování, musí být certifikát a privátní klíč umístěný v adresáři úložiště certifikátů namapovány na IoT Edge hostitelský systém souborů operačního systému. Přečtěte si téma určení možností vytvoření kontejneru v Azure Portal výše.

## <a name="configuration-via-configuration-file"></a>Konfigurace prostřednictvím konfiguračního souboru

Nejjednodušší způsob, jak nakonfigurovat vydavatele OPC, je prostřednictvím konfiguračního souboru. Ukázkový konfigurační soubor a dokumentace týkající se jeho formátu je poskytována prostřednictvím souboru [`publishednodes.json`](https://raw.githubusercontent.com/Azure/iot-edge-opc-publisher/master/opcpublisher/publishednodes.json) v tomto úložišti.
Syntaxe konfiguračního souboru se v průběhu času změnila a OPC Publisher pořád umí číst staré formáty, ale při zachování konfigurace je převede do nejnovějšího formátu, a to v automatizovaném režimu.

Základní konfigurační soubor vypadá takto:
```
[
  {
    "EndpointUrl": "opc.tcp://testserver:62541/Quickstarts/ReferenceServer",
    "UseSecurity": true,
    "OpcNodes": [
      {
        "Id": "i=2258",
        "OpcSamplingInterval": 2000,
        "OpcPublishingInterval": 5000,
        "DisplayName": "Current time"
      }
    ]
  }
]
```

Prostředky OPC UA optimalizuje šířku pásma tím, že se změny dat odesílají jenom v případě, že se data změní jenom na OPC Publisher. Pokud je potřeba změny dat publikovat častěji nebo v pravidelných intervalech, vydavatel OPC podporuje "prezenční signál" pro každou konfigurovanou datovou položku, kterou je možné povolit, a to tak, že v konfiguraci položky dat zadáte HeartbeatInterval klíč. Interval se zadává v sekundách:
```
 "HeartbeatInterval": 3600,
```

Prostředek OPC UA vždycky pošle aktuální hodnotu datové položky, když se k ní připojí Vydavatel OPC. Chcete-li zabránit publikování těchto dat do IoT Hub, může být klíč SkipFirst také zadán v konfiguraci položky dat:
```
 "SkipFirst": true,
```

Obě nastavení lze povolit globálně prostřednictvím možností příkazového řádku.

## <a name="configuration-via-command-line-arguments"></a>Konfigurace pomocí argumentů příkazového řádku

K dispozici je několik argumentů příkazového řádku, které lze použít k nastavení globálního nastavení pro vydavatele OPC. Jsou zde popsány [zde](reference-command-line-arguments.md).


## <a name="configuration-via-the-built-in-opc-ua-server-interface"></a>Konfigurace prostřednictvím integrovaného rozhraní serveru OPC UA

>[!NOTE] 
> Tato funkce je k dispozici pouze ve verzi 2,5 a níže v OPC Publisher. * *

Vydavatel OPC má integrovaný server OPC UA, který běží na portu 62222. Implementuje tři metody OPC UA:

  - PublishNode
  - UnpublishNode
  - GetPublishedNodes

K tomuto rozhraní je možné přistupovat pomocí klientské aplikace OPC UA, například [UA expert](https://www.unified-automation.com/products/development-tools/uaexpert.html).

## <a name="configuration-via-iot-hub-direct-methods"></a>Konfigurace prostřednictvím IoT Hub přímých metod

>[!NOTE] 
> Tato funkce je k dispozici pouze ve verzi 2,5 a níže v OPC Publisher. * *

Vydavatel OPC implementuje následující [IoT Hub přímé metody](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-direct-methods), které je možné volat z aplikace (odkudkoli na světě) pomocí [sady SDK pro IoT Hub zařízení](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks):

  - PublishNodes
  - UnpublishNodes
  - UnpublishAllNodes
  - GetConfiguredEndpoints
  - GetConfiguredNodesOnEndpoint
  - GetDiagnosticInfo
  - GetDiagnosticLog
  - GetDiagnosticStartupLog
  - ExitApplication
  - GetInfo

K dispozici je [ukázková konfigurační aplikace](https://github.com/Azure-Samples/iot-edge-opc-publisher-nodeconfiguration) a [aplikace pro čtení diagnostických informací](https://github.com/Azure-Samples/iot-edge-opc-publisher-diagnostics) z open source vydavatele OPC, který využívá toto rozhraní.

## <a name="configuration-via-cloud-based-companion-rest-microservice"></a>Konfigurace prostřednictvím cloudu, doprovodných mikroslužeb na bázi REST

>[!NOTE] 
> Tato funkce je k dispozici pouze ve verzi 2,6 a vyšší než OPC Publisher.

Cloudová a doprovodná mikroslužba s rozhraním REST je popsána a k dispozici [zde](https://github.com/Azure/Industrial-IoT/blob/master/docs/services/publisher.md). Dá se použít ke konfiguraci vydavatele OPC prostřednictvím rozhraní kompatibilního s OpenAPI, například pomocí Swagger.

## <a name="configuration-of-the-simple-json-telemetry-format-via-separate-configuration-file"></a>Konfigurace jednoduchého formátu telemetrie JSON prostřednictvím samostatného konfiguračního souboru

>[!NOTE] 
> Tato funkce je k dispozici pouze ve verzi 2,5 a níže v OPC Publisher.

OPC Publisher umožňuje filtrovat části nestandardizovaného a jednoduchého formátu telemetrie prostřednictvím samostatného konfiguračního souboru, který lze zadat pomocí možnosti příkazového řádku TC. Pokud není zadaný žádný konfigurační soubor, pošle se na IoT Hub úplný formát telemetrie JSON. Formát samostatného konfiguračního souboru telemetrie je popsaný [tady](reference-opc-publisher-telemetry-format.md#opc-publisher-telemetry-configuration-file-format).

## <a name="next-steps"></a>Další kroky
Teď, když jste nakonfigurovali vydavatele OPC, je dalším krokem postup, jak vyladit výkon a paměť modulu Edge:

> [!div class="nextstepaction"]
> [Optimalizace výkonu a paměti](tutorial-publisher-performance-memory-tuning-opc-publisher.md)