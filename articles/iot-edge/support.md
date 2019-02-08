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
ms.openlocfilehash: d8059ac4965ce5582b899ebc0d765e00ae9deb35
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2019
ms.locfileid: "55892792"
---
# <a name="azure-iot-edge-supported-systems"></a>Azure IoT Edge podporované systémy

Existuje řada různých způsobů, jak získat podporu pro produkt Azure IoT Edge.

**Zasílání zpráv o chybách** – většinou vývoj, která přejdou do produktu Azure IoT Edge se stane v projektu open source IoT Edge. Chyby mohou být zaznamenány na [stránky problémy](https://github.com/azure/iotedge/issues) projektu. Opravy rychle dostanou z projektu v k aktualizace produktu.

**Tým podpory zákazníků společnosti Microsoft** – uživatelé, kteří mají [plán podpory](https://azure.microsoft.com/support/plans/) můžete zapojení týmu podpory zákazníků společnosti Microsoft tak, že vytvoříte lístek podpory přímo z [webu Azure portal](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac).

**Funkce požadavků** – The Azure IoT Edge produktu sleduje žádosti o funkce prostřednictvím produktu [User Voice stránky](https://feedback.azure.com/forums/907045-azure-iot-edge).

## <a name="container-engines"></a>Moduly kontejneru
Azure IoT Edge musí modul container moduly spustit, protože jsou implementované jako kontejnery. Společnost Microsoft poskytuje modul kontejneru moby-engine ke splnění tohoto požadavku. Je založen na Moby open source projektu. Docker CE a Dockerem EE jsou ostatních vyhledávacích strojů oblíbených kontejneru. Tyto jsou také založeny na open source projektu Moby a musí být kompatibilní s Azure IoT Edge. Společnost Microsoft poskytuje nejvhodnější podporou pro systémy s využitím těchto modulů kontejneru; Microsoft nemá schopnost dodávat opravy problémů v nich. Z tohoto důvodu se společnost Microsoft doporučuje používat moby modul na provozní systémy.

<br>
<center>
![Moby jako kontejner modulu runtime](./media/support/only-moby-for-production.png)
</center>

## <a name="operating-systems"></a>Operační systémy
Azure IoT Edge běží na většině operačních systémů, které můžete spouštět kontejnery; ale všechny podporované nejsou stejně. Operační systémy jsou seskupené do vrstev, které představují úroveň podpory, které uživatelé můžou očekávat.
* Systémy vrstvy 1 si lze představit jako oficiálně podporované. To znamená, že Microsoft:
    * má tento operační systém v automatizovaných testů
    * poskytne vám jejich instalačních balíčků
* Systémy vrstvy 2 si lze představit jako kompatibilní s Azure IoT Edge a je možné poměrně snadno. To znamená, že:
    * Microsoft provedla ad-hoc testování na platformách nebo ví partnerských úspěšně spuštěných na platformě Azure IoT Edge
    * Instalační balíčky pro jiné platformy může pracovat na těchto platformách
    
Řada hostitelský operační systém musí vždy odpovídat řady hostovaného operačního systému použít uvnitř kontejneru modulu. Jinými slovy můžete použít pouze Linuxové kontejnery v Linuxu a kontejnery Windows ve Windows. Pokud používáte Windows, pouze samostatný proces kontejnery jsou podporované, ne technologie hyper-v izolovaných kontejnerech.  

<br>
<center>
![Hostitelského operačního systému odpovídá hostovaného operačního systému](./media/support/edge-on-device.png)
</center>

### <a name="tier-1"></a>Úroveň 1
Obecně dostupná
| Operační systém | AMD64 | ARM32v7 |
| ---------------- | ----- | ----- |
| Raspbian stretch | Ne | Ano|
| Ubuntu Server 16.04 | Ano | Ne |
| Ubuntu Server 18.04 | Ano | Ne |

Verze Public Preview
| Operační systém | AMD64 | ARM32v7 |
| ---------------- | ----- | ----- |
| Windows 10 IoT Core build 17763 | Ano | Ne |
| Windows 10 IoT Enterprise sestavení 17763 | Ano | Ne |
| Windows Server 2019 | Ano | Ne |

Operační systémy Windows výše jsou požadavky na zařízení, které se spustí kontejnery Windows na Windows. Toto je jedinou podporovanou konfiguraci pro produkční prostředí. Instalační balíčky Azure IoT Edge pro Windows jim umožňují používat kontejnery Linuxu ve Windows; je to ale pro vývoj a testování pouze. Použití kontejnery Linuxu ve Windows není podporovanou konfiguraci pro produkční prostředí. Všechny verze Windows 10 sestavení 14393 nebo novější a Windows Server 2016 nebo novější, je možné pro tento scénář vývoje.

### <a name="tier-2"></a>Úroveň 2

| Operační systém | AMD64 | ARM32v7 |
| ---------------- | ----- | ----- |
| CentOS 7.5 | Ano | Ano |
| Debian 8 | Ano | Ano |
| Debian 9 | Ano | Ano |
| RHEL 7.5 | Ano | Ano |
| Ubuntu 18.04 | Ano | Ano |
| Ubuntu 16.04 | Ano | Ano |
| Větru řeka 8 | Ano | Ne |
| Yocto | Ano | Ne |


## <a name="virtual-machines"></a>Virtuální počítače
Azure IoT Edge může běžet na virtuálních počítačích. To je běžné, pokud zákazníci chtějí rozšířit stávající infrastrukturu s hraniční inteligence. Řada hostitelský operační systém virtuálního počítače musí odpovídat řady hostovaného operačního systému použít uvnitř kontejneru modulu. To je stejný požadavek, když Azure IoT Edge běží přímo v zařízení. Azure IoT Edge se nerozlišují základní technologie virtualizace a pracuje ve virtuálních počítačích s využitím podle platformy, jako je Hyper-V a vSphere.

<br>
<center>
![Azure IoT Edge ve virtuálním počítači](./media/support/edge-on-vm.png)
</center>

## <a name="minimum-system-requirements"></a>Minimální požadavky na systém
Azure IoT Edge skvělé fungování v zařízení malá jako Raspberry Pi3 na hardware serveru na podnikové úrovni. Volba ten správný hardware pro váš scénář je závislé na jiné úlohy, které chcete spustit. Rozhodování konečné zařízení můžou být složité; vytváření prototypů řešení ale můžete snadno spustit na tradiční přenosné počítače nebo stolní počítače.

Prostředí při vytváření prototypů se pomoci výběr konečné zařízení. Zahrnují dotazy, které byste měli zvážit: kolik modulů zahrnuje úlohy, kolik vrstvy své moduly kontejnery sdílenou složku, v jakém jazyce jsou vaše modulů napsaných, kolik dat bude moduly zpracovávat, proveďte libovolný specializované moduly potřeba hardware pro urychlení jejich pracovní vytížení, jaké jsou požadované výkonové charakteristiky vašeho řešení, co je vašemu rozpočtu hardwaru?
