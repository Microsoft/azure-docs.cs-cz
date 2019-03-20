---
title: Podporované operační systémy, moduly container - Azure IoT Edge | Dokumentace Microsoftu
description: Zjistěte, jaké operační systémy můžete spustit démona Azure IoT Edge a modulu runtime a modulů podporované kontejner pro vaše produkční zařízení
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/18/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 5bc133e81f9917aafb406a6bfb27922cdba48ef5
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2019
ms.locfileid: "58190001"
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
Azure IoT Edge běží na většině operačních systémů, které můžete spouštět kontejnery; však všechny tyto systémy nepodporují stejně. Operační systémy jsou seskupené do vrstev, které představují úroveň podpory, které uživatelé můžou očekávat.
* Systémy vrstvy 1 si lze představit jako oficiálně podporované. Pro systémy vrstvy 1, Microsoft:
    * má tento operační systém v automatizovaných testů
    * poskytne vám jejich instalačních balíčků
* Systémy vrstvy 2 si lze představit jako kompatibilní s Azure IoT Edge a je možné poměrně snadno. Pro systémy vrstvy 2:
    * Microsoft má provést testování ad hoc na platformách nebo ví partnerských úspěšně spuštěných na platformě Azure IoT Edge
    * Instalační balíčky pro jiné platformy může pracovat na těchto platformách
    
Řada hostitelský operační systém musí vždy odpovídat řady hostovaného operačního systému použít uvnitř kontejneru modulu. Jinými slovy můžete použít pouze Linuxové kontejnery v Linuxu a kontejnery Windows ve Windows. Při použití Windows pouze procesu, které jsou podporovány izolovaných kontejnerech, technologie Hyper-V izolované kontejnery.  

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
| Windows 10 IoT Core, build 17763 | Ano | Ne |
| Windows 10 IoT Enterprise, build 17763 | Ano | Ne |
| Windows Server 2019 | Ano | Ne |

Výše uvedenými operačními systémy Windows jsou uvedeny požadavky pro zařízení se systémem Windows kontejnery ve Windows. Tato konfigurace je jedinou podporovanou konfiguraci pro produkční prostředí. Instalační balíčky Azure IoT Edge pro Windows jim umožňují používat kontejnery Linuxu ve Windows; Nicméně tato konfigurace je pro vývoj a testování pouze. Použití kontejnery Linuxu ve Windows není podporovanou konfiguraci pro produkční prostředí. Všechny verze Windows 10 sestavení 14393 nebo novější a Windows Server 2016 nebo novější, je možné pro tento scénář vývoje.

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
Azure IoT Edge může běžet na virtuálních počítačích. Používání virtuálních počítačů jako IoT Edge zařízení je běžná v případě zákazníci chtějí rozšířit stávající infrastrukturu s hraniční inteligence. Řada hostitelský operační systém virtuálního počítače musí odpovídat řady hostovaného operačního systému použít uvnitř kontejneru modulu. Tento požadavek je stejný jako při spuštění přímo na zařízení Azure IoT Edge. Azure IoT Edge se nerozlišují základní technologie virtualizace a pracuje ve virtuálních počítačích s využitím podle platformy, jako je Hyper-V a vSphere.

<br>
<center>

![Azure IoT Edge ve virtuálním počítači](./media/support/edge-on-vm.png)
</center>

## <a name="minimum-system-requirements"></a>Minimální požadavky na systém
Azure IoT Edge skvělé fungování v zařízení malá jako Raspberry Pi3 na hardware serveru na podnikové úrovni. Volba ten správný hardware pro váš scénář závisí na jiné úlohy, které chcete spustit. Rozhodování konečné zařízení můžou být složité; vytváření prototypů řešení ale můžete snadno spustit na tradiční přenosné počítače nebo stolní počítače.

Prostředí při vytváření prototypů se pomoci výběr konečné zařízení. Otázek, na které byste měli zvážit, patří: 

* Kolik modulů jsou vaše úlohy?
* Kolik vrstvy vašeho moduly kontejnery sdílejí?
* V jakém jazyce jsou moduly vytvořeny? 
* Kolik dat bude moduly zpracovávat?
* Potřebují moduly žádný speciální hardware pro urychlení jejich pracovní vytížení?
* Jaké jsou požadované výkonové charakteristiky vašeho řešení?
* Co je vašemu rozpočtu hardwaru?
