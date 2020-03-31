---
title: Instalace zařízení Microsoft Azure StorSimple 8100
description: Popisuje, jak rozbalit, rack připojit a kabel zařízení StorSimple 8100 před nasazením a konfigurací softwaru.
author: alkohli
ms.assetid: 6098a01e-c031-488a-a8d7-0b607ce665e1
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 113b72ddf7e5d508c8a0b577d4004d4fbd83e8e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267583"
---
# <a name="unpack-rack-mount-and-cable-your-storsimple-8100-device"></a>Vybalte, namontujte do racku a kabelujte zařízení StorSimple 8100

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Přehled
Vaše Zařízení Microsoft Azure StorSimple 8100 je jedno skříňové zařízení připojené do racku. Tento kurz vysvětluje, jak rozbalit, rack-mount a kabel StorSimple 8100 hardware zařízení před konfigurací a nasazením zařízení StorSimple.

## <a name="unpack-your-storsimple-8100-device"></a>Rozbalte zařízení StorSimple 8100
Následující kroky poskytují jasné a podrobné pokyny o tom, jak rozbalit paměťové zařízení StorSimple 8100. Toto zařízení se dodává v jedné krabici.

### <a name="prepare-to-unpack-your-device"></a>Příprava na rozbalení zařízení
Před rozbalením zařízení zkontrolujte následující informace.

![Upozornění](./media/storsimple-safety/IC740879.png)![Ikona těžké](./media/storsimple-8100-hardware-installation/HCS_HeavyWeight_Icon.png) váhy ikona **VAROVÁNÍ!**

1. Ujistěte se, že máte k dispozici dvě osoby pro správu hmotnosti skříně, pokud s ním manipulujete ručně. Plně nakonfigurovaná skříň může vážit až 32 kg.
2. Umístěte krabici na rovný povrch.

Dále proveďte následující kroky k rozbalení zařízení.

#### <a name="to-unpack-your-device"></a>Rozbalení zařízení
1. Zkontrolujte krabici a výplňový materiál, jestli nejsou pomačkané, pořezané nebo poškozené vodou nebo jestli nenesou stopy jiného zjevného poškození. Pokud jsou krabice nebo obal vážně poškozené, krabici neotevírejte. Obraťte se na [podporu společnosti Microsoft,](storsimple-8000-contact-microsoft-support.md) která vám pomůže posoudit, zda je zařízení v dobrém stavu.
2. Rozbalte krabici. Následující obrázek znázorňuje nezabalené zobrazení zařízení StorSimple.
   
     ![Rozbalení paměťového zařízení](./media/storsimple-8100-hardware-installation/HCSUnpackyour2Udevice.png)
   
    **Rozbalené zobrazení paměťového zařízení**
   
   | Popisek | Popis |
   | --- | --- |
   |   1 |Balicí krabice |
   |   2 |Spodní pěna |
   |   3 |Zařízení |
   |   4 |Horní pěna |
   |   5 |Krabice s příslušenstvím |
3. Po rozbalení krabice se ujistěte, že obsahuje následující:
   
   * 1 jedno skříňové zařízení
   * 2 napájecí kabely
   * 1 křížený ethernetový kabel
   * 2 kabely sériové konzole
   * 1 sériový USB převodník pro sériový přístup
   * 1 šroubovák T10 odolný proti manipulaci
   * 4 adaptéry QSFP-to-SFP+ pro použití se síťovými rozhraními 10 GbE
   * 1 sada pro montáž do racku (2 boční lišty s montážním hardwarem)
   * Dokumentace začínáme
     
     Pokud jste neobdrželi žádnou z výše uvedených položek, [obraťte se na podporu společnosti Microsoft](storsimple-8000-contact-microsoft-support.md).

Dalším krokem je usazení zařízení do racku.

## <a name="rack-mount-your-storsimple-8100-device"></a>Montáž zařízení StorSimple 8100 do racku
Postupujte podle následujících kroků k instalaci paměťového zařízení StorSimple 8100 do standardního 19palcového racku s předními a zadními sloupky . Zařízení StorSimple 8100 má jednu primární skříň.

Instalace se skládá z několika kroků, z nichž každý je popsán v následujících postupech.

> [!IMPORTANT]
> Zařízení StorSimple musí být namontována do racku pro správnou funkci.
> 
> 

### <a name="prepare-the-site"></a>Příprava webu
Zařízení musí být instalováno ve standardním 19palcovém stojanu, který má přední i zadní sloupky. Pro přípravu na instalaci do racku použijte následující postup.

