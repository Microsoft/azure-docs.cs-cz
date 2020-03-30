---
title: Instalace zařízení Microsoft Azure StorSimple 8600
description: Popisuje, jak rozbalit, připojit do racku a kabel zařízení StorSimple 8600 před nasazením a konfigurací softwaru.
author: alkohli
ms.assetid: 3d82ba5f-3e34-40dc-9c33-50f952bc6be8
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 8d118e71b29b54b034788c37916091ad8f8b996f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254752"
---
# <a name="unpack-rack-mount-and-cable-your-storsimple-8600-device"></a>Vybalte, namontujte do racku a kabelujte zařízení StorSimple 8600

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Přehled
Vaše Microsoft Azure StorSimple 8600 je zařízení se dvěma přílohami a skládá se z primární a EBOD skříň. Tento kurz vysvětluje, jak rozbalit, rack-mount a kabel StorSimple 8600 hardware zařízení před konfigurací softwaru StorSimple.

## <a name="unpack-your-storsimple-8600-device"></a>Rozbalte zařízení StorSimple 8600
Následující kroky poskytují jasné a podrobné pokyny, jak rozbalit paměťové zařízení StorSimple 8600. Toto zařízení je dodáváno ve dvou krabicích, jedna pro primární skříň a druhá pro skříň EBOD. Tyto dvě krabice jsou pak umístěny v jedné krabici.

### <a name="prepare-to-unpack-your-device"></a>Příprava na rozbalení zařízení
Před rozbalením zařízení zkontrolujte následující informace.

![Upozornění](./media/storsimple-safety/IC740879.png)![Ikona těžké](./media/storsimple-8600-hardware-installation/HCS_HeavyWeight_Icon.png) váhy ikona **VAROVÁNÍ!**

1. Ujistěte se, že máte k dispozici dvě osoby pro správu hmotnosti zařízení, pokud s ním manipulujete ručně. Plně nakonfigurovaná skříň může vážit až 32 kg.
2. Umístěte krabici na rovný povrch.

Dále proveďte následující kroky k rozbalení zařízení.

#### <a name="to-unpack-your-device"></a>Rozbalení zařízení
1. Zkontrolujte krabici a výplňový materiál, jestli nejsou pomačkané, pořezané nebo poškozené vodou nebo jestli nenesou stopy jiného zjevného poškození. Pokud jsou krabice nebo obal vážně poškozené, krabici neotevírejte. Obraťte se na [podporu společnosti Microsoft,](storsimple-8000-contact-microsoft-support.md) která vám pomůže posoudit, zda je zařízení v dobrém stavu.
2. Otevřete vnější krabici a pak vyndejte dvě krabice odpovídající primárním a EBOD skříním. Nyní můžete rozbalit primární a EBOD skříně. Následující obrázek znázorňuje nezabalený pohled na jeden z skříní.
   
    ![Rozbalení paměťového zařízení](./media/storsimple-8600-hardware-installation/HCSUnpackyour4Udevice.png)
   
    **Rozbalené zobrazení paměťového zařízení**
   
   | Popisek | Popis |
   | --- | --- |
   |   1 |Balicí krabice |
   |   2 |Kabely SAS (v zásobníku příslušenství a kabelů) |
   |   3 |Spodní pěna |
   |   4 |Zařízení |
   |   5 |Horní pěna |
   |   6 |Krabice s příslušenstvím |
3. Po rozbalení dvou krabic se ujistěte, že máte:
   
   * 1 primární skříň (primární skříň a skříň EBOD jsou ve dvou samostatných krabicích)
   * 1 Skříň EBOD
   * 4 napájecí kabely, 2 v každé krabici
   * 2 Kabely SAS (pro připojení primárního krytu k skříni EBOD)
   * 1 křížený ethernetový kabel
   * 2 kabely sériové konzole
   * 1 sériový USB převodník pro sériový přístup
   * 4 adaptéry QSFP-to-SFP+ pro použití se síťovými rozhraními 10 GbE
   * 2 sady pro montáž do racku (4 boční lišty s montážním hardwarem, 2 pro primární skříň a skříň EBOD), 1 v každé krabici
   * Dokumentace začínáme
     
     Pokud jste neobdrželi žádnou z výše uvedených položek, [obraťte se na podporu společnosti Microsoft](storsimple-8000-contact-microsoft-support.md).  

