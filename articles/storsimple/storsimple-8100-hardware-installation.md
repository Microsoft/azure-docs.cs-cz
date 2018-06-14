---
title: Instalace Microsoft Azure StorSimple 8100 zařízení | Microsoft Docs
description: Popisuje, jak rozbalit, namontujte do racku a zapojení kabeláže zařízení StorSimple 8100 před nasazením a nakonfigurujte software.
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
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2018
ms.locfileid: "27743462"
---
# <a name="unpack-rack-mount-and-cable-your-storsimple-8100-device"></a>Zařízení vybalit, namontovat do racku a zapojení kabeláže zařízení StorSimple 8100
## <a name="overview"></a>Přehled
Vaše Microsoft Azure StorSimple 8100 je jeden skříň, skříňová zařízení. Tento kurz vysvětluje, jak zařízení vybalit, namontovat do racku a hardwaru pro zařízení StorSimple 8100 kabel před konfigurací a nasazení zařízení StorSimple.

## <a name="unpack-your-storsimple-8100-device"></a>Rozbalte zařízení StorSimple 8100
Následující kroky obsahují jasným a podrobné pokyny, jak rozbalte zařízení StorSimple 8100 úložiště. Toto zařízení je dodáván v jediné pole.

### <a name="prepare-to-unpack-your-device"></a>Příprava rozbalte zařízení
Předtím, než můžete zařízení vybalit zařízení, zkontrolujte následující informace.

![Ikona upozornění](./media/storsimple-safety/IC740879.png)![velkou váhy ikonu](./media/storsimple-8100-hardware-installation/HCS_HeavyWeight_Icon.png) **upozornění!**

1. Ujistěte se, že máte dvou osob, které jsou k dispozici ke správě váhu skříni, pokud jsou její zpracování ručně. Kompletně nakonfigurovaný skříň můžete naváží až 32 kg (70 kg.).
2. Umístěte pole na povrchu plochý, úrovně.

Dále proveďte následující kroky rozbalte zařízení.

#### <a name="to-unpack-your-device"></a>Rozbalte zařízení
1. Zkontrolujte pole a pěnou balení pro crushes kusy, horních poškození nebo jiných zřejmé poškození. Pokud pole nebo balení vážně poškozen, neotevírejte pole. Prosím [kontaktovat Microsoft Support](storsimple-8000-contact-microsoft-support.md) můžete vyhodnotit, jestli je zařízení v dobrém stavu.
2. Rozbalte pole. Následující obrázek znázorňuje rozbalené zobrazení zařízení StorSimple.
   
     ![Rozbalte zařízení úložiště](./media/storsimple-8100-hardware-installation/HCSUnpackyour2Udevice.png)
   
    **Rozbalené zobrazení vašeho zařízení úložiště**
   
   | Štítek | Popis |
   | --- | --- |
   |   1 |Okolních pole |
   |   2 |Dolní pěnou |
   |   3 |Zařízení |
   |   4 |Horní pěnou |
   |   5 |Příslušenství pole |
3. Po rozbalení pole, ujistěte se, zda máte:
   
   * zařízení, která 1 jedné skříni.
   * 2 napájecích kabelů
   * 1 kříženého kabelu Ethernet
   * 2 kabely konzoly sériového portu
   * 1 sériové USB převaděč pro sériový přístup
   * 1 šroubovák T10 manipulací
   * 4 QSFP-na-SFP + adaptéry pro použití s 10 GbE síťová rozhraní
   * 1 rack připojení kit (2 straně které s připojení hardwaru)
   * Získávání dokumentaci Začínáme
     
     Pokud jste neobdrželi některou z položek uvedených výše, [kontaktovat Microsoft Support](storsimple-8000-contact-microsoft-support.md).

Dalším krokem je rack připojení zařízení.

## <a name="rack-mount-your-storsimple-8100-device"></a>Zařízení StorSimple 8100 rack připojení
Podle další kroky pro instalaci zařízení StorSimple 8100 úložiště v standardní stojanu 19 palců s přední a zadní příspěvky. Zařízení StorSimple 8100 má jeden primární skříň.

Instalaci se skládá z více kroků, z nichž každý je popsána v následujících postupech.

> [!IMPORTANT]
> Zařízení StorSimple musí být skříňová pro správný provoz.
> 
> 