#### <a name="to-prepare-the-site-for-rack-installation"></a>Příprava místa pro instalaci do racku
1. Ujistěte se, že je zařízení bezpečně umístěné na rovném a stabilním pracovním povrchu (nebo něčem podobném).
2. Ověřte, zda je na webu, kde chcete nastavit, standardní střídavý proud z nezávislého zdroje nebo z rozdělovací jednotky do racku (PDU) s nepřerušitelným zdrojem napájení (UPS).
3. Ujistěte se, že je na stojanu, do kterého chcete zařízení namontovat, k dispozici jeden slot 2U.

![Upozornění](./media/storsimple-safety/IC740879.png)![Ikona těžké](./media/storsimple-8100-hardware-installation/HCS_HeavyWeight_Icon.png) váhy ikona **VAROVÁNÍ!**

Pokud s nastavením zařízení manipulujete ručně, ujistěte se, že máte k dispozici dvě osoby pro správu hmotnosti. Plně nakonfigurovaná skříň může vážit až 32 kg.

### <a name="rack-prerequisites"></a>Předpoklady racku
Skříň 8100 je určena pro instalaci do standardní 19palcové rackové skříňky s:

* Minimální hloubka 27,84 palce od stojanu na sloupek.
* Maximální hmotnost zařízení 32 kg
* Maximální protitlak 5 Pascal (0,5 mm vodoměr).

### <a name="rack-mounting-rail-kit"></a>Sada kolejnic pro montáž do racku
Sada montážních lišt je k dispozici pro použití s 19-palcovou rackovou skříní. Kolejnice byly testovány tak, aby zvládly maximální hmotnost skříně. Tyto kolejnice také umožní instalaci více skříní bez ztráty místa v racku.

#### <a name="to-install-the-device-on-the-rails"></a>Instalace zařízení na kolejnice
1. Tento krok proveďte pouze v případě, že v zařízení nejsou nainstalovány vnitřní lišty. Obvykle jsou vnitřní kolejnice instalovány ve výrobě. Pokud nejsou instalovány kolejnice, nainstalujte po stranách podvozku skříně po levé a pravé liště. Připevňují pomocí šesti metrických šroubů na každé straně. Pro orientaci jsou lištové sklíčka označeny **jako LH – Přední** a RH – **Přední**a konec, který je připevněn k zadní části krytu, má zúžený konec.<br/>
   
    ![Připevnění kolejnicových sklíčka k podvozku skříně](./media/storsimple-8100-hardware-installation/HCSAttachingRailSlidestoEnclosureChassis.png)

    **Připevnění vnitřních lištových skluzů po stranách krytu**
   
    Popisek | Popis
    ----- | -----------
    1     | Šrouby s knoflíkovou hlavou M 3x4
    2     | Snímky podvozku

2. Připojte vnější levou lištu a vnější pravé lišty k vertikálním členům rozvaděče. Závorky jsou označeny **jako LH**, **RH**a Tato **strana nahoru,** aby vás provedla správnou orientací.
3. Vyhledejte čepy v přední a zadní části montážní lišty. Vysuňte kolejnici tak, aby se vešla mezi sloupky stojanu, a vložte kolíky do předních a zadních sloupků stojanu svislých otvorů pro členy. Ujistěte se, že sestava kolejnice je v rovině.
4. K zajištění sestavy kolejnice na svislé členy stojanu použijte dva z dodaných metrických šroubů. Použijte jeden šroub vpředu a jeden na zadní straně.
5. Opakujte tyto kroky pro druhou sestavu kolejnice.<br/>
   
     ![Připevnění lištových sklíčka k rozvaděči](./media/storsimple-8100-hardware-installation/HCSAttachingRailSlidestoRackCabinet.png)
   
    **Připevnění sestav vnější kolejnice ke stojanu**
   
   | Popisek | Popis |
   | --- | --- |
   |   1 |Upínací šroub |
   |   2 |Čtvercový přední šroub ový sloupek |
   |   3 |Kolíky levého předního místa |
   |   4 |Upínací šroub |
   |   5 |Levé kolejnice zadní umístění kolíky |

### <a name="mounting-the-device-in-the-rack"></a>Montáž zařízení do racku
Pomocí právě nainstalovaných lišt racků proveďte následující kroky pro montáž zařízení do stojanu.

