---
title: Přibližná relokalizace
description: Zjistěte, jak a kdy použít hrubou reprostředí. Hrubá reprostředí vám pomůže najít kotvy, které jsou blízko vaší blízkosti.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 01/28/2021
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.custom: devx-track-csharp
ms.openlocfilehash: 3f0b04183c4df469d4f723486103790c4f97671b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104656171"
---
# <a name="coarse-relocalization"></a>Přibližná relokalizace

Hrubá Redeklarace je funkce, která umožňuje rozsáhlou lokalizaci tím, že poskytuje přibližnou, ale rychlou odpověď na tyto otázky: 
- *Kde je teď moje zařízení?* 
- *Jaký obsah mám pozorovat?* 
 
Odpověď není přesná. Je v tomto formátu: *blíží se k těmto kotvám. Zkuste jednu z nich najít*.

Hrubá revolba funguje označením kotev s různými čteními senzorů v zařízení, které se později používají pro rychlé dotazování. V případě scénářů v rámci venkovních scénářů jsou data snímače typicky umístěním GPS (Global Positioning System) zařízení. Když GPS není k dispozici nebo je nespolehlivější, například když máte nějaké dveře, data snímače se skládají z přístupových bodů Wi-Fi a signálů Bluetooth v dosahu. Shromážděná data senzorů přispívají k údržbě prostorového indexu, který používají prostorové kotvy Azure k rychlému určení, které kotvy se blíží vašemu zařízení.

## <a name="when-to-use-coarse-relocalization"></a>Kdy použít hrubou reprostředí

Pokud plánujete zvládnout více než 35 prostorových ukotvení v prostoru, který je větší než tenisový soud, pravděpodobně budete mít k výhodu hrubého územního indexování.

Rychlé vyhledávání kotev povolených hrubou redistribuci je navrženo tak, aby zjednodušilo vývoj aplikací s využitím celosvětově škálovatelných kolekcí, neboli milionů geograficky distribuovaných kotev. Složitost prostorového indexování je skrytá, takže se můžete soustředit na logiku aplikace. Všechna obtížná práce se provádí na pozadí prostorových kotev Azure.

## <a name="using-coarse-relocalization"></a>Použití hrubého prostředí

Tady je typický pracovní postup pro vytváření a dotazování prostorových kotev Azure s hrubým přemístění:
1.  Vytvořte a nakonfigurujte poskytovatele otisku prstu snímače, abyste mohli shromažďovat data ze senzorů, která chcete.
2.  Spusťte relaci prostorových kotev Azure a vytvořte kotvy. Vzhledem k tomu, že otisk senzoru je povolený, kotvy jsou v prostoru indexovány hrubou relokální.
3.  Vyhledávejte kotvy pomocí vyhrazených vyhledávacích kritérií v relaci prostorových ukotvení a dotazování okolních kotev.

V jednom z těchto kurzů si můžete nastavit hrubou reorganizaci v aplikaci:
* [Hrubá lokální v Unity](../how-tos/set-up-coarse-reloc-unity.md)
* [Hrubá reprostředí v cíli – C](../how-tos/set-up-coarse-reloc-objc.md)
* [Hrubá reprostředí v SWIFT](../how-tos/set-up-coarse-reloc-swift.md)
* [Hrubý lokální roznárodní prostředí v jazyce Java](../how-tos/set-up-coarse-reloc-java.md)
* [Hrubá reprostředí v C++/NDK](../how-tos/set-up-coarse-reloc-cpp-ndk.md)
* [Hrubá reprostředí v C++/WinRT](../how-tos/set-up-coarse-reloc-cpp-winrt.md)

## <a name="sensors-and-platforms"></a>Senzory a platformy

### <a name="platform-availability"></a>Dostupnost platformy

Tyto typy dat senzorů můžete odeslat do služby ukotvení:

* Poloha GPS: Zeměpisná šířka, zeměpisná výška
* Síla signálu Wi-Fi body přístupu v rozsahu
* Síla signálu signálů Bluetooth v dosahu

Tato tabulka shrnuje dostupnost dat senzorů na podporovaných platformách a poskytuje informace, o kterých byste měli vědět:

|                 | HoloLens | Android | iOS |
|-----------------|----------|---------|-----|
| **GPS**         | Ne<sup>1</sup>  | Ano<sup>2</sup> | Ano<sup>3</sup> |
| **Wi-Fi**        | Ano<sup>4</sup> | Ano<sup>5</sup> | No  |
| **Majáky v/v** | Ano<sup>6</sup> | Ano<sup>6</sup> | Ano<sup>6</sup>|


