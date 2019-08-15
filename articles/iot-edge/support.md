---
title: Podporované operační systémy, moduly container - Azure IoT Edge | Dokumentace Microsoftu
description: Zjistěte, jaké operační systémy můžete spustit démona Azure IoT Edge a modulu runtime a modulů podporované kontejner pro vaše produkční zařízení
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/13/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: fdecc34b49b853c34624cabf4688e0387c303a16
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2019
ms.locfileid: "68985804"
---
# <a name="azure-iot-edge-supported-systems"></a>Azure IoT Edge podporované systémy

Tento článek poskytuje podrobné informace o tom, které systémy a součásti podporuje IoT Edge, ať už jsou oficiálně nebo ve verzi Preview. 

Pokud při používání služby Azure IoT Edge nastanou problémy, existuje několik způsobů, jak hledat podporu. Pro podporu zkuste použít jeden z následujících kanálů:

**Zasílání zpráv o chybách** – většinou vývoj, která přejdou do produktu Azure IoT Edge se stane v projektu open source IoT Edge. Chyby mohou být zaznamenány na [stránky problémy](https://github.com/azure/iotedge/issues) projektu. Opravy rychle dostanou z projektu v k aktualizace produktu.

**Tým podpory zákazníků společnosti Microsoft** – uživatelé, kteří mají [plán podpory](https://azure.microsoft.com/support/plans/) můžete zapojení týmu podpory zákazníků společnosti Microsoft tak, že vytvoříte lístek podpory přímo z [webu Azure portal](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac).

**Funkce požadavků** – The Azure IoT Edge produktu sleduje žádosti o funkce prostřednictvím produktu [User Voice stránky](https://feedback.azure.com/forums/907045-azure-iot-edge).

## <a name="container-engines"></a>Moduly kontejneru

Moduly Azure IoT Edge jsou implementovány jako kontejnery, takže IoT Edge k jejich spuštění potřebuje modul pro kontejner. Společnost Microsoft poskytuje modul kontejneru moby-engine ke splnění tohoto požadavku. Tento modul kontejneru je založen na Moby open source projektu. Docker CE a Dockerem EE jsou ostatních vyhledávacích strojů oblíbených kontejneru. Jsou také založeny na open source projektu Moby a jsou kompatibilní s Azure IoT Edge. Microsoft poskytuje nejlepší podporu pro systémy, které používají tyto kontejnerové motory; Společnost Microsoft ale nemůže dodávat opravy pro problémy v nich. Z tohoto důvodu se společnost Microsoft doporučuje používat moby modul na provozní systémy.

<br>
<center>

![Moby jako modul runtime kontejneru](./media/support/only-moby-for-production.png)
</center>

## <a name="operating-systems"></a>Operační systémy
Azure IoT Edge běží na většině operačních systémů, které můžou spouštět kontejnery. všechny tyto systémy se ale nepodporují stejně. Operační systémy jsou seskupené do vrstev, které představují úroveň podpory, které uživatelé můžou očekávat.
* Systémy vrstvy 1 jsou podporovány. Pro systémy vrstvy 1 Microsoft:
    * má tento operační systém v automatizovaných testech
    * poskytne vám jejich instalačních balíčků
* Systémy vrstvy 2 jsou kompatibilní s Azure IoT Edge a je možné je používat relativně snadno. Pro systémy vrstvy 2:
    * Společnost Microsoft provedla ad hoc testování na platformách nebo ví, že partner úspěšně běžel Azure IoT Edge na platformě.
    * Instalační balíčky pro jiné platformy může pracovat na těchto platformách
    
Rodina hostitelského operačního systému musí vždy odpovídat rodině hostovaného operačního systému používaného uvnitř kontejneru modulu. Jinými slovy můžete používat pouze kontejnery Linux v kontejnerech Linux a Windows ve Windows. Při použití systému Windows jsou podporovány pouze izolované kontejnery, nikoli izolované kontejnery technologie Hyper-V.  

<br>
<center>

![Operační systém hostitele odpovídá hostovanému operačnímu systému](./media/support/edge-on-device.png)
</center>

### <a name="tier-1"></a>Úroveň 1

Systémy uvedené v následující tabulce jsou podporovány společností Microsoft, všeobecně dostupné nebo ve verzi Public Preview a testovány s každou novou verzí. 

| Operační systém | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| Raspbian stretch |  | ![Raspbian Stretch + ARM32v7](./media/tutorial-c-module/green-check.png) |  |
| Ubuntu Server 16.04 | ![Ubuntu Server 16,04 + AMD64](./media/tutorial-c-module/green-check.png) |  | Veřejná verze Preview  |
| Ubuntu Server 18.04 | ![Ubuntu Server 18,04 + AMD64](./media/tutorial-c-module/green-check.png) |  | Veřejná verze Preview |
| Windows 10 IoT Enterprise, Build 17763 | ![Windows 10 IoT Enterprise + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| Windows Server 2019, Build 17763 | ![Windows Server 2019 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| Windows Server IoT 2019, Build 17763 | ![Windows Server IoT 2019 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| Windows 10 IoT Core, Build 17763 | ![Windows IoT Core + AMD64](./media/tutorial-c-module/green-check.png) |  |  |


Operační systémy Windows uvedené výše jsou požadavky na zařízení, která používají kontejnery Windows ve Windows. Tato konfigurace je jedinou podporovanou konfigurací pro produkční prostředí. Instalační balíčky Azure IoT Edge pro Windows umožňují použití kontejnerů Linux v systému Windows. Tato konfigurace je však určena pouze pro vývoj a testování. Použití kontejnerů Linux ve Windows není podporovaná konfigurace pro produkční prostředí. Pro tento vývojový scénář se dají použít všechny verze Windows 10 Build 14393 nebo novější a Windows Server 2016 nebo novější.

### <a name="tier-2"></a>Úroveň 2

Systémy uvedené v následující tabulce jsou považovány za kompatibilní s Azure IoT Edge, ale nejsou aktivně testovány ani udržovány. 

| Operační systém | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| CentOS 7.5 | ![CentOS + AMD64](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM64](./media/tutorial-c-module/green-check.png) |
| Debian 8 | ![Debian 8 a AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 8 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 8 + ARM64](./media/tutorial-c-module/green-check.png) |
| Debian 9 | ![Debian 9 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM64](./media/tutorial-c-module/green-check.png) |
| Debian 10<sup>1</sup> | ![Debian 10 a AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM64](./media/tutorial-c-module/green-check.png) |
| RHEL 7.5 | ![RHEL 7,5 + AMD64](./media/tutorial-c-module/green-check.png) | ![RHEL 7,5 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![RHEL 7,5 + ARM64](./media/tutorial-c-module/green-check.png) |
| Ubuntu 16.04 | ![Ubuntu 16,04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 16,04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 16,04 + ARM64](./media/tutorial-c-module/green-check.png) |
| Ubuntu 18.04 | ![Ubuntu 18,04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18,04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18,04 + ARM64](./media/tutorial-c-module/green-check.png) |
| Větru řeka 8 | ![Vítr řek 8 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| Yocto | ![Yocto + AMD64](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM64](./media/tutorial-c-module/green-check.png) |
| Raspbian Buster<sup>1</sup> |  | ![Raspbian Buster + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Raspbian Buster + ARM64](./media/tutorial-c-module/green-check.png) |

<sup>1</sup> Debian 10 systémy, včetně Raspian Buster, používají verzi OpenSSL, kterou IoT Edge nepodporuje. K instalaci starší verze před instalací IoT Edge použijte následující příkaz: 

```bash
sudo apt-get install libssl1.0.2
```

## <a name="virtual-machines"></a>Virtuální počítače
Azure IoT Edge lze spustit na virtuálních počítačích. Použití virtuálního počítače jako zařízení IoT Edge je běžné, když zákazníci chtějí rozšiřovat stávající infrastrukturu pomocí Edge Intelligence. Rodina hostitelského operačního systému virtuálního počítače musí odpovídat rodině hostovaného operačního systému používaného uvnitř kontejneru modulu. Tento požadavek je stejný, jako když se Azure IoT Edge spustí přímo na zařízení. Azure IoT Edge nezávislá na základní virtualizační technologii a funguje na virtuálních počítačích využívajících platformy, jako je Hyper-V a vSphere.

<br>
<center>

![Azure IoT Edge ve virtuálním počítači](./media/support/edge-on-vm.png)
</center>

## <a name="minimum-system-requirements"></a>Minimální požadavky na systém
Azure IoT Edge běží skvěle na zařízeních, což je malé jako PI3 k hardwaru na úrovni serveru. Výběr správného hardwaru pro váš scénář závisí na úlohách, které chcete spustit. Rozhodování, že konečné rozhodnutí zařízení může být složité; Můžete však snadno spustit vytváření prototypů řešení na tradičních přenosných počítačích nebo na počítačích.

Prostředí při vytváření prototypů vám pomůže pořídit finální výběr zařízení. Mezi otázky, které byste měli vzít v úvahu, patří: 

* Kolik modulů je ve vašem zatížení?
* Kolik vrstev sdílí vaše moduly s kontejnery?
* V jakých jazycích jsou vytvořeny vaše moduly? 
* Kolik dat budou vaše moduly zpracovávat?
* Potřebují vaše moduly pro urychlení svých úloh specializovaný hardware?
* Jaké jsou požadované charakteristiky výkonu vašeho řešení?
* Co je váš hardwarový rozpočet?