### <a name="prepare-the-site"></a>Příprava webu
Zařízení musí být nainstalovány ve standardní rack 19 palců, který má přední a zadní příspěvky. Příprava pro instalaci do racku použijte následující postup.

#### <a name="to-prepare-the-site-for-rack-installation"></a>Příprava webu pro instalaci do racku
1. Ujistěte se, že zařízení bezpečně postavená na ploché, stabilní a úrovně pracovní plochy (nebo podobnou).
2. Ověřte, zda má lokality, kde chcete nastavit standardní AC napájení z nezávislých zdroj nebo jednotka distribuci napájení (PDU) rack se nepřerušitelný zdroj napájení (UPS).
3. Ujistěte se, že jeden slot 2 u je k dispozici v racku, ve kterém chcete připojit zařízení.

![Ikona upozornění](./media/storsimple-safety/IC740879.png)![velkou váhy ikonu](./media/storsimple-8100-hardware-installation/HCS_HeavyWeight_Icon.png) **upozornění!**

Ujistěte se, že máte dvou osob, které jsou k dispozici ke správě váhu, pokud jsou zpracování nastavení zařízení ručně. Kompletně nakonfigurovaný skříň můžete naváží až 32 kg (70 kg.).

### <a name="rack-prerequisites"></a>Rack požadavky
Skříň 8100 je určené pro instalaci v standardní 19 palců stojanu CAB s:

* Minimální hloubka 27.84 palce z rack post na post.
* Maximální váhu 32 kg pro zařízení
* Maximální zpětný tlak 5 Pascal (0,5 mm horních měřidla).

### <a name="rack-mounting-rail-kit"></a>Připojování rack liště kit
Sadu připojení, které se poskytuje pro použití s 19 palců rack CAB. Které byl testován pro zpracování maximální skříň váhu. Tyto které také umožní instalaci více skříní beze ztrát místa v rámci racku.

#### <a name="to-install-the-device-on-the-rails"></a>Na které instalace zařízení
1. Tento krok proveďte jenom v případě, že vnitřní které nejsou nainstalovány na vašem zařízení. Vnitřní které jsou obvykle nainstalovány na objekt pro vytváření. Pokud které nejsou nainstalovány, nainstalujte na strany skříň skříň liště levé a pravé liště snímky. Jsou připojit pomocí šesti metriky šrouby na každé straně. Usnadní orientaci, jsou označeny snímky liště **LH – přední** a **RH – přední**, a end, který je opatřen dozadu skříni má vřetenovitým end.<br/>
   
    ![Snímky liště se připojuje k skříň skříň](./media/storsimple-8100-hardware-installation/HCSAttachingRailSlidestoEnclosureChassis.png)

    **Vnitřní liště snímky se připojuje k postranní skříni**
   
    Štítek | Popis
    ----- | -----------
    1     | M 3 × 4 šrouby tlačítko head
    2     | Skříň snímky

2. Připojte vnější liště levé a pravé liště vnější sestavení do racku CAB svislé členů. Jsou označeny hranatých závorkách **LH**, **RH**, a **této straně až** vás provedou správnou orientaci.
3. Vyhledejte liště PIN v přední a zadní liště sestavení. Rozšiřte liště přizpůsobit mezi příspěvcích racku a vkládání do přední a zadní rack post svislé člen díry kódy PIN. Ujistěte se, že je sestavení liště úroveň.
4. Použijte dva zadané metriky šroubů zabezpečit liště sestavení do racku svislé členy. Použijte jeden šroub na popředí a na zadní jeden.
5. Opakujte tyto kroky u jiných liště sestavení.<br/>
   
     ![Snímky liště se připojuje k rack CAB](./media/storsimple-8100-hardware-installation/HCSAttachingRailSlidestoRackCabinet.png)
   
    **Připojení vnější liště sestavení do racku**
   
   | Štítek | Popis |
   | --- | --- |
   |   1 |Upínací šroubovacím |
   |   2 |Šroubovacím post front rack hranaté hierarchie |
   |   3 |Levé liště front umístění PIN |
   |   4 |Upínací šroubovacím |
   |   5 |Levé liště zadní umístění PIN |

### <a name="mounting-the-device-in-the-rack"></a>Připojení zařízení do racku
Pomocí které rack, které byly nainstalovány právě, proveďte následující postup připojení zařízení do racku.

