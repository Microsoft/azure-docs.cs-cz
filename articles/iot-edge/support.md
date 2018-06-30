---
title: Podpora platformy Azure IoT Edge | Microsoft Docs
description: Platformách podporovaných aplikací Azure IoT Edge
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 6/21/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 05a571c4491122ec5c7c35f6bccc4b8c332a4be2
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37130705"
---
# <a name="azure-iot-edge-support"></a>Podpora Azure IoT Edge
Existuje mnoho různých způsobů, jak získat podporu pro produkt Azure IoT okraj.

**Zasílání zpráv o chybách** – většina vývoj, která přejde do Azure IoT Edge produktu probíhá IoT Edge projektu open source. Může být ohlášeny chyby [stránky problémy](https://github.com/azure/iotedge/issues) projektu. Opravy rychle zkontrolujte cestě z projektu v aktualizace produktu.

**Tým podpory zákazníků společnosti Microsoft** – uživatelé, kteří mají [plán podpory](https://azure.microsoft.com/support/plans/) tým podpory zákazníků společnosti Microsoft můžete použít tak, že vytvoříte lístek podpory přímo z [portál Azure]( https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac).

**Funkce požadavky** – Azure IoT Edge produktu sleduje žádosti o funkce prostřednictvím produktu [User Voice stránky](https://feedback.azure.com/forums/907045-azure-iot-edge).

## <a name="operating-systems"></a>Operační systémy
Azure IoT Edge spouští na většině operačních systémů, které můžou běžet kontejnery; ale všechny podporované nejsou stejně. Operační systémy jsou seskupené do vrstvy, které představují úroveň podpory, které uživatelé mohou očekávat.

### <a name="tier-1"></a>Úroveň 1
Systémy vrstvy 1 můžete představit jako oficiálně podporován. To znamená, že společnosti Microsoft:
* má tyto operační systém v automatizované testy
* poskytuje instalační balíčky pro ně

Obecně k dispozici
* Ubuntu Server 18.04
* Ubuntu Server 16.04
* Raspbian-stretch

Ve verzi Public Preview
* Server Windows 10 1803
* Windows 10 IoT Enterprise (s duben 2018 aktualizace)
* Jádro IoT Windows 10 (s duben 2018 aktualizace)

### <a name="tier-2"></a>Úroveň 2
Systémy vrstvy 2 si lze představit jako kompatibilní s Azure IoT okraj a je možné poměrně snadno. To znamená, že:
* Společnost Microsoft provedla ad-hoc testování na platformách nebo zná partnerských úspěšném spuštění Azure IoT Edge na platformě
* Instalační balíčky pro jiné platformy může fungovat na těchto platformách

Ubuntu 18.04

Ubuntu 16.04

Oblasti větru 8

Yocto

Debian

Mac

## <a name="container-engines"></a>Kontejner moduly
Azure IoT okraj musí modul kontejneru spustit moduly, bez ohledu na operační systém, na kterém je spuštěný. Společnost Microsoft poskytuje modul kontejneru moby – modul ke splnění tohoto požadavku. Je založena na projekt Moby open source. Docker CE a Docker EE jsou ostatní moduly oblíbených kontejneru. Jejich jsou také založené na projekt se otevře source Moby a jsou kompatibilní s Azure IoT hranou. Společnost Microsoft poskytuje nejlepší úsilí podporu pro systémy s využitím těchto modulů kontejneru; Microsoft ale nemá schopnost dodávat opravy problémů v nich. Z tohoto důvodu společnost Microsoft doporučuje používat modul moby na produkční systémy.


<!-- Links -->
[lnk-edge-blog]: https://azure.microsoft.com/blog/securing-the-intelligent-edge/ 
