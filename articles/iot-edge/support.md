---
title: Podporované operační systémy, moduly container - Azure IoT Edge | Dokumentace Microsoftu
description: Zjistěte, jaké operační systémy můžete spustit démona Azure IoT Edge a modulu runtime a modulů podporované kontejner pro vaše produkční zařízení
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/17/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 6443260de0a8bd8531edb303fa581d281034fef3
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2018
ms.locfileid: "53555604"
---
# <a name="azure-iot-edge-supported-systems"></a>Azure IoT Edge podporované systémy

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

Verze Public Preview
| Operační systém | AMD64 | ARM32 |
| ---------------- | ----- | ----- |
| Sestavení Windows 10 IoT Core 17763 | Ano | Ne |
| Windows 10 sestavení 17763 pro kontejnery Windows<br><br>Windows 10 sestavení 14393 nebo novější pro kontejnery Linuxu\* | Ano | Ne |
| Windows Server 2019 pro kontejnery Windows<br><br>Windows Server 2016 nebo novější pro kontejnery Linuxu\* | Ano | Ne |

\* Společnost Microsoft poskytuje pro kontejnery Linuxu na zařízeních s Windows pro vývoj a testování pouze instalační balíčky. Toto není podporovaná konfigurace pro použití v produkčním prostředí. 

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

