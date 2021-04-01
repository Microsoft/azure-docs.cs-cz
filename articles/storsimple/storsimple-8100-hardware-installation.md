---
title: Nainstalovat zařízení Microsoft Azure StorSimple 8100
description: Popisuje, jak před nasazením a konfigurací softwaru rozbalit, montovat do racku a zapojte zařízení s StorSimple 8100.
author: alkohli
ms.assetid: 6098a01e-c031-488a-a8d7-0b607ce665e1
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 113b72ddf7e5d508c8a0b577d4004d4fbd83e8e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "84699103"
---
# <a name="unpack-rack-mount-and-cable-your-storsimple-8100-device"></a>Rozbalení, připojení k racku a zapojení zařízení StorSimple 8100

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Přehled
Microsoft Azure StorSimple 8100 je jediná skříň zařízení připojená k racku. V tomto kurzu se dozvíte, jak před konfigurací a nasazením zařízení StorSimple nabalit, montovat a kabelovat hardware zařízení StorSimple 8100.

## <a name="unpack-your-storsimple-8100-device"></a>Rozbalení zařízení StorSimple 8100
Následující kroky poskytují jasné a podrobné pokyny k rozbalení úložného zařízení StorSimple 8100. Toto zařízení se dodává v jedné krabici.

### <a name="prepare-to-unpack-your-device"></a>Příprava na rozbalení zařízení
Před rozbalením zařízení si přečtěte následující informace.

![Ikona upozornění ](./media/storsimple-safety/IC740879.png)![ velké váhy ikona ](./media/storsimple-8100-hardware-installation/HCS_HeavyWeight_Icon.png) **Upozornění!**

1. Ujistěte se, že máte k dispozici dvě lidi, abyste mohli spravovat váhu skříně, pokud ji zpracováváte ručně. Plně nakonfigurovaná skříň může vážit až 32 kg (70 kg).
2. Umístěte krabici na rovný povrch.

Dále proveďte následující kroky a rozbalte své zařízení.

#### <a name="to-unpack-your-device"></a>Postup rozbalení zařízení
1. Zkontrolujte krabici a výplňový materiál, jestli nejsou pomačkané, pořezané nebo poškozené vodou nebo jestli nenesou stopy jiného zjevného poškození. Pokud jsou krabice nebo obal vážně poškozené, krabici neotevírejte. Obraťte se prosím na [Podpora Microsoftu](storsimple-8000-contact-microsoft-support.md) , které vám pomůžou posoudit, jestli je zařízení v dobrém provozním pořadí.
2. Rozbalte krabici. Na následujícím obrázku vidíte nebalené zobrazení zařízení StorSimple.
   
     ![Rozbalení úložného zařízení](./media/storsimple-8100-hardware-installation/HCSUnpackyour2Udevice.png)
   
    **Nebalené zobrazení úložného zařízení**
   
   | Popisek | Description |
   | --- | --- |
   |   1 |Expediční box |
   |   2 |Spodní pěna |
   |   3 |Zařízení |
   |   4 |Hlavní pěna |
   |   5 |Pole pro příslušenství |
3. Po rozbalení krabice se ujistěte, že obsahuje následující:
   
   * 1 zařízení s jednou skříňkou
   * 2 napájecí šňůra
   * 1 křížený kabel Ethernet
   * 2 kabely sériové konzoly
   * 1 sériový převaděč USB pro sériový přístup
   * 1 T10 Screwdriver pro kontrolu proti falšování
   * 4 QSFP-to-SFP + adaptéry pro použití se síťovými rozhraními 10 GbE
   * 1 skříň – přípojný Kit (2 Side kolejnice s připojením hardwaru)
   * Dokumentace k Začínáme
     
     Pokud jste neobdrželi žádnou z výše uvedených položek, [kontaktujte podpora Microsoftu](storsimple-8000-contact-microsoft-support.md).

Dalším krokem je usazení zařízení do racku.

## <a name="rack-mount-your-storsimple-8100-device"></a>Stojan – připojení zařízení StorSimple 8100
Postupujte podle dalších kroků a nainstalujte zařízení úložiště StorSimple 8100 ve standardní racku na 19 – 15palcový s předními a zadními příspěvky. Zařízení StorSimple 8100 má jednu primární skříň.

Instalace se skládá z několika kroků, z nichž každá je popsána v následujících postupech.

> [!IMPORTANT]
> Zařízení StorSimple musí být připojená do racku pro správnou operaci.
> 
> 