<sup>1</sup> externí zařízení GPS se dá přidružit k HoloLens. Pokud byste chtěli použít HoloLens s sledovacím nástrojem GPS, kontaktujte [naši podporu](../spatial-anchor-support.md) .<br/>
<sup>2</sup> podporováno prostřednictvím rozhraní [LOCATIONMANAGER][3] API (GPS a Network).<br/>
<sup>3</sup> podporováno prostřednictvím rozhraní [CLLocationManager][4] API.<br/>
<sup>4</sup> podporuje se rychlostí přibližně jedné kontroly každé 3 sekundy. <br/>
<sup>5</sup> Počínaje rozhraním API úrovně 28 jsou kontroly Wi-Fi v každé 2 minutách omezené na čtyři volání. Od Androidu 10 můžete toto omezení zakázat v nabídce **nastavení pro vývojáře** . Další informace najdete v [dokumentaci k Androidu][5].<br/>
<sup>6</sup> omezené na [Eddystone][1] a [blokovat iBeacon u][2].

### <a name="which-sensor-to-enable"></a>Který senzor chcete povolit

Výběr senzoru závisí na aplikaci, kterou vyvíjíte, a na platformě.
Tento diagram poskytuje výchozí bod pro určení, jakou kombinaci senzorů můžete povolit, v závislosti na scénáři lokalizace:

![Diagram znázorňující povolené senzory pro různé scénáře.](media/coarse-relocalization-enabling-sensors.png)

V následujících částech najdete další informace o výhodách a omezeních jednotlivých typů senzorů.

### <a name="gps"></a>GPS

GPS je možnost možnosti přejít pro venkovní scénáře.
Při použití GPS v aplikaci mějte na paměti, že čtení, které poskytuje hardware, je obvykle:

* Asynchronní a nízká frekvence (méně než 1 Hz).
* Nespolehlivé/hlučné (v průměru, 7-m standardní odchylka).

Obecně platí, že operační systém a prostorové ukotvení zařízení provede některé filtrování a extrapolaci nezpracovaného signálu GPS při pokusu o zmírnění těchto problémů. Toto dodatečné zpracování vyžaduje čas pro konvergenci, takže pro dosažení nejlepších výsledků byste se měli pokusit:

* Vytvořte jednoho poskytovatele otisků prstů na senzory co nejdříve ve vaší aplikaci.
* Nechejte poskytovatele otisku prstu snímače aktivní mezi několika relacemi.
* Nasdílejte poskytovatele otisku prstu snímače mezi více relacemi.

Zařízení GPS pro uživatele jsou obvykle nepřesná. Studie od [Zandenbergen a Barbeau (2011)][6] hlásí, že střední přesnost mobilních telefonů, které mají s asistencí GPS (a-GPS) přibližně 7 metrů. To je poměrně velká hodnota, kterou je možné ignorovat. Aby se tyto chyby měření zohlednily, služba považuje kotvy jako rozdělení pravděpodobnosti v prostoru GPS. Takže kotva je oblast místa, která s největší pravděpodobností (s více než 95% spolehlivostí) obsahuje svou skutečnou, neznámou polohu GPS.

Stejný důvod platí při dotazování pomocí GPS. Zařízení je reprezentované jako jiná prostorová důvěra kolem své pravdivé, neznámé pozice GPS. Zjišťování okolních kotev se překládá na hledání ukotvení s oblastmi s jistotami *blízko dostatečné blízkosti* oblasti spolehlivosti zařízení, jak je znázorněno zde:

![Diagram, který znázorňuje hledání kandidátů na kotvu pomocí GPS.](media/coarse-reloc-gps-separation-distance.png)

### <a name="wi-fi"></a>Wi-Fi

U HoloLens a Androidu může být Wi-Fi síla signálu dobrým způsobem, jak povolit překládání v interiéru.
Výhodou je potenciální Okamžitá dostupnost přístupových bodů Wi-Fi (například běžné v prostorách Office a nákupních Malls) bez nutnosti dalšího nastavení.

> [!NOTE]
> iOS neposkytuje rozhraní API pro čtení Wi-Fi síly signálu, takže se nedá použít pro hrubou místní podporu povolenou přes Wi-Fi.

Při použití Wi-Fi ve vaší aplikaci mějte na paměti, že čtení, které poskytuje hardware, je obvykle:

* Asynchronní a nízká frekvence (méně než 0,1 Hz).
* Potenciálně omezené na úrovni operačního systému.
* Nespolehlivé/hlučné (v průměru se standardní odchylkou ze 3-dBm).

Prostorová ukotvení se při pokusu o zmírnění těchto problémů pokusí vytvořit filtrovanou mapu Wi-Fi síly signálu během relace. Pro dosažení nejlepších výsledků zkuste:

