---
title: Instalace Microsoft Azure StorSimple 8100 zařízení | Dokumentace Microsoftu
description: Popisuje, jak jeho usazení do racku a zapojení kabeláže zařízení StorSimple 8100, než můžete nasadit a nakonfigurovat software.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: 6098a01e-c031-488a-a8d7-0b607ce665e1
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 5fbc407a9792d033037fdaa2b14f4055d94c15ab
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38701938"
---
# <a name="unpack-rack-mount-and-cable-your-storsimple-8100-device"></a>Rozbalení připojení do racku a zapojení kabeláže zařízení StorSimple 8100
## <a name="overview"></a>Přehled
Vaše Microsoft Azure StorSimple 8100 je jeden skříň, skříňová zařízení. Tento kurz vysvětluje, jak rozbalit, do racku a hardwaru zařízení StorSimple 8100 kabel před konfigurací a nasazení zařízení StorSimple.

## <a name="unpack-your-storsimple-8100-device"></a>Rozbalit zařízení StorSimple 8100
Následující kroky obsahují jasné, podrobné pokyny o tom, jak rozbalit úložného zařízení StorSimple 8100. Toto zařízení se posílá do jednoho pole.

### <a name="prepare-to-unpack-your-device"></a>Příprava na jeho usazení do zařízení
Před jeho usazení do zařízení, zkontrolujte následující informace.

![Ikona upozornění](./media/storsimple-safety/IC740879.png)![Tučná tloušťka ikonu](./media/storsimple-8100-hardware-installation/HCS_HeavyWeight_Icon.png) **upozornění!**

1. Ujistěte se, že máte dvou osob, které jsou k dispozici ke správě váhu přílohu, pokud je její zpracování ručně. Plně nakonfigurovaného skříně můžete zvážit až 32 kg (70 lbs.).
2. Umístěte pole na povrch bez stromové struktury, úroveň.

Dále proveďte následující kroky k jeho usazení do vašeho zařízení.

#### <a name="to-unpack-your-device"></a>Rozbalte vaše zařízení
1. Zkontrolujte pole a balení maskota crushes, kusy, vody poškození nebo jiných zřejmé poškození. Pokud pole nebo balení je vážně poškozen, neotevírejte pole. Prosím [obraťte se na Microsoft Support](storsimple-8000-contact-microsoft-support.md) umožňují posoudit, jestli je zařízení v dobrém stavu.
2. Rozbalte pole. Následující obrázek ukazuje rozbalené zobrazení zařízení StorSimple.
   
     ![Rozbalit zařízení úložiště](./media/storsimple-8100-hardware-installation/HCSUnpackyour2Udevice.png)
   
    **Rozbalené zobrazení vašeho zařízení úložiště**
   
   | Štítek | Popis |
   | --- | --- |
   |   1 |Balení pole |
   |   2 |Dolní maskota |
   |   3 |Zařízení |
   |   4 |Začátek maskota |
   |   5 |Příslušenství pole |
3. Po rozbalení pole, ujistěte se, že máte:
   
   * 1 jeden skříň zařízení
   * 2 napájecích kabelů
   * 1 kříženého kabelu Ethernet
   * 2 kabely konzoly sériového portu
   * 1 sériové USB převaděč pro sériového přístupu
   * 1 šroubovák T10 odolného proti
   * 4 QSFP-na-SFP + adaptéry pro použití s rozhraní sítí 10 GbE
   * 1 namontovat kit (2 na straně rails s připojením hardwaru)
   * Dokumentace k získávání spuštěno
     
     Pokud jste některá z položek uvedených výše, neobdrželi [obraťte se na Microsoft Support](storsimple-8000-contact-microsoft-support.md).

Dalším krokem je namontovat zařízení.

## <a name="rack-mount-your-storsimple-8100-device"></a>Namontovat zařízení StorSimple 8100
Postupujte podle dalších kroků při instalaci úložného zařízení StorSimple 8100 ve standardní stojanu 19 palce na přední a zadní příspěvky. Zařízení StorSimple 8100 má jeden primární skříň.

Instalace se skládá z několika kroků, z nichž každý je popsána v následujících postupech.

> [!IMPORTANT]
> Musí být zařízení StorSimple skříňová pro správný provoz.
> 
> 

### <a name="prepare-the-site"></a>Příprava webu
Zařízení musí být nainstalovány ve standardní 19 palec stojanu, který má přední a zadní příspěvky. Pomocí následujícího postupu připravte instalaci stojanu.

