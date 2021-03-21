---
title: zahrnout soubor
description: zahrnout soubor
services: iot-fundamentals
author: robinsh
ms.service: iot-fundamentals
ms.topic: include
ms.date: 08/07/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 28609ad27330ae4ea5ea7c0d02d5a61181fbe0df
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "95564226"
---
Při navrhování systému je důležité pochopit potenciální hrozby pro daný systém a odpovídajícím způsobem přidat patřičnou ochranu, protože systém je navržený a navržený. Je důležité navrhnout produkt od začátku s ohledem na zabezpečení, protože porozumět tomu, jak útočník může ohrozit zabezpečení systému, pomáhá zajistit, aby byla na začátku vhodná omezení.

## <a name="security-starts-with-a-threat-model"></a>Zabezpečení začíná modelem hrozeb

Společnost Microsoft pro své produkty používala dlouhodobé modely hrozeb a v rámci veřejně dostupného procesu modelování hrozeb společnosti. Zkušenost ze společnosti ukazuje, že modelování má neočekávané výhody nad rámec okamžitého porozumění, které hrozby jsou v souvislosti s nimi. Například také vytvoří způsob pro otevřenou diskuzi s ostatními mimo vývojový tým, což může vést k novým nápadům a vylepšením v produktu.

Cílem modelování hrozeb je pochopit, jak může útočník ohrozit systém a pak zajistit, aby byla zajištěna vhodná omezení. Modelování hrozeb vynutí, aby tým návrhu zvážil omezení rizik, protože systém je navržený spíše než po nasazení systému. Tato skutečnost je kriticky důležitá, protože retrofitting zabezpečení ochrany nesčetných zařízení v poli je neproveditelné, náchylné k chybám a ponechávají zákazníky v ohrožení.

Řada vývojových týmů má vynikající úlohu, která zachytí funkční požadavky pro systém, který má výhody pro zákazníky. Ale identifikace nezjevných způsobů, které může někdo zneužít, je ale náročnější. Modelování hrozeb může vývojářům pomáhat s tím, co může útočník dělat a proč. Modelování hrozeb je strukturovaný proces, který vytváří diskuzi o rozhodnutích o návrhu zabezpečení v systému, a také o změnách návrhu, které se provedou způsobem, který má vliv na zabezpečení. I když je model hrozby prostě dokumentem, tato dokumentace také představuje ideální způsob, jak zajistit kontinuitu znalostí, podobu uchování zkušeností a rychlé zprovoznění nového týmu. Nakonec je výsledkem modelování hrozeb, že vám umožní vzít v úvahu další aspekty zabezpečení, jako třeba jaké závazky zabezpečení, které chcete zákazníkům poskytnout. Tyto závazky ve spojení se zaměřením na modelování hrozeb a testování řešení Internet věcí (IoT).

### <a name="when-to-do-threat-modeling"></a>Kdy provést modelování hrozeb

