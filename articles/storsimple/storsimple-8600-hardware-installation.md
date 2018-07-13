---
title: Instalace Microsoft Azure StorSimple 8600 zařízení | Dokumentace Microsoftu
description: Popisuje, jak jeho usazení do racku a zapojení kabeláže zařízení StorSimple 8600 předtím, než můžete nasadit a nakonfigurovat software.
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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38719001"
---
# <a name="unpack-rack-mount-and-cable-your-storsimple-8600-device"></a>Rozbalení připojení do racku a zapojení kabeláže zařízení StorSimple 8600
## <a name="overview"></a>Přehled
Vaše Microsoft Azure StorSimple 8600 je duální skříň zařízení a skládá se z primární a EBOD skříň. Tento kurz vysvětluje, jak rozbalit, do racku a hardwaru zařízení StorSimple 8600 kabel před konfigurace softwaru StorSimple.

## <a name="unpack-your-storsimple-8600-device"></a>Rozbalit zařízení StorSimple 8600
Následující kroky obsahují jasné, podrobné pokyny o tom, jak rozbalit úložného zařízení StorSimple 8600. Toto zařízení se posílá ve dvou polích, jeden pro primární skříň a druhý pro EBOD skříň. Tyto dvě pole jsou poté umístěny v jediné pole.

### <a name="prepare-to-unpack-your-device"></a>Příprava na jeho usazení do zařízení
Před jeho usazení do zařízení, zkontrolujte následující informace.

![Ikona upozornění](./media/storsimple-safety/IC740879.png)![Tučná tloušťka ikonu](./media/storsimple-8600-hardware-installation/HCS_HeavyWeight_Icon.png) **upozornění!**

1. Ujistěte se, že máte dvou osob, které jsou k dispozici ke správě váhu zařízení, pokud je její zpracování ručně. Plně nakonfigurovaného skříně můžete zvážit až 32 kg (70 lbs.).
2. Umístěte pole na povrch bez stromové struktury, úroveň.

Dále proveďte následující kroky k jeho usazení do vašeho zařízení.

#### <a name="to-unpack-your-device"></a>Rozbalte vaše zařízení
1. Zkontrolujte pole a balení maskota crushes, kusy, vody poškození nebo jiných zřejmé poškození. Pokud pole nebo balení je vážně poškozen, neotevírejte pole. Prosím [obraťte se na Microsoft Support](storsimple-8000-contact-microsoft-support.md) umožňují posoudit, jestli je zařízení v dobrém stavu.
2. Otevření okna vnější a pak proveďte na dvou políček na primárním serverem a EBOD skříně. Můžete teď rozbalit primární a EBOD skříně. Následující obrázek znázorňuje rozbalené zobrazení jedné skříních.
   
    ![Rozbalit zařízení úložiště](./media/storsimple-8600-hardware-installation/HCSUnpackyour4Udevice.png)
   
    **Rozbalené zobrazení vašeho zařízení úložiště**
   
   | Štítek | Popis |
   | --- | --- |
   |   1 |Balení pole |
   |   2 |SAS kabely (v Příslušenství a kabely zásobníku) |
   |   3 |Dolní maskota |
   |   4 |Zařízení |
   |   5 |Začátek maskota |
   |   6 |Příslušenství pole |
3. Po rozbalení dvě pole, ujistěte se, že máte:
   
   * 1 primární skříň (primární skříň a EBOD zařízení jsou ve dvou samostatných polích.)
   * 1 skříň EBOD
   * 4 napájecích kabelů, 2 v každém poli
   * 2 SAS kabely (pro připojení primární zařízení ke skříni EBOD)
   * 1 kříženého kabelu Ethernet
   * 2 kabely konzoly sériového portu
   * 1 sériové USB převaděč pro sériového přístupu
   * 4 QSFP-na-SFP + adaptéry pro použití s rozhraní sítí 10 GbE
   * 2 racku připojení kits (4 na straně rails s připojením hardwaru, 2 pro primární skříň a skříně EBOD), 1 v každém poli
   * Získání dokumentace Začínáme
     
     Pokud jste některá z položek uvedených výše, neobdrželi [obraťte se na Microsoft Support](storsimple-8000-contact-microsoft-support.md).  

