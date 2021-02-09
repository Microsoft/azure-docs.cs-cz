---
title: Průvodce zabezpečením Azure Stack v programu Edge Mini R | Microsoft Docs
description: Popisuje bezpečnostní konvence, pokyny, požadavky a vysvětluje, jak bezpečně nainstalovat a provozovat Azure Stack hraničních zařízení R.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 02/08/2021
ms.author: alkohli
ms.openlocfilehash: aa363b1eeddff6c3b10d8e36371becb8b690697c
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2021
ms.locfileid: "99981006"
---
# <a name="azure-stack-edge-mini-r-safety-instructions"></a>Pokyny pro bezpečnost Azure Stackho okraje – Mini R

![Ikona upozornění 1 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
 ![ Ikona upozornění na bezpečnost čtení ](./media/azure-stack-edge-mini-r-safety/icon-safety-read-all-instructions.png)
 **informace o zabezpečení a stavu**

Přečtěte si všechny bezpečnostní údaje v tomto článku, abyste mohli používat Azure Stack hraničních zařízení v R, kompozici jedné baterie, jednu ELEKTRICKou energii napájení, jeden modul napájení a jeden serverový modul. Neúspěšné zpracování pokynů může vést k požáru, úrazu elektrického úrazu, zranění nebo poškození vašich vlastností. Před použitím Azure Stack hraničního Mini R si přečtěte všechny bezpečnostní údaje níže.

## <a name="safety-icon-conventions"></a>Bezpečnostní konvence ikon

U příznaků upozorňujících na nebezpečí nebezpečnosti jsou k disdobu následující slova:

| Ikona | Description |
|:--- |:--- |
| ![Symbol nebezpečí](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)| **Nebezpečí:** Označuje nebezpečnou situaci, která, pokud se nevyhne, má za následek smrt nebo vážnou škodu. <br> **Upozornění:** Označuje nebezpečnou situaci, že pokud se nevyhnete, může dojít k úmrtí nebo závažné újmě. <br> **Upozornění:** Označuje nebezpečnou situaci, že pokud se nevyhnete, může dojít k menší nebo střední újmě.|
|

Při nastavování a spouštění Azure Stackového Miniho zařízení R se budou pozorovat následující ikony nebezpečí:

| Ikona | Description |
|:--- |:--- |
| ![Nejprve si přečtěte všechny pokyny.](./media/azure-stack-edge-mini-r-safety/icon-safety-read-all-instructions.png) | Nejprve si přečtěte všechny pokyny. |
| ![Oznámení ikony oznámení ](./media/azure-stack-edge-mini-r-safety/icon-safety-notice.png) **:** | Indikuje informace považované za důležité, ale nesouvisí s rizikem. |
| ![Symbol nebezpečí](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) | Symbol nebezpečí |
| ![Ikona elektrického úrazu](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png) | Nebezpečí elektrického úrazu |
| ![Jenom vnitřní použití](./media/azure-stack-edge-mini-r-safety/icon-safety-indoor-use-only.png) | Jenom vnitřní použití |
| ![Ikona žádné části uživatelských služeb](./media/azure-stack-edge-mini-r-safety/icon-safety-do-not-access.png) | Žádné části s uživatelskými službami Nepoužívejte přístup, pokud není řádně vyškolená. |
|

## <a name="handling-precautions-and-site-selection"></a>Zpracování opatření a výběr webu

Azure Stack hraničního zařízení v síti R obsahuje následující opatření pro zpracování a kritéria pro výběr lokality:

![Ikona upozornění 2 ikona ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
 ![ elektrického úrazu ikona pro ](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png)
 ![ uživatelské služby bez ](./media/azure-stack-edge-mini-r-safety/icon-safety-do-not-access.png) **Upozornění:**

* Zkontrolujte, že zařízení *přijalo* neškodné škody. Pokud je skříň zařízení poškozená, [kontaktujte podpora Microsoftu](azure-stack-edge-placeholder.md) k získání náhrady. Nepokoušejte se zařízení provozovat.
* Pokud se domníváte, že zařízení nefunguje, obraťte se na [Podpora Microsoftu](azure-stack-edge-placeholder.md) , abyste získali náhradu. Nepokoušejte se službu zařízení provést.
* Zařízení neobsahuje žádné části uživatelsky neobsluhované. V rámci jsou přítomné nebezpečné napětí, aktuální a energetické úrovně. Neotevírejte. Vraťte zařízení do Microsoftu pro obsluhu.

![Ikona upozornění 3 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **Upozornění:**

Doporučuje se provozovat systém:

* Od zdrojů tepla, včetně přímých slunečních a příjezdů.
* V místech, která nejsou vystavena vlhkosti nebo deště.
* Umístěný v prostoru, který minimalizuje vibrace a fyzické nárazy.  Systém je navržen pro nárazy a vibrace podle MIL-STD-810G.
* Izolované od silných elektromagnetických polí vyráběných elektrickými zařízeními.
* Nepovolujte žádné kapalné ani žádné cizí objekty k zadání systému. Neumísťujte nápoje ani žádné jiné kapalné kontejnery na systém nebo poblíž.

![Ikona upozornění 4 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
 ![ Upozornění na ikonu pro uživatelské služby ](./media/azure-stack-edge-mini-r-safety/icon-safety-do-not-access.png) **:**

* Toto vybavení obsahuje baterii lithium. Nepokoušejte se Service Pack vystavovat. Baterie v tomto vybavení nepatří uživatelům. Riziko výbuchu v případě, že je baterie nahrazena nesprávným typem.

![Ikona upozornění 5 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **Upozornění:**

Pokud je součástí Azure Stackho Miniho zařízení R, účtuje se jenom za baterii, Neúčtujte ho jako samostatné zařízení.

![Ikona upozornění 6 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **Upozornění:**

* Přepínač ZAPNUTo/vypnuto na bateriovém balíčku slouží k vystavení baterie jenom pro modul serveru. Pokud je napájecí adaptér zapojen do balíčku baterie, je napájení modulu serveru provedeno i v případě, že je přepínač v VYPNUTé pozici.

![Ikona upozornění 7 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **Upozornění:**

* Nepoužívejte vypálení nebo zkrácení okruhu balíčku baterie. Je nutné jej recyklovat nebo odstranit správně.

![Ikona upozornění 8 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **Upozornění:**

* Místo použití poskytnutého napájení AC/DC má tento systém také možnost použít pole, které poskytuje typ 2590 baterie. V takovém případě musí koncový uživatel ověřit, jestli splňuje všechny použitelné bezpečnostní předpisy, dopravu, environmentální prostředí a jakékoli jiné vnitrostátní/místní předpisy.
* Při provozu systému s typem 2590 baterie provozujte baterii v podmínkách použití specifikovanou výrobcem baterie.

![Ikona upozornění 9 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **Upozornění:**

Toto zařízení má dvě porty SFP +, které se dají používat s optickými vysílači. Abyste se vyhnuli nebezpečnému laserovému záření, používejte jenom s vysílači třídy 1.

## <a name="electrical-precautions"></a>Elektrická opatření

Azure Stack hraničních zařízení v jazyce R obsahuje následující opatření pro elektroinstalace:

![Ikona upozornění 10 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) ![ elektrického úrazu ](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png) **– varování ikony:**

Při použití s adaptérem dodávek napájení:

* Zajištění bezpečného připojení elektrického uzemnění k napájecímu kabelu napájení. Střídavý kabel aktuální (AC) má tři vývody uzemněného plug-in (plug-in, který má uzemněný PIN kód). Tento modul plug-in zapadá jenom na uzemněné vývody. Nepoužívejte nepřipravený účel základu PIN kódu.
* Vzhledem k tomu, že modul plug-in napájení je hlavním zařízením pro odpojení, je nutné zajistit, aby se sokety dostaly poblíž zařízení a byly snadno dostupné.
* Odpojte napájecí šňůry (tím, že vybíráte konektor, ne kabel), a odpojte všechny kabely, pokud existuje kterákoli z následujících podmínek:

  * Napájecí kabel nebo modul plug-in se přenese do pokrytí nebo jinak poškozený.
  * Zařízení se zveřejňuje v případě deště, nadměrné vlhkosti nebo jiných kapalin.
  * Zařízení bylo vyřazeno a velikost písmen zařízení byla poškozena.
  * Máte podezření, že zařízení potřebuje službu nebo opravu.
* Před přesunutím jednotky trvale odpojte, nebo pokud se domníváte, že dojde k poškození jakýmkoli způsobem.

* Poskytněte vhodné zdroje energie s ochranou přetížení elektrického zabezpečení, aby splňovaly následující specifikace napájení:

* Napětí: 100 – 240 voltů v AC
* Aktuální: 1,7 Amperes
* Frekvence: 50 až 60 Hz

![Ikona upozornění 11 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
 ![ Upozornění na ikonu elektrického úrazu ](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png) **:**

* Nepokoušejte se změnit ani použít napájecí šňůry střídavého napětí, než jaké jsou k dispozici u zařízení.

![Ikona upozornění 12 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
 ![ elektrického úrazu ikona se stavem ](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png)
 ![ vnitřního použití jenom ](./media/azure-stack-edge-mini-r-safety/icon-safety-indoor-use-only.png) **Upozornění:**

* Zdroj napájení označený tímto symbolem se vyhodnotil jenom pro použití v interiéru.

## <a name="regulatory-information"></a>Regulativní informace

Níže najdete informace o zákonech pro Azure Stack hraničních zařízení v R, regulativní model číslo: TMA01.

Azure Stack hraničních zařízení v R je navrženo pro použití s NRTL uvedenými (UL, CSA, ETL atd.), a IEC/EN 60950-1 nebo IEC/EN 62368-1 splňující požadavky (CE označené) informační technologie.

V jiných zemích, než USA a Kanadě, se s tímto zařízením neinstalují síťové kabely (nedodávané s tímto vybavením), pokud jejich délka je větší než 3 měřiče.

Toto zařízení je určené k provozu v těchto prostředích:

| Prostředí | Specifikace |
|:---  |:--- |
| Specifikace teploty systému | <ul><li>Teplota úložiště: – 20 &deg; c – 50 &deg; c (– 4 &deg; f-122 &deg; f)</li><li>Průběžná operace: 0 &deg; c – 40 &deg; c (32 &deg; F – 104 &deg; f)</li></ul> |
| Specifikace relativní vlhkosti (RH) | <ul><li>Úložiště: 5% až 95% relativní vlhkost</li><li>Provoz: 10% až 90% relativní vlhkost</li></ul>|
| Specifikace maximální výšky | <ul><li>Operační: 15 000 metrů (4 572 měřičů)</li><li>Nefunkční: 40 000 metrů (12 192 měřičů)</li></ul>|

> ![Ikona oznámení – 2 ](./media/azure-stack-edge-mini-r-safety/icon-safety-notice.png) **Upozornění:** &nbsp; změny nebo změny zařízení, které nejsou výslovně schválené společností Microsoft, mohou zrušit oprávnění uživatele k provozu tohoto zařízení.

#### <a name="canada-and-usa"></a>Kanada a USA:

> ![Ikona oznámení-3 ](./media/azure-stack-edge-mini-r-safety/icon-safety-notice.png) **Upozornění:** &nbsp; toto vybavení bylo testováno a bylo zjištěno, že vyhovuje omezením pro třídu a digitálním zařízením, a to v souladu s částí 15 pravidel FCC. Tato omezení jsou navržená tak, aby poskytovala rozumnou ochranu proti škodlivému rušení, když je zařízení provozováno v komerčním prostředí. Toto vybavení vygeneruje, používá a může vycházet z frekvence rádiového kmitočtu a pokud není nainstalované a používané v souladu s ruční příručkou, může způsobit škodlivou interferenci s rádiovou komunikací. Provoz tohoto vybavení v domácí oblasti pravděpodobně způsobí škodlivou interferenci v takovém případě bude uživatel muset opravit rušivý vliv na své náklady.

Adaptér A6150 Wi-Fi USB, který je součástí tohoto zařízení, je určený k tomu, aby se mohl provozovat blízko lidského těla, se testuje podle dodržování předpisů pro konkrétní tělo (správní oblast). Mezní hodnota pro správní oblast, která je určena pro FCC, je 1,6 W/kg, pokud je průměrně větší než 1 g tkáně. Při nastavování produktu nebo jeho použití při práci s vaším tělem udržuje od těla vzdálenosti 10 mm, aby se zajistila shoda s požadavky na expozici RF.

Adaptér NETGEAR A6150 Wi-Fi splňuje standard ANSI/IEEE C 95.1-1999 a byl testován v souladu s metodami měření a postupy určenými v bulletinu OET 65 dodatku C.

A6150 specifická absorpce na NETGEAR (správní oblast): 1,18 W/kg, průměr v 1 g tkáně

Adaptér NETGEAR A6150 Wi-Fi se používá jenom se schválenými anténami. Toto zařízení a jeho antény se nesmí nacházet společně se všemi jinými anténami nebo vysílači s výjimkou případů, kdy se používají postupy pro více vysílačů pro produkty FCC. U produktů, které jsou dostupné na trhu USA, se dá provozovat jenom kanál 1 ~ 11. Výběr jiných kanálů není možný.

Operace v pásmu 5150 – 5250 MHz je určena jenom pro použití v interiéru ke snížení potenciálu škodlivých rušivých přenosů do mobilních satelitních systémů souběžného kanálu.

![Upozornění na regulativní informace – použití v interiéru](./media/azure-stack-edge-mini-r-safety/regulatory-information-indoor-use-only.png)


Uživatelům se doporučuje, aby se vysoce výkonné paprsky přiřazují jako primární uživatelé (prioritní uživatelé) pásem 5250 – 5350 MHz a 5650 – 5850 MHz. Tyto paprsky můžou způsobit rušení nebo poškození zařízení LE-LAN.

Toto vybavení generuje, používá a může vycházet z frekvence rádiového kmitočtu a pokud není nainstalováno a používáno v souladu s pokyny, může způsobit škodlivé rušení rádiových komunikací. Neexistuje však záruka, že k rušení nedojde v konkrétní instalaci.

Pokud by toto zařízení způsobilo škodlivé rušení rádiového nebo televizního přijímače, které se dá určit zapnutím a vypnutím zařízení, doporučuje se uživateli pokusit se opravit rušení jedním nebo více následujícími opatřeními:

- Přeorientovat nebo přemístit přijímací anténu.
- Zvyšte oddělení mezi zařízením a přijímačem.
- Připojte zařízení k zásuvce na okruhu, který se liší od místa, ke kterému je příjemce připojen.
- Požádejte o podporu svého prodejce nebo TELEVIZNÍho technika.

Další informace o problémech s rušením najdete na webu FCC na adrese [fcc.gov/cgb/consumerfacts/interference.html](https://www.fcc.gov/consumers/guides/interference-radio-tv-and-telephone-signals). Můžete také volat FCC v 1-888 – zavolá FCC, aby vyžádala rušivé a rušivé vyrušování.

Další informace o bezpečnosti radiofrequency najdete na webu FCC na [https://www.fcc.gov/general/radio-frequency-safety-0](https://www.fcc.gov/general/radio-frequency-safety-0) webu a na webu odvětví Kanady na adrese [http://www.ic.gc.ca/eic/site/smt-gst.nsf/eng/sf01904.html](http://www.ic.gc.ca/eic/site/smt-gst.nsf/eng/sf01904.html) .

Tento produkt ukázal dodržování předpisů EMC za podmínek, které zahrnují použití odpovídajících periferních zařízení a stíněných kabelů mezi systémovými součástmi. Je důležité, abyste používali vyhovující periferní zařízení a stíněné kabely mezi systémovými součástmi, aby se snížila pravděpodobnost, že by došlo k rušení rádiových přepínačů, televizních zařízení a dalších elektronických zařízení.

Toto zařízení je v souladu s částí 15 pravidel FCC a s výjimkou standardu RSS pro odvětví Kanady. Operace podléhá následujícím dvěma podmínkám: (1) Toto zařízení nemusí způsobovat škodlivou interferenci a (2) Toto zařízení musí přijmout jakékoli rušivé rušení, včetně rušení, které by mohlo způsobit nepožadovanou činnost zařízení.

![Regulativní informace – upozornění 1](./media/azure-stack-edge-mini-r-safety/regulatory-information-1.png)

CAN ICES-3 (A)/NMB-3 (A)

Microsoft Corporation, One Microsoft Way, Redmond, WA 98052, USA

USA: (800) 426-9400

Kanada: (800) 933-4750

A6150 Wi-Fi USB adaptéru rozhraní pro identifikaci: PY318300429
 
ID adaptéru A6150 Wi-Fi USB Adapter: 4054A-18300429

Adaptér A6150 Wi-Fi USB, který je součástí tohoto zařízení, dodržuje předpisy pro obecné plnění nebo neřízené limity ozáření v IC RSS-102 a byl testován v souladu s metodami měření a postupy určenými v IEEE 1528. Udržujte alespoň 10 mm pro podmíněnou podmínku pro tělo.

Adaptér A6150 Wi-Fi USB je v souladu s maximálním limitem pro vystavení přenosného RF v Kanadě stanoveném pro nekontrolované prostředí a je bezpečný pro zamýšlené operace, jak je popsáno v jeho příručce. Další snížení expozice RF se dá dosáhnout tím, že produkt co nejvíce vydržíte od vašeho těla nebo nastavíte zařízení na nižší výstupní výkon, pokud je taková funkce dostupná.

Tabulka s konkrétní mírou absorpce (správní oblast), která je průměrně větší než 1 g pro každý produkt, se může zobrazit v části USA výše.

![Regulativní informace – upozornění 2](./media/azure-stack-edge-mini-r-safety/regulatory-information-2.png)

#### <a name="european-union"></a>EVROPSKÁ UNIE:

Požádat o kopii prohlášení o shodě EU pro toto zařízení.

Adaptér A6150 Wi-Fi USB, který je součástí tohoto zařízení, je v souladu se směrnicí 2014/53/EU a dá se taky zadat na vyžádání.

> ![Ikona upozornění 13 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) Toto je produkt třídy. V domácím prostředí může tento produkt v takovém případě způsobit rušivé porušování. uživatel může být nutný k tomu, aby přijal vhodná opatření.

Odstraňování odpadních baterií a elektrických a elektronických zařízení:

![Ikona upozornění 14](./media/azure-stack-edge-mini-r-safety/icon-ewaste-disposal.png)

Tento symbol v produktu nebo jeho bateriích nebo jeho obalech znamená, že tento produkt a všechny baterie, které obsahuje, nesmí být zneškodněny s využitím vašich domácích odpadů. Místo toho je vaší zodpovědností předat ho do vhodného bodu shromažďování pro recyklaci baterií a elektrického a elektronického vybavení. Tato samostatná kolekce a recyklace bude pomáhat šetřit přírodní prostředky a zabránit potenciálním negativním důsledkům pro lidské zdraví a prostředí z důvodu možné přítomnosti nebezpečných látek v bateriích a elektrických i elektronických zařízeních, což může být způsobeno nevhodným vyřazením. Další informace o tom, kde odkládat baterie a elektrická a elektronický odpad, získáte od místního města, služby pro odstranění odpadu v domácnosti nebo nákupu, kde jste tento produkt zakoupili. Kontaktujte erecycle@microsoft.com pro další informace o OEEZ.

Tento produkt obsahuje buněčnou baterii buněk (y).

Adaptér USB A6150 Wi-Fi, který je součástí tohoto zařízení, je určený k provozu blízko lidského těla a je testován na dodržování předpisů pro konkrétní míru absorpce v těle (viz nižší hodnoty). Při nastavování produktu nebo jeho použití při práci s vaším tělem udržují 10mm od těla, aby se zajistila shoda s požadavky na expozici RF.

Průměrná **míra absorpce A6150 v NETGEAR (správní oblast):** 0,54 W/kg, nad 10g z tkáně

 
Toto zařízení může fungovat ve všech členských státech EU. Sledujte vnitrostátní a místní předpisy, kde se zařízení používá. Toto zařízení se omezuje jenom na vnitřní použití při provozu v rozsahu kmitočtu 5150-5350 MHz v následujících zemích:  

![Země EU, které vyžadují jenom vnitřní použití](./media/azure-stack-edge-mini-r-safety/mini-r-safety-eu-indoor-use-only.png)

V souladu s článkem 10.8 (a) a 10.8 (b) červené informace v následující tabulce najdete informace o používaných frekvenčních pásmech a o maximálním výkonu RF bezdrátových produktů NETGEAR pro prodej v rámci EU:

**WiFi**

| Rozsah frekvence (MHz) | Použité kanály | Maximální síla přenosu (dBm/mW) |
| --------------------- | ------------- | --------------------------- |
| 2 400 – 2483.5 | 1-13    | ODFM: 19,9 dBm (97,7 mW) <br> CCK: 17,9 dBm (61,7 mW) |
| 5150-5320   | 36-48   | 22,9 dBm (195 mW) |
| 5250-5350   | 52-64   | 22,9 dBm (195 mW) s TPC <br> 19,9 dBm (97,7 mW) bez TPC |
| 5470-5725   | 100-140 | 29,9 dBm (977 mW) s TPC <br> 29,6 dBm (490 mW) bez TPC |

Microsoft Ireland Sandyford – Est – Dublin D18 KX32 IRL

Telefonní číslo: + 353 1 295 3826

Číslo faxu: + 353 1 706 4110

#### <a name="singapore"></a>Singapur:

Adaptér NETGEAR A6150 Wi-Fi, který je součástí tohoto vybavení, vyhovuje standardům IMDA.


## <a name="next-steps"></a>Další kroky

- [Příprava na nasazení Azure Stack Minikou R](azure-stack-edge-mini-r-deploy-prep.md)
