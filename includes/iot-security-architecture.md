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
ms.openlocfilehash: f2c7111373ac880d27298deb4fc919d797713f3e
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/02/2019
ms.locfileid: "53995670"
---
# <a name="internet-of-things-iot-security-architecture"></a>Architektura zabezpečení Internetu věcí (IoT)

Při návrhu systému, je důležité porozumět potenciálních hrozeb na daném systému a přidejte odpovídající ochrany podle toho, jak je systém navržený a navržen. Je důležité při návrhu produktů od začátku s ohledem na bezpečnost, protože princip, jak útočník může mohl ohrozit systém pomáhá, ujistěte se, že odpovídající způsoby zmírnění rizik jsou na místě od začátku.

## <a name="security-starts-with-a-threat-model"></a>Zabezpečení se spustí model hrozeb

Microsoft dlouho použil modely hrozeb pro jeho produktů a bylo provedeno proces veřejně k dispozici modelování hrozeb vaší společnosti. Prostředí společnosti ukazuje, že má modelování neočekávané výhody nad rámec okamžitý přehled o jaké hrozby jsou nejvíce týkající se. Například vytvoří také zacházíme otevřít informace s jinými uživateli mimo vývojový tým, což může vést k nové nápady a vylepšení v produktu.

Cílem modelování hrozeb je zjistit, jak útočník může ohrozit zabezpečení systému a ujistěte se, že odpovídající zmírnění rizik jsou na místě. Nasazuje se vynutí modelování hrozeb tým vzít v úvahu způsoby zmírnění rizik, protože systém je navržený tak, spíše než po systému. Tato skutečnost má zásadní význam, protože zpětné přidání obrany zabezpečení do velkého počtu zařízení v terénu je nemožné, náchylné k chybám a nechá zákazníkům rizika.

Mnoho vývojových týmů provést úlohu vynikající zachytávání funkční požadavky pro systému, které přináší výhody zákazníkům. Identifikace není zřejmé, že někdo může zneužít v systému způsobů je však ještě náročnější. Modelování hrozeb může pomoct pochopit, co může útočník udělat vývojových týmů a proč. Modelování ohrožení je strukturovaných proces, který vytvoří diskuze o zabezpečení rozhodnutí o návrhu v systému, stejně jako se změní na návrh, který jsou na cestě k zabezpečení dopad. Zatímco model hrozeb je jednoduše dokumentu, tato dokumentace také představuje ideální způsob, jak k zajištění kontinuity podnikových procesů znalostí, uchovávání skupině lekcí se naučili, a pomoc nového týmu připojení rychle. Nakonec jako výsledek modelování hrozeb je umožnit vzít v úvahu další aspekty zabezpečení, jako je například jaké jejích závazků chcete poskytnout zákazníkům. Tyto závazky ve spojení s modelování hrozeb informovat a jednotky testování vašeho řešení Internetu věcí (IoT).

### <a name="when-to-do-threat-modeling"></a>Když pro modelování hrozeb