Dalším krokem je usazení zařízení do racku.

## <a name="rack-mount-your-storsimple-8600-device"></a>Montáž zařízení StorSimple 8600 do racku
Postupujte podle následujících kroků k instalaci úložného zařízení StorSimple 8600 do standardního 19palcového racku s předními a zadními sloupky. Toto zařízení je dodáváno se dvěma skříněmi: primární skříní a skříní EBOD. Oba tyto musí být namontovány do racku.

Instalace se skládá z několika kroků, z nichž každý je popsán v následujících postupech.

> [!IMPORTANT]
> Zařízení StorSimple musí být namontována do racku pro správnou funkci.
> 
> 

### <a name="site-preparation"></a>Příprava staveniště
Skříně musí být instalovány ve standardním 19palcovém stojanu, který má přední i zadní sloupky. Pro přípravu na instalaci do racku použijte následující postup.

#### <a name="to-prepare-the-site-for-rack-installation"></a>Příprava místa pro instalaci do racku
1. Ujistěte se, že primární skříně a skříně EBOD bezpečně spočívají na rovné, stabilní a rovné pracovní ploše (nebo podobné).
2. Ověřte, zda je na webu, kde chcete nastavit, standardní střídavý proud z nezávislého zdroje nebo rackové jednotky pro distribuci napájení (PDU) s nepřerušitelným zdrojem napájení (UPS).
3. Ujistěte se, že jeden slot 4U (2 X 2U) je k dispozici na stojanu, ve kterém chcete skříně namontovat.

![Upozornění](./media/storsimple-safety/IC740879.png)![Ikona těžké](./media/storsimple-8600-hardware-installation/HCS_HeavyWeight_Icon.png) váhy ikona **VAROVÁNÍ!**

 Pokud s nastavením zařízení manipulujete ručně, ujistěte se, že máte k dispozici dvě osoby pro správu hmotnosti. Plně nakonfigurovaná skříň může vážit až 32 kg.

### <a name="rack-prerequisites"></a>Předpoklady racku
Skříně jsou určeny pro instalaci do standardní 19palcové rozvaděče s:

* Minimální hloubka 27,84 palce od stojanu na sloupek
* Maximální hmotnost zařízení 32 kg
* Maximální protitlak 5 Pascalů (0,5 mm vodoměr)

### <a name="rack-mounting-rail-kit"></a>Sada kolejnic pro montáž do racku
Sada montážních lišt bude k dispozici pro použití s 19-palcovou rackovou skříní. Kolejnice byly testovány tak, aby zvládly maximální hmotnost skříně. Tyto kolejnice také umožní instalaci více skříní bez ztráty místa v regálu. Nejprve nainstalujte skříň EBOD.

#### <a name="to-install-the-ebod-enclosure-on-the-rails"></a>Instalace krytu EBOD na kolejnice
1. Tento krok proveďte pouze v případě, že v zařízení nejsou nainstalovány vnitřní lišty. Obvykle jsou vnitřní kolejnice instalovány ve výrobě. Pokud nejsou instalovány kolejnice, nainstalujte po stranách podvozku skříně po levé a pravé liště. Připevňují pomocí šesti metrických šroubů na každé straně. Pro orientaci jsou lištové sklíčka označeny **jako LH – Přední** a RH – **Přední**a konec, který je připevněn k zadní části krytu, má zúžený konec.
   
    ![Připevnění kolejnicových sklíčka k podvozku skříně](./media/storsimple-8600-hardware-installation/HCSAttachingRailSlidestoEnclosureChassis.png)
   
    **Připevnění lištových sklíčka po stranách krytu**
   
   | Popisek | Popis |
   | --- | --- |
   |  1 |Šrouby s knoflíkovou hlavou M 3x4 |
   |  2 |Snímky podvozku |
