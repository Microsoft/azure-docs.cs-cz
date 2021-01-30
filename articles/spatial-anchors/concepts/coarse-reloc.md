---
title: Hrubá reprostředí
description: Přečtěte si o použití hrubých místních rozhledání, abyste našli kotvy blízko sebe.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 01/28/2021
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.custom: devx-track-csharp
ms.openlocfilehash: fc04242e61c748d7ae52e61e40206ba338a1b6aa
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2021
ms.locfileid: "99071123"
---
# <a name="coarse-relocalization"></a>Přibližná relokalizace

Hrubá lokální změna je funkce, která umožňuje velkou lokalizaci a poskytuje přibližnou, rychlou odpověď na otázku: *kde je moje zařízení. teď se mám na to, jaký obsah mám pozorovat?* Odpověď není přesná, ale je ve formátu: *blížíte se k těmto kotvám; zkuste jednu z nich najít*.

Hrubá revolba funguje označením kotev s různými čteními senzorů v zařízení, které se později používají pro rychlé dotazování. V případě scénářů v rámci venkovních scénářů jsou data snímače typicky umístěním GPS (Global Positioning System) zařízení. Pokud GPS není k dispozici nebo je nespolehlivá (například z dvířek), data snímače se skládají z přístupových bodů Wi-Fi a signálů Bluetooth v dosahu. Shromážděná data senzorů přispívají k údržbě prostorového indexu používaného prostorovými kotvami Azure k rychlému určení, které kotvy jsou v blízkosti vašeho zařízení.

## <a name="when-to-use-coarse-relocalization"></a>Kdy použít hrubou reprostředí

Pokud plánujete zvládnout více než 35 prostorových ukotvení v prostoru, který je větší než tenisový soud, pravděpodobně budete mít k výhodu hrubého územního indexování.

Rychlý pohled na kotvy, které jsou povolené hrubou šířkou, je navržený tak, aby zjednodušil vývoj aplikací s využitím špičkových kolekcí (například miliony geograficky distribuovaných) kotev. Složitost prostorového indexování je pryč, takže se můžete soustředit na logiku aplikace. Všechna kotva se po celém čase vyzvednutím prostorových ukotvení Azure provádí na pozadí.

## <a name="using-coarse-relocalization"></a>Použití hrubého prostředí

Typický pracovní postup pro vytváření a dotazování prostorových kotev Azure s hrubým přemístním využitím:
1.  Vytvořte a nakonfigurujte poskytovatele otisku prstu snímače, abyste mohli shromažďovat data ze senzorů podle vašeho výběru.
2.  Spusťte relaci prostorového kotvy Azure a vytvořte kotvy. Vzhledem k tomu, že otisk senzoru je povolený, kotvy jsou v prostoru indexovány hrubou relokální.
3.  Pomocí vyhrazených vyhledávacích kritérií v relaci prostorového kotvy Azure získáte dotaz na kotvy s využitím hrubých proměnných.

V příslušném následujícím kurzu můžete nastavit hrubou reorganizaci v aplikaci:
* [Hrubá lokální v Unity](../how-tos/set-up-coarse-reloc-unity.md)
* [Hrubá reprostředí v cíli – C](../how-tos/set-up-coarse-reloc-objc.md)
* [Hrubá reprostředí v SWIFT](../how-tos/set-up-coarse-reloc-swift.md)
* [Hrubý lokální roznárodní prostředí v jazyce Java](../how-tos/set-up-coarse-reloc-java.md)
* [Hrubá reprostředí v C++/NDK](../how-tos/set-up-coarse-reloc-cpp-ndk.md)
* [Hrubá reprostředí v C++/WinRT](../how-tos/set-up-coarse-reloc-cpp-winrt.md)

## <a name="sensors-and-platforms"></a>Senzory a platformy

### <a name="platform-availability"></a>Dostupnost platformy

Typy dat senzorů, které můžete odeslat do služby kotvy:

* Poloha GPS: Zeměpisná šířka, zeměpisná výška.
* Síla signálu přístupových bodů Wi-Fi v dosahu.
* Síla signálu signálů Bluetooth v dosahu.

Následující tabulka shrnuje dostupnost dat senzorů na podporovaných platformách spolu s případnými výstrahami, které jsou specifické pro konkrétní platformu:

|                 | HoloLens | Android | iOS |
|-----------------|----------|---------|-----|
| **GPS**         | Ne<sup>1</sup>  | Ano<sup>2</sup> | Ano<sup>3</sup> |
| **WiFi**        | Ano<sup>4</sup> | Ano<sup>5</sup> | NO  |
| **Majáky v/v** | Ano<sup>6</sup> | Ano<sup>6</sup> | Ano<sup>6</sup>|


