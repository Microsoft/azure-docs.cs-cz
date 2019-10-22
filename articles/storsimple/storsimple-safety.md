---
title: Zabezpečení pro zařízení StorSimple | Microsoft Docs
description: Popisuje bezpečnostní konvence, pokyny a požadavky a vysvětluje, jak bezpečně nainstalovat a provozovat zařízení StorSimple.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''
ms.assetid: dae6d535-1ca2-4d2b-b221-6819043aa068
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2016
ms.author: alkohli
ms.openlocfilehash: 4622a8575d7b6a38226ee3a980c05f143c128356
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2019
ms.locfileid: "68963537"
---
# <a name="safely-install-and-operate-your-storsimple-device"></a>Bezpečné instalace a obsluha zařízení StorSimple

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

ikona ![Warning ](./media/storsimple-safety/IC740879.png)
 ![Read ikona upozornění na bezpečnost ](./media/storsimple-safety/IC740885.png) **informace o bezpečnosti čtení a stavu**

Přečtěte si veškeré informace o bezpečnosti a stavu v tomto článku, které se vztahují na vaše zařízení Microsoft Azure StorSimple. Ponechte všechna tištěná vodítka dodávaná se zařízením StorSimple, abyste mohli budoucí reference. Při nedodržení pokynů a správném nastavení, použití a péče o tento produkt se může zvýšit riziko závažné újmy nebo smrti nebo poškození zařízení nebo zařízení. K dispozici je také [verze této příručky ke stažení](https://www.microsoft.com/download/details.aspx?id=44233) .

## <a name="safety-icon-conventions"></a>Bezpečnostní konvence ikon
Tady jsou ikony, které najdete, když provedete kontrolu bezpečnostních opatření, která se mají pozorovat při nastavování a spouštění zařízení Microsoft Azure StorSimple.

| Ikona | Popis |
|:--- |:--- |
| Ikona ![Danger ](./media/storsimple-safety/IC740879.png) **nebezpečnosti!** |Označuje nebezpečnou situaci, která, pokud se nevyhne, má za následek smrt nebo vážnou škodu. Toto slovo signálu se bude omezovat na nejvíce extrémních situací. |
| Ikona ![Warning ](./media/storsimple-safety/IC740879.png) **Upozornění!** |Označuje nebezpečnou situaci, že pokud se nevyhnete, může dojít k úmrtí nebo závažné újmě. |
| Ikona ![Warning ](./media/storsimple-safety/IC740879.png) **opatrní!** |Označuje nebezpečnou situaci, že pokud se nevyhnete, může dojít k menší nebo střední újmě. |
| ![Notice ikona ](./media/storsimple-safety/IC740881.png) **Upozornění:** |Indikuje informace považované za důležité, ale nesouvisí s rizikem. |
| Ikona úrazu ![Electrical ](./media/storsimple-safety/IC740882.png) **nebezpečí elektrického nárazu** |Vysoké napětí |
| Ikona váhy ![Heavy ](./media/storsimple-safety/IC740883.png) **velkou váhou** | |
| Ikona ![Noch uživatelských služeb ](./media/storsimple-safety/IC740879.png) **žádné části s uživatelskými službami** |Nepoužívejte přístup, pokud není řádně vyškolená. |
| Ikona upozornění na bezpečnost ![Read –](./media/storsimple-safety/IC740885.png)**nejprve přečíst všechny pokyny** | |
| Ikona nebezpečí ![Tip ](./media/storsimple-safety/IC740886.png) **nebezpečí špičky** | |

## <a name="handling-precautions"></a>Zpracování preventivních opatření
ikona ![Warning ](./media/storsimple-safety/IC740879.png) ![Heavy ikona váhy ](./media/storsimple-safety/IC740883.png) **Upozornění** 

Chcete-li snížit riziko újmy:

* Plně nakonfigurovaná skříň může vážit až 32 kg (70 kg); Nepokoušejte se sami si ho vyzvednutím sami.
* Před přesunem skříně vždy zajistěte, aby byly k dispozici dvě osoby pro zpracování váhy. Uvědomte si, že jedna osoba, která se pokusila o zvedání této váhy, může tolerovat zranění.
* Nezvedněte skříň pomocí obslužných rutin v modulech napájení a chlazení (PCMs) umístěných na zadní části jednotky. Nejsou navržené tak, aby vybraly váhu.

## <a name="connection-precautions"></a>Upozornění na připojení
ikona ![Warning ](./media/storsimple-safety/IC740879.png) ![Electrical ikona úrazu ](./media/storsimple-safety/IC740882.png) **Upozornění**

Pro snížení pravděpodobnosti zranění, elektrického úrazu nebo smrti:

* Pokud využíváte více zdrojů střídavých proudů, odpojte všechny napájecí síly pro kompletní izolaci.
* Před přesunutím jednotky trvale odpojte, nebo pokud se domníváte, že dojde k poškození jakýmkoli způsobem.
* Zajištění bezpečného připojení elektrického uzemnění kabelům napájení. Než začnete používat napájení, ověřte, že základní plášť splňuje vnitrostátní a místní požadavky.
* Zajistěte, aby bylo připojení napájení vždy odpojeno před odebráním PCM z skříně.
* Vzhledem k tomu, že modul plug-in napájení je hlavním zařízením pro odpojení, je nutné zajistit, aby se sokety dostaly poblíž zařízení a byly snadno dostupné.

ikona ![Warning ](./media/storsimple-safety/IC740879.png) ![Electrical ikona úrazu ](./media/storsimple-safety/IC740882.png) **Upozornění**

Chcete-li snížit pravděpodobnost přehřívání nebo požáru z elektrického připojení:

* Poskytněte vhodný zdroj napájení s ochranou přetížení elektrického zabezpečení, aby splňoval požadavky popsané v technické specifikaci.
* Nepoužívejte bifurcated napájecí šňůry ("Y").
* Aby bylo možné dodržovat příslušné požadavky na bezpečnost, emise a tepelné dodržování předpisů, je nutné odebrat žádné vazby a všechny pozice musí být naplněny moduly plug-in nebo prázdnými jednotkami.
* Ujistěte se, že se zařízení používá způsobem určeným výrobcem. Pokud se toto zařízení používá způsobem nespecifikovaným výrobcem, může být ochrana poskytovaná tímto zařízením poškozená.

![Notice ikona ](./media/storsimple-safety/IC740881.png) **Upozornění:**

Správné fungování vašeho zařízení a prevence škod v produktu:

* Porty RJ45 na zadní straně zařízení jsou jenom pro připojení k síti Ethernet. Tyto aplikace nesmí být připojeny k telekomunikační síti.
* Nezapomeňte zařízení nainstalovat do racku, který může pojmout návrh zepředu na pozadí.
* Všechny moduly modulů plug-in a prázdné desky jsou součástí skříně systému. Tato možnost musí být odebrána, pouze pokud je možné okamžitě přidat náhradní náhradu. Systém nesmí být spuštěný bez všech modulů nebo prázdných umístění.

## <a name="rack-system-precautions"></a>Systémová opatření pro rack
Pokud připojíte zařízení v skříňovém souboru CAB, je potřeba vzít v úvahu následující bezpečnostní požadavky.

ikona ![Warning ](./media/storsimple-safety/IC740879.png) ![Tip ikona nebezpečí ](./media/storsimple-safety/IC740886.png) **Upozornění**

Chcete-li snížit pravděpodobnost poranění z špičky, postupujte takto:

* Návrh stojanu by měl podporovat celkovou váhu nainstalovaných skříní a měl by zahrnovat stabilizační funkce vhodné k tomu, aby se zabránilo zakrytí nebo posunutí stojanu během instalace nebo normálního použití.
* Při načítání stojanu se stojan vyplní zdola nahoru a vyplní se shora dolů.
* Nevytvářejte více než jedno místo v racku současně, abyste se vyhnuli nebezpečí topplingí racku.

ikona ![Warning ](./media/storsimple-safety/IC740879.png) ![Electrical ikona úrazu ](./media/storsimple-safety/IC740882.png) **Upozornění**

Pro snížení pravděpodobnosti zranění, elektrického úrazu nebo smrti:

* Stojan by měl mít bezpečný systém distribuce elektrické distribuce. Je nutné, aby pro skříň poskytovala aktuální ochranu a aby ji nebylo možné přenášet celkovým počtem nainstalovaných skříní. Mělo by se pozorovat hodnocení spotřeby elektrické energie uvedené na nameplate.
* Systém elektrické distribuce musí poskytovat spolehlivý základ pro každou skříňku v racku.
* Návrh systému elektrické distribuce musí brát v úvahu celkové úniky podzemních zásob ze všech skříní ve všech skříních. Všimněte si, že každý zdroj napájení v každé skříni má za následek únik z provozu z 1,0 mA maxima v 60 Hz, 264 v voltech. Stojan může vyžadovat označení s vysokou netěsnou aktuální. Uzemněné připojení je před připojením k základní. "
* Stojan, který je nakonfigurovaný s skříněmi, musí splňovat požadavky na bezpečnost pro: UL 60950-1 a IEC 60950-1/EN 60950-1.

![Notice ikona ](./media/storsimple-safety/IC740881.png) **Upozornění:**

Pro správné chlazení systému racku:

* Zajistěte, aby se návrh stojanu zohlednil v maximálním počtu provozních okolních teplot 35 stupních Celsia (95 stupňů Fahrenheita).
* Systém je provozován s nízkým tlakem, back-výfukovou instalací (zpětný tlak vytvořený dveřmi dveří a překážkami, které nepřekračují 5 Pascal [0,5 mm vodou]).

## <a name="power-cooling-module-pcm-precautions"></a>Bezpečnostní opatření pro modul Power chlazení (PCM)
Zařízení je navržené tak, aby fungovalo se dvěma PCMs. Každý z PCMs má zdroj napájení a ventilátor se dvěma osami. Během kritické podmínky systém umožňuje selhání jednoho zdroje napájení a přitom pokračuje v normálním provozu. Dvě PCMs (a tudíž napájení) se musí vždycky nainstalovat. Jeden modul PCM neposkytuje redundantní napájení. Proto selhání i jednoho PCM může způsobit výpadky nebo možnou ztrátu dat.

ikona ![Warning ](./media/storsimple-safety/IC740879.png) ![Electrical ikona úrazu ](./media/storsimple-safety/IC740882.png) **Upozornění**

Pro snížení pravděpodobnosti zranění, elektrického úrazu nebo smrti:

* Neodstraňujte vazby ze služby PCM. Je nebezpečí elektrického úrazu uvnitř. Pokud chcete vrátit PCM a získat náhradní, [kontaktní podpora Microsoftu](storsimple-contact-microsoft-support.md).

![Notice ikona ](./media/storsimple-safety/IC740881.png) **Upozornění:**

Správné fungování vašeho zařízení a prevence škod v produktu:

* Neúspěšný PCM je potřeba nahradit během 24 hodin. Po odebrání PCM z důvodu nahrazení musí být náhrada dokončena do 10 minut po odebrání.
* Neodstraňujte PCM, pokud nemůžete nainstalovat náhradu hned. Skříň nesmí být provozována bez použití všech modulů.

## <a name="electrostatic-discharge-esd-precautions"></a>Bezpečnostní opatření elektrostatického uvolnění (ESD)
![Notice ikona ](./media/storsimple-safety/IC740881.png) **Upozornění:**

Podívejte se na následující bezpečnostní opatření týkající se ESD.

* Ujistěte se, že jste nainstalovali a zkontrolovali vhodný nebo nestatický popruh na zápěstí nebo umyvadlo.
* Při zpracování modulů a komponent Sledujte všechna konvenční ESDá opatření.
* Vyhněte se kontaktu s komponentami pro neplánování a konektory modulů.
* ESD škodu nepokrývá záruka.

## <a name="battery-disposal-precautions"></a>Preventivní opatření při odstraňování baterie
Zdroj napájení používá speciální baterii k ochraně obsahu paměti během dočasných, krátkodobých výpadků. Baterie se sedí v PCM. Mějte na paměti následující informace o baterii.

Ikona ![Warning ](./media/storsimple-safety/IC740879.png) **Upozornění!**

Pro snížení rizika krátkodobého, požáru, výbuchu, zranění nebo smrti:

* Likvidaci využitých baterií v souladu s vnitrostátními/regionálními předpisy.
* Neprovádějte zpětný překlad, rozdrcení ani tepelné navýšení nad 60 stupňů Celsia (140 stupňů Fahrenheita) nebo spalování. Vyměňte baterii PCM jenom dodanou baterií. Používání jiné baterie může představovat riziko požáru nebo výbuchu.
* Používejte ochranné zakončení pro baterie, pokud jsou odebrána ze zdroje napájení.

![Notice ikona ](./media/storsimple-safety/IC740881.png) **Upozornění:**

Při přepravování nebo jinak přepravování baterií pomocí Air postupujte podle pokynů v dokumentu s pokyny pro baterii IATA pro baterii, který je dostupný na [https://www.iata.org/whatwedo/cargo/dgr/Pages/lithium-batteries.aspx](https://www.iata.org/whatwedo/cargo/dgr/Pages/lithium-batteries.aspx)

Po kontrole těchto bezpečnostních oznámení se v dalších krocích rozbalí zařízení do racku a zapojte do jeho kabelu.

## <a name="next-steps"></a>Další kroky
* V případě zařízení 8100 navštivte [instalaci zařízení StorSimple 8100](storsimple-8100-hardware-installation.md).
* V případě zařízení 8600 navštivte [instalaci zařízení StorSimple 8600](storsimple-8600-hardware-installation.md).

