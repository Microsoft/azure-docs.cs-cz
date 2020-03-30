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
ms.openlocfilehash: a2eafd6bb34b897f3492ddcffd6841f0fabc4ca7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73034542"
---
Při navrhování systému je důležité pochopit potenciální hrozby pro tento systém a odpovídajícím způsobem přidat vhodnou obranu, protože systém je navržen a navržen. Je důležité navrhnout produkt od začátku s ohledem na zabezpečení, protože pochopení toho, jak by útočník mohl ohrozit systém, pomáhá zajistit, aby byly od začátku zavedeny vhodné skutečnosti snižující závažnost rizika.

## <a name="security-starts-with-a-threat-model"></a>Zabezpečení začíná modelem hrozeb

Společnost Microsoft již dlouho používá modely hrozeb pro své produkty a veřejně zpřístupnila proces modelování hrozeb společnosti. Zkušenosti společnosti ukazují, že modelování má nečekané výhody nad rámec okamžitého pochopení toho, jaké hrozby jsou nejvíce znepokojující. Například také vytváří cestu pro otevřenou diskusi s ostatními mimo vývojový tým, což může vést k novým nápadům a vylepšením produktu.

Cílem modelování hrozeb je pochopit, jak útočník může být schopen ohrozit systém a ujistěte se, že jsou na místě vhodná opatření ke zmírnění rizik. Modelování hrozeb nutí návrhářský tým zvážit skutečnosti snižující závažnost rizika, protože systém je navržen, nikoli po nasazení systému. Tato skutečnost je kriticky důležitá, protože dovybavení bezpečnostní obrany na nesčetné množství zařízení v terénu je neproveditelné, náchylné k chybám a ohrožuje zákazníky.

Mnoho vývojových týmů odvádějí vynikající práci při zachycení funkčních požadavků pro systém, které jsou přínosem pro zákazníky. Identifikace nezřejmých způsobů, jak by někdo mohl systém zneužít, je však náročnější. Modelování hrozeb může pomoci vývojovým týmům pochopit, co by útočník mohl udělat a proč. Modelování hrozeb je strukturovaný proces, který vytváří diskusi o rozhodnutích návrhu zabezpečení v systému, jakož i o změnách návrhu, které jsou prováděny na cestě, která má vliv na zabezpečení. Zatímco model hrozeb je prostě dokument, tato dokumentace také představuje ideální způsob, jak zajistit kontinuitu znalostí, uchovávání získaných zkušeností a pomoci novému týmu na palubě rychle. A konečně, výsledkem modelování hrozeb je umožnit vám zvážit další aspekty zabezpečení, například jaké závazky zabezpečení chcete poskytnout svým zákazníkům. Tyto závazky ve spojení s modelováním hrozeb informují a řídí testování vašeho řešení Internetu věcí (IoT).

### <a name="when-to-do-threat-modeling"></a>Kdy udělat modelování hrozeb

