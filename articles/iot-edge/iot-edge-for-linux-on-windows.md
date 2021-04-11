---
title: Co je Azure IoT Edge pro Linux ve Windows | Microsoft Docs
description: Přehled na zařízeních s Windows 10 můžete spouštět IoT Edge moduly pro Linux.
author: kgremban
manager: philmea
ms.reviewer: twarwick
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 01/20/2021
ms.author: kgremban
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 330eaf5c12372347917e9f3a4aeafb6a2088c592
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103492570"
---
# <a name="what-is-azure-iot-edge-for-linux-on-windows-preview"></a>Co je Azure IoT Edge pro Linux ve Windows (Preview)

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Azure IoT Edge pro Linux ve Windows umožňuje spouštět v nasazeních Windows IoT úlohy s kontejnery pro Linux spolu s aplikacemi pro Windows. Firmy, které využívají Windows IoT k napájení svých hraničních zařízení, teď můžou využívat výhod řešení pro nativní cloudové analýzy, která jsou sestavená v systému Linux.

IoT Edge pro Linux ve Windows funguje spuštěním virtuálního počítače se systémem Linux na zařízení se systémem Windows. Virtuální počítač se systémem Linux je dodáván předem s modulem runtime IoT Edge. Všechny IoT Edge moduly nasazené do zařízení běží ve virtuálním počítači. Aplikace systému Windows, které běží na hostitelském zařízení s Windows, můžou komunikovat s moduly běžícími na virtuálním počítači se systémem Linux.

[Začněte s Preview](how-to-install-iot-edge-on-windows.md) ještě dnes.

>[!NOTE]
>Vezměte prosím v úvahu náš [průzkum produktů](https://aka.ms/AzEFLOW-Registration) , abychom vám pomohli vylepšit Azure IoT Edge pro Linux ve Windows na základě vašich IoT Edge na pozadí a cílech. Tento průzkum můžete použít také k registraci do budoucích Azure IoT Edge pro Linux v oznámeních systému Windows.

## <a name="components"></a>Komponenty

IoT Edge pro Linux ve Windows používá následující komponenty k tomu, aby se mohly spouštět úlohy Linux a Windows spolu navzájem a bez problémů komunikovat:

* **Virtuální počítač se systémem Linux, na kterém běží Azure IoT Edge**: virtuální počítač pro Linux založený na operačním systému Microsoft první strany [Mariner](https://github.com/microsoft/CBL-Mariner) je sestaven s modulem runtime IoT Edge a ověřen jako prostředí s podporovanou vrstvou 1 pro IoT Edge úlohy.

* **Centrum pro správu systému Windows**: rozšíření IoT Edge pro centrum pro správu systému Windows usnadňuje instalaci, konfiguraci a diagnostiku IoT Edge na virtuálním počítači se systémem Linux. Centrum pro správu Windows může nasazovat IoT Edge pro Linux v systému Windows na místním zařízení nebo se může připojit k cílovým zařízením a vzdáleně je spravovat.

* **Microsoft Update**: Integration with Microsoft Update udržuje součásti prostředí Windows Runtime, virtuální počítač s Mariner Linux a IoT Edge aktuální.

![Virtuální počítač se systémem Windows a Linux běží paralelně, zatímco centrum pro správu systému Windows ovládá obě součásti.](./media/iot-edge-for-linux-on-windows/architecture-and-communication.png)

Obousměrná komunikace mezi procesem Windows a virtuálním počítačem se systémem Linux znamená, že procesy Windows můžou poskytovat uživatelská rozhraní nebo hardwarové proxy servery pro úlohy spuštěné v kontejnerech Linux.

## <a name="samples"></a>ukázky

IoT Edge pro Linux ve Windows zvýrazňuje interoperabilitu mezi součástmi systému Linux a Windows.

Ukázky, které ukazují komunikaci mezi aplikacemi Windows a IoT Edge moduly, najdete v tématu [ukázky pro Windows 10 IoT](https://github.com/microsoft/Windows-IoT-Samples).

## <a name="public-preview"></a>Verze Public Preview

IoT Edge pro Linux ve Windows je momentálně ve [verzi Public Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Procesy instalace a správy se mohou lišit od obecně dostupných funkcí.

## <a name="support"></a>Podpora

Použijte kanály podpory a zpětné vazby IoT Edge k získání pomoci s IoT Edge pro Linux ve Windows.

**Hlášení chyb** – chyby mohou být hlášeny na [stránce problémy](https://github.com/azure/iotedge/issues) v IoT Edge open source projektu. Chyby související s Azure IoT Edge pro Linux ve Windows se dají ohlásit na [stránce problémů iotedge-eFlow](https://github.com/azure/iotedge-eflow/issues).

**Tým zákaznické podpory Microsoftu** – uživatelé, kteří mají [plán podpory](https://azure.microsoft.com/support/plans/) , můžou tým podpory zákazníků společnosti Microsoft zahájit vytvořením lístku podpory přímo z [Azure Portal](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac).

**Požadavky na funkce** – Azure IoT Edge produkt sleduje požadavky funkcí prostřednictvím [hlasového telefonu uživatele](https://feedback.azure.com/forums/907045-azure-iot-edge)produktu.

## <a name="next-steps"></a>Další kroky

Podívejte se [na IoT Edge pro Linux ve Windows 10 IoT Enterprise](https://aka.ms/EFLOWPPC9) , kde najdete další informace a ukázku v akci.

Podle pokynů v části [instalace a zřízení Azure IoT Edge pro Linux na zařízení s Windows](how-to-install-iot-edge-on-windows.md) nastavte zařízení s IoT Edge pro Linux ve Windows.