#### <a name="to-prepare-the-site-for-rack-installation"></a>Příprava rack instalace lokality
1. Ujistěte se, že zařízení bezpečně postavená na bez stromové struktury, stabilní a úrovni pracovní plochy (nebo podobnou).
2. Ověřte, zda má lokality, kde máte v úmyslu nastavit standardní napájení z nezávislých zdroje nebo jednotka distribuci napájení (PDU) rack s nepřerušitelný zdroj napájení (UPS).
3. Ujistěte se, že tento jeden slot 2 u na stojan, ve které máte v úmyslu připojení zařízení k dispozici.

![Ikona upozornění](./media/storsimple-safety/IC740879.png)![Tučná tloušťka ikonu](./media/storsimple-8100-hardware-installation/HCS_HeavyWeight_Icon.png) **upozornění!**

Ujistěte se, že máte dvou osob, které jsou k dispozici ke správě váha, pokud se zpracování nastavení zařízení ručně. Plně nakonfigurovaného skříně můžete zvážit až 32 kg (70 lbs.).

### <a name="rack-prerequisites"></a>Požadavky do racku
Zařízení 8100 je určen při instalaci ve standardní rack 19 palec CAB pomocí:

* Minimální hloubka 27.84 palcích od rack příspěvku k příspěvku
* Maximální tloušťka 32 kg pro zařízení
* Maximální protitlak z 5 Pascal (0,5 mm vody měřidla).

### <a name="rack-mounting-rail-kit"></a>Sada lišty montáže do racku
Sada připojení rails poskytuje pro použití s 19 palec rack soubor CAB. Byly testovány rails zpracování váha maximální skříň. Tyto rails také umožní instalaci více skříní bez ztráty místa v rámci racku.

#### <a name="to-install-the-device-on-the-rails"></a>Na zařízení nainstalovat rails
1. Tento krok proveďte jenom v případě, že vnitřní rails nejsou nainstalovány na vašem zařízení. Vnitřní rails se obvykle instaluje na objekt pro vytváření. Pokud nejsou nainstalovány rails, potom nainstalujte snímky lišty vlevo a vpravo lišty stranách skříně skříň. Se připojit s použitím šesti metriky šrouby na každé straně. Abyste při orientaci, jsou označeny snímky lišty **LH – přední** a **RH – přední**, a end, který je připojeno směrem dozadu přílohu má vřetenovitým konec.<br/>
   
    ![Připojení k skříň skříně snímky lišty](./media/storsimple-8100-hardware-installation/HCSAttachingRailSlidestoEnclosureChassis.png)

    **Vnitřní lišty snímky se připojuje k okraji přílohu**
   
    Štítek | Popis
    ----- | -----------
    1     | M 3 x 4 šrouby hlavní tlačítko
    2     | Snímky skříně

2. Připojení k CAB svislé členy rack levé vnější lišty a vnější pravé liště sestavení. Závorky jsou označeny **LH**, **RH**, a **této straně si** které vás provedou správné orientaci.
3. Vyhledejte lišty PIN kódy na přední a zadní lišty sestavení. Rozšiřte lišty přizpůsobit mezi příspěvků do racku a vložení do přední a zadní rack příspěvek svislé člen děr kódy PIN. Ujistěte se, že je sestavení lišty úroveň.
4. Dvě zadané metriky šrouby použijte k zabezpečení sestavení lišty do racku svislé členy. Použijte jeden šroub na popředí a na zadní.
5. Tento postup opakujte pro další lišty sestavení.<br/>
   
     ![Připojení k rack CAB snímky lišty](./media/storsimple-8100-hardware-installation/HCSAttachingRailSlidestoRackCabinet.png)
   
    **Připojení vnější lišty sestavení do racku**
   
   | Štítek | Popis |
   | --- | --- |
   |   1 |Upnutí šroubovacím |
   |   2 |Přední rack čtverec riziko příspěvek šroubovacím |
   |   3 |PIN kódy přední umístění levé lišty |
   |   4 |Upnutí šroubovacím |
   |   5 |PIN kódy zadní umístění levé lišty |

### <a name="mounting-the-device-in-the-rack"></a>Připojení zařízení do racku
Pomocí rails rack, stačí nainstalované, proveďte následující kroky pro připojení zařízení do racku.

