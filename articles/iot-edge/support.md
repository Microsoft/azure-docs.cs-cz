---
title: Podporované operační systémy, kontejnerové moduly – Azure IoT Edge
description: Zjistěte, které operační systémy mohou spustit démona Azure IoT Edge a modul runtime a podporované kontejnerové moduly pro vaše produkční zařízení.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/09/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: b17f1f32a3e49e9161afe92d62b85a162affcd9f
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/21/2021
ms.locfileid: "98630526"
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

![Modul Moby jako modul runtime kontejneru](./media/support/only-moby-for-production.png)
</center>

## <a name="operating-systems"></a>Operační systémy

Azure IoT Edge běží na většině operačních systémů, které můžou spouštět kontejnery. Ne všechny tyto systémy jsou ale stejně podporované. Operační systémy jsou seskupené do vrstev, které představují úroveň uživatelů podpory, kterou můžou očekávat.

* Systémy vrstvy 1 jsou podporovány. Pro systémy vrstvy 1 Microsoft:
  * má tento operační systém v automatizovaných testech
  * poskytuje instalační balíčky pro ně.
* Systémy vrstvy 2 jsou kompatibilní s Azure IoT Edge a je možné je používat relativně snadno. Pro systémy vrstvy 2:
  * Microsoft provedl neformální testování platforem nebo ví, že partner úspěšně běžel Azure IoT Edge na platformě.
  * Instalační balíčky pro jiné platformy můžou na těchto platformách fungovat.

Rodina hostitelského operačního systému musí vždy odpovídat rodině hostovaného operačního systému používaného uvnitř kontejneru modulu. Jinými slovy můžete používat pouze kontejnery Linux v kontejnerech Linux a Windows ve Windows. Při použití systému Windows jsou podporovány pouze izolované kontejnery, nikoli izolované kontejnery technologie Hyper-V.  

IoT Edge pro Linux ve Windows používá IoT Edge na virtuálním počítači se systémem Linux běžícím na hostiteli se systémem Windows. Tímto způsobem můžete spustit moduly Linux na zařízení s Windows.

### <a name="tier-1"></a>Vrstva 1

Systémy uvedené v následujících tabulkách jsou podporovány společností Microsoft, všeobecně dostupné nebo ve verzi Public Preview a testovány s každou novou verzí.

Azure IoT Edge podporuje moduly sestavené jako kontejnery pro Linux nebo Windows. Kontejnery Linux se dají nasadit na zařízení se systémem Linux nebo nasadit na zařízení s Windows pomocí IoT Edge pro Linux ve Windows. Kontejnery Windows se dají nasadit jenom na zařízení s Windows.

#### <a name="linux-containers"></a>Linuxové kontejnery

Moduly sestavené jako kontejnery Linux lze nasadit do zařízení se systémem Linux nebo Windows. V případě zařízení se systémem Linux se modul runtime IoT Edge nainstaluje přímo na hostitelském zařízení. U zařízení s Windows je virtuální počítač se systémem Linux předem sestavený s IoT Edge modulem runtime spuštěn na hostitelském zařízení.

IoT Edge pro Linux ve Windows je momentálně ve verzi Public Preview, ale je doporučený způsob, jak spustit IoT Edge na zařízeních s Windows.

| Operační systém | AMD | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| Malina Pi OS Stretch |  | ![Malina Pi OS Stretch + ARM32v7](./media/tutorial-c-module/green-check.png) |  |
| Ubuntu Server 16.04 | ![Ubuntu Server 16,04 + AMD64](./media/tutorial-c-module/green-check.png) |  | Verze Public Preview  |
| Ubuntu Server 18.04 | ![Ubuntu Server 18,04 + AMD64](./media/tutorial-c-module/green-check.png) |  | Verze Public Preview |
| Windows 10 Pro | Verze Public Preview |  |  |
| Windows 10 Enterprise | Verze Public Preview |  |  |
| Windows 10 IoT Enterprise | Verze Public Preview |  |  |
| Windows Server 2019 | Verze Public Preview |  |  |

Všechny operační systémy Windows musí být verze 1809 (Build 17763) nebo novější.

#### <a name="windows-containers"></a>Kontejnery Windows

Moduly sestavené jako kontejnery Windows se dají nasadit jenom na zařízení s Windows.

| Operační systém | AMD | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| Windows 10 IoT Enterprise | ![check1](./media/tutorial-c-module/green-check.png) |  |  |
| Windows 10 IoT Core<sup>1</sup><br> | ![check1](./media/tutorial-c-module/green-check.png) |  |  |
| Windows Server 2019  | ![check1](./media/tutorial-c-module/green-check.png) |  |  |
| Windows Server IoT 2019<br> | ![check1](./media/tutorial-c-module/green-check.png) |  |  |

<sup>1</sup> po 1.0.10 verze se nebude podporovat Windows 10 IoT Core.

Všechny operační systémy Windows musí být verze 1809 (Build 17763). Pro IoT Edge ve Windows se vyžaduje konkrétní sestavení Windows, protože verze kontejnerů Windows se musí přesně shodovat s verzí hostitelského zařízení s Windows. Kontejnery Windows aktuálně používají pouze Build 17763.

### <a name="tier-2"></a>Vrstva 2

Systémy uvedené v následující tabulce jsou považovány za kompatibilní s Azure IoT Edge, ale nejsou aktivně testovány nebo spravovány společností Microsoft.

