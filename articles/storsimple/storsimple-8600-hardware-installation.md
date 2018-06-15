---
title: Instalace Microsoft Azure StorSimple 8600 zařízení | Microsoft Docs
description: Popisuje, jak rozbalit, namontujte do racku a zapojení kabeláže zařízení StorSimple 8600 před nasazením a nakonfigurujte software.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: 3d82ba5f-3e34-40dc-9c33-50f952bc6be8
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 5a8b460441323cb668a3d9939cce434636afc44d
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2018
ms.locfileid: "27743708"
---
# <a name="unpack-rack-mount-and-cable-your-storsimple-8600-device"></a>Zařízení vybalit, namontovat do racku a zapojení kabeláže zařízení StorSimple 8600
## <a name="overview"></a>Přehled
Vaše Microsoft Azure StorSimple 8600 duální skříň zařízení a skládá se z primárního serveru a EBOD skříň. Tento kurz vysvětluje, jak rozbalit, namontovat do racku a kabel StorSimple 8600 hardwaru zařízení před konfiguraci softwaru zařízení StorSimple.

## <a name="unpack-your-storsimple-8600-device"></a>Rozbalte zařízení StorSimple 8600
Následující kroky poskytují jasným a podrobné pokyny o tom, jak rozbalit paměťovém zařízení StorSimple 8600. Toto zařízení je dodáván ve dvou polích, jeden pro skříni primární a druhý pro EBOD skříň. Tyto dva seznamy jsou pak umístěny v jediné pole.

### <a name="prepare-to-unpack-your-device"></a>Příprava rozbalte zařízení
Předtím, než můžete zařízení vybalit zařízení, zkontrolujte následující informace.

![Ikona upozornění](./media/storsimple-safety/IC740879.png)![velkou váhy ikonu](./media/storsimple-8600-hardware-installation/HCS_HeavyWeight_Icon.png) **upozornění!**

1. Ujistěte se, že máte dvou osob, které jsou k dispozici ke správě váhu zařízení, pokud jsou její zpracování ručně. Kompletně nakonfigurovaný skříň můžete naváží až 32 kg (70 kg.).
2. Umístěte pole na povrchu plochý, úrovně.

Dále proveďte následující kroky rozbalte zařízení.

#### <a name="to-unpack-your-device"></a>Rozbalte zařízení
1. Zkontrolujte pole a pěnou balení pro crushes kusy, horních poškození nebo jiných zřejmé poškození. Pokud pole nebo balení vážně poškozen, neotevírejte pole. Prosím [kontaktovat Microsoft Support](storsimple-8000-contact-microsoft-support.md) můžete vyhodnotit, jestli je zařízení v dobrém stavu.
2. Otevřete vnější pole a pak proveďte na dvě pole odpovídající primární a EBOD skříně. Můžete teď rozbalte primárním serverem a EBOD skříně. Následující obrázek znázorňuje rozbalené zobrazení jednoho skříních.
   
    ![Rozbalte zařízení úložiště](./media/storsimple-8600-hardware-installation/HCSUnpackyour4Udevice.png)
   
    **Rozbalené zobrazení vašeho zařízení úložiště**
   
   | Štítek | Popis |
   | --- | --- |
   |   1 |Okolních pole |
   |   2 |SAS kabely (v Příslušenství a kabely panelu) |
   |   3 |Dolní pěnou |
   |   4 |Zařízení |
   |   5 |Horní pěnou |
   |   6 |Příslušenství pole |
