---
title: Podpora platformy Azure IoT Edge | Dokumentace Microsoftu
description: Platformách podporovaných službou Azure IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/07/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 45c5c7245a2af3b0d0e328bfcc43112eaee406ee
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2018
ms.locfileid: "51564999"
---
# <a name="azure-iot-edge-support"></a>Podpora Azure IoT Edge
Existuje řada různých způsobů, jak získat podporu pro produkt Azure IoT Edge.

**Zasílání zpráv o chybách** – většinou vývoj, která přejdou do produktu Azure IoT Edge se stane v projektu open source IoT Edge. Chyby mohou být zaznamenány na [stránky problémy](https://github.com/azure/iotedge/issues) projektu. Opravy rychle dostanou z projektu v k aktualizace produktu.

**Tým podpory zákazníků společnosti Microsoft** – uživatelé, kteří mají [plán podpory](https://azure.microsoft.com/support/plans/) můžete zapojení týmu podpory zákazníků společnosti Microsoft tak, že vytvoříte lístek podpory přímo z [webu Azure portal](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac).

**Funkce požadavků** – The Azure IoT Edge produktu sleduje žádosti o funkce prostřednictvím produktu [User Voice stránky](https://feedback.azure.com/forums/907045-azure-iot-edge).

## <a name="operating-systems"></a>Operační systémy
Azure IoT Edge běží na většině operačních systémů, které můžete spouštět kontejnery; ale všechny podporované nejsou stejně. Operační systémy jsou seskupené do vrstev, které představují úroveň podpory, které uživatelé můžou očekávat.

### <a name="tier-1"></a>Úroveň 1
Systémy vrstvy 1 si lze představit jako oficiálně podporované. To znamená, že Microsoft:
* má tyto operační systém v automatizovaných testů
* poskytne vám jejich instalačních balíčků

Obecně dostupná
| Operační systém | AMD64 | ARM32 |
| ---------------- | ----- | ----- |
| Raspbian stretch | Ne | Ano|
| Ubuntu Server 16.04 | Ano | Ne |
| Ubuntu Server 18.04 | Ano | Ne |

Ve verzi Public Preview
| Operační systém | AMD64 | ARM32 |
| ---------------- | ----- | ----- |
| Windows 10 IoT Core (duben 2018 update) | Ano | Ne |
| Windows 10 IoT Enterprise (duben 2018 update) | Ano | Ne |
| Server Windows 10 verzi 1803 | Ano | Ne |

### <a name="tier-2"></a>Úroveň 2
Systémy vrstvy 2 si lze představit jako kompatibilní s Azure IoT Edge a je možné poměrně snadno. To znamená, že:
* Microsoft provedla ad-hoc testování na platformách nebo ví partnerských úspěšně spuštěných na platformě Azure IoT Edge
* Instalační balíčky pro jiné platformy může pracovat na těchto platformách

| Operační systém | AMD64 | ARM32 |
| ---------------- | ----- | ----- |
| CentOS 7.5 | Ano | Ano |
| Debian 8 | Ano | Ano |
| Debian 9 | Ano | Ano |
| RHEL 7.5 | Ano | Ano |
| Ubuntu 18.04 | Ano | Ano |
| Ubuntu 16.04 | Ano | Ano |
| Větru řeka 8 | Ano | Ne |
| Yocto | Ano | Ne |

## <a name="container-engines"></a>Moduly kontejneru
Azure IoT Edge musí modul container spustit moduly, bez ohledu na operační systém, na kterém je spuštěný. Společnost Microsoft poskytuje modul kontejneru moby-engine ke splnění tohoto požadavku. Je založen na Moby open source projektu. Docker CE a Dockerem EE jsou ostatních vyhledávacích strojů oblíbených kontejneru. Tyto jsou také založeny na Moby otevře zdrojový projekt a jsou kompatibilní s Azure IoT Edge. Společnost Microsoft poskytuje nejvhodnější podporou pro systémy s využitím těchto modulů kontejneru; Microsoft nemá schopnost dodávat opravy problémů v nich. Z tohoto důvodu se společnost Microsoft doporučuje používat moby modul na provozní systémy.