[Modelování hrozeb](https://www.microsoft.com/en-us/sdl/adopt/threatmodeling.aspx) nabízí největší hodnotu, když jej začleníte do fáze návrhu. Při navrhování máte největší flexibilitu při provádění změn k odstranění hrozeb. Odstranění hrozeb podle návrhu je požadovaný výsledek. Je to mnohem jednodušší než přidávání zmírnění, testování je, a zajistit, aby zůstaly aktuální a navíc, takové odstranění není vždy možné. Je stále těžší eliminovat hrozby, protože produkt se stává zralejší, a na oplátku nakonec vyžaduje více práce a mnohem těžší kompromisy než modelování hrozeb brzy ve vývoji.

### <a name="what-to-consider-for-threat-modeling"></a>Co je třeba zvážit pro modelování hrozeb

Měli byste se podívat na řešení jako celek a také se zaměřit na následující oblasti:

* Funkce zabezpečení a ochrany osobních údajů
* Funkce, jejichž selhání jsou důležité pro zabezpečení
* Funkce, které se dotýkají hranice důvěryhodnosti

### <a name="who-performs-threat-modeling"></a>Kdo provádí modelování hrozeb

Modelování hrozeb je proces jako každý jiný. Je vhodné zacházet s dokumentem modelu hrozeb jako s jakoukoli jinou součástí řešení a ověřit jej. Mnoho vývojových týmů odvádějí vynikající práci při zachycení funkčních požadavků pro systém, které jsou přínosem pro zákazníky. Identifikace nezřejmých způsobů, jak by někdo mohl systém zneužít, je však náročnější. Modelování hrozeb může pomoci vývojovým týmům pochopit, co by útočník mohl udělat a proč.

### <a name="how-to-perform-threat-modeling"></a>Jak provádět modelování hrozeb

Proces modelování hrozeb se skládá ze čtyř kroků; tyto kroky jsou:

* Modelujte aplikaci
* Výčet hrozeb
* Zmírnění hrozeb
* Ověřit skutečnosti snižující závažnost rizika

#### <a name="the-process-steps"></a>Kroky procesu

Při vytváření modelu hrozeb je třeba mít na paměti tři pravidla:

1. Vytvořte diagram z referenční architektury.

2. Začněte šíři-první. Získejte přehled a pochopte systém jako celek před hlubokým potápěním. Tento přístup pomáhá zajistit, že se ponoříte do hloubky na správných místech.

3. Řídit proces, nenechte se řídit proces. Pokud najdete problém ve fázi modelování a chcete ho prozkoumat, jděte na to! Nemějte pocit, že je třeba sledovat tyto kroky otrocky.

#### <a name="threats"></a>Hrozby

Čtyři základní prvky modelu hrozeb jsou:

* Procesy, jako jsou webové služby, služby Win32 a *nix daemons. Některé složité entity (například brány v terénu a senzory) mohou být abstrahovány jako proces, pokud není možné technické přejít k podrobnostem v těchto oblastech.

* Úložiště dat (všude jsou uložena data, například konfigurační soubor nebo databáze)

* Tok dat (kde se data přesouvají mezi ostatními prvky v aplikaci)

* Externí entity (vše, co interaguje se systémem, ale není pod kontrolou aplikace, příklady zahrnují uživatele a satelitní kanály)

Všechny prvky v architektonickém diagramu jsou vystaveny různým hrozbám; tento článek stride mnemotechnické pomůcky. Přečtěte si [modelování hrozeb znovu, KROK](https://blogs.msdn.microsoft.com/larryosterman/2007/09/04/threat-modeling-again-stride/) se dozvíte více o prvky STRIDE.

Různé prvky diagramu aplikace podléhají určitým hrozbám STRIDE:

* Procesy podléhají STRIDE
* Toky dat podléhají TID
* Úložiště dat podléhají TID a někdy R, když úložiště dat jsou soubory protokolu.
* Externí subjekty podléhají SRD

## <a name="security-in-iot"></a>Zabezpečení v IoT

Připojená zařízení pro zvláštní účely mají značný počet potenciálních povrchových ploch a vzorců interakce, které musí být považovány za rámec pro zabezpečení digitálního přístupu k těmto zařízením. Termín "digitální přístup" se zde používá k odlišení od všech operací, které jsou prováděny prostřednictvím přímé interakce zařízení, kde je zabezpečení přístupu poskytováno prostřednictvím fyzického řízení přístupu. Například uvedení zařízení do místnosti se zámkem na dveřích. Zatímco fyzický přístup nelze odepřít pomocí softwaru a hardwaru, mohou být přijata opatření, která zabrání fyzickému přístupu vést k rušení systému.

Při zkoumání vzorců interakce se podívejte na "ovládání zařízení" a "data zařízení" se stejnou úrovní pozornosti. "Ovládání zařízení" může být klasifikováno jako jakákoli informace, která je poskytována zařízení jakoukoli stranou s cílem změnit nebo ovlivnit jeho chování vůči jeho stavu nebo stavu jeho prostředí. "Údaje o zařízení" lze klasifikovat jako jakékoli informace, které zařízení vydává jakékoli jiné straně o jeho stavu a pozorovaném stavu jeho prostředí.

Za účelem optimalizace osvědčených postupů zabezpečení se doporučuje, aby byla typická architektura IoT rozdělena do několika komponent nebo zón jako součást cvičení modelování hrozeb. Tyto zóny jsou podrobně popsány v této části a zahrnují:

* Zařízení
* Brána v terénu,
* Cloudové brány a
* Služby.

Zóny jsou široký způsob, jak segmentovat řešení; každá zóna má často své vlastní požadavky na data a ověřování a autorizaci. Zóny lze také použít k izolaci poškození a omezit dopad zón s nízkou důvěryhodností na zóny s vyšší důvěryhodností.

Každá zóna je oddělena hranicí vztahu důvěryhodnosti, která je v následujícím diagramu označena jako tečkovaná červená čára. Představuje přechod dat/informací z jednoho zdroje do druhého. Během tohoto přechodu mohou být data/informace předmětem falšování, falšování, odmítnutí, zveřejňování informací, odmítnutí služby a zvýšení oprávnění (STRIDE).

![Zóny zabezpečení IoT](media/iot-security-architecture/iot-security-architecture-fig1.png) 

Součásti zobrazené v rámci každé hranice jsou také podrobeny STRIDE, což umožňuje úplné 360 zobrazení modelování hrozeb řešení. Následující oddíly se týkají jednotlivých součástí a zvláštních bezpečnostních obav a řešení, která by měla být zavedena.

Následující části popisují standardní součásti, které se obvykle nacházejí v těchto zónách.

### <a name="the-device-zone"></a>Zóna zařízení

Prostředí zařízení je bezprostřední fyzický prostor kolem zařízení, kde je možný fyzický přístup a/nebo digitální přístup mezi dvěma účastníky. Předpokládá se, že "lokální síť" je síť, která je odlišná a izolovaná od veřejného internetu, ale potenciálně přepnutá, a zahrnuje jakoukoli bezdrátovou rádiovou technologii krátkého dosahu, která umožňuje komunikaci zařízení peer-to-peer. Nezahrnuje *not* žádnou technologii virtualizace sítě, která vytváří iluzi takové místní sítě, a také nezahrnuje sítě veřejných operátorů, které vyžadují komunikaci mezi veřejným i síťovým prostorem, pokud by vstoupily do komunikačního vztahu mezi dvěma účastníky.

### <a name="the-field-gateway-zone"></a>Zóna brány pole

Brána v terénu je zařízení/zařízení nebo nějaký univerzální serverový počítačový software, který funguje jako komunikační zařízení a potenciálně jako systém řízení zařízení a centrum pro zpracování dat zařízení. Zóna brány pole zahrnuje samotnou bránu pole a všechna zařízení, která jsou k ní připojena. Jak už název napovídá, brány v terénu působí mimo vyhrazená zařízení pro zpracování dat, jsou obvykle vázány na umístění, jsou potenciálně předmětem fyzického vniknutí a mají omezenou provozní redundanci. Vše říci, že pole brána je běžně věc, kterou lze dotknout a sabotáž, zatímco věděl, co je jeho funkce.

Brána pole se liší od pouhého směrovače přenosu v tom, že má aktivní roli při správě přístupu a toku informací, což znamená, že se jedná o entitu adresovskou aplikací a terminál připojení k síti nebo terminál relace. Zařízení nebo brána firewall nanasytých firewall se naopak nepovažuje za brány v terénu, protože se nejedná o explicitní terminály připojení nebo relace, ale spíše o připojení nebo relace pro které byly prováděny prostřednictvím trasy (nebo blokování). Brána pole má dvě odlišné plochy. Jeden čelí zařízení, která jsou k němu připojena a představuje vnitřek zóny a druhý čelí všechny vnější strany a je okraj zóny.

### <a name="the-cloud-gateway-zone"></a>Zóna cloudové brány

Cloudová brána je systém, který umožňuje vzdálenou komunikaci ze zařízení a do zařízení nebo polních bran z několika různých míst ve veřejném síťovém prostoru, obvykle směrem k cloudovému řídicímu systému a systému analýzy dat, což je federace takových systémů. V některých případech může cloudová brána okamžitě usnadnit přístup k zařízením pro speciální účely z terminálů, jako jsou tablety nebo telefony. V kontextu zde popsaném "cloud" je určen k označení vyhrazeného systému zpracování dat, který není vázán na stejnou síť jako připojená zařízení nebo brány v terénu. Provozní opatření také v cloudové zóně brání cílenému fyzickému přístupu a nemusí být nutně vystavena infrastruktuře "veřejného cloudu".  

Cloudová brána může být potenciálně mapována do překrytí virtualizace sítě, aby izolovala cloudovou bránu a všechna její připojená zařízení nebo brány v terénu od jakéhokoli jiného síťového provozu. Samotná cloudová brána není řídicí systém zařízení nebo zařízení pro zpracování nebo ukládání dat zařízení; rozhraní s cloudovou bránou. Zóna cloudových bran zahrnuje samotnou cloudovou bránu spolu se všemi bránami a zařízeními v terénu, které jsou k ní přímo nebo nepřímo připojeny. Okraj zóny je zřetelná plocha, přes kterou komunikují všechny externí strany.

### <a name="the-services-zone"></a>Zóna služeb

"Služba" je definována pro tento kontext jako jakákoli softwarová součást nebo modul, který se propojí se zařízeními prostřednictvím brány pole nebo cloudu pro sběr a analýzu dat, stejně jako pro příkaz y a řízení. Služby jsou mediátory. Jednají pod svou identitou vůči bránám a dalším subsystémům, ukládají a analyzují data, autonomně vydávají příkazy zařízením na základě přehledů dat nebo plánů a vystavují informace a kontrolní schopnosti oprávněným koncovým uživatelům.

### <a name="information-devices-versus-special-purpose-devices"></a>Informační zařízení versus zařízení pro zvláštní účely

Počítače, telefony a tablety jsou především interaktivní informační zařízení. Telefony a tablety jsou explicitně optimalizovány kolem maximalizace životnosti baterie. Pokud možno se částečně vypnou, když okamžitě nekomunikují s osobou nebo když neposkytují služby, jako je přehrávání hudby nebo vedení svého majitele na určité místo. Z hlediska systémů, tyto informační technologie zařízení působí především jako proxy vůči lidem. Jsou to "lidé aktuátory" naznačuje akce a "lidé senzory" shromažďování vstupů.

Speciální zařízení, od jednoduchých teplotních senzorů až po složité výrobní linky s tisíci komponenty uvnitř, jsou odlišná. Tato zařízení jsou mnohem více disponován v účelu, a to i v případě, že poskytují některé uživatelské rozhraní, jsou do značné míry s cílem propojit s nebo být integrovány do aktiv ve fyzickém světě. Měří a hlásí okolnosti životního prostředí, otáčejí ventily, řídí serva, zvukové alarmy, vypínají světla a dělají mnoho dalších úkolů. Pomáhají dělat práci, pro kterou informační zařízení je buď příliš obecný, příliš drahé, příliš velké, nebo příliš křehké. Konkrétní účel okamžitě určuje jejich technický design, stejně jako dostupný peněžní rozpočet pro jejich výrobu a plánovaný celoživotní provoz. Kombinace těchto dvou klíčových faktorů omezuje dostupný provozní rozpočet energie, fyzickou stopu a tím i dostupné možnosti ukládání, výpočetních prostředků a zabezpečení.

Pokud se něco "pokazí" s automatizovanými nebo dálkově ovladatelnými zařízeními, například fyzickými závadami nebo řízením logických vad k úmyslnému neoprávněnému vniknutí a manipulaci. Výrobní pozemky mohou být zničeny, budovy mohou být vypleněny nebo vypáleny a lidé mohou být zraněni nebo dokonce zemřít. Tohle je úplně jiná třída poškození, než když někdo vyčerpá v limitu ukradené kreditní karty. Panel zabezpečení pro zařízení, která věci přesouvají, a také pro data ze senzorů, která nakonec vyústí v příkazy, které způsobují pohyb věcí, musí být vyšší než v jakémkoli scénáři elektronického obchodování nebo bankovnictví.

### <a name="device-control-and-device-data-interactions"></a>Ovládání zařízení a interakce s daty zařízení

Připojená zařízení pro zvláštní účely mají značný počet potenciálních povrchových ploch a vzorců interakce, které musí být považovány za rámec pro zabezpečení digitálního přístupu k těmto zařízením. Termín "digitální přístup" se zde používá k odlišení od všech operací, které jsou prováděny prostřednictvím přímé interakce zařízení, kde je zabezpečení přístupu poskytováno prostřednictvím fyzického řízení přístupu. Například uvedení zařízení do místnosti se zámkem na dveřích. Zatímco fyzický přístup nelze odepřít pomocí softwaru a hardwaru, mohou být přijata opatření, která zabrání fyzickému přístupu vést k rušení systému.

Při zkoumání vzorců interakce se podívejte na "ovládání zařízení" a "data zařízení" se stejnou úrovní pozornosti při modelování hrozeb. "Ovládání zařízení" může být klasifikováno jako jakákoli informace, která je poskytována zařízení jakoukoli stranou s cílem změnit nebo ovlivnit jeho chování vůči jeho stavu nebo stavu jeho prostředí. "Údaje o zařízení" lze klasifikovat jako jakékoli informace, které zařízení vydává jakékoli jiné straně o jeho stavu a pozorovaném stavu jeho prostředí.

## <a name="performing-threat-modeling-for-the-azure-iot-reference-architecture"></a>Provádění modelování hrozeb pro referenční architekturu Azure IoT

Microsoft používá rámec popsaný dříve k modelování hrozeb pro Azure IoT. Následující část používá konkrétní příklad referenční architektury Azure IoT k předvedení, jak přemýšlet o modelování hrozeb pro IoT a jak řešit zjištěné hrozby. Tento příklad identifikuje čtyři hlavní oblasti zaměření:

* Zařízení a zdroje dat,
* Přenos dat,
* Zpracování zařízení a událostí a
* Zobrazení

![Modelování hrozeb pro Azure IoT](media/iot-security-architecture/iot-security-architecture-fig2.png)

Následující diagram poskytuje zjednodušené zobrazení architektury IoT společnosti Microsoft pomocí modelu diagramu toku dat, který používá nástroj Microsoft Threat Modeling Tool:

![Modelování hrozeb pro Azure IoT pomocí nástroje MS Threat Modeling Tool](media/iot-security-architecture/iot-security-architecture-fig3.png)

Je důležité si uvědomit, že architektura odděluje zařízení a brány schopnosti. Tento přístup umožňuje uživateli využívat zařízení brány, která jsou bezpečnější: jsou schopni komunikovat s cloudovou bránou pomocí zabezpečených protokolů, což obvykle vyžaduje větší režii zpracování, kterou by mohlo nativní zařízení – například termostat – poskytnout samostatně. V zóně služeb Azure předpokládejme, že cloudová brána je reprezentována službou Azure IoT Hub.

### <a name="device-and-data-sourcesdata-transport"></a>Zdroje zařízení a dat/přenos dat

Tato část se zabývá architekturou nastíněnou dříve prostřednictvím objektivu modelování hrozeb a poskytuje přehled o tom, jak řešit některé inherentní problémy. Tento příklad se zaměřuje na základní prvky modelu hrozeb:

* Procesy (jak pod vaší kontrolou, tak i externí položky)
* Komunikace (také tzv. datové toky)
* Úložiště (nazývané také úložiště dat)

#### <a name="processes"></a>Procesy

V každé z kategorií popsaných v architektuře Azure IoT se tento příklad pokusí zmírnit řadu různých hrozeb v různých fázích, ve kterých data/informace existují: proces, komunikace a úložiště. Následuje přehled nejběžnějších pro kategorii "proces", následovaný přehledem toho, jak by tyto hrozby mohly být nejlépe zmírněny:

**Falšování identity (S):** Útočník může extrahovat materiál kryptografického klíče ze zařízení, a to buď na úrovni softwaru nebo hardwaru, a následně přistupovat k systému s jiným fyzickým nebo virtuálním zařízením pod identitou zařízení, ze které byl materiál klíče odebrán. Dobrou ilustrací jsou dálkové ovladače, které mohou otáčet jakoukoli televizi a které jsou populární mizérnástroje.

**Odmítnutí služby (D):** Zařízení může být vystavováno neschopným funkce nebo komunikace zasahováním do rádiových frekvencí nebo řezáním drátů. Například sledovací kamera, která měla své napájení nebo síťové připojení záměrně vyřazeno, nemůže vykazovat data vůbec.

**Neoprávněných manipulací (T)**: Útočník může částečně nebo zcela nahradit software spuštěný na zařízení, což potenciálně umožňuje vyměněnému softwaru využít skutečnou identitu zařízení, pokud byl k dispozici klíčový materiál nebo kryptografická zařízení, která jsou v držení klíčových materiálů pro nezákonný program. Útočník může například využít extrahovaný materiál klíče k zachycení a potlačení dat ze zařízení na komunikační cestě a nahradit je falešnými daty, která jsou ověřena ukradeným materiálem klíče.

**Zpřístupnění informací (I)**: Pokud je v zařízení spuštěn software s manipulovaným softwarem, mohl by takový zmanipulovaný software potenciálně unikat data neoprávněným stranám. Útočník může například využít extrahovaný materiál klíče k tomu, aby se sám vstříkl do komunikační cesty mezi zařízením a řadičem nebo bránou v poli nebo cloudovou bránou k odsávání informací.

**Zvýšení oprávnění (E):** Zařízení, které provádí určitou funkci, může být nuceno dělat něco jiného. Například ventil, který je naprogramován tak, aby se otevřel na půl cesty, může být oklamán, aby se otevřel celou cestu.

| **Komponenta** | **Hrozba** | **Omezení rizik** | **Riziko** | **Implementace** |
| --- | --- | --- | --- | --- |
| Zařízení |S |Přiřazení identity k zařízení a ověření zařízení |Výměna zařízení nebo jeho části za jiné zařízení. Jak víte, že mluvíte se správným zařízením? |Ověření zařízení pomocí zabezpečení transportní vrstvy (TLS) nebo protokolu IPSec. Infrastruktura by měla podporovat použití předsdíleného klíče (PSK) na zařízeních, která nezvládnou úplnou asymetrickou kryptografii. Využití Azure AD, [OAuth](https://www.rfc-editor.org/pdfrfc/rfc6755.txt.pdf) |
|| TRID |Aplikujte na zařízení mechanismy odolné proti neoprávněné manipulaci, například tím, že znemožníte extrahování klíčů a jiného kryptografického materiálu ze zařízení. |Riziko spoje v případě, že někdo manipuluje se zařízením (fyzické rušení). Jak jste si jistý, že s tím zařízením nebylo manipulováno. |Nejúčinnější zmírnění je funkce modulu důvěryhodné platformy (TPM), která umožňuje ukládání klíčů ve speciálních obvodech na čipu, ze kterých nelze číst klíče, ale lze je použít pouze pro kryptografické operace, které používají klíč, ale nikdy klíč nezveřejňují. Šifrování paměti zařízení. Správa klíčů pro zařízení. Podepisování kódu. |
|| E |S řízením přístupu k zařízení. Autorizační schéma. |Pokud zařízení umožňuje provádět jednotlivé akce na základě příkazů z vnějšího zdroje nebo dokonce narušených senzorů, umožňuje útoku provádět operace, které nejsou jinak přístupné. |S povolovacím schématem pro zařízení |
| Brána v terénu |S |Ověřování brány pole do cloudové brány (například na základě certifikátu, PSK nebo Deklarace.) |Pokud někdo může zfalšovat Field Gateway, může se prezentovat jako jakékoli zařízení. |TLS RSA/PSK, IPSEC, [RFC 4279](https://tools.ietf.org/html/rfc4279). Všechny stejné klíče skladování a atestace obavy zařízení obecně – nejlepší je použít TPM. 6Rozšíření LowPAN pro protokol IPSec pro podporu bezdrátových senzorových sítí (WSN). |
|| TRID |Ochrana brány pole před neoprávněným zásahem (TPM?) |Falšování útoků, které oklamou cloudovou bránu a myslí si, že mluví s bránou v terénu, by mohlo vést ke zpřístupnění informací a manipulaci s daty |Šifrování paměti, čip TPM, ověřování. |
|| E |Mechanismus řízení přístupu pro bránu v terénu | | |

Zde je několik příkladů hrozeb v této kategorii:

**Falšování identity**: Útočník může extrahovat materiál kryptografického klíče ze zařízení, a to buď na úrovni softwaru nebo hardwaru, a následně přistupovat k systému s jiným fyzickým nebo virtuálním zařízením pod identitou zařízení, ze které byl materiál klíče odebrán.

**Odmítnutí služby**: Zařízení může být vystavováno neschopným fungovat nebo komunikovat zasahováním do rádiových frekvencí nebo řezáním drátů. Například sledovací kamera, která měla své napájení nebo síťové připojení záměrně vyřazeno, nemůže vykazovat data vůbec.

**Manipulace**: Útočník může částečně nebo zcela nahradit software spuštěný v zařízení, což potenciálně umožňuje vyměněnému softwaru využít skutečnou identitu zařízení, pokud byl k dispozici materiál klíče nebo kryptografická zařízení, která mají klíčové materiály pro nezákonný program.

**Manipulace**: Bezpečnostní kamera, která ukazuje viditelný obraz prázdné chodby, by mohla být zaměřena na fotografii takové chodby. Kouřový nebo požární senzor může hlásit někoho, kdo pod ním drží zapalovač. V obou případech může být zařízení technicky plně důvěryhodné vůči systému, ale hlásí manipulované informace.

**Manipulace**: Útočník může využít extrahovaný materiál klíče k zachycení a potlačení dat ze zařízení na komunikační cestě a nahradit je falešnými daty, která jsou ověřena ukradeným materiálem klíče.

**Manipulace**: Útočník může částečně nebo úplně nahradit software spuštěný v zařízení, což potenciálně umožňuje vyměněnému softwaru využít skutečnou identitu zařízení, pokud byl k dispozici klíčový materiál nebo kryptografická zařízení, která mají klíčové materiály pro nezákonný program.

**Zpřístupnění informací**: Pokud je v zařízení spuštěn software s manipulovaným softwarem, mohl by takový manipulovaný software potenciálně unikat data neoprávněným stranám.

**Zpřístupnění informací**: Útočník může využít extrahovaný materiál klíče k tomu, aby se sám vstříkl do komunikační cesty mezi zařízením a řadičem nebo bránou v poli nebo cloudovou bránou k odsávání informací.

**Odmítnutí služby**: Zařízení lze vypnout nebo převést do režimu, kdy komunikace není možná (což je záměrné v mnoha průmyslových strojích).

**Manipulace**: Zařízení může být překonfigurováno tak, aby fungovalo ve stavu neznámém řídicímu systému (mimo známé kalibrační parametry) a poskytnout tak data, která mohou být nesprávně interpretována

**Zvýšení oprávnění**: Zařízení, které provádí určitou funkci, může být nuceno dělat něco jiného. Například ventil, který je naprogramován tak, aby se otevřel na půl cesty, může být oklamán, aby se otevřel celou cestu.

**Odmítnutí služby**: Zařízení může být přeměněno na stav, ve kterém komunikace není možná.

**Manipulace**: Zařízení může být překonfigurováno tak, aby fungovalo ve stavu neznámém řídicímu systému (mimo známé kalibrační parametry) a poskytovalo tak data, která mohou být nesprávně interpretována.

**Falšování/manipulace/odmítnutí**: Pokud není zabezpečena (což je zřídka případ spotřebitelských dálkových ovladačů), útočník může manipulovat se stavem zařízení anonymně. Dobrou ilustrací jsou dálkové ovladače, které mohou otáčet jakoukoli televizi a které jsou populární mizérnástroje.

#### <a name="communication"></a>Communication

Hrozby kolem komunikační cesty mezi zařízeními, zařízeními a branami v terénu a bránou zařízení a cloudu. Následující tabulka obsahuje některé pokyny týkající se otevřených soketů na zařízení/VPN:

| **Komponenta** | **Hrozba** | **Omezení rizik** | **Riziko** | **Implementace** |
| --- | --- | --- | --- | --- |
| Centrum IoT pro zařízení |Tid |(D) TLS (PSK/RSA) pro šifrování provozu |Odposlouchávání nebo rušení komunikace mezi zařízením a bránou |Bezpečnost na úrovni protokolu. S vlastními protokoly musíte zjistit, jak je chránit. Ve většině případů probíhá komunikace ze zařízení do služby IoT Hub (zařízení inicializuje připojení). |
| Zařízení k zařízení |Tid |(D) TLS (PSK/RSA) pro šifrování provozu. |Čtení dat při přenosu mezi zařízeními. Manipulování s daty. Přetížení zařízení novými připojeními |Zabezpečení na úrovni protokolu (MQTT/AMQP/HTTP/CoAP. S vlastními protokoly musíte zjistit, jak je chránit. Zmírnění hrozby DoS je peer zařízení prostřednictvím cloudu nebo brány pole a mít je pouze fungovat jako klienti směrem k síti. Partnerský vztah může vést k přímému spojení mezi partnery poté, co byl zprostředkován bránou |
| Zařízení externí entity |Tid |Silné párování externí entity se zařízením |Odposlouchávání připojení k zařízení. Zasahování do komunikace se zařízením |Bezpečné spárování externí entity se zařízením NFC/Bluetooth LE. Ovládání provozního panelu zařízení (Fyzikální) |
| Cloudová brána field gateway |Tid |TLS (PSK/RSA) pro šifrování provozu. |Odposlouchávání nebo rušení komunikace mezi zařízením a bránou |Zabezpečení na úrovni protokolu (MQTT/AMQP/HTTP/CoAP). S vlastními protokoly musíte zjistit, jak je chránit. |
| Cloudová brána zařízení |Tid |TLS (PSK/RSA) pro šifrování provozu. |Odposlouchávání nebo rušení komunikace mezi zařízením a bránou |Zabezpečení na úrovni protokolu (MQTT/AMQP/HTTP/CoAP). S vlastními protokoly musíte zjistit, jak je chránit. |

Zde je několik příkladů hrozeb v této kategorii:

**Odmítnutí služby**: Omezená zařízení jsou obecně pod hrozbou DoS, když aktivně poslouchají příchozí připojení nebo nevyžádané datagramy v síti, protože útočník může otevřít mnoho připojení paralelně a neobsluhovat je nebo je obsluhovat pomalu nebo zařízení může být zaplaveno nevyžádaným provozem. V obou případech může být zařízení efektivně v síti nefunkční.

**Falšování, zveřejňování informací**: Omezená zařízení a zařízení pro zvláštní účely mají často jedno-pro-všechny bezpečnostní zařízení, jako je ochrana heslem nebo PIN, nebo se plně spoléhají na důvěřování síti, což znamená, že udělují přístup k informacím, když je zařízení ve stejné síti a tato síť je často chráněna pouze sdíleným klíčem. To znamená, že při zveřejnění sdíleného tajného klíče k zařízení nebo síti je možné ovládat zařízení nebo sledovat data vysílaná ze zařízení.  

**Falšování :** útočník může zachytit nebo částečně přepsat vysílání a zfalšovat původce (muž uprostřed)

**Neoprávněných zásahů**: útočník může zachytit nebo částečně přepsat vysílání a odeslat nepravdivé informace 

**Zpřístupnění informací:** Útočník může odposlouchávat vysílání a získávat informace bez autorizace **odmítnutí služby:** útočník může rušit vysílací signál a odepřít distribuci informací.

#### <a name="storage"></a>Úložiště

Každé zařízení a brána pole má nějakou formu úložiště (dočasné pro řazení dat do fronty, úložiště bitových obrázků operačního systému (OS).

| **Komponenta** | **Hrozba** | **Omezení rizik** | **Riziko** | **Implementace** |
| --- | --- | --- | --- | --- |
| Úložiště zařízení |TRID |Šifrování úložiště, podepisování protokolů |Čtení dat z úložiště (DATA PII), manipulace s telemetrickými daty. Manipulace s daty řízení příkazů ve frontě nebo v mezipaměti. Manipulace s balíčky konfigurace nebo aktualizace firmwaru v mezipaměti nebo ve frontě místně může vést k ohrožení operačního systému a /nebo systémových součástí. |Šifrování, ověřovací kód zprávy (MAC) nebo digitální podpis. Pokud je to možné, silné řízení přístupu prostřednictvím seznamů řízení přístupu k prostředkům (ACLs) nebo oprávnění. |
| Obrázek operačního systému zařízení |TRID | |Manipulace s OS /výměna součástí operačního systému |Oddíl operačního systému jen pro čtení, podepsaný obrázek operačního systému, šifrování |
| Úložiště field gateway (zařazování dat do fronty) |TRID |Šifrování úložiště, podepisování protokolů |Čtení dat z úložiště (DATA PII), manipulace s telemetrickými daty, manipulace s daty řízení příkazů ve frontě nebo v mezipaměti. Manipulace s balíčky konfigurace nebo aktualizace firmwaru (určené pro zařízení nebo bránu pole) při lokálním ukládání do mezipaměti nebo ve frontě může vést k ohrožení operačního systému a/nebo systémových součástí. |BitLocker |
| Obrázek operačního systému Field Gateway |TRID | |Manipulace s OS /výměna součástí operačního systému |Oddíl operačního systému jen pro čtení, podepsaný obrázek operačního systému, šifrování |

### <a name="device-and-event-processingcloud-gateway-zone"></a>Zóna služby Zpracování zařízení a událostí/cloudové brány

Cloudová brána je systém, který umožňuje vzdálenou komunikaci ze zařízení a do zařízení nebo polních bran z několika různých míst ve veřejném síťovém prostoru, obvykle směrem k cloudovému řídicímu systému a systému analýzy dat, což je federace takových systémů. V některých případech může cloudová brána okamžitě usnadnit přístup k zařízením pro speciální účely z terminálů, jako jsou tablety nebo telefony. V kontextu zde popsaném je "cloud" určen k označení vyhrazeného systému zpracování dat, který není vázán na stejnou lokalitu jako připojená zařízení nebo brány v terénu a kde provozní opatření brání cílenému fyzickému přístupu, ale nemusí být nutně " infrastruktury veřejného cloudu". Cloudová brána může být potenciálně mapována do překrytí virtualizace sítě, aby izolovala cloudovou bránu a všechna její připojená zařízení nebo brány v terénu od jakéhokoli jiného síťového provozu. Samotná cloudová brána není řídicí systém zařízení nebo zařízení pro zpracování nebo ukládání dat zařízení; rozhraní s cloudovou bránou. Zóna cloudových bran zahrnuje samotnou cloudovou bránu spolu se všemi bránami a zařízeními v terénu, které jsou k ní přímo nebo nepřímo připojeny.

Cloudová brána je většinou vlastní software běžící jako služba s odhalenými koncovými body, ke kterým se brána pole a zařízení připojují. Jako takový musí být navržen s ohledem na bezpečnost. Postupujte podle procesu [SDL](https://www.microsoft.com/sdl) pro navrhování a vytváření této služby.

#### <a name="services-zone"></a>Zóna služeb

Řídicí systém (nebo řadič) je softwarové řešení, které je propojeno se zařízením, bránou v terénu nebo cloudovou bránou za účelem řízení jednoho nebo více zařízení a/nebo shromažďování a/nebo ukládání a/nebo analýzy dat zařízení pro účely prezentace nebo následného řízení. Kontrolní systémy jsou jedinými subjekty v rámci této diskuse, které mohou okamžitě usnadnit interakci s lidmi. Výjimky jsou mezilehlé fyzické ovládací plochy na zařízeních, jako je přepínač, který umožňuje osobě vypnout zařízení nebo změnit jiné vlastnosti a pro které neexistuje žádný funkční ekvivalent, který lze přistupovat digitálně.

Mezilehlé fyzické ovládací plochy jsou ty, kde řídící logika omezuje funkci povrchu fyzického řízení tak, že ekvivalentní funkce může být spuštěna na dálku nebo lze vyhnout konfliktům vstupů se vzdáleným vstupem – takové zprostředkované ovládací plochy jsou koncepčně připojeny k místnímu řídicímu systému, který využívá stejné základní funkce jako jakýkoli jiný systém dálkového ovládání, ke kterému může být zařízení připojeno paralelně. Hlavní hrozby pro cloud computing si můžete přečíst na stránce [Cloud Security Alliance (CSA).](https://cloudsecurityalliance.org/articles/csa-releases-top-threats-to-cloud-computing-deep-dive/)

## <a name="additional-resources"></a>Další zdroje

Další informace najdete v těchto článcích:

* [Nástroj pro modelování hrozeb SDL](https://www.microsoft.com/sdl/adopt/threatmodeling.aspx)
* [Referenční architektura Microsoft Azure IoT](https://azure.microsoft.com/updates/microsoft-azure-iot-reference-architecture-available/)
