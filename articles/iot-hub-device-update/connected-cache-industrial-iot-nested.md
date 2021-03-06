---
title: Mezipaměť propojená Microsoftem v rámci Azure IoT Edge pro konfiguraci průmyslu IoT | Microsoft Docs
description: Kurz propojené mezipaměti Microsoftu v rámci kurzu konfigurace Azure IoT Edge pro průmyslový obor IoT
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 083c7bf6edc7da1fd617487e91b0a3848fb401fe
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2021
ms.locfileid: "107811806"
---
# <a name="microsoft-connected-cache-preview-deployment-scenario-sample-microsoft-connected-cache-within-an-azure-iot-edge-for-industrial-iot-configuration"></a>Ukázka scénáře nasazení Microsoft Internet Cache Preview: propojená mezipaměť Microsoftu v rámci Azure IoT Edge pro konfiguraci pro průmyslový IoT

Výrobní sítě jsou často organizovány v hierarchických vrstvách za [modelem Purdue](https://en.wikipedia.org/wiki/Purdue_Enterprise_Reference_Architecture) (zahrnuté v standardech [ISA 95](https://en.wikipedia.org/wiki/ANSI/ISA-95) a [ISA 99](https://www.isa.org/standards-and-publications/isa-standards/isa-standards-committees/isa99) ). V těchto sítích má připojení ke cloudu jenom nejvyšší vrstva a nižší vrstvy v hierarchii můžou komunikovat jenom s sousedními severními a jižními vrstvami.

Tato ukázka GitHubu [Azure IoT Edge pro průmyslové IoT](https://github.com/Azure-Samples/iot-edge-for-iiot)nasadí následující:

* Simulovaná Purdue síť v Azure
* Průmyslové prostředky 
* Hierarchie Azure IoT Edge bran
  
Tyto součásti budou použity k získání průmyslových dat a bezpečnému nahrání do cloudu, aniž by došlo k narušení zabezpečení sítě. Mezipaměť propojená Microsoftem se dá nasadit tak, aby podporovala stahování obsahu na všech úrovních v rámci sítě kompatibilní s ISA 95.

Klíč ke konfiguraci nasazení Microsoftem připojené mezipaměti v rámci sítě kompatibilní s ISA 95 konfiguruje proxy server *a* nadřazeného hostitele v bráně L3 IoT Edge.

1. Konfigurace nasazení s připojenou mezipamětí od Microsoftu na úrovni L5 a L4, jak je popsáno v ukázce Two-Level vnořená IoT Edge Gateway 
2. Nasazení na IoT Edge bráně L3 musí určovat:
   
   * UPSTREAM_HOST – IP adresa nebo plně kvalifikovaný název domény IoT Edge brány pro L4, které mezipaměť L3 (Microsoft Connected cache) bude požadovat obsah.
   * UPSTREAM_PROXY – IP adresa/plně kvalifikovaný název domény: PORT z proxy server OT.

3. Proxy server musí do povolených přidat plně kvalifikovaný název domény nebo IP adresu MCC L4.

Chcete-li ověřit, zda mezipaměť propojená Microsoftem funguje správně, spusťte následující příkaz v terminálu IoT Edge zařízení, hostování modulu nebo jakéhokoli zařízení v síti. Nahraďte \<Azure IoT Edge Gateway IP\> IP adresou nebo názvem hostitele brány IoT Edge. (informace o viditelnosti této sestavy najdete v tématu podrobnosti o proměnné prostředí).

```bash
    wget http://<L3 IoT Edge Gateway IP>/mscomtest/wuidt.gif?cacheHostOrigin=au.download.windowsupdate.com
```