### <a name="prepare-the-site"></a>Příprava webu
Zařízení musí být nainstalované na standardu 19 palců, který obsahuje obě přední i zadní stanoviště. K přípravě instalace racku použijte následující postup.

#### <a name="to-prepare-the-site-for-rack-installation"></a>Příprava lokality na instalaci racku
1. Ujistěte se, že je zařízení bezpečně umístěné na rovném a stabilním pracovním povrchu (nebo něčem podobném).
2. Ověřte, že lokalita, kde máte v úmyslu nastavit, má standardní sílu napájení z nezávislého zdroje nebo jednotky PDU napájení (AC) s nepřerušitelným zdrojem napájení (UPS).
3. Ujistěte se, že je na stojanu, ve kterém chcete zařízení připojit, jeden slot 2U.

![Ikona upozornění ](./media/storsimple-safety/IC740879.png)![ velké váhy ikona ](./media/storsimple-8100-hardware-installation/HCS_HeavyWeight_Icon.png) **Upozornění!**

Ujistěte se, že máte ke správě váhy k dispozici dvě osoby, pokud spouštíte ruční nastavení zařízení. Plně nakonfigurovaná skříň může vážit až 32 kg (70 kg).

### <a name="rack-prerequisites"></a>Požadavky na stojan
Skříň 8100 je navržená pro instalaci ve standardní skříňové skříni z 19 mm s:

* Minimální hloubka 27,84 palců z rackového příspěvku k příspěvku
* Maximální váha 32 kg pro zařízení
* Maximální tlak v 5 Pascal (0,5 mm měřiče vody).

### <a name="rack-mounting-rail-kit"></a>Montážní skříň – montáž železniční Kit
K dispozici je sada montážní kolejnice, která se používá pro skříň na racku 19 – 15palcový. Kolejnice byly testovány tak, aby zpracovávala maximální váhu skříně. Tyto kolejnice budou také umožňovat instalaci více skříní bez ztráty místa v rámci stojanu.

#### <a name="to-install-the-device-on-the-rails"></a>Instalace zařízení do kolejnic
1. Tento krok proveďte jenom v případě, že na zařízení nejsou nainstalované vnitřní kolejnice. Vnitřní kolejnice jsou obvykle nainstalovány v továrně. Pokud nejsou kolejnice nainstalované, nainstalujte snímky z levé a pravé kolejnice na strany skříně skříně. Připojovat se šesti metrickými šrouby na každé straně. Pro usnadnění orientace jsou železniční snímky označeny jako **LH – přední** a **RH – dopředu** a konec, který je připevněn ke zadní části skříně, má zúžený konec.<br/>
   
    ![Připojení železničních snímků k skříni skříně](./media/storsimple-8100-hardware-installation/HCSAttachingRailSlidestoEnclosureChassis.png)

    **Připojení vnitřních vlakových snímků ke stěnám skříně**
   
    Popisek | Description
    ----- | -----------
    1     | M 3x4 tlačítka – šrouby hlav
    2     | Snímky skříní

2. Připojte vnějšího a vnějšího železničního sestavení se svislými členy racku Cabinet. Hranaté závorky jsou označeny jako **LH**, **RH** a **Tato strana až** po správné orientaci.
3. Vyhledejte čepy v přední a zadní části montážní lišty. Rozšíříte kolejnici tak, aby vyhovovala mezi příspěvky v racku, a vkládat PIN kódy do čelního a zadního skříňku pro vertikální členské otvory. Ujistěte se, že je na úrovni železničního sestavení úroveň.
4. Pomocí dvou z poskytnutých šrouby metriky Zabezpečte železniční sestavení se svislými členy stojanu. Použijte jeden šroub na přední stranu a druhý na zadní straně.
5. Opakujte tyto kroky pro druhé železniční sestavení.<br/>
   
     ![Připojení snímků po železnici k racku CAB](./media/storsimple-8100-hardware-installation/HCSAttachingRailSlidestoRackCabinet.png)
   
    **Připojení vnějších železničních sestavení k stojanu**
   
   | Popisek | Description |
   | --- | --- |
   |   1 |Montážní šroub |
   |   2 |Šroubový prstenec na druhou stranu |
   |   3 |PIN kódy na levé straně kolejnice |
   |   4 |Montážní šroub |
   |   5 |PIN kódy zadních míst v železnici |

### <a name="mounting-the-device-in-the-rack"></a>Zařízení se připojuje do stojanu.
Pomocí následujících kroků připojte zařízení v racku a použijte přitom rackové kolejnice, které jste právě nainstalovali, a proveďte následující kroky.