* Před umístěním první kotvy vytvořte správnou relaci.
* Nechejte relaci aktivní, dokud je to možné. (To znamená vytvoření všech kotev a dotazování v jedné relaci.)

### <a name="bluetooth-beacons"></a>Signály Bluetooth
<a name="beaconsDetails"></a>

Pečlivé nasazení signálů Bluetooth je dobré řešení pro rozsáhlé scénáře s velkým množstvím velkých scénářů, kde GPS chybí nebo je nepřesné. Je to také jediná metoda vnitřních podporovaná na všech třech platformách.

Signály jsou obvykle všestranná zařízení, na kterých je možné nakonfigurovat všechno, včetně identifikátorů UUID a adres MAC. Prostorové kotvy Azure očekává, že se signály jednoznačně identifikují svými identifikátory UUID. Pokud tuto jedinečnost nebudete zajišťovat, budete pravděpodobně mít k nesprávné výsledky. Pro dosažení nejlepších výsledků:

* Přiřaďte k majákům jedinečné identifikátory UUID.
* Nasaďte signály způsobem, který pokrývá vaše místo jednotně a tak, aby byly dostupné alespoň tři signály z libovolného místa v prostoru.
* Předejte seznam jedinečných identifikátorů UUID signálů poskytovateli otisku prstu snímače.

Rádiové signály, jako je Bluetooth, mají vliv na překážky a můžou narušovat jiné rádiové signály. Proto může být obtížné odhadnout, zda se vaše místo stejnoměrně pokrývá. Abychom zajistili lepší prostředí pro zákazníky, doporučujeme, abyste ručně otestovali pokrytí vašich majáků. Test můžete provést procházením místa na základě kandidátských zařízení a aplikace, která zobrazuje Bluetooth v dosahu. Při testování pokrytí se ujistěte, že máte k dispozici alespoň tři signály z jakékoli strategické pozice v prostoru. Příliš mnoho signálů může mít za následek větší vzájemné rušení a nemusí nutně zlepšit přesnost hrubého rozšíření.

Signály Bluetooth obvykle pokrývají 80 měřičů, pokud se v prostoru nevyskytují žádné překážky.
Takže pro prostor, který nemá žádné velké překážky, můžete nasazovat signály ve vzorové mřížce každých 40 měřičů.

Výsledkem bude, že při vystavování z baterie dojde k ovlivnění výsledků, takže je potřeba pravidelně monitorovat nasazení za nízké nebo nenabité baterie.

Prostorové kotvy Azure budou sledovat jenom signály Bluetooth, které jsou v seznamu známých a neznámých IDENTIFIKÁTORů v blízkosti. Ale škodlivé signály, na které se allowlisted identifikátory UUID, můžou negativně ovlivnit kvalitu služby. Takže získáte nejlepší výsledky v prostorech, kde můžete řídit nasazení signalizace.

### <a name="sensor-accuracy"></a>Přesnost senzoru

Přesnost signálu GPS během vytváření kotvy i během dotazů má významný vliv na sadu vrácených kotev. Dotazy založené na Wi-Fi/majákech naopak budou brát v úvahu všechny kotvy, které mají alespoň jeden přístupový bod nebo signál běžný s dotazem. V takovém smyslu je výsledek dotazu, který je založen na Wi-Fi/majákech, určený hlavně fyzickým rozsahem přístupových bodů/majáků a překážek v prostředí.
Tato tabulka odhadne očekávaný prostor pro hledání u každého typu snímače:

| Elektrické      | Hledání – poloměr místa (přibližná) | Podrobnosti |
|-------------|:-------:|---------|
| **GPS**         | 20 m až 30 metrů | Určeno nejistotou GPS mimo jiné faktory. Nahlášená čísla jsou odhadnuta pro střední přesnost GPS mobilních telefonů s-GPS: 7 měřiči. |
| **Wi-Fi**        | 50 m až 100 m | Určuje rozsah bezdrátových přístupových bodů. Závisí na četnosti, síle vysílače, fyzických překážkách, rušení atd. |
| **Majáky v/v** |  70 m | Určuje rozsahem majáku. Závisí na četnosti, síle přenosu, fyzických překážkách, rušení atd. |

<!-- Reference links in article -->
[1]: https://developers.google.com/beacons/eddystone
[2]: https://developer.apple.com/ibeacon/
[3]: https://developer.android.com/reference/android/location/LocationManager
[4]: https://developer.apple.com/documentation/corelocation/cllocationmanager?language=objc
[5]: https://developer.android.com/guide/topics/connectivity/wifi-scan
[6]: https://www.cambridge.org/core/journals/journal-of-navigation/article/positional-accuracy-of-assisted-gps-data-from-highsensitivity-gpsenabled-mobile-phones/E1EE20CD1A301C537BEE8EC66766B0A9
