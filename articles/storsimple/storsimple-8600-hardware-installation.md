---
title: Nainstalovat zařízení Microsoft Azure StorSimple 8600
description: Popisuje, jak před nasazením a konfigurací softwaru rozbalit, montovat do racku a zapojte zařízení s StorSimple 8600.
author: alkohli
ms.assetid: 3d82ba5f-3e34-40dc-9c33-50f952bc6be8
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 8d118e71b29b54b034788c37916091ad8f8b996f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "91871809"
---
# <a name="unpack-rack-mount-and-cable-your-storsimple-8600-device"></a>Rozbalení, připojení k racku a zapojení zařízení StorSimple 8600

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Přehled
Vaše Microsoft Azure StorSimple 8600 je zařízení se dvěma skříňmi a skládá se z primárního a EBOD skříně. V tomto kurzu se dozvíte, jak před konfigurací softwaru StorSimple nabalit, montovat do racku a kabelovat hardware zařízení StorSimple 8600.

## <a name="unpack-your-storsimple-8600-device"></a>Rozbalení zařízení StorSimple 8600
Následující kroky poskytují jasné a podrobné pokyny k rozbalení úložného zařízení StorSimple 8600. Toto zařízení se dodává ve dvou krabicích, jednu pro primární skříň a druhou pro EBOD skříň. Tato dvě pole jsou pak umístěna do jednoho pole.

### <a name="prepare-to-unpack-your-device"></a>Příprava na rozbalení zařízení
Před rozbalením zařízení si přečtěte následující informace.

![Ikona upozornění ](./media/storsimple-safety/IC740879.png)![ velké váhy ikona ](./media/storsimple-8600-hardware-installation/HCS_HeavyWeight_Icon.png) **Upozornění!**

1. Ujistěte se, že máte k dispozici dvě osoby, abyste mohli spravovat váhu zařízení, pokud ho zpracováváte ručně. Plně nakonfigurovaná skříň může vážit až 32 kg (70 kg).
2. Umístěte krabici na rovný povrch.

Dále proveďte následující kroky a rozbalte své zařízení.

#### <a name="to-unpack-your-device"></a>Postup rozbalení zařízení
1. Zkontrolujte krabici a výplňový materiál, jestli nejsou pomačkané, pořezané nebo poškozené vodou nebo jestli nenesou stopy jiného zjevného poškození. Pokud jsou krabice nebo obal vážně poškozené, krabici neotevírejte. Obraťte se prosím na [Podpora Microsoftu](storsimple-8000-contact-microsoft-support.md) , které vám pomůžou posoudit, jestli je zařízení v dobrém provozním pořadí.
2. Otevřete vnější pole a pak proveďte dvě krabice odpovídající primárním a EBOD skříni. Nyní můžete rozbalit primární a EBOD skříňky. Následující obrázek ukazuje nebalené zobrazení jednoho ze skříní.
   
    ![Rozbalení úložného zařízení](./media/storsimple-8600-hardware-installation/HCSUnpackyour4Udevice.png)
   
    **Nebalené zobrazení úložného zařízení**
   
   | Popisek | Description |
   | --- | --- |
   |   1 |Expediční box |
   |   2 |Kabely SAS (v zásobníku příslušenství a kabely) |
   |   3 |Spodní pěna |
   |   4 |Zařízení |
   |   5 |Hlavní pěna |
   |   6 |Pole pro příslušenství |
3. Po rozbalení obou polí se ujistěte, že máte následující:
   
   * 1 primární skříň (primární skříň a skříň EBOD jsou ve dvou samostatných polích)
   * 1 skříň EBOD
   * 4 napájecí šňůra, 2 v každém boxu
   * 2 kabely SAS (pro připojení primární skříně k skříni EBOD)
   * 1 křížený kabel Ethernet
   * 2 kabely sériové konzoly
   * 1 sériový převaděč USB pro sériový přístup
   * 4 QSFP-to-SFP + adaptéry pro použití se síťovými rozhraními 10 GbE
   * 2 rackové sady pro připojení (4 boční kolejnice s připojením hardwaru, 2 každé pro primární skříň a skříň EBOD), 1 v každém poli
   * Dokumentace Začínáme
     
     Pokud jste neobdrželi žádnou z výše uvedených položek, [kontaktujte podpora Microsoftu](storsimple-8000-contact-microsoft-support.md).  