#### <a name="to-mount-the-device"></a>Připojení zařízení
1. S pomocníkem, zvedněte skříň a zarovnejte ji se rozvaděčovou kolejnicí.
2. Zařízení pečlivě vložte do kolejnic a pak zařízení nahrajte do racku CAB.<br/>
   
    ![Vkládání zařízení do stojanu](./media/storsimple-8100-hardware-installation/HCSInsertingDeviceintheRack.png)
   
    **Zařízení se připojuje do stojanu.**
3. Odstraňte levou a pravou přední stranu přírubu tím, že uvolníte bezplatnou volnost. Zakončení přírub stačí Přitahovat na příruby.
4. Zabezpečíte skříň v racku tak, že jednou přírubou přivedete jednu ze Phillips šroubů, doleva a doprava.
5. Nastavte zakončení přírub tak, že je stisknete na místo a přichycením na místě.<br/>
   
     ![Instalace Cap k přírubám](./media/storsimple-8100-hardware-installation/HCSInstallingFlangeCaps.png)
   
    **Instalace Cap k přírubám**
   
   | Popisek | Description |
   | --- | --- |
   |   1 |Šroub uzávěrky skříně |

Dalším krokem je připojení zařízení k napájení, síti a sériovému přístupu.

## <a name="cable-your-storsimple-8100-device"></a>Zapojení kabeláže zařízení StorSimple 8100
Následující postupy vysvětlují, jak můžete zařízení StorSimple 8100 připojit k napájení, síťovému a sériovému připojení.

### <a name="prerequisites"></a>Požadavky
Než začnete zapojovat kabeláž zařízení, budete potřebovat následující:

* Zařízení úložiště, kompletně nezabalená a připojená do racku.
* 2 napájecí kabely dodávané s vaším zařízením
* Přístup k 2 jednotkám distribuce napájení (doporučeno).
* Síťové kabely
* Zadané sériové kabely
* Převaděč sériového USB s příslušným ovladačem nainstalovaným na vašem počítači (v případě potřeby)
* Poskytnuté 4 adaptéry QSFP-to-SFP + pro použití se síťovými rozhraními 10 GbE
* [Podporovaný hardware pro síťová rozhraní 10 GbE na zařízení StorSimple](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)

### <a name="power-cabling"></a>Kabely napájení
Vaše zařízení obsahuje redundantní moduly napájení a chlazení (PCMs). Aby se zajistila vysoká dostupnost, musí být nainstalovaná a připojená PCMs k různým zdrojům napájení.

Pomocí následujících kroků Zapojte zařízení do elektrického napájení.

[!INCLUDE [storsimple-cable-8100-for-power](../../includes/storsimple-cable-8100-for-power.md)]

### <a name="network-cabling"></a>Síťové kabely
Vaše zařízení je konfigurací v aktivním pohotovostním režimu: v jednom okamžiku je aktivní jeden modul kontroleru a zpracovává všechny operace disku a sítě, zatímco je druhý modul kontroleru v pohotovostním režimu. Pokud se kontroler nepovede, kontroler pohotovostní se aktivuje okamžitě a pokračuje všemi operacemi na disku a v síti.

Pro podporu tohoto redundantního převzetí služeb při selhání je potřeba kabelovat síť zařízení, jak je popsáno v následujícím postupu.

#### <a name="to-cable-for-network-connection"></a>Pro připojení k síti
1. Vaše zařízení má na každém řadiči šest síťových rozhraní: 4 1 GB/s a porty sítě Ethernet 2 10 GB/s. Identifikujte různé datové porty v rámci plánu zařízení.
   
    ![Backplane pro zařízení 8100](./media/storsimple-8100-hardware-installation/HCSBackplaneof2UDevicewithPortsLabeled.jpg)
   
    **Zpět zařízení, které zobrazuje datové porty**
   
   | Popisek | Description |
   | --- | --- |
   |   0, 1, 4, 5 |Síťová rozhraní 1 GbE |
   |   2, 3 |Síťová rozhraní 10 GbE |
   |   6 |Sériové porty |
2. Podívejte se na následující diagram pro síťové kabely. (Minimální konfigurace sítě se zobrazuje na plných modrých řádcích. Další konfigurace požadovaná pro vysokou dostupnost a výkon se zobrazuje pomocí teček.)

    ![Zapojení zařízení 2U do sítě](./media/storsimple-8100-hardware-installation/HCSCableYour2UDeviceforNetwork.png)

    **Síťové kabely pro vaše zařízení**

   |Popisek | Description |
   |----- | ----------- |
   | A    | SÍŤ LAN s přístupem k Internetu |
   | B    | Kontroler 0 |
   | C    | PCM 0 |
   | D    | Kontroler 1 |
   | E    | PCM 1 |
   | F, G | Hostitelé |
   | 0-5  | Síťová rozhraní |