Dalším krokem je namontovat zařízení.

## <a name="rack-mount-your-storsimple-8600-device"></a>Namontovat zařízení StorSimple 8600
Postupujte podle dalších kroků při instalaci úložného zařízení StorSimple 8600 ve standardní stojanu 19 palce na přední a zadní příspěvky. Toto zařízení se dodává s dvě skříně: primární skříň a EBOD skříň. Obě tyto musí být skříňová.

Instalace se skládá z několika kroků, z nichž každý je popsána v následujících postupech.

> [!IMPORTANT]
> Musí být zařízení StorSimple skříňová pro správný provoz.
> 
> 

### <a name="site-preparation"></a>Příprava serveru
Skříních musí být nainstalován ve standardní 19 palec stojanu, který má přední a zadní příspěvky. Pomocí následujícího postupu připravte instalaci stojanu.

#### <a name="to-prepare-the-site-for-rack-installation"></a>Příprava rack instalace lokality
1. Ujistěte se, že primárním serverem a skříně EBOD jsou řady bezpečně na ploše bez stromové struktury, stabilní a úrovni práce (nebo podobnou).
2. Ověřte, zda má lokality, kde máte v úmyslu nastavit standardní napájení z nezávislých zdroje nebo racková skříň jednotky distribuci napájení (PDU) s nepřerušitelný zdroj napájení (UPS).
3. Zajistěte, aby že tento slot jeden 4U (2 u 2 X) je k dispozici na stojan, ve které máte v úmyslu připojit skříních.

![Ikona upozornění](./media/storsimple-safety/IC740879.png)![Tučná tloušťka ikonu](./media/storsimple-8600-hardware-installation/HCS_HeavyWeight_Icon.png) **upozornění!**

 Ujistěte se, že máte dvou osob, které jsou k dispozici ke správě váha, pokud se zpracování nastavení zařízení ručně. Plně nakonfigurovaného skříně můžete zvážit až 32 kg (70 lbs.).

### <a name="rack-prerequisites"></a>Požadavky do racku
Skříních jsou navržené tak, že při instalaci ve standardní rack 19 palec CAB pomocí:

* Minimální hloubky 27.84 palcích od rack příspěvku k příspěvku
* Maximální tloušťka 32 kg pro zařízení
* Maximální protitlak 5 Pascal (0,5 mm vody měřidla)

### <a name="rack-mounting-rail-kit"></a>Sada lišty montáže do racku
Pro použití s soubor CAB 19 palec rack vám poskytneme sadu připojení rails. Byly testovány rails zpracování váha maximální skříň. Tyto rails také umožní instalaci více skříní bez ztráty místa v rámci racku. Nejdříve nainstalujte EBOD skříň.

#### <a name="to-install-the-ebod-enclosure-on-the-rails"></a>K instalaci na rails skříň EBOD
1. Tento krok proveďte jenom v případě, že vnitřní rails nejsou nainstalovány na vašem zařízení. Vnitřní rails se obvykle instaluje na objekt pro vytváření. Pokud nejsou nainstalovány rails, potom nainstalujte snímky lišty vlevo a vpravo lišty stranách skříně skříň. Se připojit s použitím šesti metriky šrouby na každé straně. Abyste při orientaci, jsou označeny snímky lišty **LH – přední** a **RH – přední**, a end, který je připojeno směrem dozadu přílohu má vřetenovitým konec.
   
    ![Připojení k skříň skříně snímky lišty](./media/storsimple-8600-hardware-installation/HCSAttachingRailSlidestoEnclosureChassis.png)
   
    **Snímky lišty se připojuje k okraji přílohu**
   
   | Štítek | Popis |
   | --- | --- |
   |  1 |M 3 x 4 šrouby hlavní tlačítko |
   |  2 |Snímky skříně |