[Modelování hrozeb](https://www.microsoft.com/en-us/sdl/adopt/threatmodeling.aspx) nabízí největší hodnotu, když ji zachováte do fáze návrhu. Při navrhování máte největší flexibilitu v provádění změn, které eliminují hrozby. Odstranění hrozeb podle návrhu je požadovaný výsledek. Je mnohem snazší než přidávání rizik, jejich testování a zajištění jejich aktuálnosti a navíc, takové vyloučení není vždy možné. Je obtížnější eliminovat hrozby, když se produkt stane vyšším, a nakonec vyžaduje více práce a mnohem těžší kompromisy než modelování hrozeb ve vývoji.

### <a name="what-to-consider-for-threat-modeling"></a>Co je potřeba vzít v úvahu pro modelování hrozeb

Měli byste se podívat na řešení jako celek a také se zaměřit na následující oblasti:

* Funkce zabezpečení a ochrany osobních údajů
* Funkce, jejichž chyby jsou relevantní zabezpečení
* Funkce, které se dotkne hranice vztahu důvěryhodnosti

### <a name="who-performs-threat-modeling"></a>Kdo provádí modelování hrozeb

Modelování hrozeb je proces podobný jinému. Je vhodné považovat dokument modelu hrozeb jako jakoukoli jinou komponentu řešení a ověřit ho. Řada vývojových týmů má vynikající úlohu, která zachytí funkční požadavky pro systém, který má výhody pro zákazníky. Ale identifikace nezjevných způsobů, které může někdo zneužít, je ale náročnější. Modelování hrozeb může vývojářům pomáhat s tím, co může útočník dělat a proč.

### <a name="how-to-perform-threat-modeling"></a>Jak provádět modelování hrozeb

Proces modelování hrozeb se skládá ze čtyř kroků; postup je následující:

* Modelování aplikace
* Zobrazení výčtu hrozeb
* Zmírnění hrozeb
* Ověření rizik

#### <a name="the-process-steps"></a>Kroky procesu

Při sestavování modelu hrozeb Pamatujte na tři pravidla, která vám pomůžeme:

1. Vytvořte diagram z referenční architektury.

2. Začátek šířky – první. Získejte přehled a pochopte systém jako celek před úplným začnete. Tento přístup vám pomůže zajistit, že na správných místech máte hlubokou podrobně.

3. Zařídit proces, nepovolujte vám procesovou jednotku. Pokud ve fázi modelování zjistíte problém a chcete ho prozkoumat, navštivte! Nebojte se, že budete muset postupovat podle těchto kroků slavishly.

#### <a name="threats"></a>Hrozby

Mezi čtyři základní prvky modelu hrozeb patří:

* Procesy, jako jsou webové služby, služby Win32 a NIX procesy démon. Některé komplexní entity (například brány polí a senzory) je možné v případě, že není možné technické procházení k podrobnostem v těchto oblastech, abstrakce jako proces.

* Úložiště dat (uložená data jsou uložená, například konfigurační soubor nebo databáze)

* Tok dat (kde se data pohybují mezi ostatními elementy v aplikaci)

* Externí entity (cokoli, co komunikuje se systémem, ale nejsou pod kontrolou aplikace, příklady zahrnují uživatele a satelitní kanály)

Všechny prvky v diagramu architektury podléhají různým hrozbám; v tomto článku najdete klávesové zkratky pro rozteč. Přečtěte si [znovu modelování hrozeb a](/archive/blogs/larryosterman/threat-modeling-again-stride) Projděte si další informace o elementech pro rozteč.

Na různé prvky diagramu aplikace se vztahují určité hrozby pro rozteč:

* Na procesy se vztahuje rozteč
* Toky dat podléhají TID.
* Úložiště dat podléhají TID a někdy R, pokud jsou úložiště dat soubory protokolu.
* Externí entity podléhají SRD

## <a name="security-in-iot"></a>Zabezpečení v IoT

Připojená zařízení pro zvláštní účely mají významný počet potenciálních oblastí a způsobů interakce interakce, které je potřeba vzít v úvahu, aby poskytovaly rozhraní pro zabezpečení digitálního přístupu k těmto zařízením. Pojem "Digitální přístup" se tady používá k rozlišení všech operací, které se provedou prostřednictvím přímých interakcí zařízení, kde je zajištěné zabezpečení přístupu prostřednictvím fyzického řízení přístupu. Například umístění zařízení do místnosti s zámkem na dveřích. I když se fyzický přístup nedá odepřít pomocí softwaru a hardwaru, můžou být podniknuty míry, aby se zabránilo fyzickému přístupu od vedoucího k rušení systému.

Při zkoumání vzorců interakce si prohlédněte "řízení zařízení" a "data zařízení" se stejnou úrovní pozornosti. "Ovládací prvek zařízení" může být klasifikován jako jakákoli informace, která je poskytnuta každé ze smluvních stran, s cílem změnit nebo ovlivnit jeho chování směrem ke stavu nebo stavu jeho prostředí. "Data zařízení" je možné klasifikovat jako všechny informace, které zařízení vysílá do jakékoli jiné strany o jeho stavu a pozorovaném stavu jeho prostředí.

Aby bylo možné optimalizovat osvědčené postupy zabezpečení, doporučujeme, aby byla typická architektura IoT v rámci cvičení modelování hrozeb rozdělena do několika součástí nebo zón. Tyto zóny jsou plně popsané v této části a zahrnují:

* Zařízení
* Pole Brána,
* Cloudové brány a
* Orgány.

Zóny představují širší způsob segmentace řešení; Každá zóna má často vlastní data a požadavky na ověřování a autorizaci. Zóny je také možné použít k izolaci škod a omezení dopadu zóny s nízkým vztahem důvěryhodnosti ve vyšších zónách důvěryhodnosti.

Každá zóna je oddělena hranicí vztahu důvěryhodnosti, která je zaznamenána jako tečkovaná červená čára v následujícím diagramu. Představuje přechod dat/informací z jednoho zdroje do druhého. Během tohoto přechodu mohou být data/informace předmětem falšování identity, manipulace, odmítnutí, zpřístupnění informací, odepření služby a zvýšení úrovně oprávnění (Rozteč).

![Zóny zabezpečení IoT](media/iot-security-architecture/iot-security-architecture-fig1.png) 

Komponenty, které jsou znázorněné v rámci každé hranice, se také vztahují k tomu, aby bylo možné celé řešení 360 zobrazit. Následující části jsou podrobnější na jednotlivých součástech a konkrétních tématech týkajících se zabezpečení a řešeních, která by měla být zavedena.

Následující části popisují standardní komponenty, které se obvykle nacházejí v těchto zónách.

### <a name="the-device-zone"></a>Zóna zařízení

Prostředí zařízení je bezprostřední fyzické místo kolem zařízení, kde je možné použít fyzický přístup nebo místní síť peer-to-peer Digital Access k zařízení. Předpokládá se, že "místní síť" je síť, která je odlišná a izolovaná od sítě, ale je potenciálně připojená k veřejnému Internetu, a zahrnuje libovolné technologie bezdrátového přijímače s krátkým rozsahem, která umožňuje komunikaci zařízení peer-to-peer. *Neobsahuje žádnou* technologii virtualizace sítě, která vytváří dojem takové místní sítě, a také nezahrnuje sítě veřejných operátorů, které vyžadují, aby všechna dvě zařízení komunikovala přes veřejné síťové místo, pokud museli zadat komunikační vztah peer-to-peer.

### <a name="the-field-gateway-zone"></a>Zóna brány pole

Pole Brána je zařízení nebo zařízení nebo nějaký počítačový software pro obecné účely, který funguje jako komunikační aktivátor a případně jako systém řízení zařízení a centrum zpracování dat zařízení. Zóna brány pole zahrnuje samotnou bránu pole a všechna zařízení, která jsou k němu připojená. V takovém případě jsou brány polí závislé mimo vyhrazená zařízení pro zpracování dat, obvykle se jedná o umístění, které může podléhat fyzickému vniknutí a má omezenou provozní redundanci. To vše znamená, že brána pole je obvykle jedna z nich se může dotýkat a napadení zařízení při vědomí, co je jeho funkce.

Brána pole se liší od pouhého směrovače provozu v tom, že má aktivní roli při správě přístupu a informačního toku, což znamená, že se jedná o aplikační entitu a připojení k síti nebo terminálu relace. Zařízení nebo brána firewall pro překlad adres (NAT) naopak nezpůsobuje, že se jedná o brány pole, protože nejsou explicitním připojením nebo terminály relace, ale také s připojením k trasám (nebo blokem) nebo relacím. Brána pole má dvě samostatné oblasti Surface. Jedna ploška zařízení, která jsou k němu připojená a která představují uvnitř zóny, a druhá se všemi externími stranami a je hraniční oblastí.

### <a name="the-cloud-gateway-zone"></a>Zóna cloudové brány

Cloudová brána je systém, který umožňuje vzdálenou komunikaci z a do zařízení nebo bran polí z několika různých lokalit ve veřejném síťovém prostoru, a to obvykle do cloudového ovládacího prvku a analytického systému dat, což je federace takových systémů. V některých případech může cloudová brána okamžitě usnadnit přístup k zařízením se speciálním účelem z terminálů, jako jsou tablety nebo telefony. V kontextu, který je zde popsán, se jedná o "Cloud", který odkazuje na vyhrazený systém zpracování dat, který není vázán na stejnou lokalitu jako připojená zařízení nebo brány polí. V zóně cloudu navíc provozní míry brání cílenému fyzickému přístupu a nemusí být nutně vystavené infrastruktuře "veřejného cloudu".  

Cloudová brána může být namapována na překrytí virtualizace sítě, která umožňuje izolaci cloudové brány a všech připojených zařízení nebo bran polí z jakéhokoli jiného síťového provozu. Samotná cloudová brána není systémem pro řízení zařízení nebo se zařízením pro zpracování nebo úložiště pro data zařízení. rozhraní těchto zařízení s cloudovou bránou. Zóna cloudové brány zahrnuje samotnou cloudovou bránu spolu se všemi branami a zařízeními, která jsou k ní přímo nebo nepřímo připojená. Okraj zóny je odlišná oblast povrchu, ve které všechny externí strany komunikují.

### <a name="the-services-zone"></a>Zóna služby

Pro tento kontext je definována "služba" jako jakákoli softwarová součást nebo modul, který se zařízením prochází pomocí pole nebo cloudové brány pro účely shromažďování a analýzy dat a také pro příkazy a řízení. Služby jsou zprostředkovatelé. Jednají v rámci své identity týkající se bran a dalších subsystémů, ukládání a analyzování dat, autonomního vydávání příkazů do zařízení založených na datech Insights nebo v plánech a zpřístupnění informací a možností řízení autorizovaným koncovým uživatelům.

### <a name="information-devices-versus-special-purpose-devices"></a>Informace – zařízení versus zařízení pro zvláštní účely

Počítače, telefony a tablety jsou primárně interaktivní informační zařízení. Telefony a tablety jsou explicitně optimalizované pro maximalizaci životnosti baterie. Jsou přednostně vypnuté, když bezprostředně nepracují s osobou, nebo když neposkytují služby, jako je přehrávání hudby nebo jejich vlastník na konkrétní umístění. V rámci perspektivy systémů fungují tato zařízení s těmito informacemi hlavně jako proxy servery k lidem. Jedná se o "poháněcí zařízení", které navrhují akce a shromažďují vstupy "senzory lidí".

Zařízení pro zvláštní účely, od jednoduchých senzorů teploty po komplexní výrobní linky výroby s tisíci komponentami, se liší. Tato zařízení jsou mnohem více vymezená v účelech a i když poskytují nějaké uživatelské rozhraní, jsou v podstatě vymezená pro propojení s nebo integrací do prostředků ve fyzickém světě. Měří a sestavuje podmínky prostředí, přepínají ventily, řídí servosy, zvukové alarmy, indikátory přepnutí a dělají mnoho dalších úloh. Usnadňují práci, pro kterou je informační zařízení buď příliš obecné, příliš nákladné, moc velké nebo příliš poměrně křehký. Konkrétní účel okamžitě stanoví svůj technický návrh, jakož i dostupný peněžní rozpočet pro svou provozní a plánovanou životnost. Kombinace těchto dvou klíčových faktorů omezuje dostupné provozní rozpočty energie, fyzické nároky a tudíž dostupné úložiště, výpočetní prostředky a možnosti zabezpečení.

Pokud něco "se nepovedlo" s automatizovanými nebo vzdáleně přidanými zařízeními, například fyzické vady nebo chyby logiky řízení, které willful neoprávněné vniknutí a manipulaci. Produkční šarže mohou být zničeny, budovy mohou být Looted nebo vypáleny a osoby mohou být poškozeny nebo dokonce i Die. Jedná se o celou jinou třídu poškození, než někdo maxing na limit odcizené platební karty. Panel zabezpečení pro zařízení, která usnadňují pohyb, a také pro data senzorů, která nakonec mají za následek přesunutí příkazů, které způsobují pohyb, musí být vyšší než v jakémkoli scénáři elektronického obchodování nebo v bankovnictví.

### <a name="device-control-and-device-data-interactions"></a>Interakce s daty ovládacího prvku zařízení a zařízení

Připojená zařízení pro zvláštní účely mají významný počet potenciálních oblastí a způsobů interakce interakce, které je potřeba vzít v úvahu, aby poskytovaly rozhraní pro zabezpečení digitálního přístupu k těmto zařízením. Pojem "Digitální přístup" se tady používá k rozlišení všech operací, které se provedou prostřednictvím přímých interakcí zařízení, kde je zajištěné zabezpečení přístupu prostřednictvím fyzického řízení přístupu. Například umístění zařízení do místnosti s zámkem na dveřích. I když se fyzický přístup nedá odepřít pomocí softwaru a hardwaru, můžou být podniknuty míry, aby se zabránilo fyzickému přístupu od vedoucího k rušení systému.

Při prozkoumávání vzorů interakce si prohlédněte "řízení zařízení" a "data zařízení" se stejnou úrovní pozornosti při modelování hrozeb. "Ovládací prvek zařízení" může být klasifikován jako jakákoli informace, která je poskytnuta každé ze smluvních stran, s cílem změnit nebo ovlivnit jeho chování směrem ke stavu nebo stavu jeho prostředí. "Data zařízení" je možné klasifikovat jako všechny informace, které zařízení vysílá do jakékoli jiné strany o jeho stavu a pozorovaném stavu jeho prostředí.

## <a name="performing-threat-modeling-for-the-azure-iot-reference-architecture"></a>Provádění modelování hrozeb pro referenční architekturu Azure IoT

Microsoft používá rozhraní, které bylo dříve popsáno pro modelování hrozeb pro Azure IoT. V následující části se používá konkrétní příklad referenční architektury Azure IoT a ukazuje, jak se zamyslet na modelování hrozeb pro IoT a jak řešit identifikované hrozby. Tento příklad identifikuje čtyři hlavní oblasti soustředění:

* Zařízení a zdroje dat,
* Přenos dat,
* Zpracování zařízení a událostí a
* Zobrazení

![Modelování hrozeb pro Azure IoT](media/iot-security-architecture/iot-security-architecture-fig2.png)

Následující diagram nabízí zjednodušený pohled na architekturu IoT od Microsoftu pomocí modelu toku dat, který používá Microsoft Threat Modeling Tool:

![Modelování hrozeb pro Azure IoT pomocí MS Threat Modeling Tool](media/iot-security-architecture/iot-security-architecture-fig3.png)

Je důležité si uvědomit, že architektura odděluje možnosti zařízení a brány. Tento přístup umožňuje uživateli využívat zařízení brány, která jsou bezpečnější: jsou schopná komunikovat s cloudovou bránou pomocí zabezpečených protokolů, což obvykle vyžaduje větší režii na zpracování, kterou může nativní zařízení, například termostat, poskytnout sám. V zóně služeb Azure Předpokládejme, že služba Azure IoT Hub je představována cloudovou bránou.

### <a name="device-and-data-sourcesdata-transport"></a>Zařízení a zdroje dat/přenos dat

V této části se seznámíte s architekturou, která je předem popsána v rámci modelování hrozeb, a poskytuje přehled o tom, jak řešit některé z podstatných otázek. Tento příklad se zaměřuje na základní prvky modelu hrozeb:

* Procesy (pod kontrolou a externími položkami)
* Komunikace (označované také jako toky dat)
* Úložiště (označuje se také jako úložiště dat)

#### <a name="processes"></a>Procesy

V každé z kategorií uvedených v architektuře Azure IoT se tento příklad snaží zmírnit mnoho různých hrozeb v různých fázích, v nichž existují data a informace o procesu, komunikaci a úložišti. Následuje přehled nejběžnějších funkcí pro kategorii "Process" následovaný přehledem toho, jak by mohly být tyto hrozby nejlépe zmírňované:

**Falšování**: útočník může extrahovat materiál kryptografického klíče ze zařízení, buď na úrovni softwaru nebo hardwaru, a následně přistupovat k systému s jiným fyzickým nebo virtuálním zařízením v rámci identity zařízení, ze kterého byl materiál získán. Dobrá ilustrace je vzdálené ovládací prvky, které umožňují změnit libovolné televizní pořady a oblíbené nástroje Prankster.

**Odepření služby (D)**: zařízení se dá vykreslit neschopnými fungovat nebo komunikovat, pokud se nebudete moct zasahovat do rádiových frekvencí nebo z vyjmutí vodičů. Například kamera pro dohled, která měla úmyslně vykrojit své napájení nebo síťové připojení, nemůže vůbec hlásit data.

**Manipulace (T)**: útočník může částečně nebo zcela nahradit software, který je v zařízení spuštěný, a potenciálně umožnit nahrazenému softwaru využít originální identitu zařízení, pokud je materiál klíče nebo kryptografické zařízení, které drží klíčové materiály, k dispozici nedovolenému programu. Útočník může například využít extrahovaný materiál klíče k zachycení a potlačení dat ze zařízení na komunikační cestě a nahradit je falešnými daty, která jsou ověřena pomocí odcizeného materiálu klíče.

**Zpřístupnění informací (I)**: Pokud zařízení používá běžící software, může takový software potenciálně způsobit nevracení dat na neoprávněné strany. Útočník může například využít extrahovaný klíčový materiál pro vložení do komunikační cesty mezi zařízením a řadičem nebo bránou pole nebo cloudovou bránou k odsávání informací.

**Zvýšení oprávnění (E)**: zařízení, které má konkrétní funkci, může být vynucené dělat něco jiného. Například ventil, který je naprogramován na otevřený poloviční způsob, může být obtížné otevřít, aby se otevřel celý způsob.

| **Komponenta** | **Hrozba** | **Omezení rizik** | **Riziko** | **Implementace** |
| --- | --- | --- | --- | --- |
| Zařízení |S |Přiřazení identity k zařízení a ověřování zařízení |Zařízení nebo část zařízení se nahrazuje jiným zařízením. Jak víte, že mluvíte se správným zařízením? |Ověřování zařízení pomocí protokolu TLS (Transport Layer Security) nebo IPSec. Infrastruktura by měla podporovat použití předsdíleného klíče (PSK) na zařízeních, která nemůžou zpracovávat úplný asymetrický kryptografii. Využijte Azure AD, [OAuth](https://www.rfc-editor.org/pdfrfc/rfc6755.txt.pdf) |
|| TRID |Aplikujte na zařízení mechanismy tamperproof, například tím, že nebudete moct z zařízení extrahovat klíče a další kryptografické materiály. |Riziko je v případě, že někdo zaznamená manipulaci se zařízením (při fyzickém rušení). V takovém případě jste si jisti, že zařízení nebylo manipulováno. |Nejúčinnější zmírnění je funkce čipu TPM (Trusted Platform Module), která umožňuje ukládat klíče ve zvláštních mezičipuch, ze kterých klíče nelze číst, ale lze je použít pouze pro kryptografické operace, které používají klíč, ale nikdy nezavřou klíč. Šifrování paměti zařízení. Správa klíčů pro zařízení. Podpis kódu. |
|| E |Řízení přístupu zařízení. Autorizační schéma. |Pokud zařízení umožňuje provádět jednotlivé akce na základě příkazů z vnějšího zdroje nebo dokonce napadených senzorů, umožňuje útoku provádět operace, které nejsou jinak dostupné. |Má schéma autorizace pro zařízení |
| Brána pole |S |Ověřování brány pole ke cloudové bráně (například na základě certifikátu, PSK nebo založeného na deklaracích identity) |Pokud někdo může falešně vyvažovat bránu pole, může se zobrazit jako jakékoli zařízení. |TLS RSA/PSK, IPSec, [RFC 4279](https://tools.ietf.org/html/rfc4279). Každé stejné důležité aspekty úložiště klíčů a ověření identity zařízení jsou obecně vhodné k používání TPM. rozšíření 6LowPAN pro protokol IPSec pro podporu sítí bezdrátového senzoru (WSN). |
|| TRID |Chránit bránu pole proti manipulaci (TPM?) |Útoky s falešnou identitou, které pronikají cloudovou bránu, se kterými se dá mluvit, by mohly vést k odhalení informací a manipulaci s daty |Šifrování paměti, TPM, ověřování. |
|| E |Mechanismus řízení přístupu pro bránu pole | | |

Tady jsou některé příklady hrozeb v této kategorii:

**Falšování identity**: útočník může extrahovat materiál kryptografického klíče ze zařízení, buď na úrovni softwaru nebo hardwaru, a následně přistupovat k systému s jiným fyzickým nebo virtuálním zařízením v rámci identity zařízení, ze kterého byl materiál získán.

**Odepření služby**: zařízení se dá vykreslit neschopným pracovat nebo komunikovat, a to rušením rádiových frekvencí nebo vyjmutím vodičů. Například kamera pro dohled, která měla úmyslně vykrojit své napájení nebo síťové připojení, nemůže vůbec hlásit data.

**Manipulace**: útočník může částečně nebo zcela nahradit software, který je na zařízení spuštěný, a potenciálně umožnit nahrazenému softwaru využívat originální identitu zařízení, pokud je materiál klíče nebo kryptografické zařízení, které drží klíčové materiály, k dispozici nedovolenému programu.

**Manipulace**: pracovní kamera, která zobrazuje viditelný obrázek o prázdných někde, by mohla směřovat na fotografii takového někde. Kouř nebo senzor požárů může v rámci IT nahlásit někoho jiného. V obou případech může být zařízení technicky plně důvěryhodné k systému, ale zaznamenává i manipulace s informacemi.

**Manipulace**: útočník může využít extrahovaný materiál klíče k zachycení a potlačení dat ze zařízení na komunikační cestě a jeho nahrazení daty, která jsou ověřena pomocí odcizeného materiálu klíče.

**Manipulace**: útočník může částečně nebo zcela nahradit software, který je v zařízení spuštěný, a potenciálně umožnit nahrazenému softwaru využívat originální identitu zařízení, pokud je materiál klíče nebo kryptografické zařízení, které drží klíčové materiály, k dispozici nedovolenému programu.

**Zpřístupnění informací**: Pokud zařízení používá manipulováný software, může takový software potenciálně způsobit nevracení dat na neoprávněné strany.

**Zpřístupnění informací**: útočník může využít extrahovaný klíčový materiál pro vložení do komunikačních cest mezi zařízením a řadičem nebo bránou pole nebo cloudovou bránou k odsávání informací.

**Odepření služby**: zařízení je možné vypnout nebo zapnout v režimu, ve kterém není komunikace možná (což je úmyslné v mnoha průmyslových strojích).

**Manipulace**: zařízení se dá překonfigurovat tak, aby fungovalo ve stavu, který není známý pro kontrolní systém (mimo známé parametry kalibrace), a tak poskytuje data, která je možné interpretovat.

**Zvýšení oprávnění**: zařízení, které má konkrétní funkci, může být nuceně dělat něco jiného. Například ventil, který je naprogramován na otevřený poloviční způsob, může být obtížné otevřít, aby se otevřel celý způsob.

**Odepření služby**: zařízení se může přepínat do stavu, ve kterém není komunikace možná.

**Manipulace**: zařízení je možné překonfigurovat tak, aby fungovalo ve stavu, který není známý pro kontrolní systém (mimo známé parametry kalibrace), a poskytuje tak data, která je možné interpretovat.

**Falšování/zneužití/odmítnutí**: není-li zabezpečeno (což se u spotřebitele používá jenom zřídka), může útočník manipulovat se stavem zařízení anonymně. Dobrá ilustrace je vzdálené ovládací prvky, které umožňují změnit libovolné televizní pořady a oblíbené nástroje Prankster.

#### <a name="communication"></a>Komunikace

Hrozby kolem komunikačních cest mezi zařízeními, zařízeními a branami polí a zařízeními a cloudovou bránou. Následující tabulka obsahuje pokyny k otevřeným soketům na zařízení/síti VPN:

| **Komponenta** | **Hrozba** | **Omezení rizik** | **Riziko** | **Implementace** |
| --- | --- | --- | --- | --- |
| IoT Hub zařízení |IDENTIFIKÁTORY |Trojrozměrné TLS (PSK/RSA) pro šifrování provozu |Odposlouchávání a narušování komunikace mezi zařízením a bránou |Zabezpečení na úrovni protokolu. S vlastními protokoly potřebujete zjistit, jak je chránit. Ve většině případů je komunikace provedena ze zařízení do IoT Hub (zařízení iniciuje připojení). |
| Zařízení do zařízení |IDENTIFIKÁTORY |Trojrozměrné TLS (PSK/RSA) pro šifrování provozu. |Čtení dat při přenosu mezi zařízeními. Manipulace s daty. Přetížení zařízení s novými připojeními |Zabezpečení na úrovni protokolu (MQTT/AMQP/HTTP/CoAP. S vlastními protokoly potřebujete zjistit, jak je chránit. Zmírnění hrozby DoS je rovnocenným zařízením přes bránu cloudu nebo poli a má je fungovat jenom jako klienti směrem k síti. Partnerský vztah může mít za následek přímé připojení mezi partnerskými uzly poté, co je brána zprostředkovaná bránou. |
| Zařízení externí entity |IDENTIFIKÁTORY |Silné párování externí entity se zařízením |Odposlouchávání připojení k zařízení. Narušování komunikace se zařízením |Bezpečně spáruje externí entitu se zařízením NFC/Bluetooth LE. Řízení provozního panelu zařízení (fyzický) |
| Cloudová brána pro pole brány |IDENTIFIKÁTORY |TLS (PSK/RSA) pro šifrování provozu. |Odposlouchávání a narušování komunikace mezi zařízením a bránou |Zabezpečení na úrovni protokolu (MQTT/AMQP/HTTP/CoAP). S vlastními protokoly potřebujete zjistit, jak je chránit. |
| Cloudová brána zařízení |IDENTIFIKÁTORY |TLS (PSK/RSA) pro šifrování provozu. |Odposlouchávání a narušování komunikace mezi zařízením a bránou |Zabezpečení na úrovni protokolu (MQTT/AMQP/HTTP/CoAP). S vlastními protokoly potřebujete zjistit, jak je chránit. |

Tady jsou některé příklady hrozeb v této kategorii:

**Odepření služby**: omezená zařízení se obecně nacházejí v rámci hrozby DOS, když aktivně naslouchají příchozím připojením nebo nevyžádaným datagramům v síti, protože útočník může otevřít mnoho propojení paralelně a neobsluhovat je, nebo může být zahlcena pomocí nevyžádaných přenosů. V obou případech se zařízení může efektivně vykreslovat v síti.

**Falšování identity, zpřístupnění informací**: omezená zařízení a zařízení pro zvláštní účely mají často jedno pro všechna bezpečnostní zařízení, jako je třeba ochrana heslem nebo PIN kódu, nebo zcela spoléhají na důvěřování síti, což znamená, že udělí přístup k informacím, když je zařízení ve stejné síti a že síť je často chráněná jenom sdíleným klíčem. To znamená, že když je sdílený tajný klíč k zařízení nebo síti zavřený, je možné řídit zařízení nebo sledovat data, která jsou vygenerována ze zařízení.  

**Falšování identity**: útočník může zachytit nebo částečně potlačit všesměrové vysílání a falešný původce (muž uprostřed).

**Manipulace**: útočník může zachytit nebo částečně přepsat všesměrové vysílání a odeslat falešné informace. 

**Zpřístupnění informací:** útočník se může eavesdrop při vysílání a získat informace bez **odepření autorizace služby:** útočník může zaútočit na signál všesměrového vysílání a zamítnout distribuci informací.

#### <a name="storage"></a>Storage

Každé zařízení a brána pole mají určitou formu úložiště (dočasné pro řazení dat do fronty, úložiště imagí operačního systému (OS)).

| **Komponenta** | **Hrozba** | **Omezení rizik** | **Riziko** | **Implementace** |
| --- | --- | --- | --- | --- |
| Úložiště zařízení |TRID |Šifrování úložiště, podepisování protokolů |Čtení dat z úložiště (dat PII), manipulace s daty telemetrie. Manipulace s daty řízení příkazů ve frontě nebo v mezipaměti Manipulace s balíčky aktualizací konfigurace nebo firmware při ukládání do mezipaměti nebo v místním prostředí mohou vést k ohrožení zabezpečení operačního systému nebo systémových komponent. |Šifrování, ověřovací kód zprávy (MAC) nebo digitální podpis. Pokud je to možné, silné řízení přístupu prostřednictvím seznamů řízení přístupu (ACL) prostředků nebo oprávnění. |
| Bitová kopie operačního systému zařízení |TRID | |Manipulace s operačním systémem/Replacing součástem operačního systému |Oddíl s operačním systémem jen pro čtení, podepsaná bitová kopie operačního systému, šifrování |
| Úložiště brány pole (řazení dat do fronty) |TRID |Šifrování úložiště, podepisování protokolů |Čtení dat ze služby Storage (data PII), manipulace s daty telemetrie, manipulace s daty řízení příkazů ve frontě nebo v mezipaměti Manipulace s balíčky aktualizací konfigurace nebo firmwaru (určené pro zařízení nebo pro bránu pole) při ukládání do mezipaměti nebo místně zařazené do fronty mohou způsobit ohrožení bezpečnosti operačního systému nebo systémových komponent. |BitLocker |
| Obrázek operačního systému pro pole brány |TRID | |Manipulace s operačním systémem/Replacing součástem operačního systému |Oddíl s operačním systémem jen pro čtení, podepsaná bitová kopie operačního systému, šifrování |

### <a name="device-and-event-processingcloud-gateway-zone"></a>Zařízení a zpracování událostí/zóna cloudové brány

Cloudová brána je systém, který umožňuje vzdálenou komunikaci z a do zařízení nebo bran polí z několika různých lokalit ve veřejném síťovém prostoru, a to obvykle do cloudového ovládacího prvku a analytického systému dat, což je federace takových systémů. V některých případech může cloudová brána okamžitě usnadnit přístup k zařízením se speciálním účelem z terminálů, jako jsou tablety nebo telefony. V kontextu, který je zde popsán, se jedná o pojem "Cloud", který odkazuje na vyhrazený systém zpracování dat, který není vázán na stejnou lokalitu jako připojená zařízení nebo brány polí, a kde provozní míry brání cílenému fyzickému přístupu, ale nemusí nutně odpovídat infrastruktuře "Public Cloud". Cloudová brána může být namapována na překrytí virtualizace sítě, která umožňuje izolaci cloudové brány a všech připojených zařízení nebo bran polí z jakéhokoli jiného síťového provozu. Samotná cloudová brána není systémem pro řízení zařízení nebo se zařízením pro zpracování nebo úložiště pro data zařízení. rozhraní těchto zařízení s cloudovou bránou. Zóna cloudové brány zahrnuje samotnou cloudovou bránu spolu se všemi branami a zařízeními, která jsou k ní přímo nebo nepřímo připojená.

Cloud Gateway je většinou vlastní sestavený software, který je spuštěný jako služba s vystavenými koncovými body, ke kterým se připojí brána pole a zařízení. V takovém případě musí být navržený s ohledem na zabezpečení. Pro návrh a sestavování této služby Sledujte proces [SDL](https://www.microsoft.com/sdl) .

#### <a name="services-zone"></a>Zóna služeb

Řídicí systém (nebo kontroler) je softwarové řešení, které se používá pro zařízení nebo bránu pole nebo cloudovou bránu pro účely řízení jednoho nebo více zařízení a/nebo k shromažďování a ukládání dat zařízení pro účely prezentace nebo následného řízení. Systémy řízení jsou jediné entity v rozsahu této diskuze, které mohou okamžitě usnadnit interakci s lidmi. Výjimky jsou mezilehlé fyzické ovládací prvky na zařízeních, jako je přepínač, který uživateli umožňuje zařízení vypnout nebo změnit jiné vlastnosti a pro které neexistuje funkční ekvivalent, ke kterému se dá dostat na digitálně funkční.

Mezi mezilehlé fyzické povrchy řízení patří takové, kde řízení logiky omezuje funkci fyzického ovládacího prvku, aby se mohla spustit ekvivalentní funkce, nebo když je vstup v konfliktu se vzdáleným vstupem. je možné, že tyto zprostředkující povrchy řízení jsou koncepčně připojené k místnímu řídicímu systému, který využívá stejné základní funkce jako kterýkoli jiný systém vzdáleného řízení, ke kterému se může zařízení připojit paralelně. Hlavní hrozby cloud computingu se dají přečíst na stránce [Cloud Security Alliance (CSA)](https://cloudsecurityalliance.org/articles/csa-releases-top-threats-to-cloud-computing-deep-dive/) .

## <a name="additional-resources"></a>Další zdroje informací

Další informace najdete v následujících článcích:

* [Threat Modeling Tool SDL](https://www.microsoft.com/sdl/adopt/threatmodeling.aspx)
* [Referenční architektura IoT Microsoft Azure](https://azure.microsoft.com/updates/microsoft-azure-iot-reference-architecture-available/)