| Operační systém | AMD | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| [CentOS 7.5](https://wiki.centos.org/Manuals/ReleaseNotes/CentOS7.1804) | ![CentOS + AMD64](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 8](https://www.debian.org/releases/jessie/) | ![Debian 8 a AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 8 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 8 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 9](https://www.debian.org/releases/stretch/) | ![Debian 9 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 10](https://www.debian.org/releases/buster/) | ![Debian 10 a AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Poradce pro Linux Embedded Linux – vývojové operační systém](https://www.mentor.com/embedded-software/linux/mel-flex-os/) | ![Poradce Embedded Linux Flex OS + AMD64](./media/tutorial-c-module/green-check.png) | ![Poradce Embedded Linux Flex OS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Poradce Embedded Linux Flex OS + ARM64](./media/tutorial-c-module/green-check.png) |
| [Poradce Embedded Linux Omni OS](https://www.mentor.com/embedded-software/linux/mel-omni-os/) | ![Poradce Embedded Linux Omni OS + AMD64](./media/tutorial-c-module/green-check.png) |  | ![Poradce Embedded Linux Omni OS + ARM64](./media/tutorial-c-module/green-check.png) |
| [RHEL 7.5](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/7.5_release_notes/index) | ![RHEL 7,5 + AMD64](./media/tutorial-c-module/green-check.png) | ![RHEL 7,5 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![RHEL 7,5 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 16.04](https://wiki.ubuntu.com/XenialXerus/ReleaseNotes) | ![Ubuntu 16,04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 16,04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 16,04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 18.04](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes) | ![Ubuntu 18,04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18,04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18,04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Wind River 8](https://docs.windriver.com/category/os-wind_river_linux) | ![Vítr řek 8 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Yocto](https://www.yoctoproject.org/) | ![Yocto + AMD64](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM64](./media/tutorial-c-module/green-check.png) |
| Malina Pi Buster OS |  | ![Malina Pi OS Buster + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Malina Pi OS Buster + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 20,04 <sup>1</sup>](https://wiki.ubuntu.com/FocalFossa/ReleaseNotes) | ![Ubuntu 20,04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 20,04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 20,04 + ARM64](./media/tutorial-c-module/green-check.png) |

<sup>1</sup> balíčky Debian 9 z [úložiště verzí Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases) by měly být ve Ubuntu 20,04 fungovat.

## <a name="releases"></a>Verze

Iotedge vydání a poznámky k verzi jsou k dispozici na stránce [verze Azure-](https://github.com/Azure/azure-iotedge/releases) . IoT Edge V této části jsou uvedeny informace o těchto poznámkách k verzi, které vám pomůžou vizualizovat součásti každé verze snadněji.

Součásti IoT Edge lze instalovat nebo aktualizovat jednotlivě a jsou zpětně kompatibilní s komponentami ze starších verzí. Následující tabulka uvádí komponenty zahrnuté v jednotlivých verzích:

| Vydat | Démon zabezpečení | Hraniční centrum<br>Agent Edge | Libiothsm | Moby |
|--|--|--|--|--|
| **1.0.10** | 1.0.10 | 1.0.10 | 1.0.10 |  |
| **1.0.9** | 1.0.9.5<br>1.0.9.4<br>1.0.9.3<br>1.0.9.2<br>1.0.9.1<br>1.0.9 | 1.0.9.5<br>1.0.9.4<br>1.0.9.3<br>1.0.9.2<br>1.0.9.1<br>1.0.9 | 1.0.9.5<br>1.0.9.4<br>1.0.9.3<br>1.0.9.2<br>1.0.9.1<br>1.0.9 |  |
| **1.0.8** | 1.0.8 | 1.0.8.5<br>1.0.8.4<br>1.0.8.3<br>1.0.8.2<br>1.0.8.1<br>1.0.8 | 1.0.8 | 3.0.6 |
| **1.0.7** | 1.0.7.1<br>1.0.7 | 1.0.7.1<br>1.0.7 | 1.0.7.1<br>1.0.7 | 3.0.5<br>3.0.4 (ARMv7hl, CentOS) |
| **1.0.6** | 1.0.6.1<br>1.0.6 | 1.0.6.1<br>1.0.6 | 1.0.6.1<br>1.0.6 |  |
| **1.0.5** | 1.0.5 | 1.0.5 | 1.0.5 | 3.0.2 |

IoT Edge používá sadu Microsoft. Azure. Devices. Client SDK. Další informace najdete v tématu [úložiště GitHub pro Azure IoT C# SDK](https://github.com/Azure/azure-iot-sdk-csharp) nebo [Azure SDK pro .NET – referenční obsah](/dotnet/api/overview/azure/iot/client). Následující seznam obsahuje verzi klientské sady SDK, pro kterou je každá verze testována na:

* **IoT Edge 1.0.10**: klientská sada SDK 1.28.0
* **IoT Edge 1.0.9**: klientská sada SDK 1.21.1
* **IoT Edge 1.0.8**: klientská sada SDK 1.20.3
* **IoT Edge 1.0.7**: klientská sada SDK 1.20.1
* **IoT Edge 1.0.6**: klientská sada SDK 1.17.1
* **IoT Edge 1.0.5**: klientská sada SDK 1.17.1

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