2. Připojení k CAB svislé členy rack lišty levé a pravé liště sestavení. Závorky jsou označeny **LH**, **RH**, a **této straně si** které vás provedou správné orientaci.
3. Vyhledejte lišty PIN kódy na přední a zadní lišty sestavení. Rozšiřte lišty přizpůsobit mezi příspěvků do racku a vložení do přední a zadní rack příspěvek svislé člen děr kódy PIN. Ujistěte se, že je sestavení lišty úroveň.
4. Zabezpečte lišty sestavení do racku svislé členy pomocí dvou metriky šrouby k dispozici. Použijte jeden šroub na popředí a na zadní.
5. Tento postup opakujte pro další lišty sestavení.
   
     ![Připojení k rack CAB snímky lišty](./media/storsimple-8600-hardware-installation/HCSAttachingRailSlidestoRackCabinet.png)
   
    **Připojení lišty sestavení do racku**
   
   | Štítek | Popis |
   | --- | --- |
   |   1 |Upnutí šroubovacím |
   |   2 |Přední rack čtverec riziko příspěvek šroubovacím |
   |   3 |Left PIN kódy umístění přední lišty |
   |   4 |Upnutí šroubovacím |
   |   5 |PIN kódy umístění levé zadní lišty |

### <a name="mounting-the-ebod-enclosure-in-the-rack"></a>Připojení skříň EBOD do racku
Pomocí rails rack, stačí nainstalované, proveďte následující kroky pro připojení skříň EBOD v racku.

#### <a name="to-mount-the-ebod-enclosure"></a>Připojit skříně EBOD
1. Asistent přenést skříň a obvyklému rails stojanu.
2. Pečlivě vložte přílohu rails a potom je nabídnout zcela do racku CAB.
   
    ![Vkládání zařízení do racku](./media/storsimple-8600-hardware-installation/HCSInsertingDeviceintheRack.png)
   
    **Připojení zařízení do racku**
3. Odeberte velká vlevo a vpravo přední ohraničení roztažením bezplatné limity. Ohraničení caps jednoduše do příruba Přichytit.
4. Zabezpečení skříň do racku nainstalováním jeden poskytnutý Phillips head šroub prostřednictvím jednotlivých ohraničení, levé a pravé.
5. Nainstalujte caps ohraničení klávesy na místo a přitahování na místě.
   
     ![Instalaci se limitů ohraničení](./media/storsimple-8600-hardware-installation/HCSInstallingFlangeCaps.png)
   
    **Instalaci se limitů ohraničení**
   
   | Štítek | Popis |
   | --- | --- |
   |   1 |Šroubovacím upevnění skříň |

### <a name="mounting-the-primary-enclosure-in-the-rack"></a>Připojení primární zařízení do racku
Po dokončení připojení EBOD skříni, je potřeba připojit primární skříň stejných kroků.

> [!NOTE]
> * Je možné mít několik prázdný sloty do racku mezi primární skříň a EBOD skříň.
> * Použití kabelu SAS zadané 2 min připojit primární skříň EBOD skříň.
> * Neexistují žádné omezení na hlavní jednotky a jednotky EBOD relativní umístění. Proto primární zařízení mohou být umístěny v horní slotu a níže EBOD zařízení – a naopak.
> 
> 

Dalším krokem je zapojení kabeláže zařízení kabeláž napájení, sítě a sériového přístupu.

## <a name="cable-your-storsimple-8600-device"></a>Zapojení kabeláže zařízení StorSimple 8600
Následující postupy popisují, jak zapojení kabeláže zařízení StorSimple 8600 kabeláž napájení, sítě a sériového portu připojení.

### <a name="prerequisites"></a>Požadavky
Než začnete zapojení kabeláže zařízení, budete potřebovat:

* Primární skříň a skříně EBOD zcela vybaleno
* 4 napájecích kabelů (2 pro primární a skříně EBOD), které byly dodány s vaší zařízení
* 2 SAS kabely dodané se zařízením pro připojení k primární skříň skříň EBOD
* Přístup k 2 jednotek pro distribuci napájení (PDU) (doporučeno)
* Síťové kabely
* K dispozici sériové kabely
* Převaděč sériové USB s příslušný ovladač nainstalován ve vašem počítači (v případě potřeby)
* K dispozici 4 QSFP-na-SFP + adaptéry pro použití s rozhraní sítí 10 GbE
* [Podporované hardware pro rozhraní sítí 10 GbE na zařízení StorSimple](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)

### <a name="sas-and-power-cabling"></a>SAS a kabeláž napájení
Vaše zařízení má primární skříň a EBOD skříň. To vyžaduje jednotky být zapojené dohromady pro připojení k rozhraní SCSI SAS (Serial Attached) a napájení.

Při nastavování zařízení poprvé, postupujte podle kroků pro SAS kabelů nejprve a potom postupujte podle pokynů pro kabeláž napájení.

[!INCLUDE [storsimple-cable-8600-for-SAS](../../includes/storsimple-sas-cable-8600.md)]

[!INCLUDE [storsimple-cable-8600-for-power](../../includes/storsimple-cable-8600-for-power.md)]

### <a name="network-cabling"></a>Síťové kabely
Vaše zařízení je v konfiguraci aktivní pohotovostní: v daném okamžiku je aktivní jeden modul kontroleru a zpracování všech operací disku a sítě při další řadiče modulu je v pohotovostním režimu. Pokud dojde k selhání kontroleru, kontroler v pohotovostním režimu okamžitě aktivuje a bude pokračovat všechny operace disku a sítě.

Pro podporu tohoto redundantní kontroler převzetí služeb při selhání, budete muset kabelové síti zařízení, jak je znázorněno v následujícím postupu.

#### <a name="to-cable-for-network-connection"></a>K propojení pro připojení k síti
1. Vaše zařízení má šest síťových rozhraní na každém řadiči: čtyři 1 GB/s a dvě 10 GB/s Ethernet porty. Naleznete na následujícím obrázku, k identifikaci portů data na propojovací rozhraní systému vašeho zařízení.
   
     ![Propojovací rozhraní systému zařízení 8600](./media/storsimple-8600-hardware-installation/HCSBackplaneof2UDevicewithPortsLabeled.jpg)
   
    **Zpět z vašeho zařízení zobrazení portů dat**
   
   | Štítek | Popis |
   | --- | --- |
   |   0,1,4,5 |1 GbE síťová rozhraní |
   |   2,3 |Rozhraní sítí 10 GbE |
   |   6 |Sériové porty |
2. Podívejte se na následující diagram pro síťové kabely. (Minimální konfigurace se zobrazí modré čáry. Pro vysokou dostupnost a výkon další nezbytné konfigurace se zobrazí jako tečkované čáry.)

![Zapojení kabeláže zařízení 4U pro síť](./media/storsimple-8600-hardware-installation/HCSCableYour4UDeviceforNetwork.png)

**Síťové kabely pro vaše zařízení**

| Štítek | Popis |
| --- | --- |
| A |LAN s přístupem k Internetu |
| B |Řadič 0 |
| C |PCM 0 |
| D |Řadič 1 |
| E |PCM 1 |
| F |Kontroleru EBOD 0 |
| G |Kontroleru EBOD 1 |
| H, MŮŽU |Hostitelé (třeba souborové servery) |
| 0-5 |Síťová rozhraní |
| 6 |Primární zařízení |
| 7 |Skříň EBOD |

Když kabeláže zařízení, vyžaduje minimální požadavky na konfiguraci:

* Minimálně dvě síťová rozhraní připojená na každém řadiči s jeden cloud přístupu a jeden pro iSCSI. DATA 0 portu je automaticky povolen a nakonfigurován prostřednictvím konzoly sériového portu zařízení. Kromě DATA 0 jiný port data také je potřeba nakonfigurovat prostřednictvím portálu Azure classic. V takovém případě připojit DATA 0 port na primární síť LAN (síti s přístupem k Internetu). Jiné porty dat se dá propojit k segmentu sítě SAN a iSCSI sítě LAN (VLAN) sítě, v závislosti na určené role.
* Stejné rozhraní v každém řadiči připojeny ke stejné síti k zajištění dostupnosti, pokud dojde k selhání kontroleru. Například pokud budete chtít připojit DATA 0 a DATA 3 pro jeden z řadičů, budete muset připojit odpovídající DATA 0 a DATA 3 na druhý kontroler.

Mějte na paměti pro vysokou dostupnost a výkon:

* Pokud je to možné, nakonfigurujte na každém řadiči pár síťového rozhraní pro přístup k cloudu (1 GbE) a jinou dvojici pro iSCSI (doporučuje se 10 GbE).
* Pokud je to možné, připojení k dva různé přepínače a zajistit tak dostupnost proti selhání přepínač Síťová rozhraní z každého řadiče. Obrázek znázorňuje 10 dva 10GbE síťové rozhraní DATA 2 a DATA 3 z každého řadiče se připojit k různým přepínačům dvě. Další informace najdete **síťová rozhraní** pod [požadavky na vysokou dostupnost pro zařízení StorSimple](storsimple-8000-system-requirements.md#high-availability-requirements-for-storsimple).

> [!NOTE]
> Pokud používáte SFP + vysílače vaše rozhraní sítí 10 GbE, použijte poskytnutý QSFP-SFP + adaptéry. Další informace najdete v části [hardware pro rozhraní sítí 10 GbE na zařízení StorSimple nepodporuje](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).
> 
> 

### <a name="serial-port-cabling"></a>Kabelů sériového portu
Proveďte následující kroky a propojení kabely sériového portu.

#### <a name="to-cable-for-serial-connection"></a>K zapojení pro sériové připojení
1. Vaše zařízení má sériového portu v každém řadiči, který je identifikován ikona montážního klíče. Vyhledat sériové porty, naleznete na obrázku, který zobrazuje data porty na zadní straně zařízení.
2. Identifikace aktivního kontroleru na propojovací rozhraní systému vašeho zařízení. Blikající Indikátor modré označuje, že je aktivní kontroler.
3. Zadaná sériový kabel (v případě potřeby USB sériové převaděč pro přenosný počítač) a připojení konzoly nebo počítače (emulaci terminálu na zařízení) k sériového portu aktivní kontroler.
4. Instalace ovladačů sériové USB (dodané se zařízením) v počítači.
5. Sériové připojení wmm nastavte takto:
   
   * Přenosová 115 200
   * 8 datové bity
   * 1 stop-bit
   * Žádná
   * Nastavení řízení toku **None**
6. Ověřte, že připojení funguje stisknutím klávesy Enter v konzole. Nabídka konzoly sériového portu zobrazit.

> [!NOTE]
> **Správa ILO:** při instalaci zařízení ve vzdáleném datovém centru nebo v počítačový sál s omezeným přístupem, ujistěte se, že sériové připojení na obou řadičích trvalým připojením k konzoly sériového portu přepínače nebo podobné zařízení. To umožňuje out-of-band vzdáleného řízení a podporu operací v případě výpadků sítě nebo neočekávané chyby.
> 
> 

Dokončili jste kabeláž napájení, přístup k síti a sériové připojení zařízení. Dalším krokem je konfigurace softwaru na vašem zařízení.

## <a name="next-steps"></a>Další postup
Nyní jste připraveni k [nasazení a konfigurace vašeho místního zařízení StorSimple](storsimple-8000-deployment-walkthrough-u2.md).