[Modelování ohrožení](https://www.microsoft.com/en-us/sdl/adopt/threatmodeling.aspx) nabízí největší hodnoty po začlenění do fáze návrhu. Při návrhu, máte nejvyšší flexibilitu provést změny eliminovat hrozby. Odstranění ohrožení záměrné je požadovaného výsledku. Je mnohem jednodušší než přidání způsoby zmírnění rizik, otestování a zajištění zůstává zachována možnost jejich aktuální a kromě toho toto odstranění není vždy možné. Bude obtížnější eliminovat hrozby podle produktu stane vyspělejší a pak v konečném důsledku vyžaduje více práce a kompromisy mnohem obtížnější než hrozeb modelování raném stádiu při vývoji.

### <a name="what-to-consider-for-threat-modeling"></a>Co je potřeba zvážit pro modelování hrozeb

Byste se podívat na řešení jako celek a také zaměřit se na následující oblasti:

* Funkce zabezpečení a ochrany osobních údajů
* Funkce, jejíž chyby jsou důležité zabezpečení
* Funkce, které touch hranice vztahů důvěryhodnosti

### <a name="who-performs-threat-modeling"></a>Kdo provádí modelování hrozeb

Modelování ohrožení je proces stejně jako jakýkoli jiný. Je vhodné pro zpracování dokumentu model hrozeb jako součást řešení a ověřte ho. Mnoho vývojových týmů provést úlohu vynikající zachytávání funkční požadavky pro systému, které přináší výhody zákazníkům. Identifikace není zřejmé, že někdo může zneužít v systému způsobů je však ještě náročnější. Modelování hrozeb může pomoct pochopit, co může útočník udělat vývojových týmů a proč.

### <a name="how-to-perform-threat-modeling"></a>Postup pro modelování hrozeb

Hrozby modelování procesů se skládá ze čtyř kroků, Tyto kroky jsou:

* Modelování aplikace
* Zobrazení výčtu hrozby
* Zmírnění hrozeb
* Ověření zmírnění

#### <a name="the-process-steps"></a>Kroky procesu

Tři hrubé odhady potřeba mít na paměti při sestavování model hrozeb:

1. Vytvoření diagramu z referenční architektury.

2. Spuštění první šířka. Získání přehledu a seznamte se s systém jako celek, než se podíváme hluboko. Tento přístup pomáhá zajistit, že vám podrobně na správných místech.

3. Jednotka procesu, nenechte procesu je jednotka. Pokud najdete chybu ve fázi modelování a chcete prozkoumat, přejděte k němu! Nebojte se, co potřebujete k slavishly postupujte podle těchto kroků.

#### <a name="threats"></a>Hrozby

Čtyři základní prvky modelu před internetovými útoky jsou:

* Procesy, jako jsou webové služby, služby Win32 a * nix procesy démon. Některé složité entity (například bran v terénu a senzory) můžete abstrahovaná jako proces, při technické procházení podrobností v těchto oblastech není možné.

* Úložiště dat (všude, kde jsou data uložena, například konfigurační soubor nebo databáze)

* Tok dat (kde data přesouvána mezi další prvky v aplikaci)

* Externí entity (NIC, která komunikuje s systému, ale není pod kontrolou aplikace, příklady patří uživatelům a satelitní informačních kanálů)

Všechny prvky v diagram architektury jsou v souladu s různé hrozby; Tento článek STRIDE pomůcek. Čtení [hrozeb modelování znovu, STRIDE](https://blogs.msdn.microsoft.com/larryosterman/2007/09/04/threat-modeling-again-stride/) pro další informace o prvcích STRIDE.

Různé prvky diagramu aplikace jsou v souladu s určité hrozby ROZTEČ:

* Procesy jsou v souladu s STRIDE
* Datové toky se můžou TID.
* Úložiště dat se vztahují TID a někdy R, když úložištích dat jsou soubory protokolu.
* Externí entity se vztahují SRD

## <a name="security-in-iot"></a>Zabezpečení ve službě IoT

Připojené zařízeními pro zvláštní účely mají velký počet potenciálních oblastí povrchu interakce a vzory interakcí, které musí přestat považovat poskytuje architekturu pro zabezpečení digitální přístup k těmto zařízením. Termín "přístup k digitální" se zde používá k odlišení od žádné operace, které se provádějí prostřednictvím interakce s přímým přístupem zařízení kde poskytuje zabezpečení přístupu prostřednictvím řízení přístupu na fyzické. Například přechodu zařízení do místnosti se zámkem na dveře. Když fyzický přístup nemůže odepřeno pomocí softwaru a hardwaru, můžete se opatření zabránit fyzickému přístupu k systému před rušením od předních.

Když se budete učit vzory interakcí, podívejte se na "zařízení" a řízení "zařízení" se stejnou úrovní pozornost. "Řídící" dají považovat za veškeré informace, které poskytuje k zařízení stranu s cílem změnu nebo ovlivňující chování na jeho stavu nebo stavu prostředí. "Data zařízení" dají považovat za veškeré informace, které vysílá zařízení na druhou stranu o jejím stavu a zjištěnou stav svého prostředí.

K optimalizaci osvědčené postupy zabezpečení, je doporučeno, typickou architekturu IoT je rozdělen na několik součástí/zóny jako součást hrozeb modelování cvičení. Tyto zóny jsou plně popsány v této části a zahrnují:

* Zařízení,
* Brána pole
* Cloudové brány, a
* Služby.

Zóny jsou široké až po segmentu řešení; každou zónu často má vlastní požadavky na data a ověřování a autorizace. Zóny můžete také použít k izolaci poškození a omezit dopad nízké vztahu důvěryhodnosti zóny na vyšší vztah důvěryhodnosti zóny.

Každá zóna je oddělené hranicí vztahu důvěryhodnosti, které je uvedené jako tečkovaná čára red v následujícím diagramu. Představuje přechod z data a informace z jednoho zdroje do druhého. Během tohoto přechodu je data nebo informace může být v souladu s falšování, manipulace, popírání odpovědnosti, zpřístupnění informací, dostupnost služby a zvýšení úrovně oprávnění (krok).

![Zóny zabezpečení IoT](media/iot-security-architecture/iot-security-architecture-fig1.png) 

Součásti použité v rámci každé hranice také podléhají STRIDE povolení úplné 360 hrozeb modelování tohoto řešení. V následujících částech se pracovalo na všech komponent a konkrétní bezpečnostní otázky a řešení, která mají být vloženy do místa.

Následující části popisují standardní komponenty, které se většinou nacházejí v těchto zónách.

### <a name="the-device-zone"></a>Pásmo zařízení

Prostředí zařízení je okamžitě fyzické místo kolem zařízení, kde fyzický přístup a/nebo "místní sítě" peer-to-peer Digitální přístup k zařízení je to možné. Síť, která je izolovaný od – ale potenciálně přidat do mostu do veřejného Internetu a obsahuje jakékoli krátkého dosahu bezdrátové přepínačů technologie, která umožňuje komunikaci peer-to-peer zařízení je považován za "Místní sítě". Provádí *není* zahrnout všechny technologie virtualizace sítě vytváří iluzi místní sítě a také neobsahuje veřejný operátor sítě, které vyžadují žádné dvě zařízení komunikovat přes veřejnou síť místa, pokud kdyby byly zadejte vztah peer-to-peer komunikace.

### <a name="the-field-gateway-zone"></a>Brána zónu pole

Hraniční brána je zařízení nebo zařízení nebo software počítače server pro obecné účely, který funguje jako komunikační Aktivátor a i jako systém správy zařízení a zařízení zpracování dat centra. Oblast brány pole zahrnuje samotná brána pole a všechna zařízení, která jsou k němu připojená. Jak již název napovídá, bran v terénu fungovat zařízení mimo vyhrazené zpracování dat, jsou obvykle umístění vázán, potenciálně podléhají fyzické neoprávněných vniknutí a má omezenou provozní redundance. Všechny říct, že brána pole se často to jeden dotykového ovládání a sabotáž zároveň budete vědět, co je jeho funkci.

Brána pole se liší od směrovačem i provoz měl aktivní roli při správě přístupu a informace o toku, což znamená, že je aplikace určena entity a síťové připojení nebo relaci Terminálové. Zařízení NAT nebo brány firewall, naproti tomu nesplňuje jako bran v terénu vzhledem k tomu, že se nejedná o explicitní spojení nebo relace terminály, ale spíše trasu (nebo bloku) připojení nebo prostřednictvím jejich relací. Brána pole má dva různé plochy. Jeden obličeje zařízení, která jsou k němu připojená a představuje vnitřní zóny a druhý čelí všechny externí strany a okraje zóny.

### <a name="the-cloud-gateway-zone"></a>Zóna brány cloudu

Cloudová brána je systém, který umožňuje vzdálenou komunikaci od a do zařízení nebo bran v terénu z několika různých lokalit ve veřejné síti místa, obvykle na ovládací prvek založené na cloudu a systém pro analýzy dat, federace těchto systémů. V některých případech cloudové brány může okamžitě usnadnění přístupu k zařízeními pro zvláštní účely z terminály, jako jsou tablety a telefony. V kontextu popsaném "cloud" je určená k odkazování na vyhrazené zpracování dat systému, který není vázán na stejném místě jako připojené zařízení nebo bran v terénu. V oblasti cloudu, také provozní opatření zabránit fyzickému přístupu cílené a nejsou přístupné nutně infrastruktuře "veřejný cloud".  

Cloudová brána lze mapovat potenciálně do překrytí virtualizace sítě izolovat Cloudová brána a všechny jeho připojené zařízení nebo bran v terénu od ostatního síťového přenosu. Cloudová brána samotný není systém správy zařízení nebo zpracování nebo obsluhující zařízení úložiště dat v zařízení; Tato zařízení rozhraní s cloudové bráně. Oblast brány cloud zahrnuje brány cloud společně s všech bran v terénu a zařízení k němu připojená přímo nebo nepřímo. Okraje zóny je odlišné styčné plochy, kde všechny externí strany komunikovat prostřednictvím.

### <a name="the-services-zone"></a>Zóna služby

"Služba" je definována pro tento kontext jako všechny součásti softwaru nebo modul, který je propojení se zařízeními přes bránu pole nebo cloud pro sběr dat a analýzu, stejně jako pro příkazy a ovládání. Zprostředkovatelé jsou služby. Jejich fungují v rámci brány a ostatních subsystémů svoji identitu, ukládat a analyzovat data, autonomně vydávání příkazů do zařízení na základě přehledy dat nebo plány a zobrazení informací o a řídit možnosti autorizované koncovým uživatelům.

### <a name="information-devices-versus-special-purpose-devices"></a>Informace o zařízení a zařízeními pro zvláštní účely

Počítače, telefony a tablety jsou primárně interaktivní informace o zařízení. Telefony a tablety jsou explicitně optimalizováno maximální životnost baterie. Pokud možno vypnout částečně bezprostředně interakci s osobou, nebo když není poskytování služeb, jako je přehrávání hudby nebo která bude obsahovat jejich vlastník na určité místo. Z hlediska systémy jsou tyto informace technologie zařízení hlavně funguje jako proxy pro osoby. Je "lidé Pohony" navrhuje akce a "lidé senzorů" shromažďování vstupu.

Zařízeními pro zvláštní účely, od jednoduchého teploty senzorů po komplexní objekt pro vytváření výrobní linky s tisíci součásti v nich, se liší. Tato zařízení jsou mnohem více obor v účel a i v případě, že poskytují některé části uživatelského rozhraní, jsou do značné míry vymezené propojení s nebo integrovat do prostředků ve skutečnosti. Měření a sestavy prostředí okolností, zapnout ventily, řídit servos, zvukové falešné poplachy, přepnout světla a dělat spoustu dalších úkolů. Pro práci pomáhají, u kterých je zařízení s informace příliš obecný, moc drahé, příliš velký nebo příliš křehký. Konkrétní účel okamžitě určuje jejich technického návrhu jako také k dispozici peněžní rozpočet pro jejich výrobu a plánované doby života operace. Kombinace těchto dvou klíčových faktorů omezuje dostupné provozní energie rozpočtu, fyzické nároky a proto dostupné úložiště, výpočetní výkon a funkce zabezpečení.

Pokud nastane "přejde nesprávné" pomocí automatických nebo vzdáleného zařízení může ovládat, například fyzické závady nebo ovládací prvek logiky vady úmyslného neoprávněného vniknutí a manipulaci s. Mnoho produkčním prostředí může zničen, budovy může looted nebo množství kalorií a lidí může být poškozený nebo dokonce kostka. Toto je zcela jinou třídu než někdo maxing si odcizené karty platební limit škod. Panel zabezpečení pro zařízení, která tu přesunout a také pro data ze senzorů, které nakonec způsobí příkazy, které způsobují věcí, které chcete přesunout, musí být vyšší než v libovolném elektronického obchodování nebo použití v bankovnictví.

### <a name="device-control-and-device-data-interactions"></a>Řízení zařízení a interakce dat zařízení

Připojené zařízeními pro zvláštní účely mají velký počet potenciálních oblastí povrchu interakce a vzory interakcí, které musí přestat považovat poskytuje architekturu pro zabezpečení digitální přístup k těmto zařízením. Termín "přístup k digitální" se zde používá k odlišení od žádné operace, které se provádějí prostřednictvím interakce s přímým přístupem zařízení kde poskytuje zabezpečení přístupu prostřednictvím řízení přístupu na fyzické. Například přechodu zařízení do místnosti se zámkem na dveře. Když fyzický přístup nemůže odepřeno pomocí softwaru a hardwaru, můžete se opatření zabránit fyzickému přístupu k systému před rušením od předních.

Když se budete učit vzory interakcí, podívejte se na "zařízení" a řízení "zařízení" se stejnou úrovní pozornost při modelování hrozeb. "Řídící" dají považovat za veškeré informace, které poskytuje k zařízení stranu s cílem změnu nebo ovlivňující chování na jeho stavu nebo stavu prostředí. "Data zařízení" dají považovat za veškeré informace, které vysílá zařízení na druhou stranu o jejím stavu a zjištěnou stav svého prostředí.

## <a name="performing-threat-modeling-for-the-azure-iot-reference-architecture"></a>Provádění hrozeb modelování pro referenční architektura Azure IoT

Společnost Microsoft používá rozhraní framework popsané dříve provedete před internetovými útoky pro Azure IoT modelování. Následující části ukazují, jak přemýšlet o ohrožení modelování pro IoT a tom, jak vyřešit hrozby identifikovat pomocí konkrétní příklad referenční architektura IoT v Azure. Tento příklad uvádí čtyři hlavní oblasti výběru:

* Zařízení a zdrojů dat.
* Přenos dat
* Zařízení a zpracování událostí a
* Prezentace

![Před internetovými útoky pro Azure IoT modelování](media/iot-security-architecture/iot-security-architecture-fig2.png)

Následující diagram představuje zjednodušené zobrazení architektury IoT od Microsoftu pomocí modelu dat vývojový Diagram, který používá nástroj pro modelování hrozeb Microsoftu:

![Před internetovými útoky pro Azure IoT s použitím nástroj pro modelování hrozeb MS modelování](media/iot-security-architecture/iot-security-architecture-fig3.png)

Je důležité si uvědomit, že architektura odděluje možností zařízení a brány. Tento přístup umožňuje uživateli využít zařízení brány, které jsou bezpečnější: jsou schopny komunikovat s Cloudová brána, pomocí protokolů zabezpečení, což obvykle vyžaduje větší zátěže, že by mohla nativní zařízení – například termostat - Zadejte sama o sobě. V oblasti služeb Azure se předpokládá, že Cloudová brána je reprezentována pomocí služby Azure IoT Hub.

### <a name="device-and-data-sourcesdata-transport"></a>Zařízení a zdroje/datový přenos dat

Tato část popisuje architekturu uvedených dříve přehledu modelování hrozeb a poskytuje přehled o tom, jak vyřešit některé z vlastní otázky. V tomto příkladu se zaměřuje na základní prvky modelu hrozby:

* Procesy (i v rámci ovládacího prvku a externích položek)
* Komunikace (také nazývané datové toky)
* Storage (také nazývané úložiště dat)

#### <a name="processes"></a>Procesy

V každém z kategorií uvedených v architektuře Azure IoT pokusí zmírnit celou řadou různých hrozby v různých fázích/informace o datech existuje v tomto příkladu: proces, komunikace a úložiště. Tady je přehled nejběžnějších ty pro kategorii "procesu", za nímž následuje přehled o tom, jak může nejlépe zmírnit tyto hrozby:

**Falšování identity (S)**: Útočník může extrahujte kryptografický materiál klíče ze zařízení, buď na softwarové nebo hardwarové úrovni a následně přístup, který je už zabraný systému pomocí jiné fyzické nebo virtuální zařízení s identitou zařízení materiál klíče z. Dobré obrázku je dálková ovládání, který můžete zapnout jakékoli Televizi a, které jsou prankster oblíbených nástrojů.

**Útok DoS (D)**: Zařízení lze vykreslit nepodporující fungovat nebo sdělování narušovaly rádiových frekvencí nebo vyjmutí vodičům stanice. Sledování fotoaparát, který má připojení k napájení nebo sítě záměrně vyseknuté například nejde hlásit data, vůbec.

**Manipulaci (T)**: Útočník může částečně nebo zcela nahradit softwaru spuštěného v příslušném zařízení, potenciálně umožňující nahrazené softwaru využívat originální identity zařízení materiál klíče nebo šifrování zařízení obsahující klíče materiály byly k dispozici na nedovolené programu. Například může útočník využít extrahované materiál klíče zachytí a potlačit dat ze zařízení na cestě pro komunikaci a nahraďte ji metodou false data, která se ověřuje pomocí odcizeného materiál klíče.

**Přístup k informacím (I)**: Pokud je na zařízení spuštěný s ní manipulováno softwaru, s ní manipulováno software může potenciálně způsobit únik těchto dat neoprávněným osobám. Například může útočník využít extrahované materiál klíče se vložit samotné do komunikační trasa mezi zařízení a brána kontroler nebo pole nebo Cloudová brána k siphon vypnout informace.

**Zvýšení úrovně oprávnění (E)**: Zařízení, která provádí konkrétní funkci se dá vynutit dělat něco jiného. Například může být ventilu, který je naprogramovaný tak, aby otevřete poloviční způsobem svých otevřete úplně.

| **Komponenta** | **Před internetovými útoky** | **Omezení rizik** | **Riziko** | **Implementace** |
| --- | --- | --- | --- | --- |
| Zařízení |S |Přiřazení identit zařízení a ověřování zařízení |Nahrazení část zařízení nebo zařízení s jiným zařízením. Jak víte, že jsou kdekoliv na správné zařízení? |Ověřování zařízení, pomocí zabezpečení TLS (Transport Layer) nebo protokol IPSec. Infrastruktura by měla podporovat použití předsdílený klíč (PSK) na těchto zařízeních, které nelze zpracovat úplnou asymetrické šifrování. Využijte Azure AD, [OAuth](https://www.rfc-editor.org/pdfrfc/rfc6755.txt.pdf) |
|| TRID |Použijte mechanismy tamperproof do zařízení, například tím, že pevné na možné extrahovat klíčů a dalších kryptografický materiál ze zařízení. |Riziko je, pokud někdo je falšování zařízení (fyzické rušení). Jak jste si jisti, že zařízení nebylo manipulováno. |Nejúčinnější omezení rizik je TPM module (TPM), která umožňuje ukládat klíče ve speciální na čipu obvod, ze kterého klíče nelze číst, ale jde použít jenom pro kryptografické operace využívající klíč, ale nikdy odkrývat klíč. Paměť šifrování zařízení. Klíčová Správa pro zařízení. Podepisování kódu. | |
|| E |S řízení přístupu zařízení. Schéma autorizace. |Pokud zařízení povoluje pro jednotlivé akce, která se má provést podle příkazy z vnějšího zdroje nebo dokonce ohrožených senzory, umožňuje útoku k provádění operací jinak nejsou přístupné. |Povolení režimu pro zařízení s | |
| Brána pole |S |Brána pole do cloudové brány ověřování (například certifikátu na základě PSK, nebo na základě deklarací identity.) |Pokud někdo může zfalšovat Field Gateway, pak se může zobrazit jako jakékoli zařízení. |TLS RSA/PSK, IPSec, [RFC 4279](https://tools.ietf.org/html/rfc4279). Stejné zásadními obavami úložiště a ověření zařízení v obecné – nejlepší případ je pomocí čipu TPM. 6LowPAN rozšíření pro protokol IPSec pro podporu sítí senzor bezdrátové sítě (WSN). |
|| TRID |Ochrana hraniční brána proti manipulaci (TPM)? |Falšování útokům, které se přesvědčit přemýšlení brány cloud se mluví na hraniční brána může způsobit zpřístupnění informací a úpravám dat. |Paměť šifrování, TPM společnosti, ověřování. | |
|| E |Mechanismus řízení přístupu pro bránu | | | |

Tady je několik příkladů hrozby v této kategorii:

**Falšování**: Útočník může extrahujte kryptografický materiál klíče ze zařízení, buď na softwarové nebo hardwarové úrovni a následně přístup, který je už zabraný systému pomocí jiné fyzické nebo virtuální zařízení s identitou zařízení materiál klíče z.

**Útok DoS**: Zařízení lze vykreslit nepodporující fungovat nebo sdělování narušovaly rádiových frekvencí nebo vyjmutí vodičům stanice. Sledování fotoaparát, který má připojení k napájení nebo sítě záměrně vyseknuté například nejde hlásit data, vůbec.

**Manipulace**: Útočník může částečně nebo zcela nahradit softwaru spuštěného v příslušném zařízení, potenciálně umožňující nahrazené softwaru využívat originální identity zařízení materiál klíče nebo šifrování zařízení obsahující klíče materiály byly k dispozici na nedovolené programu.

**Manipulace**: Fotoaparát institucí, který se zobrazuje obrázek viditelné spektra prázdný dále přes chodbu může zaměřené na fotografie takové dále přes chodbu. Kouř nebo fire senzor může reporting někdo uchovávající světlejší pod ním. V obou případech může být zařízení technicky plně důvěryhodný pro systém, ale hlásí informace o ní manipulováno.

**Manipulace**: Útočník může využívat extrahované materiál klíče zachytí a potlačit dat ze zařízení na cestě pro komunikaci a nahraďte ji metodou false data, která se ověřuje pomocí odcizeného materiál klíče.

**Manipulace**: Útočník může částečně nebo zcela nahradit softwaru spuštěného v příslušném zařízení, potenciálně umožňující nahrazené softwaru využívat originální identity zařízení materiál klíče nebo šifrování zařízení obsahující klíče materiály byly k dispozici nedovolené programu.

**Zpřístupnění informací**: Pokud je na zařízení spuštěný s ní manipulováno softwaru, s ní manipulováno software může potenciálně způsobit únik těchto dat neoprávněným osobám.

**Zpřístupnění informací**: Útočník může využívat extrahované materiál klíče se vložit samotné do komunikační trasa mezi zařízení a brána kontroler nebo pole nebo Cloudová brána k siphon vypnout informace.

**Útok DoS**: Zařízení můžete vypnout nebo zapnout do režimu, kdy komunikace není možné (což je úmyslné v mnoha průmyslových strojů).

**Manipulace**: Zařízení můžete nakonfigurovat tak, aby provoz ve stavu Neznámý do systému správy (mimo známé kalibrací parametry) a tak poskytuje data, která může být nesprávné interpretaci

**Zvýšení úrovně oprávnění**: Zařízení, která provádí konkrétní funkci se dá vynutit dělat něco jiného. Například může být ventilu, který je naprogramovaný tak, aby otevřete poloviční způsobem svých otevřete úplně.

**Útok DoS**: Zařízení je možné zapnout do stavu, kdy komunikace není možná.

**Manipulace**: Zařízení můžete nakonfigurovat tak, aby provoz ve stavu Neznámý do systému správy (mimo známé kalibrací parametry) a tak poskytuje data, která může být chybně interpretován.

**Falšování adres/manipulaci nebo odmítnutí**: Pokud není zabezpečený (což je zřídka v případě vzdálené uživatelské ovládací prvky), útočník může anonymně manipulaci stav zařízení. Dobré obrázku je dálková ovládání, který můžete zapnout jakékoli Televizi a, které jsou prankster oblíbených nástrojů.

#### <a name="communication"></a>Komunikace

Hrozby kolem komunikační trasa mezi zařízeními, zařízení a bran v terénu a zařízení a cloudové brány. Následující tabulka obsahuje pokyny k kolem otevřít sokety na zařízení sítě VPN:

| **Komponenta** | **Před internetovými útoky** | **Omezení rizik** | **Riziko** | **Implementace** |
| --- | --- | --- | --- | --- |
| Zařízení IoT Hub |TID. |(D) (PSK/RSA) k šifrování přenosů TLS |Odposlouchávání nebo překáží komunikaci mezi zařízením a brány |Zabezpečení na úrovni protokolu. Vlastní protokoly je nutné zjistit, jak je chránit. Ve většině případů komunikace probíhá ze zařízení do služby IoT Hub (zařízení zahájí připojení). |
| Zařízení |TID. |(D) Protokol TLS (PSK/RSA) k šifrování přenosů. |Čtení dat během přenosu mezi zařízeními. Manipulace s daty. Přetížení zařízení pomocí nového připojení |Zabezpečení na úrovni protokolu (protokol MQTT nebo AMQP a HTTP/CoAP. Vlastní protokoly je nutné zjistit, jak je chránit. Omezení rizik pro hrozby DoS je navázání partnerského vztahu mezi zařízení prostřednictvím cloudu nebo pole brány a potom kliknul pouze act jako klienti směrem k síti. Partnerský vztah může vést k přímé připojení mezi rovnocennými počítači po s byla zprostředkovaných bránou |
| Externí Entity zařízení |TID. |Silné párování externí entita, která má zařízení |Připojení k zařízení odposlouchávání. Komunikace zasahovala do zařízení |Bezpečně párování externí entita, která má zařízení NFC/Bluetooth LE. Řízení panelu provozní zařízení (fyzické) |
| Cloudová brána pole brány |TID. |Protokol TLS (PSK/RSA) k šifrování přenosů. |Odposlouchávání nebo překáží komunikaci mezi zařízením a brány |Zabezpečení na úrovni protokolu (protokol MQTT nebo AMQP a HTTP/CoAP). Vlastní protokoly je nutné zjistit, jak je chránit. |
| Cloudová brána zařízení |TID. |Protokol TLS (PSK/RSA) k šifrování přenosů. |Odposlouchávání nebo překáží komunikaci mezi zařízením a brány |Zabezpečení na úrovni protokolu (protokol MQTT nebo AMQP a HTTP/CoAP). Vlastní protokoly je nutné zjistit, jak je chránit. |

Tady je několik příkladů hrozby v této kategorii:

**Útok DoS**: Omezené zařízení jsou obecně v části před internetovými útoky DoS při jejich aktivně naslouchání pro příchozí připojení nebo nevyžádané datagramů v síti, protože útočník nelze otevřít mnoho připojení paralelně a jejich služby nebo služeb je pomalu, nebo může být zařízení budete zaplaveni s nevyžádanou komunikací. V obou případech zařízení lze účinně vykreslit nefunkční v síti.

**Falšování identity, zpřístupnění informací**: Zabezpečení jeden pro všechna zařízení jako heslo nebo PIN kód ochrany nebo jejich plně využívají důvěřování síti, což znamená, že udělit přístup k informacím, když je zařízení ve stejné síti a že síť mají často omezené zařízeními a zařízeními pro zvláštní účely je často jenom chráněný sdílený klíč. To znamená, že když jsou zveřejňovány sdílený tajný klíč pro zařízení nebo v síti, je možné řídit zařízení nebo sledovat data ze zařízení, protože ho.  

**Falšování**: útočník může zachytit nebo částečně přepsat vysílání a zfalšovat původce (man uprostřed)

**Manipulace**: útočník může zachytit nebo částečně přepsat vysílání a odeslat nepravdivé informace 

**Zpřístupnění informací:** útočník může tajně poslouchat vysílání a získat informace o bez autorizace **Denial of Service:** útočník může jamu signál vysílání a odepřít informace distribuce

#### <a name="storage"></a>Storage

Každé zařízení a pole brány má určitou formu úložiště (dočasné pro službu Řízení front dat, úložiště bitové kopie operačního systému (OS)).

| **Komponenta** | **Před internetovými útoky** | **Omezení rizik** | **Riziko** | **Implementace** |
| --- | --- | --- | --- | --- |
| Zařízení úložiště |TRID |Šifrování úložiště, podepisování protokoly |Čtení dat ze služby storage (data identifikovatelné osobní údaje), manipulaci s telemetrická data. Manipulace s zařazené do fronty nebo příkaz Ovládací prvek dat uložených v mezipaměti. Manipulace s konfigurací nebo firmware balíčky aktualizací do mezipaměti nebo místně ve frontě může mít za následek operačního systému a/nebo systém komponenty, jejichž zabezpečení je ohrožené |Šifrování, ověřovací kód zprávy (MAC) nebo digitální podpis. Kde řízení přístupu je to možné, silné prostřednictvím přístupu k prostředkům řídí oprávnění nebo seznamy ACL. |
| Image operačního systému zařízení |TRID | |Manipulace s operačním systémem nebo nahrazení součásti operačního systému |Oddíl OS jen pro čtení, podepsaný image operačního systému, šifrování |
| Brána pole úložiště (data služby Řízení front) |TRID |Šifrování úložiště, podepisování protokoly |Čtení dat ze služby storage (data identifikovatelné osobní údaje), manipulaci s telemetrická data, manipulaci s zařazené do fronty nebo příkaz Ovládací prvek dat uložených v mezipaměti. Manipulace s konfigurací nebo firmware balíčky aktualizací (určené pro zařízení nebo pole) do mezipaměti nebo místně ve frontě může mít za následek operačního systému a/nebo systém komponenty, jejichž zabezpečení je ohrožené |BitLocker |
| Image operačního systému pole brány |TRID | |Manipulace s operačním systémem nebo nahrazení součásti operačního systému |Oddíl OS jen pro čtení, podepsaný image operačního systému, šifrování |

### <a name="device-and-event-processingcloud-gateway-zone"></a>Zařízení a událostí zóny zpracování/Cloudová brána

Cloudové brány je systém, který umožňuje vzdálenou komunikaci od a do zařízení nebo bran v terénu z několika různých lokalit ve veřejné síti místa, obvykle na ovládací prvek založené na cloudu a systém pro analýzy dat, federace těchto systémů. V některých případech cloudové brány může okamžitě usnadnění přístupu k zařízeními pro zvláštní účely z terminály, jako jsou tablety a telefony. V rámci popsané tady "cloud" slouží k odkazování na vyhrazené zpracování dat systému, který není vázán na stejném místě jako připojené zařízení nebo bran v terénu a kde provozní opatření brání cílový fyzický přístup ale není nutně položky " infrastruktura veřejného cloudu". Cloudová brána lze mapovat potenciálně do překrytí virtualizace sítě izolovat Cloudová brána a všechny jeho připojené zařízení nebo bran v terénu od ostatního síťového přenosu. Cloudová brána samotný není systém správy zařízení nebo zpracování nebo obsluhující zařízení úložiště dat v zařízení; Tato zařízení rozhraní s cloudové bráně. Oblast brány cloud zahrnuje brány cloud společně s všech bran v terénu a zařízení k němu připojená přímo nebo nepřímo.

Cloudová brána je ve většině případů vlastní sestavené část softwaru, která běží jako služba s zveřejněných koncových bodů, do kterých zařízení a hraniční brána připojit. Jako takové musí být navržena s ohledem na bezpečnost. Postupujte podle [SDL](https://www.microsoft.com/sdl) proces pro návrh a vytváření této služby.

#### <a name="services-zone"></a>Zóna služby

Systém správy (nebo řadič) je softwarové řešení, které sdílí rozhraní se zařízení, nebo pole bránou nebo Cloudová brána za účelem řízení jedno nebo více zařízení a/nebo chcete shromažďovat a ukládat a analyzovat data zařízení pro prezentaci, nebo Následující ovládací prvek účely. Systémy správy jsou pouze entity v rámci této diskuse, který může usnadnit okamžitě interakce s uživateli. Výjimky jsou zprostředkující fyzické oblasti ovládacího prvku na zařízeních, jako jsou přepínače, který umožňuje uživateli vypnout zařízení nebo změnily některé vlastnosti, a pro něž neexistuje žádný funkční ekvivalent, který je přístupný digitálně.

Zprostředkující fyzické zařízení Surface ovládacího prvku jsou tam, kde řídící logiky omezí funkce povrchu fyzické ovládacího prvku tak, aby ekvivalentní funkce lze inicializovat vzdáleně nebo vstupní je v konfliktu s vzdálené vstupu se lze vyvarovat – například intermediated ovládací prvek plochy jsou koncepčně připojené k místní řídicím systémem, který využívá stejné základní funkce jako jakémkoli jiném systému vzdáleného řízení, který zařízení může být připojené k paralelně. Hlavní hrozby na cloud computingu, může být čtení na [Cloud Security Alliance (CSA)](https://cloudsecurityalliance.org/articles/csa-releases-top-threats-to-cloud-computing-deep-dive/) stránky.

## <a name="additional-resources"></a>Další materiály

Další informace najdete v následujících článcích:

* [SDL nástroj pro modelování hrozeb](https://www.microsoft.com/sdl/adopt/threatmodeling.aspx)
* [Referenční Architektura Microsoft Azure IoT](https://azure.microsoft.com/updates/microsoft-azure-iot-reference-architecture-available/)
