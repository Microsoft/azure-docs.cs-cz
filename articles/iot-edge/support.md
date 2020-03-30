---
title: Podporované operační systémy, kontejnerové motory – Azure IoT Edge
description: Zjistěte, které operační systémy můžou spouštět daemon a runtime Azure IoT Edge a podporované kontejnerové motory pro vaše produkční zařízení.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/06/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 42c0a5d0c590f8c395c2afe366a00fcb9c83ce46
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536934"
---
# <a name="azure-iot-edge-supported-systems"></a>Podporované systémy Azure IoT Edge

Tento článek obsahuje podrobnosti o tom, které systémy a součásti jsou podporovány ioT Edge, ať už oficiálně nebo ve verzi Preview.

Pokud při používání služby Azure IoT Edge dochází k problémům, existuje několik způsobů, jak vyhledat podporu. Zkuste podporu jedním z následujících kanálů:

**Vytváření sestav –** většina vývoje, který přejde do produktu Azure IoT Edge se stane v projektu open source IoT Edge. Chyby mohou být hlášeny na [stránce problémy](https://github.com/azure/iotedge/issues) projektu. Opravy rychle, aby jejich cestu z projektu do aktualizací produktu.

**Tým zákaznické podpory Microsoftu** – uživatelé, kteří mají [plán podpory,](https://azure.microsoft.com/support/plans/) mohou zapojit tým zákaznické podpory Microsoftu vytvořením lístku podpory přímo z [portálu Azure](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac).

**Požadavky na funkce** – produkt Azure IoT Edge sleduje požadavky na funkce prostřednictvím [stránky User Voice](https://feedback.azure.com/forums/907045-azure-iot-edge)produktu .

## <a name="container-engines"></a>Kontejnerové motory

Moduly Azure IoT Edge se implementují jako kontejnery, takže IoT Edge potřebuje k jejich spuštění modul kontejneru. Společnost Microsoft poskytuje kontejnerový modul moby-engine ke splnění tohoto požadavku. Tento modul kontejneru je založen na projektu moby open source. Docker CE a Docker EE jsou další oblíbené kontejnerové motory. Jsou také založené na projektu moby open source a jsou kompatibilní s Azure IoT Edge. Společnost Microsoft poskytuje maximální podporu úsilí pro systémy používající tyto kontejnerové motory. Společnost Microsoft však nemůže doručovat opravy problémů v nich. Z tohoto důvodu společnost Microsoft doporučuje používat moby-engine ve výrobních systémech.

<br>
<center>

![Motor Moby jako modul runtime kontejneru](./media/support/only-moby-for-production.png)
</center>

## <a name="operating-systems"></a>Operační systémy

Azure IoT Edge běží na většině operačních systémů, které můžou spouštět kontejnery. ne všechny tyto systémy jsou však podporovány stejně. Operační systémy jsou seskupeny do úrovní, které představují úroveň podpory, kterou mohou uživatelé očekávat.

* Systémy úrovně 1 jsou podporovány. Pro systémy úrovně 1, Microsoft:
  * má tento operační systém v automatizovaných testech
  * poskytuje instalační balíčky pro ně
* Systémy Úrovně 2 jsou kompatibilní s Azure IoT Edge a lze je používat poměrně snadno. Pro systémy úrovně 2:
  * Microsoft provedl neformální testování na platformách nebo ví o partnerovi, který na platformě úspěšně používá Azure IoT Edge
  * Instalační balíčky pro jiné platformy mohou na těchto platformách fungovat

Rodina hostitelského operačního operačního modulu musí vždy odpovídat rodině hostovaného operačního modulu používaného uvnitř kontejneru modulu. Jinými slovy, můžete používat pouze linuxové kontejnery v kontejnerech Linux a Windows v systému Windows. Při použití systému Windows jsou podporovány pouze proces izolované kontejnery, nikoli Hyper-V izolované kontejnery.  

<br>
<center>

![Hostitelský operační shody hostovaný operační spoje](./media/support/edge-on-device.png)
</center>

### <a name="tier-1"></a>Vrstva 1

Systémy uvedené v následující tabulce jsou podporovány společností Microsoft, obvykle dostupné nebo ve verzi Public Preview, a jsou testovány s každou novou verzí. 

| Operační systém | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| Raspbian Stretch |  | ![Raspbian Stretch + ARM32v7](./media/tutorial-c-module/green-check.png) |  |
| [Ubuntu Server 16.04](https://wiki.ubuntu.com/XenialXerus/ReleaseNotes) | ![Ubuntu Server 16.04 + AMD64](./media/tutorial-c-module/green-check.png) |  | Veřejná verze Preview  |
| [Ubuntu Server 18.04](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes) | ![Ubuntu Server 18.04 + AMD64](./media/tutorial-c-module/green-check.png) |  | Veřejná verze Preview |
| [Windows 10 IoT Core](https://docs.microsoft.com/windows/iot-core/windows-iot-core), sestavení 17763 | ![Windows IoT Core + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows 10 IoT Enterprise](https://docs.microsoft.com/windows/iot-core/windows-iot-enterprise), sestavení 17763 | ![Windows 10 IoT Enterprise + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows Server 2019](https://docs.microsoft.com/windows-server/get-started-19/rel-notes-19), sestavení 17763 | ![Windows Server 2019 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows Server IoT 2019](https://docs.microsoft.com/windows/iot-core/windows-server), sestavení 17763 | ![Windows Server IoT 2019 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |

Operační systémy Windows uvedené výše jsou požadavky na zařízení, která používají kontejnery systému Windows v systému Windows, což je jediná podporovaná konfigurace pro produkční prostředí. Instalační balíčky Azure IoT Edge pro Windows umožňují použití linuxových kontejnerů v systému Windows. Tato konfigurace je však pouze pro vývoj a testování. Další informace najdete [v tématu Použití IoT Edge ve Windows ke spuštění linuxových kontejnerů](how-to-install-iot-edge-windows-with-linux.md).

### <a name="tier-2"></a>Vrstva 2

Systémy uvedené v následující tabulce jsou považovány za kompatibilní s Azure IoT Edge, ale nejsou aktivně testovány nebo spravovány společností Microsoft.

| Operační systém | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| [CentOS 7.5](https://wiki.centos.org/Manuals/ReleaseNotes/CentOS7.1804) | ![CentOS + AMD64](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 8](https://www.debian.org/releases/jessie/) | ![Debian 8 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 8 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 8 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 9](https://www.debian.org/releases/stretch/) | ![Debian 9 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 10](https://www.debian.org/releases/buster/) <sup>1</sup> | ![Debian 10 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Mentor Embedded Linux Flex OS](https://www.mentor.com/embedded-software/linux/mel-flex-os/) | ![Mentor Embedded Linux Flex OS + AMD64](./media/tutorial-c-module/green-check.png) | ![Mentor Embedded Linux Flex OS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Mentor Embedded Linux Flex OS + ARM64](./media/tutorial-c-module/green-check.png) |
| [Mentor Embedded Linux Omni OS](https://www.mentor.com/embedded-software/linux/mel-omni-os/) | ![Mentor Embedded Linux Omni OS + AMD64](./media/tutorial-c-module/green-check.png) |  | ![Mentor Embedded Linux Omni OS + ARM64](./media/tutorial-c-module/green-check.png) |
| [RHEL 7.5](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/7.5_release_notes/index) | ![RHEL 7,5 + AMD64](./media/tutorial-c-module/green-check.png) | ![RHEL 7,5 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![RHEL 7,5 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 16.04](https://wiki.ubuntu.com/XenialXerus/ReleaseNotes) | ![Ubuntu 16.04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 16.04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 16.04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 18.04](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes) | ![Ubuntu 18.04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18.04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18.04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Wind River 8](https://docs.windriver.com/category/os-wind_river_linux) | ![Větrná řeka 8 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Yocto](https://www.yoctoproject.org/) | ![Yocto + AMD64](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM64](./media/tutorial-c-module/green-check.png) |
| Raspbian Buster <sup>1</sup> |  | ![Raspbian Buster + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Raspbian Buster + ARM64](./media/tutorial-c-module/green-check.png) |

<sup>1</sup> Systémy Debian10, včetně Raspian Buster, používají verzi OpenSSL, kterou IoT Edge nepodporuje. Pomocí následujícího příkazu nainstalujte starší verzi před instalací ioT Edge:

```bash
sudo apt-get install libssl1.0.2
```

## <a name="releases"></a>Vydání

Datové zdroje verze IoT Edge a poznámky k verzi jsou k dispozici na stránce [azure-iotedge vydání.](https://github.com/Azure/azure-iotedge/releases) Tato část odráží informace z těchto poznámek k verzi, které vám pomohou snadněji vizualizovat součásti každé verze.

Komponenty IoT Edge lze nainstalovat nebo aktualizovat jednotlivě a jsou zpětně kompatibilní s komponentami ze starších verzí. V následující tabulce jsou uvedeny součásti obsažené v každé verzi:

| Vydat   | Bezpečnostní daemon  | Hraniční rozbočovač<br>Okrajový agent | Libiothismus | Moby  |
| --------- | ---------------- | ---------------------- | --------- | ----- |
| **1.0.9** | 1.0.9            | 1.0.9                  | 1.0.9     |       |
| **1.0.8** | 1.0.8            | 1.0.8.5<br>1.0.8.4<br>1.0.8.3<br>1.0.8.2<br>1.0.8.1<br>1.0.8 | 1.0.8 | 3.0.6 |
| **1.0.7** | 1.0.7.1<br>1.0.7 | 1.0.7.1<br>1.0.7       | 1.0.7.1<br>1.0.7 | 3.0.5<br>3.0.4 (ARMv7hl, CentOS) |
| **1.0.6** | 1.0.6.1<br>1.0.6 | 1.0.6.1<br>1.0.6       | 1.0.6.1<br>1.0.6 |  |
| **1.0.5** | 1.0.5            | 1.0.5                  | 1.0.5     | 3.0.2 |

IoT Edge používá sadu Microsoft.Azure.Device.Client SDK. Další informace najdete v [tématu Azure IoT C# SDK GitHub úložiště](https://github.com/Azure/azure-iot-sdk-csharp) nebo [Azure SDK pro .NET referenční obsah](https://docs.microsoft.com/dotnet/api/overview/azure/iot/client?view=azure-dotnet). V následujícím seznamu je uvedena verze sady SDK klienta, proti které je každá verze testována:

* **IoT Edge 1.0.9**: Sada SDK klienta 1.21.1
* **IoT Edge 1.0.8**: Sada SDK klienta 1.20.3
* **IoT Edge 1.0.7**: Sada SDK klienta 1.20.1
* **IoT Edge 1.0.6**: Sada SDK klienta 1.17.1
* **IoT Edge 1.0.5**: Sada SDK klienta 1.17.1

## <a name="virtual-machines"></a>Virtuální počítače

Azure IoT Edge se dá spouštět ve virtuálních počítačích. Použití virtuálního počítače jako zařízení IoT Edge je běžné, když zákazníci chtějí rozšířit stávající infrastrukturu o hraniční inteligenci. Rodina hostitelského operačního operačního modulu virtuálního soudu se musí shodovat s rodinou hostovaného operačního modulu použitého uvnitř kontejneru modulu. Tento požadavek je stejný jako při Azure IoT Edge se spouštějí přímo na zařízení. Azure IoT Edge je agnostik základní virtualizační technologie a funguje ve virtuálních počítačích poháněných platformami, jako je Hyper-V a vSphere.

<br>
<center>

![Azure IoT Edge ve virtuálním počítači](./media/support/edge-on-vm.png)
</center>

## <a name="minimum-system-requirements"></a>Minimální systémové požadavky

Azure IoT Edge funguje skvěle na zařízeních tak malých, jako je Raspberry Pi3 na hardware třídy serveru. Výběr správného hardwaru pro váš scénář závisí na úlohách, které chcete spustit. Konečné rozhodnutí o zařízení může být komplikované; můžete však snadno začít vytvářet prototypy řešení na tradičních noteboocích nebo stolních počítačích.

Zkušenosti při vytváření prototypů vám pomohou vést konečný výběr zařízení. Otázky, které byste měli zvážit, zahrnují:

* Kolik modulů je ve vaší pracovní zátěži?
* Kolik vrstev sdílejí kontejnery modulů?
* V jakém jazyce jsou vaše moduly napsány?
* Kolik dat budou vaše moduly zpracovávat?
* Potřebují vaše moduly nějaký specializovaný hardware pro urychlení jejich úloh?
* Jaké jsou požadované výkonové charakteristiky vašeho řešení?
* Jaký je váš rozpočet hardwaru?