#### <a name="to-mount-the-device"></a>Připojit zařízení
1. Asistent přenést skříň a obvyklému rails stojanu.
2. Pečlivě insert rails pro zařízení a potom nasdílejte zařízení zcela do racku CAB.<br/>
   
    ![Vkládání zařízení do racku](./media/storsimple-8100-hardware-installation/HCSInsertingDeviceintheRack.png)
   
    **Připojení zařízení do racku**
3. Odeberte velká vlevo a vpravo přední ohraničení roztažením bezplatné limity. Ohraničení caps jednoduše do příruba Přichytit.
4. Zabezpečené skříni v racku nainstalováním jeden poskytnutý Phillips head šroub prostřednictvím jednotlivých ohraničení, levé a pravé.
5. Nainstalujte caps ohraničení klávesy na místo a přitahování na místě.<br/>
   
     ![Instalaci se limitů ohraničení](./media/storsimple-8100-hardware-installation/HCSInstallingFlangeCaps.png)
   
    **Instalaci se limitů ohraničení**
   
   | Štítek | Popis |
   | --- | --- |
   |   1 |Šroubovacím upevnění skříň |

Dalším krokem je zapojení kabeláže zařízení kabeláž napájení, sítě a sériového přístupu.

## <a name="cable-your-storsimple-8100-device"></a>Zapojení kabeláže zařízení StorSimple 8100
Následující postupy popisují, jak zapojení kabeláže zařízení StorSimple 8100 kabeláž napájení, sítě a sériového portu připojení.

### <a name="prerequisites"></a>Požadavky
Než začnete kabeláže zařízení, budete potřebovat:

* Úložné zařízení zcela vybaleno a racku.
* 2 napájecích kabelů, které byly dodány s vaší zařízení
* Přístup k 2 jednotek pro distribuci napájení (doporučeno).
* Síťové kabely
* K dispozici sériové kabely
* Převaděč sériového portu USB s příslušný ovladač nainstalován ve vašem počítači (v případě potřeby)
* K dispozici 4 QSFP-na-SFP + adaptéry pro použití s rozhraní sítí 10 GbE
* [Podporované hardware pro rozhraní sítí 10 GbE na zařízení StorSimple](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)

### <a name="power-cabling"></a>Kabeláž napájení
Zařízení obsahuje redundantní napájení a chlazení moduly (PCMs). Jak PCMs musí být nainstalované a připojený k různým zdrojům napájení k zajištění vysoké dostupnosti.

Proveďte následující kroky a zapojení kabeláže zařízení za výkon.

[!INCLUDE [storsimple-cable-8100-for-power](../../includes/storsimple-cable-8100-for-power.md)]

### <a name="network-cabling"></a>Síťové kabely
Vaše zařízení je konfiguraci aktivní pohotovostní: v daném okamžiku je aktivní jeden modul kontroleru a zpracování všech operací disku a sítě při další řadiče modulu je v pohotovostním režimu. Pokud kontroler selže, kontroler v pohotovostním režimu je aktivovat hned a pokračuje v disku a síťových operací.

Pro podporu tohoto redundantní kontroler převzetí služeb při selhání, budete muset kabelové síti zařízení, jak je popsáno v následujících krocích.

#### <a name="to-cable-for-network-connection"></a>K propojení pro připojení k síti
1. Vaše zařízení má šest síťových rozhraní na každém řadiči: čtyři 1 GB/s a dvě 10 GB/s Ethernet portů. Identifikujte různé porty data na propojovací rozhraní systému vašeho zařízení.
   
    ![Propojovací rozhraní systému zařízení 8100](./media/storsimple-8100-hardware-installation/HCSBackplaneof2UDevicewithPortsLabeled.jpg)
   
    **Zadní zařízení, na kterém porty dat**
   
   | Štítek | Popis |
   | --- | --- |
   |   0,1,4,5 |1 GbE síťová rozhraní |
   |   2,3 |Rozhraní sítí 10 GbE |
   |   6 |Sériové porty |
2. Podívejte se na následující diagram pro síťové kabely. (Minimální konfigurace se zobrazí modré čáry. Další konfigurace pro vysokou dostupnost a výkon se zobrazí jako tečkované čáry.)

    ![Zapojení kabeláže 2 u zařízení pro síť](./media/storsimple-8100-hardware-installation/HCSCableYour2UDeviceforNetwork.png)

    **Síťové kabely pro vaše zařízení**

   |Štítek | Popis |
   |----- | ----------- |
   | A    | LAN s přístupem k Internetu |
   | B    | Řadič 0 |
   | C    | PCM 0 |
   | D    | Řadič 1 |
   | E    | PCM 1 |
   | F, G | Hostitelé |
   | 0-5  | Síťová rozhraní |