#### <a name="to-mount-the-device"></a>Připojení zařízení
1. S asistentem zvedněte kryt a zarovnejte jej s lištami racku.
2. Opatrně zasuňte zařízení do kolejnic a poté jej zcela zatlačte do rozvaděče.<br/>
   
    ![Vložení zařízení do stojanu](./media/storsimple-8100-hardware-installation/HCSInsertingDeviceintheRack.png)
   
    **Montáž zařízení do racku**
3. Odstraňte levé a pravé přední kryty příruby vytažením víčka. Krytky příruby se jednoduše přichytí k přírubám.
4. Zajistěte kryt ve stojanu instalací jednoho dodanýho šroubu s křížovou hlavou přes každou přírubu, levou a pravou.
5. Nasaďte kryty příruby tak, že je zatlačíte do polohy a přitisknete je na místo.<br/>
   
     ![Instalace krytů přírub](./media/storsimple-8100-hardware-installation/HCSInstallingFlangeCaps.png)
   
    **Instalace krytů přírub**
   
   | Popisek | Popis |
   | --- | --- |
   |   1 |Upevnění šroubu skříně |

Dalším krokem je kabel zařízení pro napájení, síť a sériový přístup.

## <a name="cable-your-storsimple-8100-device"></a>Zapojení kabeláže zařízení StorSimple 8100
Následující postupy vysvětlují, jak vytvořit kabel zařízení StorSimple 8100 pro napájení, síť a sériová připojení.

### <a name="prerequisites"></a>Požadavky
Než začnete zapojovat kabeláž zařízení, budete potřebovat následující:

* Vaše úložné zařízení, zcela vybalené a namontované do racku.
* 2 napájecí kabely dodané se zařízením
* Přístup ke 2 napájecím distribučním jednotkám (doporučeno).
* Síťové kabely
* Dodávané sériové kabely
* Sériový USB převodník s příslušným ovladačem nainstalovaným v počítači (v případě potřeby)
* Za předpokladu, 4 QSFP-to-SFP + adaptéry pro použití s 10 GbE síťových rozhraní
* [Podporovaný hardware pro síťová rozhraní 10 GbE na zařízení StorSimple](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)

### <a name="power-cabling"></a>Elektrická kabeláž
Zařízení obsahuje redundantní napájecí a chladicí moduly (PCM). Aby byla zajištěna vysoká dostupnost, musí být oba pcmy nainstalovány a připojeny k různým zdrojům napájení.

Proveďte následující kroky k připojení zařízení k napájení.

[!INCLUDE [storsimple-cable-8100-for-power](../../includes/storsimple-cable-8100-for-power.md)]

### <a name="network-cabling"></a>Síťová kabeláž
Vaše zařízení je aktivní konfigurace v pohotovostním režimu: v každém okamžiku je jeden modul řadiče aktivní a zpracovává všechny operace na disku a síti, zatímco druhý modul řadiče je v pohotovostním režimu. Pokud řadič selže, pohotovostní řadič se aktivuje okamžitě a pokračuje ve všech operacích disku a sítě.

Chcete-li podpořit tento redundantní řadič převzetí služeb při selhání, je třeba kabel sítě zařízení, jak je popsáno v následujících krocích.

#### <a name="to-cable-for-network-connection"></a>Kabel pro připojení k síti
1. Vaše zařízení má na každém řadiči šest síťových rozhraní: čtyři 1 Gb/s a dva ethernetové porty 10 Gb/s. Identifikujte různé datové porty v backplane vašeho zařízení.
   
    ![Zadní rovina zařízení 8100](./media/storsimple-8100-hardware-installation/HCSBackplaneof2UDevicewithPortsLabeled.jpg)
   
    **Zadní část zařízení zobrazující datové porty**
   
   | Popisek | Popis |
   | --- | --- |
   |   0,1,4,5 |1 GbE síťová rozhraní |
   |   2,3 |10 GbE síťová rozhraní |
   |   6 |Sériové porty |
2. Síťové kabely naleznete v následujícím diagramu. (Minimální konfigurace sítě je zobrazena plnými modrými čarami. Další konfigurace požadovaná pro vysokou dostupnost a výkon je zobrazena tečkovanými čarami.)

    ![Kabel zařízení 2U pro síť](./media/storsimple-8100-hardware-installation/HCSCableYour2UDeviceforNetwork.png)

    **Síťová kabeláž pro vaše zařízení**

   |Popisek | Popis |
   |----- | ----------- |
   | A    | LAN s přístupem k internetu |
   | B    | Kontroler 0 |
   | C    | PCM 0 |
   | D    | Kontroler 1 |
   | E    | PCM 1 |
   | F, G | Hostitelé |
   | 0-5  | Síťová rozhraní |