Dalším krokem je usazení zařízení do racku.

## <a name="rack-mount-your-storsimple-8600-device"></a>Stojan – připojení zařízení StorSimple 8600
Postupujte podle dalších kroků a nainstalujte zařízení úložiště StorSimple 8600 ve standardní racku na 19 – 15palcový s předními a zadními příspěvky. Toto zařízení se dodává se dvěma skříními: primární skříň a skříň EBOD. Oba tyto typy musí být připojeny k racku.

Instalace se skládá z několika kroků, z nichž každá je popsána v následujících postupech.

> [!IMPORTANT]
> Zařízení StorSimple musí být připojená do racku pro správnou operaci.
> 
> 

### <a name="site-preparation"></a>Příprava lokality
Skříně musí být nainstalované na standardu 19 palců, který obsahuje obě přední i zadní stanoviště. K přípravě instalace racku použijte následující postup.

#### <a name="to-prepare-the-site-for-rack-installation"></a>Příprava lokality na instalaci racku
1. Ujistěte se, že primární a EBOD skříňce bezpečně odcházejí na plochém, stabilním a pracovním povrchu (nebo podobném).
2. Ověřte, že lokalita, kde máte v úmyslu nastavit, má standardní sílu napájení z nezávislého zdroje nebo jednotky PDU napájení (AC) s nepřerušitelným zdrojem napájení (UPS).
3. Ujistěte se, že je jeden slot 4U (2 X 2U) dostupný na stojanu, ve kterém máte v úmyslu připojit skříně.

![Ikona upozornění ](./media/storsimple-safety/IC740879.png)![ velké váhy ikona ](./media/storsimple-8600-hardware-installation/HCS_HeavyWeight_Icon.png) **Upozornění!**

 Ujistěte se, že máte ke správě váhy k dispozici dvě osoby, pokud spouštíte ruční nastavení zařízení. Plně nakonfigurovaná skříň může vážit až 32 kg (70 kg).

### <a name="rack-prerequisites"></a>Požadavky na stojan
Skříně jsou navržené pro instalaci ve standardní skříňové skříni z 19 mm s:

* Minimální hloubka 27,84 palců z rackového příspěvku na post
* Maximální váha 32 kg pro zařízení
* Maximální tlak v 5 Pascal (0,5 mm měřič vody)

### <a name="rack-mounting-rail-kit"></a>Montážní skříň – montáž železniční Kit
K dispozici je sada montážní kolejnice, která se bude používat pro skříň na racku s 19 mm. Kolejnice byly testovány tak, aby zpracovávala maximální váhu skříně. Tyto kolejnice budou také umožňovat instalaci více skříní bez ztráty místa v rámci stojanu. Nejdřív nainstalujte skříň EBOD.

#### <a name="to-install-the-ebod-enclosure-on-the-rails"></a>Instalace skříně EBOD na kolejnicích
1. Tento krok proveďte jenom v případě, že na zařízení nejsou nainstalované vnitřní kolejnice. Vnitřní kolejnice jsou obvykle nainstalovány v továrně. Pokud nejsou kolejnice nainstalované, nainstalujte snímky z levé a pravé kolejnice na strany skříně skříně. Připojovat se šesti metrickými šrouby na každé straně. Pro usnadnění orientace jsou železniční snímky označeny jako **LH – přední** a **RH – dopředu** a konec, který je připevněn ke zadní části skříně, má zúžený konec.
   
    ![Připojení železničních snímků k skříni skříně](./media/storsimple-8600-hardware-installation/HCSAttachingRailSlidestoEnclosureChassis.png)
   
    **Připojení kolejnicových snímků ke stěnám skříně**
   
   | Popisek | Description |
   | --- | --- |
   |  1 |M 3x4 tlačítka – šrouby hlav |
   |  2 |Snímky skříní |