Minimální konfigurace při kabeláži zařízení vyžaduje:

* Aspoň dvě síťová rozhraní připojená na každém řadiči s jedním pro cloudový přístup a jednu pro iSCSI. Port DATA 0 je automaticky povolen a nakonfigurován prostřednictvím sériové konzoly zařízení. Kromě DATA 0 je také potřeba nakonfigurovat další datový port na portálu Azure Classic. V takovém případě připojte k primární síti LAN port DATA 0 (síť s přístupem k Internetu). Ostatní datové porty lze připojit k segmentu sítě SAN nebo iSCSI LAN (VLAN) v závislosti na zamýšlené roli.
* Stejné rozhraní na každém řadiči připojeném ke stejné síti, aby se zajistila dostupnost, pokud dojde k převzetí služeb při selhání řadiče. Pokud se například rozhodnete připojit k jednomu z řadičů DATA 0 a DATA 3, musíte na druhém řadiči připojit odpovídající DATA 0 a DATA 3.

Pamatujte na vysokou dostupnost a výkon:

* Pokud je to možné, nakonfigurujte dvojici síťového rozhraní pro cloudový přístup (1 GbE) a další pár pro iSCSI (doporučeno 10 MB) na každém řadiči.
* Pokud je to možné, připojte síťová rozhraní z každého kontroleru k dvěma různým přepínačům, abyste zajistili dostupnost v případě výpadku přepínače. Obrázek znázorňuje síťová rozhraní 2 10 GbE, DATA 2 a DATA 3 z každého kontroleru připojeného ke dvěma různým přepínačům.

Další informace najdete v tématu **Síťová rozhraní** podle [požadavků vysoké dostupnosti pro vaše zařízení StorSimple](storsimple-8000-system-requirements.md#high-availability-requirements-for-storsimple).

> [!NOTE]
> Pokud používáte přijímače SFP a přijímače se síťovými rozhraními 10 GbE, použijte dodávané adaptéry QSFP-SFP +. Další informace najdete na [podporovaném hardwaru pro síťová rozhraní 10 GbE na zařízení StorSimple](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).
> 
> 

### <a name="serial-port-cabling"></a>Kabely sériového portu
K zapojení sériového portu proveďte následující kroky.

#### <a name="to-cable-for-serial-connection"></a>Kabel pro sériové připojení
1. Vaše zařízení má sériový port na každém kontroleru, který je identifikovaný ikonou klíče. Pokud chcete najít sériové porty pro reportování zařízení, přečtěte si část ilustrace [síťového kabeláže](#network-cabling) .
2. Identifikujte aktivní kontroler v rámci plánu vašeho zařízení. Blikání INDIKÁTORu Blue indikuje, že je kontroler aktivní.
3. Použijte zadané sériové kabely (v případě potřeby i převodník sériového čísla USB pro přenosný počítač) a připojte svoji konzolu nebo počítač (s emulací terminálu k zařízení) k sériovému portu aktivního řadiče.
4. Nainstalujte na svém počítači ovladače sériového rozhraní USB (dodávané se zařízením).
5. Nastavte sériové připojení následujícím způsobem: 115 200 baud, 8 datových bitů, 1 stop bitů, žádná parita a řízení toku nastaveno na hodnotu žádné.
6. Ověřte, zda připojení funguje, stisknutím klávesy ENTER v konzole nástroje. Měla by se zobrazit nabídka konzoly sériového portu.

> [!NOTE]
> **Správa světla**: Pokud je zařízení nainstalované ve vzdáleném datovém centru nebo v počítači s omezeným přístupem, ujistěte se, že sériová připojení k oběma řadičům jsou vždycky připojená k přepínači sériové konzoly nebo podobnému vybavení. To umožňuje vzdálené řízení a operace podpory mimo pásmo, pokud dojde k výpadkům sítě nebo neočekávaným selháním.
> 
> 

Vaše zařízení je teď kabelové pro napájení, přístup k síti a sériové připojení. Dalším krokem je konfigurace softwaru a nasazení zařízení.

## <a name="next-steps"></a>Další kroky
Přečtěte si, jak [nasadit a nakonfigurovat vaše místní zařízení StorSimple](storsimple-8000-deployment-walkthrough-u2.md).