#### <a name="to-mount-the-device"></a>Připojit zařízení
1. Asistent navýšení skříni a zarovnané s které racku.
2. Pečlivě vložit do které zařízení a potom push zařízení zcela do racku CAB.<br/>
   
    ![Vkládání zařízení do racku](./media/storsimple-8100-hardware-installation/HCSInsertingDeviceintheRack.png)
   
    **Připojení zařízení do racku**
3. Odeberte CAP levé a pravé front příruby přidáváním CAP volné. Cap příruby jednoduše na příruba snap.
4. Zabezpečené skříni v racku nainstalováním jeden zadaný šroub Phillips head prostřednictvím každý příruby vlevo a vpravo.
5. Nainstalujte CAP příruby stisknutím kláves je do pozice a uchycení je na místě.<br/>
   
     ![Instalace příruby CAP k vzdálené ploše](./media/storsimple-8100-hardware-installation/HCSInstallingFlangeCaps.png)
   
    **Instalace příruby CAP k vzdálené ploše**
   
   | Štítek | Popis |
   | --- | --- |
   |   1 |Skříň upevnění šroubovacím |

Dalším krokem je zapojení kabeláže zařízení pro napájení, sítě a sériového přístupu.

## <a name="cable-your-storsimple-8100-device"></a>Zapojení kabeláže zařízení StorSimple 8100
Následující postupy popisují, jak zapojení kabeláže zařízení StorSimple 8100 pro napájení, sítě a sériové připojení.

### <a name="prerequisites"></a>Požadavky
Než začnete, kabelů zařízení, budete potřebovat:

* Zařízení úložiště, zcela vybaleno a racku.
* 2 napájecích kabelů dodaných s vaším zařízením
* Přístup k 2 jednotek pro distribuci napájení (doporučeno).
* Síťové kabely
* Zadat sériové kabely
* Převaděč sériového portu USB s příslušný ovladač nainstalován ve vašem počítači (v případě potřeby)
* Poskytuje 4 QSFP-na-SFP + adaptéry pro použití s 10 GbE síťová rozhraní
* [Podporovaném hardwaru 10 GbE síťových rozhraních zařízení StorSimple](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)

### <a name="power-cabling"></a>Kabeláž napájení
Zařízení obsahuje redundantní napájení a chlazení moduly (PCMs). Jak PCMs musí být nainstalovaný a připojený k jiné power zdroje k zajištění vysoké dostupnosti.

Proveďte následující kroky a zapojení kabeláže zařízení pro napájení.

[!INCLUDE [storsimple-cable-8100-for-power](../../includes/storsimple-cable-8100-for-power.md)]

### <a name="network-cabling"></a>Síťové kabely
Zařízení je konfigurace aktivní pohotovostní: v každém okamžiku je aktivní jeden modul řadiče a zpracování všech diskových a síťových operací při jiné řadiče modul je do úsporného režimu. Pokud řadič selže, pohotovostní řadiče se ihned aktivuje a bude pokračovat disku a síťových operací.

Pro podporu převzetí této redundantní řadiče, budete muset kabelové síti zařízení, jak je popsáno v následujících krocích.

#### <a name="to-cable-for-network-connection"></a>K kabel pro síťové připojení
1. Vaše zařízení má šest síťových rozhraní v každém řadiči: čtyři 1 GB/s a dvě 10 GB/s Ethernet porty. Identifikujte různé porty data na propojovacího rozhraní vašeho zařízení.
   
    ![Propojovací rozhraní systému 8100 zařízení](./media/storsimple-8100-hardware-installation/HCSBackplaneof2UDevicewithPortsLabeled.jpg)
   
    **Zpět z zařízení zobrazení dat portů**
   
   | Štítek | Popis |
   | --- | --- |
   |   0,1,4,5 |1 GbE síťová rozhraní |
   |   2,3 |10 GbE síťová rozhraní |
   |   6 |Sériové porty |
2. Podívejte se na následující diagram pro síťové kabely. (Minimální síťová konfigurace se zobrazí modré plné čáry. Další konfigurace požadované pro vysokou dostupnost a výkon se zobrazí linkami tečkovaná.)

    ![Zapojení kabeláže 2 u zařízení pro síť](./media/storsimple-8100-hardware-installation/HCSCableYour2UDeviceforNetwork.png)

    **Sítě kabelů pro vaše zařízení**

   |Štítek | Popis |
   |----- | ----------- |
   | A    | LAN s přístupem k Internetu |
   | B    | Řadič 0 |
   | C    | PCM 0 |
   | D    | Řadič 1 |
   | E    | PCM 1 |
   | F, G | Hostitelé |
   | 0-5  | Síťová rozhraní |