<sup>1</sup> externí zařízení GPS se dá přidružit k HoloLens. Pokud byste chtěli použít HoloLens s sledovacím nástrojem GPS, kontaktujte [naši podporu](../spatial-anchor-support.md) .<br/>
<sup>2</sup> podporováno prostřednictvím rozhraní [LOCATIONMANAGER][3] API (GPS i síť)<br/>
<sup>3</sup> podporováno prostřednictvím rozhraní [CLLocationManager][4] API<br/>
<sup>4</sup> podporované rychlostí přibližně jedné kontroly každé 3 sekundy <br/>
<sup>5</sup> Počínaje rozhraním API Level 28 jsou kontroly Wi-Fi omezené na 4 volání každé 2 minuty. Z Androidu 10 se omezování dá zakázat v nabídce nastavení pro vývojáře. Další informace najdete v [dokumentaci k Androidu][5].<br/>
<sup>6</sup> omezené na [Eddystone][1] a [blokovat iBeacon u][2]

### <a name="which-sensor-to-enable"></a>Který senzor chcete povolit

Volba snímače je specifická pro aplikaci, kterou vyvíjíte, a platformu.
Následující diagram poskytuje výchozí bod, ve kterém je možné zapnout kombinaci senzorů v závislosti na scénáři lokalizace:

![Diagram výběru povolených senzorů](media/coarse-relocalization-enabling-sensors.png)

Následující části obsahují další informace o výhodách a omezeních pro jednotlivé typy senzorů.

### <a name="gps"></a>GPS

GPS je možnost možnosti přejít pro venkovní scénáře.
Při použití GPS v aplikaci mějte na paměti, že čtení, které poskytuje hardware, je obvykle:

* asynchronní a nízká frekvence (méně než 1 Hz).
* nespolehlivé/hlučné (v průměru 7 – m standardní odchylka).

Obecně platí, že operační systém pro zařízení i prostorové ukotvení Azure provede některé filtrování a extrapolaci nezpracovaného signálu GPS při pokusu o zmírnění těchto problémů. Toto dodatečné zpracování vyžaduje čas pro konvergenci, takže pro dosažení nejlepších výsledků byste měli zkusit:

* Vytvoření jednoho poskytovatele otisků prstů na senzoru co nejdříve ve vaší aplikaci
* ponechat poskytovatele otisku prstu snímače aktivní mezi více relacemi
* sdílet poskytovatele otisků prstů senzorů mezi více relacemi

Zařízení GPS pro uživatele jsou obvykle nepřesná. Studie od [Zandenbergen a Barbeau (2011)][6] oznamuje střední přesnost mobilních telefonů pomocí programu GPS (A GPS), který má být kolem 7 metrů, takže velká hodnota se bude ignorovat. Aby se tyto chyby měření zohlednily, služba považuje kotvy jako rozdělení pravděpodobnosti v prostoru GPS. V takovém případě je kotva teď oblast místa, která s největší pravděpodobností (tj. s více než 95% spolehlivost) obsahuje svou skutečnou, neznámou polohu GPS.

Stejný důvod je použit při dotazování pomocí GPS. Zařízení je reprezentované jako jiná prostorová důvěra kolem své pravdivé, neznámé pozice GPS. Zjišťování okolních kotev se překládá do pouhého nalezení kotev s oblastmi spolehlivosti *blízkou dostatečně blízko* v oblasti spolehlivosti zařízení, jak je znázorněno na následujícím obrázku:

![Výběr kandidátů kotvy pomocí GPS](media/coarse-reloc-gps-separation-distance.png)

### <a name="wifi"></a>WiFi

U HoloLens a Androidu může být dobrým vhodným možností, aby bylo možné zajistit revariantu v interiéru.
Jeho výhodou je potenciální Okamžitá dostupnost přístupových bodů Wi-Fi (společná v systému, například prostory Office nebo nákupní Malls) bez dalších potřebných nastavení.

> [!NOTE]
> iOS neposkytuje žádné rozhraní API pro čtení síly signálu Wi-Fi a nedá se použít pro hrubou reprostředí s podporou Wi-Fi.

Při použití Wi-Fi ve vaší aplikaci mějte na paměti, že čtení, které poskytuje hardware, je obvykle:

* asynchronní a nízká frekvence (méně než 0,1 Hz).
* potenciálně omezené na úrovni operačního systému.
* nespolehlivé/hlučné (v průměru 3 – směrodatná odchylka přepálené úrovně).

Prostorové kotvy Azure se při pokusu o zmírnění těchto problémů pokusí vytvořit filtrovanou mapu síly signálu WiFi během relace. Nejlepších výsledků byste měli vyzkoušet:

* před umístěním první kotvy vytvořte správnou relaci.
* Udržujte relaci aktivní tak dlouho, jak je to možné (to znamená vytvoření všech kotev a dotazování v jedné relaci).