Při kabeláži zařízení vyžaduje minimální konfigurace:

* Alespoň dvě síťová rozhraní připojená na každém řadiči s jedním pro přístup ke cloudu a jedním pro iSCSI. Port DATA 0 je automaticky povolen a konfigurován prostřednictvím sériové konzoly zařízení. Kromě DATA 0, další datový port také musí být nakonfigurován prostřednictvím klasického portálu Azure. V takovém případě připojte port DATA 0 k primární síti LAN (síť s přístupem k internetu). Ostatní datové porty mohou být připojeny k segmentu sítě SÍTĚ SÍTĚ/iSCSI LAN (VLAN) v závislosti na zamýšlené roli.
* Identické rozhraní na každém řadiči připojeném ke stejné síti, aby byla zajištěna dostupnost, pokud dojde k převzetí služeb při selhání řadiče. Pokud se například rozhodnete připojit data 0 a data 3 pro jeden z řadičů, musíte připojit odpovídající DATA 0 a DATA 3 na druhém řadiči.

Mějte na paměti vysokou dostupnost a výkon:

* Pokud je to možné, nakonfigurujte dvojici síťového rozhraní pro přístup ke cloudu (1 GbE) a další dvojici pro iSCSI (doporučeno 10 GbE) na každém řadiči.
* Pokud je to možné, připojte síťová rozhraní z každého řadiče ke dvěma různým přepínačům, abyste zajistili dostupnost proti selhání přepínače. Obrázek znázorňuje dvě síťová rozhraní 10 GbE, DATA 2 a DATA 3, z každého řadiče připojeného ke dvěma různým přepínačům.

Další informace naleznete v **části Síťová rozhraní** v části Požadavky na vysokou dostupnost zařízení [StorSimple](storsimple-8000-system-requirements.md#high-availability-requirements-for-storsimple).

> [!NOTE]
> Pokud používáte vysílače SFP+ se síťovými rozhraními 10 GbE, použijte dodaný adaptérY QSFP-SFP+. Další informace naleznete v části [Podporovaný hardware síťových rozhraní 10 GbE na zařízení StorSimple](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).
> 
> 

### <a name="serial-port-cabling"></a>Kabeláž sériového portu
Provedením následujících kroků pro kabel sériového portu.

#### <a name="to-cable-for-serial-connection"></a>Ke kabelu pro sériové připojení
1. Zařízení má na každém ovladači sériový port, který je označen ikonou klíče. V části [Síťová kabeláž](#network-cabling) vyhledejte sériové porty v propojovací rovině zařízení na obrázku.
2. Identifikujte aktivní ovladač v backplane zařízení. Blikající modrá LED dioda signalizuje, že ovladač je aktivní.
3. Použijte dodaný sériový kabel (v případě potřeby usb-sériový převodník pro váš notebook) a připojte konzolu nebo počítač (s emulací terminálu k zařízení) k sériovému portu aktivního ovladače.
4. Nainstalujte do počítače ovladače sériového USB (dodávané se zařízením).
5. Sériové připojení nastavte takto: 115 200 baudů, 8 datových bitů, 1 stop bit, žádná parita a řízení toku nastaveno na možnost Žádný.
6. Stisknutím klávesy Enter na konzole ověřte, zda připojení funguje. Měla by se zobrazit nabídka sériové konzoly.

> [!NOTE]
> **Správa zhasínání světel**: Pokud je zařízení nainstalováno ve vzdáleném datovém centru nebo v počítačové místnosti s omezeným přístupem, ujistěte se, že sériová připojení k oběma řadičům jsou vždy připojena k přepínači sériové konzole nebo podobnému zařízení. To umožňuje mimo pásmové operace vzdáleného řízení a podpory, pokud dojde k přerušení sítě nebo neočekávaným selháním.
> 
> 

Zařízení je nyní připojeno k síti pro napájení, přístup k síti a sériové připojení. Dalším krokem je konfigurace softwaru a nasazení zařízení.

## <a name="next-steps"></a>Další kroky
Přečtěte si, jak [nasadit a nakonfigurovat místní zařízení StorSimple](storsimple-8000-deployment-walkthrough-u2.md).

