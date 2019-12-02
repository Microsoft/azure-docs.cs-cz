---
title: Podporované operační systémy, kontejnerové moduly – Azure IoT Edge
description: Zjistěte, které operační systémy mohou spustit démona Azure IoT Edge a modul runtime a podporované kontejnerové moduly pro vaše produkční zařízení.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/13/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 0d6b5e826720cf51d38b3bc3c2b87d274a2ea816
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2019
ms.locfileid: "74665878"
---
# <a name="azure-iot-edge-supported-systems"></a>Azure IoT Edge podporované systémy

Tento článek poskytuje podrobné informace o tom, které systémy a součásti podporuje IoT Edge, ať už jsou oficiálně nebo ve verzi Preview. 

Pokud při používání služby Azure IoT Edge nastanou problémy, existuje několik způsobů, jak hledat podporu. Pro podporu zkuste použít jeden z následujících kanálů:

**Hlášení chyb** – většina vývoje, která se do Azure IoT Edgeho produktu stane, se nachází v projektu IoT Edge open source. Chyby mohou být hlášeny na [stránce problémy](https://github.com/azure/iotedge/issues) v projektu. Opravy se rychle dostanou z projektu na aktualizace produktů.

**Tým zákaznické podpory Microsoftu** – uživatelé, kteří mají [plán podpory](https://azure.microsoft.com/support/plans/) , můžou tým podpory zákazníků společnosti Microsoft zahájit vytvořením lístku podpory přímo z [Azure Portal](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac).

**Požadavky na funkce** – Azure IoT Edge produkt sleduje požadavky funkcí prostřednictvím [hlasového telefonu uživatele](https://feedback.azure.com/forums/907045-azure-iot-edge)produktu.

## <a name="container-engines"></a>Moduly kontejneru

Moduly Azure IoT Edge jsou implementovány jako kontejnery, takže IoT Edge k jejich spuštění potřebuje modul pro kontejner. Společnost Microsoft poskytuje kontejnerový modul, Moby-Engine, aby splňoval tento požadavek. Tento modul kontejneru je založen na Moby open source projektu. Docker CE a Docker EE jsou další oblíbené moduly pro kontejnery. Jsou také založeny na open source projektu Moby a jsou kompatibilní s Azure IoT Edge. Microsoft poskytuje nejlepší podporu pro systémy, které používají tyto kontejnerové motory; Společnost Microsoft ale nemůže dodávat opravy pro problémy v nich. Z tohoto důvodu Microsoft doporučuje používat Moby v produkčních systémech.

<br>
<center>

![Moby jako modul runtime kontejneru](./media/support/only-moby-for-production.png)
</center>

## <a name="operating-systems"></a>Operační systémy
Azure IoT Edge běží na většině operačních systémů, které můžou spouštět kontejnery. všechny tyto systémy se ale nepodporují stejně. Operační systémy jsou seskupené do vrstev, které představují úroveň uživatelů podpory, kterou můžou očekávat.
* Systémy vrstvy 1 jsou podporovány. Pro systémy vrstvy 1 Microsoft:
    * má tento operační systém v automatizovaných testech
    * poskytuje instalační balíčky pro ně.
* Systémy vrstvy 2 jsou kompatibilní s Azure IoT Edge a je možné je používat relativně snadno. Pro systémy vrstvy 2:
    * Společnost Microsoft provedla ad hoc testování na platformách nebo ví, že partner úspěšně běžel Azure IoT Edge na platformě.
    * Instalační balíčky pro jiné platformy můžou na těchto platformách fungovat.
    
Rodina hostitelského operačního systému musí vždy odpovídat rodině hostovaného operačního systému používaného uvnitř kontejneru modulu. Jinými slovy můžete používat pouze kontejnery Linux v kontejnerech Linux a Windows ve Windows. Při použití systému Windows jsou podporovány pouze izolované kontejnery, nikoli izolované kontejnery technologie Hyper-V.  

<br>
<center>

operační systém ![se shoduje s operačním systémem hosta](./media/support/edge-on-device.png)
</center>

### <a name="tier-1"></a>Úroveň 1

Systémy uvedené v následující tabulce jsou podporovány společností Microsoft, všeobecně dostupné nebo ve verzi Public Preview a testovány s každou novou verzí. 

| Operační systém | AMD | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| Raspbian Stretch |  | ![Raspbian Stretch + ARM32v7](./media/tutorial-c-module/green-check.png) |  |
| [Ubuntu Server 16,04](https://wiki.ubuntu.com/XenialXerus/ReleaseNotes) | ![Ubuntu Server 16,04 + AMD64](./media/tutorial-c-module/green-check.png) |  | Verze Public Preview  |
| [Ubuntu Server 18,04](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes) | ![Ubuntu Server 18,04 + AMD64](./media/tutorial-c-module/green-check.png) |  | Verze Public Preview |
| [Windows 10 IoT Core](https://docs.microsoft.com/windows/iot-core/windows-iot-core), Build 17763 | ![Windows IoT Core + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows 10 IoT Enterprise](https://docs.microsoft.com/windows/iot-core/windows-iot-enterprise), Build 17763 | ![Windows 10 IoT Enterprise + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows Server 2019](https://docs.microsoft.com/windows-server/get-started-19/rel-notes-19), Build 17763 | ![Windows Server 2019 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows Server IoT 2019](https://docs.microsoft.com/windows/iot-core/windows-server), Build 17763 | ![Windows Server IoT 2019 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |


Operační systémy Windows uvedené výše jsou požadavky na zařízení, která používají kontejnery Windows ve Windows, což je jediná podporovaná konfigurace pro produkční prostředí. Instalační balíčky Azure IoT Edge pro Windows umožňují použití kontejnerů Linux v systému Windows. Tato konfigurace je však určena pouze pro vývoj a testování. Další informace najdete v tématu [použití IoT Edge ve Windows ke spouštění kontejnerů Linux](how-to-install-iot-edge-windows-with-linux.md).

### <a name="tier-2"></a>Úroveň 2

Systémy uvedené v následující tabulce jsou považovány za kompatibilní s Azure IoT Edge, ale nejsou aktivně testovány nebo spravovány společností Microsoft.

| Operační systém | AMD | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| [CentOS 7,5](https://wiki.centos.org/Manuals/ReleaseNotes/CentOS7.1804) | ![CentOS + AMD64](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 8](https://www.debian.org/releases/jessie/) | ![Debian 8 a AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 8 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 8 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 9](https://www.debian.org/releases/stretch/) | ![Debian 9 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 10](https://www.debian.org/releases/buster/) <sup>1</sup> | ![Debian 10 a AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Poradce pro Linux Embedded Linux – vývojové operační systém](https://www.mentor.com/embedded-software/linux/mel-flex-os/) | ![Poradce Embedded Linux Flex OS + AMD64](./media/tutorial-c-module/green-check.png) | ![Poradce Embedded Linux Flex OS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Poradce Embedded Linux Flex OS + ARM64](./media/tutorial-c-module/green-check.png) |
| [Poradce Embedded Linux Omni OS](https://www.mentor.com/embedded-software/linux/mel-omni-os/) | ![Poradce Embedded Linux Omni OS + AMD64](./media/tutorial-c-module/green-check.png) |  | ![Poradce Embedded Linux Omni OS + ARM64](./media/tutorial-c-module/green-check.png) |
| [RHEL 7,5](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/7.5_release_notes/index) | ![RHEL 7,5 + AMD64](./media/tutorial-c-module/green-check.png) | ![RHEL 7,5 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![RHEL 7,5 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 16,04](https://wiki.ubuntu.com/XenialXerus/ReleaseNotes) | ![Ubuntu 16,04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 16,04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 16,04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 18,04](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes) | ![Ubuntu 18,04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18,04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18,04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Vítr – řeka 8](https://docs.windriver.com/category/os-wind_river_linux) | ![Vítr řek 8 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Yocto](https://www.yoctoproject.org/) | ![Yocto + AMD64](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM64](./media/tutorial-c-module/green-check.png) |
| Raspbian Buster <sup>1</sup> |  | ![Raspbian Buster + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Raspbian Buster + ARM64](./media/tutorial-c-module/green-check.png) |

<sup>1</sup> Debian 10 systémy, včetně Raspian Buster, používají verzi OpenSSL, kterou IoT Edge nepodporuje. K instalaci starší verze před instalací IoT Edge použijte následující příkaz: 

```bash
sudo apt-get install libssl1.0.2
```

## <a name="virtual-machines"></a>Služba Virtual Machines
Azure IoT Edge lze spustit na virtuálních počítačích. Použití virtuálního počítače jako zařízení IoT Edge je běžné, když zákazníci chtějí rozšiřovat stávající infrastrukturu pomocí Edge Intelligence. Rodina hostitelského operačního systému virtuálního počítače musí odpovídat rodině hostovaného operačního systému používaného uvnitř kontejneru modulu. Tento požadavek je stejný, jako když se Azure IoT Edge spustí přímo na zařízení. Azure IoT Edge nezávislá na základní virtualizační technologii a funguje na virtuálních počítačích využívajících platformy, jako je Hyper-V a vSphere.

<br>
<center>

![Azure IoT Edge na virtuálním počítači](./media/support/edge-on-vm.png)
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