2. Připojte levé kolejnice a pravé kolejnice sestavy na rack skříň svislé členy. Závorky jsou označeny **jako LH**, **RH**a Tato **strana nahoru,** aby vás provedla správnou orientací.
3. Vyhledejte čepy v přední a zadní části montážní lišty. Vysuňte kolejnici tak, aby se vešla mezi sloupky stojanu, a vložte kolíky do svislých otvorů pro přední a zadní sloupek. Ujistěte se, že sestava kolejnice je v rovině.
4. Zajistěte sestavu kolejnice ke svislým členům stojanu pomocí dvou z dodaných metrických šroubů. Použijte jeden šroub vpředu a jeden na zadní straně.
5. Opakujte tyto kroky pro druhou sestavu kolejnice.
   
     ![Připevnění lištových sklíčka k rozvaděči](./media/storsimple-8600-hardware-installation/HCSAttachingRailSlidestoRackCabinet.png)
   
    **Připevnění kolejnicových sestav ke stojanu**
   
   | Popisek | Popis |
   | --- | --- |
   |   1 |Upínací šroub |
   |   2 |Čtvercový přední šroub ový sloupek |
   |   3 |Levé přední železniční lokalizační kolíky |
   |   4 |Upínací šroub |
   |   5 |Levé zadní kolejnice umístění kolíky |

### <a name="mounting-the-ebod-enclosure-in-the-rack"></a>Montáž skříně EBOD do stojanu
Pomocí právě instalovaných stojanových lišt proveďte následující kroky pro montáž skříně EBOD do stojanu.

#### <a name="to-mount-the-ebod-enclosure"></a>Pro montáž skříně EBOD
1. S asistentem zvedněte kryt a zarovnejte jej s lištami racku.
2. Opatrně vložte kryt do kolejnic a poté jej zcela zatlačte do rozvaděče.
   
    ![Vložení zařízení do stojanu](./media/storsimple-8600-hardware-installation/HCSInsertingDeviceintheRack.png)
   
    **Montáž krytu do stojanu**
3. Odstraňte levé a pravé přední kryty příruby vytažením víčka. Krytky příruby se jednoduše přichytí k přírubám.
4. Zajistěte kryt do stojanu instalací jednoho dodanýho šroubu s křížovou hlavou přes každou přírubu, levou a pravou.
5. Nasaďte kryty příruby tak, že je zatlačíte do polohy a zacvaknete je na místo.
   
     ![Instalace krytů přírub](./media/storsimple-8600-hardware-installation/HCSInstallingFlangeCaps.png)
   
    **Instalace krytů přírub**
   
   | Popisek | Popis |
   | --- | --- |
   |   1 |Upevnění šroubu skříně |

### <a name="mounting-the-primary-enclosure-in-the-rack"></a>Montáž primárního krytu do stojanu
Po dokončení montáže skříně EBOD budete muset připojit primární skříň stejnýmzpůsobem.

> [!NOTE]
> * Je možné mít několik prázdných slotů ve stojanu mezi primární skříní a skříní EBOD.
> * Použijte dodaný 2m SAS kabel pro připojení primární skříně k skříni EBOD.
> * Neexistují žádná omezení pro relativní umístění vedoucí jednotky k jednotce EBOD. Proto může být primární skříň umístěna v horním slotu a skříni EBOD níže - nebo naopak.
> 
> 

Dalším krokem je kabel zařízení pro napájení, síť a sériový přístup.

## <a name="cable-your-storsimple-8600-device"></a>Zapojení kabeláže zařízení StorSimple 8600
Následující postupy vysvětlují, jak vytvořit kabel zařízení StorSimple 8600 pro napájení, síť a sériová připojení.