2. Připojte levou kolejnici a pravou železniční sestavení k vertikálním členům racku Cabinet. Závorky jsou označené jako **LH**, **RH** a **Tato strana až** po správné orientaci.
3. Vyhledejte čepy v přední a zadní části montážní lišty. Rozšíříte kolejnici tak, aby vyhovovala mezi příspěvky v racku, a vkládat PIN kódy do front a zadních stojanů po svislých členských otvorech. Ujistěte se, že je na úrovni železničního sestavení úroveň.
4. Zabezpečte železniční sestavení na vertikálních členech racku pomocí dvou dostupných příšroubů metriky. Použijte jeden šroub na přední stranu a druhý na zadní straně.
5. Opakujte tyto kroky pro druhé železniční sestavení.
   
     ![Připojení snímků po železnici k racku CAB](./media/storsimple-8600-hardware-installation/HCSAttachingRailSlidestoRackCabinet.png)
   
    **Připojení železničních sestavení k stojanu**
   
   | Popisek | Description |
   | --- | --- |
   |   1 |Montážní šroub |
   |   2 |Šroubový prstenec na druhou stranu |
   |   3 |Kódy PIN pro polohu v levé horní části |
   |   4 |Montážní šroub |
   |   5 |PIN kódy s levou zadní kolejnicí |

### <a name="mounting-the-ebod-enclosure-in-the-rack"></a>Připojení skříně EBOD do stojanu
Pomocí následujících kroků připojte skříň EBOD do racku a použijte rackové kolejnice, které jste právě nainstalovali, a proveďte následující kroky.

#### <a name="to-mount-the-ebod-enclosure"></a>Připojení skříně EBOD
1. S pomocníkem, zvedněte skříň a zarovnejte ji se rozvaděčovou kolejnicí.
2. Pečlivě vložte skříň do kolejnic a pak ji nahrajte úplně do racku CAB.
   
    ![Vkládání zařízení do stojanu](./media/storsimple-8600-hardware-installation/HCSInsertingDeviceintheRack.png)
   
    **Připojení skříně do stojanu**
3. Odstraňte levou a pravou přední stranu přírubu tím, že uvolníte bezplatnou volnost. Zakončení přírub stačí Přitahovat na příruby.
4. Zabezpečíte skříň na rack tím, že si nainstalujete jednu poskytnutou Phillips hlavičku prostřednictvím každé příruby, doleva a doprava.
5. Odložte zakončení přírubami tak, že je stisknete na pozici a přichycením na ně.
   
     ![Instalace Cap k přírubám](./media/storsimple-8600-hardware-installation/HCSInstallingFlangeCaps.png)
   
    **Instalace Cap k přírubám**
   
   | Popisek | Description |
   | --- | --- |
   |   1 |Šroub uzávěrky skříně |

### <a name="mounting-the-primary-enclosure-in-the-rack"></a>Připojení primární skříně do stojanu
Po dokončení připojení k skříni EBOD budete muset primární skříň připojit podle stejného postupu.

> [!NOTE]
> * Je možné mít v racku několik prázdných slotů mezi primární skříňkou a skříňkou EBOD.
> * K připojení primární skříně k skříni EBOD použijte poskytnutý kabel SAS 2 min.
> * Neexistují žádná omezení relativního umístění hlavní jednotky do jednotky EBOD. Proto lze primární skříň umístit do horního slotu a z EBOD skříně níže (nebo naopak).
> 
> 

Dalším krokem je připojení zařízení k napájení, síti a sériovému přístupu.

## <a name="cable-your-storsimple-8600-device"></a>Zapojení kabeláže zařízení StorSimple 8600
Následující postupy vysvětlují, jak můžete zařízení StorSimple 8600 připojit k napájení, síťovému a sériovému připojení.