3. Po rozbalení dvě pole, ujistěte se, zda máte:
   
   * 1 primární skříň (primární skříně a EBOD skříň jsou dvě samostatné polí)
   * 1 EBOD skříň
   * 4 napájecích kabelů, 2 v každém poli
   * 2 SAS kabely (pro připojení primární skříň k EBOD skříň)
   * 1 kříženého kabelu Ethernet
   * 2 kabely konzoly sériového portu
   * 1 sériové USB převaděč pro sériový přístup
   * 4 QSFP-na-SFP + adaptéry pro použití s 10 GbE síťová rozhraní
   * 2 rack sadách připojení (4 které straně s připojení hardwaru, 2 pro primární skříně a EBOD skříň), 1 v každém poli
   * Získávání dokumentaci Začínáme
     
     Pokud jste neobdrželi některou z položek uvedených výše, [kontaktovat Microsoft Support](storsimple-8000-contact-microsoft-support.md).  

Dalším krokem je rack připojení zařízení.

## <a name="rack-mount-your-storsimple-8600-device"></a>Zařízení StorSimple 8600 rack připojení
Podle další kroky pro instalaci zařízení StorSimple 8600 úložiště v standardní stojanu 19 palců s přední a zadní příspěvky. Toto zařízení se dodává s dvěma skříně: primární skříně a EBOD skříň. Obě tyto musí být skříňová.

Instalaci se skládá z více kroků, z nichž každý je popsána v následujících postupech.

> [!IMPORTANT]
> Zařízení StorSimple musí být skříňová pro správný provoz.
> 
> 

### <a name="site-preparation"></a>Příprava lokality
Skříních musí být nainstalovány ve standardní rack 19 palců, který má přední a zadní příspěvky. Příprava pro instalaci do racku použijte následující postup.

#### <a name="to-prepare-the-site-for-rack-installation"></a>Příprava webu pro instalaci do racku
1. Ujistěte se, že se primární a skříně EBOD řady bezpečně na ploché, stabilní a úrovně pracovní prostor (nebo podobnou).
2. Ověřte, zda má lokality, kde chcete nastavit standardní AC napájení z nezávislých zdroje nebo racková skříň jednotky distribuční napájení (PDU) s nepřerušitelný zdroj napájení (UPS).
3. Zkontrolujte, zda že je k dispozici v racku, ve kterém chcete připojit skříních patice jeden 4U (2 u 2 X).

![Ikona upozornění](./media/storsimple-safety/IC740879.png)![velkou váhy ikonu](./media/storsimple-8600-hardware-installation/HCS_HeavyWeight_Icon.png) **upozornění!**

 Ujistěte se, že máte dvou osob, které jsou k dispozici ke správě váhu, pokud jsou zpracování nastavení zařízení ručně. Kompletně nakonfigurovaný skříň můžete naváží až 32 kg (70 kg.).

### <a name="rack-prerequisites"></a>Rack požadavky
Skříních jsou určené pro instalaci v standardní CAB s stojanu palců 19:

* Minimální hloubka 27.84 palce z rack post na post
* Maximální váhu 32 kg pro zařízení
* Maximální zpětný tlak 5 Pascal (0,5 mm horních měřidla)

### <a name="rack-mounting-rail-kit"></a>Připojování rack liště kit
Sadu připojení, které bude poskytnuta pro použití s 19 palců rack CAB. Které byl testován pro zpracování maximální skříň váhu. Tyto které také umožní instalaci více skříní bez ztráty místa v rámci racku. Skříň EBOD nejprve nainstalujte.

#### <a name="to-install-the-ebod-enclosure-on-the-rails"></a>Chcete-li nainstalovat skříni EBOD na které
1. Tento krok proveďte jenom v případě, že vnitřní které nejsou nainstalovány na vašem zařízení. Vnitřní které jsou obvykle nainstalovány na objekt pro vytváření. Pokud které nejsou nainstalovány, nainstalujte na strany skříň skříň liště levé a pravé liště snímky. Jsou připojit pomocí šesti metriky šrouby na každé straně. Usnadní orientaci, jsou označeny snímky liště **LH – přední** a **RH – přední**, a end, který je opatřen dozadu skříni má vřetenovitým end.
   
    ![Snímky liště se připojuje k skříň skříň](./media/storsimple-8600-hardware-installation/HCSAttachingRailSlidestoEnclosureChassis.png)
   
    **Připojení k postranní skříni liště snímky**
   
   | Štítek | Popis |
   | --- | --- |
   |  1 |M 3 × 4 šrouby tlačítko head |
   |  2 |Skříň snímky |