Pokud kabeláže zařízení, vyžaduje minimální požadavky na konfiguraci:

* Aspoň dvě rozhraní sítě připojené v každém řadiči s jeden pro přístup do cloudu a jeden pro iSCSI. DATA 0 portu je automaticky povolené a nakonfigurované prostřednictvím konzole sériového portu zařízení. Kromě DATA 0 jiný port data také musí být nakonfigurované přes portál Azure classic. V takovém případě připojit DATA 0 port (síť s přístupem k Internetu) v primární síti LAN. Další data porty může být připojen k segmentu sítě SAN nebo iSCSI sítí LAN (VLAN) sítě, v závislosti na roli určený.
* Stejná rozhraní v každém řadiči připojené ke stejné síti k zajištění dostupnosti, pokud dojde k selhání řadiče. Například pokud zvolíte možnost připojit DATA 0 a DATA 3 pro jedním z řadičů, kterou potřebujete připojit odpovídající DATA 0 a DATA 3 na jiný řadič.

Mějte na paměti pro vysokou dostupnost a výkon:

* Pokud je to možné, nakonfigurujte v každém řadiči pár síťového rozhraní pro přístup do cloudu (1 GbE) a jinou dvojici pro iSCSI (10 GbE doporučené).
* Pokud je to možné, připojte síťová rozhraní z každého řadiče dva různé přepínače k zajištění dostupnosti proti selhání přepínače. Na obrázku je znázorněn dvě 10 GbE síťová rozhraní, DATA 2 a DATA 3 z každého řadiče připojené k dva různé přepínače.

Další informace najdete v části **síťových rozhraní** pod [požadavků na vysokou dostupnost pro zařízení StorSimple](storsimple-8000-system-requirements.md#high-availability-requirements-for-storsimple).

> [!NOTE]
> Pokud používáte SFP + vysílače s 10 GbE síťové rozhraní, pomocí zadané QSFP-SFP + adaptéry. Další informace, přejděte na [podporovaném hardwaru 10 GbE síťových rozhraních zařízení StorSimple](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).
> 
> 

### <a name="serial-port-cabling"></a>Kabeláž sériového portu
Proveďte následující kroky k zapojení sériového portu.

#### <a name="to-cable-for-serial-connection"></a>K kabel pro sériové připojení
1. Vaše zařízení má sériového portu v každém řadiči, která je identifikovaná ikona klíče. Najdete na obrázku v [síťové kabely](#network-cabling) části najít sériových portů v propojovacího rozhraní vašeho zařízení.
2. Identifikujte řadič active na vaše zařízení propojovacího rozhraní. Blikající blue DIODU označuje, že je aktivní kontroleru.
3. Pomocí zadané sériové kabely (v případě potřeby, USB sériové převaděč pro přenosný) a připojení konzoly nebo počítače (s emulaci terminálu do zařízení) k sériového portu aktivní řadiče.
4. Instalace ovladačů sériové USB (dodané se zařízením) v počítači.
5. Nastavte sériové připojení takto: 115 200 přenosová, 8 datových bitů, 1 stop-bit, žádná parita a řízení toku nastavena na hodnotu None.
6. Ověřte, zda je připojení funkční stisknutím klávesy Enter v konzole. By se zobrazit nabídce konzoly sériového portu.

> [!NOTE]
> **Správa lights-Out**: když je zařízení nainstalované v datacentru vzdálené nebo v počítači místnosti s omezeným přístupem, ověřte, zda jsou sériové připojení pro oba řadiče vždy připojen do konzoly sériového portu přepínače nebo podobné zařízení. To umožňuje out-of-band vzdáleného řízení a podporu operací, pokud existují síťová přerušení nebo neočekávaných chyb.
> 
> 

Zařízení je nyní zapojena jeho kabeláž napájení, přístup k síti a sériové připojení. Dalším krokem je konfigurace softwaru a nasazení zařízení.

## <a name="next-steps"></a>Další postup
Zjistěte, jak [nasaďte a nakonfigurujte místní zařízení StorSimple](storsimple-8000-deployment-walkthrough-u2.md).

