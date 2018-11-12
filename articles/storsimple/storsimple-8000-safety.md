---
title: Bezpečný přístup z více zařízení StorSimple | Dokumentace Microsoftu
description: Popisuje konvence bezpečnost, pokyny a důležité informace a vysvětluje, jak bezpečně instalaci a provozování zařízení StorSimple.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/04/2017
ms.author: alkohli
ms.openlocfilehash: 428bc3504416f3a99518572f9f5bcb39423295b2
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51261578"
---
# <a name="safely-install-and-operate-your-storsimple-device"></a>Bezpečně instalaci a provozování zařízení StorSimple
![Ikona upozornění](./media/storsimple-safety/IC740879.png)
![čtení bezpečnosti Všimněte si, že ikona](./media/storsimple-safety/IC740885.png) **čtení bezpečnosti a informace o stavu**

Číst všechny bezpečnosti a stavu informace v tomto článku, který se vztahuje na zařízení Microsoft Azure StorSimple. Zachovejte vytištěné vodítka dodané se zařízením StorSimple pro budoucí použití. Nepodařilo se postupujte podle pokynů a správně nastavit, použití a péče pro tento produkt může zvýšit riziko vážným nebo smrti nebo poškození na zařízení. A [verzi ke stažení této příručky](https://www.microsoft.com/download/details.aspx?id=44233) je také k dispozici.

## <a name="safety-icon-conventions"></a>Bezpečný přístup z více ikonu konvence
Tady jsou ikony, které zjistíte, když zkontrolujete bezpečnostní opatření, která má být dodržen po nastavení a spuštění zařízení Microsoft Azure StorSimple.

| Ikona | Popis |
|:--- |:--- |
| ![Ikona nebezpečí](./media/storsimple-safety/IC740879.png) **nebezpečí!** |Označuje nebezpečné situace, která, pokud není vyhnout, bude výsledkem úmrtí nebo vážným. Toto slovo signálu je omezená na v nejextrémnějších případech. |
| ![Ikona upozornění](./media/storsimple-safety/IC740879.png) **upozornění!** |Označuje nebezpečné situace, která, pokud není vyhnout, může způsobit úmrtí nebo vážným. |
| ![Ikona upozornění](./media/storsimple-safety/IC740879.png) **OPATRNĚ!** |Označuje nebezpečné situace, která, pokud není vyhnout, by mohlo způsobit vedlejší nebo střední škody. |
| ![Všimněte si ikony](./media/storsimple-safety/IC740881.png) **oznámení:** |Označuje informace, které jsou považovány za důležité, ale ne související nebezpečí. |
| ![Ikona k elektrické Otřást](./media/storsimple-safety/IC740882.png) **nebezpečí k elektrické Otřást** |Snímač napětí vysoké |
| ![Tučná tloušťka ikonu](./media/storsimple-safety/IC740883.png) **Tučná tloušťka** | |
| ![Žádný uživatel možnost změny části ikonu](./media/storsimple-safety/IC740879.png) **žádné části možnost změny uživatele** |K přístup, pokud správně školení. |
| ![Přečtěte si všimněte si, že ikona bezpečný přístup z více](./media/storsimple-safety/IC740885.png)**nejprve přečíst všechny pokyny** | |
| ![Ikona nebezpečí tipu](./media/storsimple-safety/IC740886.png) **Tip ohrožení** | |

## <a name="handling-precautions"></a>Zpracování opatření
![Ikona upozornění](./media/storsimple-safety/IC740879.png) ![Tučná tloušťka ikonu](./media/storsimple-safety/IC740883.png) **upozornění!** 

Aby se snížilo riziko poškození:

* Plně nakonfigurovaného skříně můžete naváží až 32 kg (70 lbs); Nepokoušejte se přenést sami.
* Před přechodem skříň, vždy ujistěte se, že dva lidé jsou k dispozici pro zpracování váhu. Mějte na paměti, že udržet schopnost se zranit při pokusu o zrušení této váha osob.
* Není lift přílohu pomocí obslužné rutiny na napájení a chlazení moduly (PCMs) nachází na zadní jednotky. Ty nejsou navržené tak, aby váhu.

## <a name="connection-precautions"></a>Opatření připojení
![Ikona upozornění](./media/storsimple-safety/IC740879.png) ![ikonu k elektrické Otřást](./media/storsimple-safety/IC740882.png) **upozornění!**

Chcete-li snížit pravděpodobnost, že smrti, elektrické otřást nebo zranění:

* Při napájení z více zdrojů AC, odpojte všechny dodávek energie pro naprosté izolaci.
* Trvale Odpojte jednotku před přesunutím nebo pokud si myslíte, že je poškozená žádným způsobem.
* Poskytují bezpečné elektrické earth připojení k dodání napájecích. Ověřte, že seznámíte s obecnými přílohu splňuje požadavky státní a místní před použitím napájení.
* Ujistěte se, že je připojení power vždy odpojen před odebráním PCM od skříně.
* Vzhledem k tomu, že moduly na napájecí kabel napájení je hlavním odpojení zařízení, ujistěte se, že výstupy soketu se nacházejí v zařízení a jsou snadno dostupné.

![Ikona upozornění](./media/storsimple-safety/IC740879.png) ![ikonu k elektrické Otřást](./media/storsimple-safety/IC740882.png) **upozornění!**

Snížit pravděpodobnost, že přehřívání nebo vyvolat z elektrické připojení:

* Zdroj napájení vhodné poskytněte elektrické přetížení ochranu splňovat požadavky popsané v technické specifikace.
* Nepoužívejte napájecích kabelů bifurcated ("Y" zájemci).
* Pro dosažení souladu s příslušné bezpečnost, emisí a teplotní požadavky, by měly být odstraněny žádné pozadí a všechny pozice musí být naplněn moduly plug-in nebo jednotka prázdné hodnoty.
* Ujistěte se, že zařízení používá způsobem podle výrobce. Pokud se toto zařízení používá způsobem, který není zadán výrobcem, může dojít k přerušení protection poskytované službou zařízení.

![Všimněte si ikony](./media/storsimple-safety/IC740881.png) **oznámení:**

Pro správný provoz vašeho zařízení a zabránit poškození produktu:

* Porty RJ45 zádi zařízení jsou pro připojení sítě Ethernet. Tyto nesmí být připojené k síti telekomunikačními operátory.
* Nezapomeňte nainstalovat zařízení do racku, který zvládne chladicí návrhu zepředu dozadu.
* Všechny zásuvné moduly a prázdné talířů jsou součástí systému. Musí být odstraněny pouze při můžou nahradit aktuální soubor je možné okamžitě přidat. Systém nemůže spustit bez všechny moduly nebo prázdné hodnoty na místě.

## <a name="rack-system-precautions"></a>Opatření rack systému
Následující bezpečnostní požadavky je potřeba zvážit při připojení zařízení do racku CAB.

![Ikona upozornění](./media/storsimple-safety/IC740879.png) ![nebezpečí ikona tipu](./media/storsimple-safety/IC740886.png) **upozornění!**

Ke snížení pravděpodobnosti škod od tip přes:

* Návrh rack by měly podporovat Celková váha nainstalovaných skříně a by měla zahrnovat stabilizační funkce vhodné racku zabránit v ukládání nebo stisknuté přes během instalace nebo normální použití.
* Při načítání stojanu, vyplnit rack zdola nahoru a prázdný shora dolů.
* Postupně, aby se zabránilo ohrožení-of-rack toppling snímku není více než jednu přílohu z racku.

![Ikona upozornění](./media/storsimple-safety/IC740879.png) ![ikonu k elektrické Otřást](./media/storsimple-safety/IC740882.png) **upozornění!**

Chcete-li snížit pravděpodobnost, že smrti, elektrické otřást nebo zranění:

* Racku by měl mít systém bezpečné elektrické distribuce. Musíte zadat navýšení aktuální ochranu pro přílohu a nesmí být přetíženy podle celkový počet skříní nainstalované. Hodnocení spotřeby elektrické energie na jmenovka by měl.
* Systém elektrické distribuce musí poskytují spolehlivé základu pro každou skříň v racku.
* Návrh systému elektrické distribuce musí vzít v úvahu úniku celkový základu aktuální ze všech napájení všechny skříně. Všimněte si, že každý zdroj napájení v každém zařízení má aktuální úniku základu 1.0 mA maximální na 60 Hz 264 intenzita. Racku může vyžadovat značení s "vysoká ÚNIKU aktuální. Připojení základu (země) je nezbytné před připojením dodávky."
* Stojan, když je nakonfigurována s skříně, musí splňovat požadavky zabezpečení UL 60950-1 a IEC 60950-1/EN 60950-1.

![Všimněte si ikony](./media/storsimple-safety/IC740881.png) **oznámení:**

Pro správné chlazení systému rack:

* Ujistěte se, že návrh rack vezme v úvahu maximální skříň provozní okolní teploty 35 stupních Celsia (95 stupních Fahrenheita).
* Systém je provozována Nízkotlaké, zadní výfukového instalaci (protitlak vytvořené dveře do racku a překážky, která nepřekročí 5 Pascal [0,5 mm vody měřidla]).

## <a name="power-cooling-module-pcm-precautions"></a>Opatření Power chlazení modulu (PCM)
Zařízení je navržený pro použití dvou PCMs. Každý z PCMs má zdroj napájení a ventilátor duální osu. Během kritického stavu systému umožňuje selhání jednoho napájení přitom normálního provozu. Dvě PCMs (a tedy napájení) musí být vždy nainstalovaná. Jeden PCM neposkytuje redundantní napájení. Proto může způsobit výpadek nebo ke ztrátě selhání i jenom jednu PCM.

![Ikona upozornění](./media/storsimple-safety/IC740879.png) ![ikonu k elektrické Otřást](./media/storsimple-safety/IC740882.png) **upozornění!**

Chcete-li snížit pravděpodobnost, že smrti, elektrické otřást nebo zranění:

* Neodstraňujte z PCM na pozadí. Je-li riziko electric otřást uvnitř. Získat náhradu, a vrátí PCM [obraťte se na Microsoft Support](storsimple-contact-microsoft-support.md).

![Všimněte si ikony](./media/storsimple-safety/IC740881.png) **oznámení:**

Pro správný provoz vašeho zařízení a zabránit poškození produktu:

* Je třeba nahradit neúspěšné PCM během 24 hodin. Po odebrání PCM určena k nahrazení nahrazení musí dokončit během 10 minut po odebrání.
* Neodebírejte PCM, pokud můžou nahradit aktuální soubor lze nainstalovat hned. Přílohu nesmí být provozována bez všechny moduly v místě.

## <a name="electrostatic-discharge-esd-precautions"></a>Opatření elektrostatické výbojem
![Všimněte si ikony](./media/storsimple-safety/IC740881.png) **oznámení:**

Prohlédněte si následující ESD související upozornění.

* Ujistěte se, že máte nainstalované a zaškrtnutí vhodný antistatická zápěstí nebo kotníkových popruh.
* Sledujte všechny konvenční ESD opatření při zpracování moduly a komponenty.
* Vyhněte se v kontaktu s propojovací rozhraní komponenty a konektory modulu.
* Poškození ESD není předmětem záruky.

## <a name="battery-disposal-precautions"></a>Vyřazení opatření baterie
Napájení používá k ochraně obsahu paměti během výpadků napájení dočasné, krátkodobé speciální baterie. Baterie sedí v PCM. Mějte tyto informace o baterie.

![Ikona upozornění](./media/storsimple-safety/IC740879.png) **upozornění!**

Aby se snížilo riziko šortky, fire, rozbalení, škod nebo smrti:

* Odstranění použitých baterie v souladu s předpisy státní nebo místní.
* Převod ze strojového kódu, překonání, nebo zahřívá vyšší než 60 stupňů Celsia (140 stupních Fahrenheita) ani spalování. Baterie PCM nahraďte pouze zadaný baterie. Použití jiného baterie může představovat riziko fire nebo výbuchu.
* Použijte ochranné zakončení na baterie, pokud tyto jsou odebrány z napájení.

![Všimněte si ikony](./media/storsimple-safety/IC740881.png) **oznámení:**

Při přesouvání nebo jinak přenos baterie ve vzduchu, postupovat podle pokynů k dispozici na IATA Lithium baterie pokyny dokumentu [http://www.iata.org/whatwedo/cargo/dgr/Pages/lithium-batteries.aspx](http://www.iata.org/whatwedo/cargo/dgr/Pages/lithium-batteries.aspx)

Po zkontrolování těchto oznámení o bezpečnost, jsou tyto další kroky k jeho usazení do racku a zapojení kabeláže zařízení.

## <a name="next-steps"></a>Další postup
* Zařízení 8100, přejděte na [instalaci zařízení StorSimple 8100](storsimple-8100-hardware-installation.md).
* Zařízení 8600, přejděte na [instalaci zařízení StorSimple 8600](storsimple-8600-hardware-installation.md).