2. Připojte liště levé a pravé liště sestavení do racku CAB svislé členů. Jsou označeny hranatých závorkách **LH**, **RH**, a **této straně až** vás provedou správnou orientaci.
3. Vyhledejte liště PIN v přední a zadní liště sestavení. Rozšiřte liště přizpůsobit mezi příspěvcích racku a vkládání do přední a zadní rack post svislé člen díry kódy PIN. Ujistěte se, že je sestavení liště úroveň.
4. Zabezpečení liště sestavení do racku svislé členy pomocí dvou metriky šroubů zadat. Použijte jeden šroub na popředí a na zadní jeden.
5. Opakujte tyto kroky u jiných liště sestavení.
   
     ![Snímky liště se připojuje k rack CAB](./media/storsimple-8600-hardware-installation/HCSAttachingRailSlidestoRackCabinet.png)
   
    **Připojení liště sestavení do racku**
   
   | Štítek | Popis |
   | --- | --- |
   |   1 |Upínací šroubovacím |
   |   2 |Šroubovacím post front rack hranaté hierarchie |
   |   3 |Levé front liště umístění PIN |
   |   4 |Upínací šroubovacím |
   |   5 |Levé zadní liště umístění PIN |

### <a name="mounting-the-ebod-enclosure-in-the-rack"></a>Připojení skříni EBOD v racku
Které rack, které byly nainstalovány právě používá, proveďte následující kroky připojit skříni EBOD v racku.

#### <a name="to-mount-the-ebod-enclosure"></a>Chcete-li připojit EBOD skříň
1. Asistent navýšení skříni a zarovnané s které racku.
2. Pečlivě vložit do které skříni a pak poslat ho úplně do racku CAB.
   
    ![Vkládání zařízení do racku](./media/storsimple-8600-hardware-installation/HCSInsertingDeviceintheRack.png)
   
    **Připojení skříni v racku**
3. Odeberte CAP levé a pravé front příruby přidáváním CAP volné. Cap příruby jednoduše na příruba snap.
4. Zabezpečené skříni do racku nainstalováním jeden zadaný šroub Phillips head prostřednictvím každý příruby vlevo a vpravo.
5. Nainstalujte CAP příruby stisknutím kláves je do pozice a uchycení je na místě.
   
     ![Instalace příruby CAP k vzdálené ploše](./media/storsimple-8600-hardware-installation/HCSInstallingFlangeCaps.png)
   
    **Instalace příruby CAP k vzdálené ploše**
   
   | Štítek | Popis |
   | --- | --- |
   |   1 |Skříň upevnění šroubovacím |

### <a name="mounting-the-primary-enclosure-in-the-rack"></a>Připojení primární skříň v racku
Po dokončení připojení skříni EBOD musíte připojit primární skříň stejně.

> [!NOTE]
> * Je možné, že několik prázdný sloty v racku mezi primární skříň a EBOD skříň.
> * Použití kabelu zadané 2m SAS pro připojení ke skříni EBOD primární skříň.
> * Neexistují žádná omezení na relativní umístění head jednotky a jednotek EBOD. Proto se může primární skříň v nejvyšší pozici a níže EBOD skříň – nebo naopak.
> 
> 

Dalším krokem je zapojení kabeláže zařízení pro napájení, sítě a sériového přístupu.

## <a name="cable-your-storsimple-8600-device"></a>Zapojení kabeláže zařízení StorSimple 8600
Následující postupy popisují, jak zapojení kabeláže zařízení StorSimple 8600 pro napájení, sítě a sériové připojení.

### <a name="prerequisites"></a>Požadavky
Než začnete zapojení kabeláže zařízení, budete potřebovat:

* Vaše primární skříně a skříni EBOD zcela vybaleno.
* 4 napájecích kabelů (2 každé primární a skříň EBOD), které byly dodány s vaším zařízením
* 2 SAS kabely, které jsou součástí zařízení pro připojení ke skříni primární EBOD skříň
* Přístup k 2 jednotek pro distribuci napájení (PDU) (doporučeno)
* Síťové kabely
* Zadat sériové kabely
* Převaděč sériové USB s příslušný ovladač nainstalován ve vašem počítači (v případě potřeby)
* Poskytuje 4 QSFP-na-SFP + adaptéry pro použití s 10 GbE síťová rozhraní
* [Podporovaném hardwaru 10 GbE síťových rozhraních zařízení StorSimple](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)

### <a name="sas-and-power-cabling"></a>SAS a kabeláž napájení
Vaše zařízení má skříni primární i EBOD skříň. To vyžaduje jednotky k být společně kabelové připojení Serial Attached (SCSI SAS) a napájení.

Při nastavování toto zařízení poprvé, postupujte podle kroků pro SAS kabelů nejprve a potom postupujte podle pokynů pro kabeláž napájení.

[!INCLUDE [storsimple-cable-8600-for-SAS](../../includes/storsimple-sas-cable-8600.md)]

[!INCLUDE [storsimple-cable-8600-for-power](../../includes/storsimple-cable-8600-for-power.md)]

### <a name="network-cabling"></a>Síťové kabely
Zařízení je v konfiguraci aktivní pohotovostní: v každém okamžiku je aktivní jeden modul řadiče a zpracování všech diskových a síťových operací při jiné řadiče modul je do úsporného režimu. Pokud dojde k selhání řadiče, pohotovostní řadiče ihned aktivuje a bude pokračovat všechny operace disku a sítě.

Pro podporu převzetí této redundantní řadiče, budete muset kabelové síti zařízení, jak je znázorněno v následujícím postupu.

#### <a name="to-cable-for-network-connection"></a>K kabel pro síťové připojení
1. Vaše zařízení má šest síťových rozhraní v každém řadiči: čtyři 1 GB/s a dvě 10 GB/s Ethernet porty. Naleznete na následujícím obrázku k identifikaci portů data na propojovacího rozhraní vašeho zařízení.
   
     ![Propojovací rozhraní systému 8600 zařízení](./media/storsimple-8600-hardware-installation/HCSBackplaneof2UDevicewithPortsLabeled.jpg)
   
    **Zpět z vašeho zařízení zobrazení dat portů**
   
   | Štítek | Popis |
   | --- | --- |
   |   0,1,4,5 |1 GbE síťová rozhraní |
   |   2,3 |10 GbE síťová rozhraní |
   |   6 |Sériové porty |
2. Podívejte se na následující diagram pro síťové kabely. (Minimální síťová konfigurace se zobrazí modré plné čáry. Pro vysokou dostupnost a výkon další nezbytné konfigurace je zobrazen linkami tečkovaná.)

![Zapojení kabeláže zařízení 4U pro síť](./media/storsimple-8600-hardware-installation/HCSCableYour4UDeviceforNetwork.png)

**Sítě kabelů pro vaše zařízení**

| Štítek | Popis |
| --- | --- |
| A |LAN s přístupem k Internetu |
| B |Řadič 0 |
| C |PCM 0 |
| D |Řadič 1 |
| E |PCM 1 |
| F |EBOD řadič 0 |
| G |EBOD řadiči 1 |
| H, I |Hostitele (například souborové servery) |
| 0-5 |Síťová rozhraní |
| 6 |Primární skříň |
| 7 |EBOD skříň |

Pokud kabeláže zařízení, vyžaduje minimální požadavky na konfiguraci:

* Aspoň dvě rozhraní sítě připojené v každém řadiči s jeden pro přístup do cloudu a jeden pro iSCSI. DATA 0 portu je automaticky povolené a nakonfigurované prostřednictvím konzole sériového portu zařízení. Kromě DATA 0 jiný port data také musí být nakonfigurované přes portál Azure classic. V takovém případě připojit DATA 0 port (síť s přístupem k Internetu) v primární síti LAN. Další data porty může být připojen k segmentu sítě SAN nebo iSCSI sítí LAN (VLAN) sítě, v závislosti na roli určený.
* Stejná rozhraní v každém řadiči připojené ke stejné síti k zajištění dostupnosti, pokud dojde k selhání řadiče. Například pokud zvolíte možnost připojit DATA 0 a DATA 3 pro jedním z řadičů, kterou potřebujete připojit odpovídající DATA 0 a DATA 3 na jiný řadič.

Mějte na paměti pro vysokou dostupnost a výkon:

* Pokud je to možné, nakonfigurujte v každém řadiči pár síťového rozhraní pro přístup do cloudu (1 GbE) a jinou dvojici pro iSCSI (10 GbE doporučené).
* Pokud je to možné, připojte síťová rozhraní z každého řadiče dva různé přepínače k zajištění dostupnosti proti selhání přepínače. Na obrázku je znázorněn dvě 10 GbE síťová rozhraní, DATA 2 a DATA 3 z každého řadiče připojené k dva různé přepínače. Další informace najdete v části **síťových rozhraní** pod [požadavků na vysokou dostupnost pro zařízení StorSimple](storsimple-8000-system-requirements.md#high-availability-requirements-for-storsimple).

> [!NOTE]
> Pokud používáte SFP + vysílače s 10 GbE síťové rozhraní, pomocí zadané QSFP-SFP + adaptéry. Další informace, přejděte na [podporovaném hardwaru 10 GbE síťových rozhraních zařízení StorSimple](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).
> 
> 

### <a name="serial-port-cabling"></a>Kabeláž sériového portu
Proveďte následující kroky k zapojení sériového portu.

#### <a name="to-cable-for-serial-connection"></a>K kabel pro sériové připojení
1. Vaše zařízení má sériového portu v každém řadiči, která je identifikovaná ikona klíče. Vyhledat sériové porty, najdete na obrázku, která zobrazuje data porty na zadní straně zařízení.
2. Identifikujte řadič active na vaše zařízení propojovacího rozhraní. Blikající blue DIODU označuje, že je aktivní kontroleru.
3. Pomocí zadané sériový kabel (v případě potřeby, USB sériové převaděč pro přenosný) a připojení konzoly nebo počítače (s emulaci terminálu do zařízení) k sériového portu aktivní řadiče.
4. Instalace ovladačů sériové USB (dodané se zařízením) v počítači.
5. Sériové připojení nastavte takto:
   
   * Přenosová 115 200
   * 8 datových bitů
   * 1 stop-bit
   * Žádná parita
   * Nastavte na řízení toku **None**
6. Ověřte, zda je připojení funkční stisknutím klávesy Enter v konzole. By se zobrazit nabídce konzoly sériového portu.

> [!NOTE]
> **Správa lights-Out:** když je zařízení nainstalované v datacentru vzdálené nebo v počítači místnosti s omezeným přístupem, ověřte, zda jsou sériové připojení pro oba řadiče vždy připojen do konzoly sériového portu přepínače nebo podobné zařízení. To umožňuje out-of-band vzdáleného řízení a podporu operací v případě přerušení sítě nebo neočekávaných chyb.
> 
> 

Dokončili jste kabeláž napájení, přístup k síti a sériové připojení zařízení. Dalším krokem je konfigurace softwaru na vašem zařízení.

## <a name="next-steps"></a>Další postup
Nyní jste připraveni k [nasaďte a nakonfigurujte místní zařízení StorSimple](storsimple-8000-deployment-walkthrough-u2.md).