### <a name="prerequisites"></a>Předpoklady
Než začnete kabelovat zařízení, budete potřebovat:

* Vaše primární skříň a skříň EBOD kompletně rozbalené
* 4 napájecí kabely (2 pro primární a EBOD skříň) dodávané s vaším zařízením
* 2 kabely SAS dodávané se zařízením pro připojení skříně EBOD k primární skříni
* Přístup k 2 jednotkám distribuce napájení (doporučeno)
* Síťové kabely
* Zadané sériové kabely
* Převaděč sériového portu USB s příslušným ovladačem nainstalovaným na vašem počítači (v případě potřeby)
* Poskytnuté 4 adaptéry QSFP-to-SFP + pro použití se síťovými rozhraními 10 GbE
* [Podporovaný hardware pro síťová rozhraní 10 GbE na zařízení StorSimple](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)

### <a name="sas-and-power-cabling"></a>SAS a kabely napájení
Vaše zařízení má primární skříň i skříň EBOD. To vyžaduje, aby jednotky byly zapojeny pro připojení a napájení přes SAS (Serial Attached SCSI).

Při prvním nastavování tohoto zařízení proveďte kroky pro kabely SAS a pak dokončete postup pro kabely napájení.

[!INCLUDE [storsimple-cable-8600-for-SAS](../../includes/storsimple-sas-cable-8600.md)]

[!INCLUDE [storsimple-cable-8600-for-power](../../includes/storsimple-cable-8600-for-power.md)]

### <a name="network-cabling"></a>Síťové kabely
Vaše zařízení je v konfiguraci s aktivním pohotovostním režimem: v jednom okamžiku je aktivní jeden modul kontroleru a zpracovává všechny operace disku a sítě, zatímco je druhý modul kontroleru v pohotovostním režimu. Pokud dojde k selhání řadiče, pohotovostní kontroler okamžitě aktivuje a pokračuje všemi operacemi disků a sítí.

Pro podporu tohoto redundantního převzetí služeb při selhání je potřeba kabelovat síť zařízení, jak je znázorněno v následujícím postupu.

#### <a name="to-cable-for-network-connection"></a>Pro připojení k síti
1. Vaše zařízení má na každém řadiči šest síťových rozhraní: porty Ethernet 4 1 GB/s a 2 10 GB/s. Pro identifikaci datových portů při replánování zařízení použijte následující obrázek.
   
     ![Backplane pro zařízení 8600](./media/storsimple-8600-hardware-installation/HCSBackplaneof2UDevicewithPortsLabeled.jpg)
   
    **Zpět zařízení, které zobrazuje datové porty**
   
   | Popisek | Description |
   | --- | --- |
   |   0, 1, 4, 5 |Síťová rozhraní 1 GbE |
   |   2, 3 |Síťová rozhraní 10 GbE |
   |   6 |Sériové porty |
2. Podívejte se na následující diagram pro síťové kabely. (Minimální konfigurace sítě se zobrazuje na plných modrých řádcích. Pro zajištění vysoké dostupnosti a výkonu se vyžaduje další konfigurace, kterou zobrazují tečkované řádky.)

![Zapojení zařízení 4U do sítě](./media/storsimple-8600-hardware-installation/HCSCableYour4UDeviceforNetwork.png)

**Síťové kabely pro vaše zařízení**

| Popisek | Description |
| --- | --- |
| A |SÍŤ LAN s přístupem k Internetu |
| B |Kontroler 0 |
| C |PCM 0 |
| D |Kontroler 1 |
| E |PCM 1 |
| F |EBOD Controller 0 |
| G |EBOD řadič 1 |
| H, I |Hostitelé (například souborové servery) |
| 0-5 |Síťová rozhraní |
| 6 |Primární skříň |
| 7 |EBOD skříň |

Minimální konfigurace při kabeláži zařízení vyžaduje:

* Aspoň dvě síťová rozhraní připojená na každém řadiči s jedním pro cloudový přístup a jednu pro iSCSI. Port DATA 0 je automaticky povolen a nakonfigurován prostřednictvím sériové konzoly zařízení. Kromě DATA 0 je také potřeba nakonfigurovat další datový port na portálu Azure Classic. V takovém případě připojte k primární síti LAN port DATA 0 (síť s přístupem k Internetu). Ostatní datové porty lze připojit k segmentu sítě SAN nebo iSCSI LAN (VLAN) v závislosti na zamýšlené roli.
* Stejné rozhraní na každém řadiči připojeném ke stejné síti, aby se zajistila dostupnost, pokud dojde k převzetí služeb při selhání řadiče. Pokud se například rozhodnete připojit k jednomu z řadičů DATA 0 a DATA 3, musíte na druhém řadiči připojit odpovídající DATA 0 a DATA 3.

Pamatujte na vysokou dostupnost a výkon:

* Pokud je to možné, nakonfigurujte dvojici síťového rozhraní pro cloudový přístup (1 GbE) a další pár pro iSCSI (doporučeno 10 MB) na každém řadiči.
* Pokud je to možné, připojte síťová rozhraní z každého kontroleru k dvěma různým přepínačům, abyste zajistili dostupnost v případě výpadku přepínače. Obrázek znázorňuje síťová rozhraní 2 10 GbE, DATA 2 a DATA 3 z každého kontroleru připojeného ke dvěma různým přepínačům. Další informace najdete v tématu **Síťová rozhraní** podle [požadavků vysoké dostupnosti pro vaše zařízení StorSimple](storsimple-8000-system-requirements.md#high-availability-requirements-for-storsimple).

> [!NOTE]
> Pokud používáte síťové rozhraní SFP a přijímače s 10 GbE, použijte poskytnuté adaptéry QSFP-SFP +. Další informace najdete na [podporovaném hardwaru pro síťová rozhraní 10 GbE na zařízení StorSimple](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).
> 
> 

### <a name="serial-port-cabling"></a>Kabely sériového portu
K zapojení sériového portu proveďte následující kroky.

#### <a name="to-cable-for-serial-connection"></a>Kabel pro sériové připojení
1. Vaše zařízení má sériový port na každém kontroleru, který je identifikovaný ikonou klíče. Pokud chcete najít sériové porty, přečtěte si obrázek, který zobrazuje datové porty na zadní straně zařízení.
2. Identifikujte aktivní kontroler v rámci plánu vašeho zařízení. Blikání INDIKÁTORu Blue indikuje, že je kontroler aktivní.
3. Použijte zadaný sériový kabel (Pokud je to potřeba, převaděč sériového čísla USB pro přenosný počítač) a připojte svoji konzolu nebo počítač (s emulací terminálu k zařízení) k sériovému portu aktivního řadiče.
4. Nainstalujte na svém počítači ovladače sériového rozhraní USB (dodávané se zařízením).
5. Sériové připojení nastavte následujícím způsobem:
   
   * 115 200 baud
   * 8 datových bitů
   * 1 stopový bit
   * Bez parity
   * Řízení toku nastaveno na **možnost žádné**
6. Ověřte, zda připojení funguje, stisknutím klávesy ENTER v konzole nástroje. Měla by se zobrazit nabídka konzoly sériového portu.

> [!NOTE]
> **Správa světla:** Když je zařízení nainstalované ve vzdáleném datovém centru nebo v počítačové místnosti s omezeným přístupem, ujistěte se, že sériová připojení k oběma řadičům jsou vždycky připojená k přepínači sériové konzoly nebo podobnému vybavení. To umožňuje vzdálené řízení a operace podpory v případě výpadku sítě nebo neočekávaných selhání.
> 
> 

Dokončili jste propojení zařízení s napájením, přístupem k síti a sériovým připojením. Dalším krokem je konfigurace softwaru na vašem zařízení.

## <a name="next-steps"></a>Další kroky
Teď jste připraveni [nasadit a nakonfigurovat místní zařízení StorSimple](storsimple-8000-deployment-walkthrough-u2.md).