Když kabeláže zařízení, vyžaduje minimální požadavky na konfiguraci:

* Minimálně dvě síťová rozhraní připojená na každém řadiči s jeden cloud přístupu a jeden pro iSCSI. DATA 0 portu je automaticky povolen a nakonfigurován prostřednictvím konzoly sériového portu zařízení. Kromě DATA 0 jiný port data také je potřeba nakonfigurovat prostřednictvím portálu Azure classic. V takovém případě připojit DATA 0 port na primární síť LAN (síti s přístupem k Internetu). Jiné porty dat se dá propojit k segmentu sítě SAN a iSCSI sítě LAN (VLAN) sítě, v závislosti na určené role.
* Stejné rozhraní v každém řadiči připojeny ke stejné síti k zajištění dostupnosti, pokud dojde k selhání kontroleru. Například pokud budete chtít připojit DATA 0 a DATA 3 pro jeden z řadičů, budete muset připojit odpovídající DATA 0 a DATA 3 na druhý kontroler.

Mějte na paměti pro vysokou dostupnost a výkon:

* Pokud je to možné, nakonfigurujte na každém řadiči pár síťového rozhraní pro přístup k cloudu (1 GbE) a jinou dvojici pro iSCSI (doporučuje se 10 GbE).
* Pokud je to možné, připojení k dva různé přepínače a zajistit tak dostupnost proti selhání přepínač Síťová rozhraní z každého řadiče. Obrázek znázorňuje 10 dva 10GbE síťové rozhraní DATA 2 a DATA 3 z každého řadiče se připojit k různým přepínačům dvě.

Další informace najdete **síťová rozhraní** pod [požadavky na vysokou dostupnost pro zařízení StorSimple](storsimple-8000-system-requirements.md#high-availability-requirements-for-storsimple).

> [!NOTE]
> Pokud používáte SFP + vysílače s vaší rozhraní sítí 10 GbE, použijte poskytnutý QSFP-SFP + adaptéry. Další informace najdete v části [hardware pro rozhraní sítí 10 GbE na zařízení StorSimple nepodporuje](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).
> 
> 

### <a name="serial-port-cabling"></a>Kabelů sériového portu
Proveďte následující kroky a propojení kabely sériového portu.

#### <a name="to-cable-for-serial-connection"></a>K zapojení pro sériové připojení
1. Vaše zařízení má sériového portu v každém řadiči, který je identifikován ikona montážního klíče. Najdete na obrázku v [síťové kabely](#network-cabling) části najít sériových portů v propojovací rozhraní systému vašeho zařízení.
2. Identifikace aktivního kontroleru na propojovací rozhraní systému vašeho zařízení. Blikající Indikátor modré označuje, že je aktivní kontroler.
3. Použijte zadaný sériové kabely (v případě potřeby USB sériové převaděč pro přenosný počítač) a připojení konzoly nebo počítače (emulaci terminálu na zařízení) k sériového portu aktivní kontroler.
4. Instalace ovladačů sériové USB (dodané se zařízením) v počítači.
5. Nastavte sériové připojení následujícím způsobem: 115 200 přenosová, 8 datové bity, 1 stop-bit, žádná a řízení toku na hodnotu None.
6. Ověřte, že připojení funguje stisknutím klávesy Enter v konzole. Nabídka konzoly sériového portu zobrazit.

> [!NOTE]
> **Správa ILO**: když je zařízení nainstalované ve vzdáleném datovém centru nebo v počítačový sál s omezeným přístupem, ujistěte se, že sériové připojení na obou řadičích trvalým připojením k konzoly sériového portu přepínače nebo podobné zařízení. To umožňuje out-of-band vzdáleného řízení a podporu operací, pokud síťová přerušení nebo neočekávaných chyb.
> 
> 

Vaše zařízení je zapojené nyní kabeláž napájení, přístup k síti a sériové připojení. Dalším krokem je ke konfiguraci softwaru a nasazení vašeho zařízení.

## <a name="next-steps"></a>Další postup
Zjistěte, jak [nasazení a konfigurace vašeho místního zařízení StorSimple](storsimple-8000-deployment-walkthrough-u2.md).