### <a name="bluetooth-beacons"></a>Signály Bluetooth
<a name="beaconsDetails"></a>

Pečlivé nasazení signálů Bluetooth je dobré řešení pro scénáře s velkým množstvím velkých scénářů, ve kterých se nevyskytuje nebo není přesná Změna prostředí. Ta je také jedinou vnitřní metodou, která je podporována na všech třech platformách.

Majáky jsou obvykle všestranná zařízení, kde je možné nakonfigurovat všechno, co zahrnuje identifikátory UUID a adresy MAC. Prostorové kotvy Azure očekává, že se signály jednoznačně identifikují svými identifikátory UUID. Selhání, aby se zajistilo, že tato jedinečnost bude pravděpodobně příčinou špatných výsledků. Pro dosažení nejlepších výsledků byste měli:

* Přiřaďte k majákům jedinečné identifikátory UUID.
* Nasaďte je způsobem, který pokrývá vaše místo jednotně a tak, aby byly dostupné nejméně 3 signály z libovolného místa v prostoru.
* předat seznam jedinečných identifikátorů UUID signalizace do poskytovatele otisků prstů snímače

Rádiové signály, jako je Bluetooth, mají vliv na překážky a můžou narušovat jiné rádiové signály. Z těchto důvodů může být obtížné odhadnout, zda se vaše místo stejnoměrně pokrývá. Aby bylo zaručeno lepší prostředí pro zákazníky, doporučujeme, abyste ručně otestovali pokrytí vašich majáků. To se dá udělat tak, že provedete své místo pomocí kandidátských zařízení a aplikaci, která zobrazuje Bluetooth v dosahu. Při testování pokrytí se ujistěte, že se můžete dostat k nejméně 3 signálům z jakékoli strategické pozice vašeho prostoru. Nastavení příliš velkého počtu signálů může mít za následek více rušení a nebude nutně vylepšit hrubou přesnost reúprav.

Signály Bluetooth mají typicky pokrytí 80 měřičů, pokud se v prostoru nevyskytují žádné překážky.
To znamená, že pro prostor, který nemá žádné velké překážky, může jeden nasazovat signály na vzor mřížky každých 40 měřičů.

Signalizace vycházející z baterie bude mít negativní vliv na výsledky, takže se ujistěte, že se vaše nasazení pravidelně monitoruje s nízkými nebo neaktivními bateriemi.

Prostorové kotvy Azure budou sledovat jenom signály Bluetooth, které jsou uvedené v seznamu známých identifikátorů UUID pro signály. Škodlivé signály, které mají povolené identifikátory UUID, můžou mít negativní vliv na kvalitu služby, i když. Z tohoto důvodu získáte nejlepší výsledky v prostorech, kde můžete řídit jejich nasazení.

### <a name="sensors-accuracy"></a>Přesnost senzorů

Přesnost signálu GPS, jak při vytváření kotvy, tak i během dotazů má velký vliv na sadu vrácených kotev. Dotazy založené na Wi-Fi a signalizaci naopak budou brát v úvahu všechny kotvy, které mají alespoň jeden přístupový bod nebo signál běžný s dotazem. V takovém smyslu je výsledkem dotazu založeného na Wi-Fi a signalizaci většinou určení fyzickým rozsahem přístupových bodů/majáků a překážek v prostředí.
Následující tabulka odhadne očekávaný hledaný prostor pro každý typ senzoru:

| Elektrické      | Hledat v poloměru místa (přibližně) | Podrobnosti |
|-------------|:-------:|---------|
| GPS         | 20 m – 30 metrů | Určeno nejistotum GPS mezi ostatními faktory. Hlášené počty jsou odhadované pro střední přesnost GPS mobilních telefonů pomocí GPS, což je 7 metrů. |
| WiFi        | 50 m – 100 m | Určuje rozsah bezdrátových přístupových bodů. Závisí na četnosti, síle vysílače, fyzických překážkách, rušení atd. |
| Majáky v/v |  70 m | Určuje rozsahem majáku. Závisí na četnosti, síle přenosu, fyzických překážkách, rušení atd. |

<!-- Reference links in article -->
[1]: https://developers.google.com/beacons/eddystone
[2]: https://developer.apple.com/ibeacon/
[3]: https://developer.android.com/reference/android/location/LocationManager
[4]: https://developer.apple.com/documentation/corelocation/cllocationmanager?language=objc
[5]: https://developer.android.com/guide/topics/connectivity/wifi-scan
[6]: https://www.cambridge.org/core/journals/journal-of-navigation/article/positional-accuracy-of-assisted-gps-data-from-highsensitivity-gpsenabled-mobile-phones/E1EE20CD1A301C537BEE8EC66766B0A9