### <a name="prerequisites"></a>Požadavky
Než začnete zařízení kabelovat, budete potřebovat:

* Váš primární kryt a skříň EBOD, kompletně vybalená
* 4 napájecí kabely (po 2 pro primární a skříň EBOD), které byly dodané se zařízením
* 2 Kabely SAS dodávané se zařízením pro připojení skříně EBOD k primární skříni
* Přístup ke 2 napájecím distribučním jednotkám (PDU) (doporučeno)
* Síťové kabely
* Dodávané sériové kabely
* Serial-USB převodník s příslušným ovladačem nainstalovaným v počítači (v případě potřeby)
* Za předpokladu, 4 QSFP-to-SFP + adaptéry pro použití s 10 GbE síťových rozhraní
* [Podporovaný hardware pro síťová rozhraní 10 GbE na zařízení StorSimple](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)

### <a name="sas-and-power-cabling"></a>Kabeláž SAS a Power
Vaše zařízení má primární kryt i skříň EBOD. To vyžaduje, aby jednotky byly spojeny dohromady pro připojení a napájení Serial Attached SCSI (SAS).

Při prvním nastavení tohoto zařízení proveďte nejprve kroky pro kabeláž SAS a poté proveďte kroky pro napájení kabeláže.

[!INCLUDE [storsimple-cable-8600-for-SAS](../../includes/storsimple-sas-cable-8600.md)]

[!INCLUDE [storsimple-cable-8600-for-power](../../includes/storsimple-cable-8600-for-power.md)]

### <a name="network-cabling"></a>Síťová kabeláž
Vaše zařízení je v aktivní konfiguraci: v každém okamžiku je jeden modul řadiče aktivní a zpracovává všechny operace na disku a síti, zatímco druhý modul řadiče je v pohotovostním režimu. Pokud dojde k selhání řadiče, pohotovostní řadič okamžitě aktivuje a pokračuje ve všech operacích na disku a v síti.

Chcete-li podpořit tento redundantní řadič převzetí služeb při selhání, je třeba kabel sítě zařízení, jak je znázorněno v následujících krocích.

#### <a name="to-cable-for-network-connection"></a>Kabel pro připojení k síti
1. Vaše zařízení má na každém řadiči šest síťových rozhraní: čtyři 1 Gb/s a dva ethernetové porty 10 Gb/s. Na následujícím obrázku najdete datové porty v páteřní rovině zařízení.
   
     ![Zadní rovina zařízení 8600](./media/storsimple-8600-hardware-installation/HCSBackplaneof2UDevicewithPortsLabeled.jpg)
   
    **Zadní část zařízení s datovými porty**
   
   | Popisek | Popis |
   | --- | --- |
   |   0,1,4,5 |1 GbE síťová rozhraní |
   |   2,3 |10 GbE síťová rozhraní |
   |   6 |Sériové porty |
2. Síťové kabely naleznete v následujícím diagramu. (Minimální konfigurace sítě je zobrazena plnými modrými čarami. Pro vysokou dostupnost a výkon je požadována další konfigurace zobrazena tečkovanými čarami.)

![Kabel zařízení 4U pro síť](./media/storsimple-8600-hardware-installation/HCSCableYour4UDeviceforNetwork.png)

**Síťová kabeláž pro vaše zařízení**

| Popisek | Popis |
| --- | --- |
| A |LAN s přístupem k internetu |
| B |Kontroler 0 |
| C |PCM 0 |
| D |Kontroler 1 |
| E |PCM 1 |
| F |Řadič EBOD 0 |
| G |Řadič EBOD 1 |
| H,I |Hostitelé (například souborové servery) |
| 0-5 |Síťová rozhraní |
| 6 |Primární skříň |
| 7 |Skříň EBOD |

Při kabeláži zařízení vyžaduje minimální konfigurace:

* Alespoň dvě síťová rozhraní připojená na každém řadiči s jedním pro přístup ke cloudu a jedním pro iSCSI. Port DATA 0 je automaticky povolen a konfigurován prostřednictvím sériové konzoly zařízení. Kromě DATA 0, další datový port také musí být nakonfigurován prostřednictvím klasického portálu Azure. V takovém případě připojte port DATA 0 k primární síti LAN (síť s přístupem k internetu). Ostatní datové porty mohou být připojeny k segmentu sítě SÍTĚ SÍTĚ/iSCSI LAN (VLAN) v závislosti na zamýšlené roli.
* Identické rozhraní na každém řadiči připojeném ke stejné síti, aby byla zajištěna dostupnost, pokud dojde k převzetí služeb při selhání řadiče. Pokud se například rozhodnete připojit data 0 a data 3 pro jeden z řadičů, musíte připojit odpovídající DATA 0 a DATA 3 na druhém řadiči.

Mějte na paměti vysokou dostupnost a výkon:

* Pokud je to možné, nakonfigurujte dvojici síťového rozhraní pro přístup ke cloudu (1 GbE) a další dvojici pro iSCSI (doporučeno 10 GbE) na každém řadiči.
* Pokud je to možné, připojte síťová rozhraní z každého řadiče ke dvěma různým přepínačům, abyste zajistili dostupnost proti selhání přepínače. Obrázek znázorňuje dvě síťová rozhraní 10 GbE, DATA 2 a DATA 3, z každého řadiče připojeného ke dvěma různým přepínačům. Další informace naleznete v **části Síťová rozhraní** v části Požadavky na vysokou dostupnost zařízení [StorSimple](storsimple-8000-system-requirements.md#high-availability-requirements-for-storsimple).

> [!NOTE]
> Pokud používáte vysílače SFP+ se síťovými rozhraními 10 GbE, použijte dodávané adaptéry QSFP-SFP+. Další informace naleznete v části [Podporovaný hardware síťových rozhraní 10 GbE na zařízení StorSimple](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).
> 
> 

### <a name="serial-port-cabling"></a>Kabeláž sériového portu
Provedením následujících kroků pro kabel sériového portu.

#### <a name="to-cable-for-serial-connection"></a>Ke kabelu pro sériové připojení
1. Zařízení má na každém ovladači sériový port, který je označen ikonou klíče. Sériové porty najdete na obrázku, který zobrazuje datové porty na zadní straně zařízení.
2. Identifikujte aktivní ovladač v backplane zařízení. Blikající modrá LED dioda signalizuje, že ovladač je aktivní.
3. Použijte dodaný sériový kabel (v případě potřeby usb-sériový převodník pro váš notebook) a připojte konzolu nebo počítač (s emulací terminálu k zařízení) k sériovému portu aktivního ovladače.
4. Nainstalujte do počítače ovladače sériového USB (dodávané se zařízením).
5. Sériové připojení nastavte následujícím způsobem:
   
   * 115 200 baudů
   * 8 datových bitů
   * 1 stop bit
   * Žádná parita
   * Řízení toku nastaveno na **žádný**
6. Stisknutím klávesy Enter na konzole ověřte, zda připojení funguje. Měla by se zobrazit nabídka sériové konzoly.

> [!NOTE]
> **Správa světel a zhasínání světel:** Pokud je zařízení nainstalováno ve vzdáleném datovém centru nebo v počítačové místnosti s omezeným přístupem, ujistěte se, že sériová připojení k oběma řadičům jsou vždy připojena k přepínači sériové konzole nebo podobnému zařízení. To umožňuje mimopásmové operace dálkového ovládání a podpory v případě přerušení sítě nebo neočekávaných selhání.
> 
> 

Dokončili jste kabeláž zařízení pro napájení, přístup k síti a sériové připojení. Dalším krokem je konfigurace softwaru v zařízení.

## <a name="next-steps"></a>Další kroky
Nyní jste připraveni [nasadit a nakonfigurovat místní zařízení StorSimple](storsimple-8000-deployment-walkthrough-u2.md).

