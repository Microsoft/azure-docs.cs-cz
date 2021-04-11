---
title: Úvod do Azure Active Directory ověřitelných přihlašovacích údajů (Preview)
description: Přehled ověřitelných přihlašovacích údajů Azure
services: active-directory
author: barclayn
manager: daveba
editor: ''
ms.service: identity
ms.subservice: verifiable-credentials
ms.topic: overview
ms.date: 04/01/2021
ms.author: barclayn
ms.reviewer: ''
ms.openlocfilehash: 4ae7ad186e2ed8868be6205d48e9cfbef525d810
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2021
ms.locfileid: "106172790"
---
# <a name="introduction-to-azure-active-directory-verifiable-credentials-preview"></a>Úvod do Azure Active Directory ověřitelných přihlašovacích údajů (Preview)

> [!IMPORTANT]
> Azure Active Directory ověřitelných přihlašovacích údajů je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Naše digitální a fyzické života jsou stále stále propojeny s aplikacemi, službami a zařízeními, které používáme pro přístup k bohatě se sadou prostředí. Tato digitální transformace nám umožňuje spolupracovat se stovkami společností a tisíci dalších uživatelů způsobem, který byl dřív neimaginární.

Data identity se ale často vystavila v rozporu se zabezpečením. Tato porušení jsou ovlivněná na osoby, které mají vliv na sociální, profesionální a finanční život. Společnost Microsoft se domnívá, že existuje lepší způsob. Každá osoba má právo na identitu, kterou vlastní a řídí, a ta tak bezpečně ukládá prvky své digitální identity a zachovává ochranu osobních údajů. V tomto úvodu se dozvíte, jak se připojovat k různým komunitám a vytvářet otevřená, důvěryhodná, interoperabilní a standardizovaná řešení založená na standardech pro jednotlivce a organizace.

## <a name="why-we-need-decentralized-identity"></a>Proč musíme decentralizované identity 

Dnes používáme naši digitální identitu v práci, doma i v každé aplikaci, službě a zařízení, které používáme. Sestavili jsme všechno, co říkáme, a máte zkušenosti, a to v rámci našich životních lístků pro událost, kontrolu hotelu nebo dokonce objednávání oběda. V současné době je naše identita a všechny naše digitální interakce vlastněné a ovládané dalšími stranami, přičemž některé z nich ještě neznáte.

Obecně platí, že uživatelé si můžou udělit souhlas s několika aplikacemi a zařízeními. Tento přístup vyžaduje vysoký stupeň bdělosti na straně uživatele, aby bylo možné sledovat, kdo má přístup k těmto informacím. V podnikovém frontě musí spolupráce se zákazníky a partnery zajistit vysokou orchestraci, aby bezpečně vyměnila data způsobem, který udržuje ochranu osobních údajů a zabezpečení pro všechny účastníky.

Věříme, že systém s decentralizovanými identitami založený na standardech může odemknout novou sadu prostředí, která uživatelům a organizacím umožní lepší kontrolu nad svými daty a dodávat vyšší úroveň důvěryhodnosti a zabezpečení pro aplikace, zařízení a poskytovatele služeb.

## <a name="lead-with-open-standards"></a>Vedoucí s otevřenými standardy 

Zavázali jsme se spolupracovat se zákazníky, partnery a komunitou, abychom mohli odemknout novou generaci prostředí založeného na identitách, a s radostí se budeme zapojovat s osobami a organizacemi, které v tomto prostoru poskytují nedostupné příspěvky. Pokud by byl ekosystém růst, standardů, technických komponent a dodávek kódu, musí být otevřený zdroj a přístupný všem.

Společnost Microsoft aktivně spolupracuje se členy decentralizované Identity Foundation (DIF), skupiny komunit přihlašovacích údajů W3C a širší komunitou identity. S těmito skupinami jsme pracovali k identifikaci a vývoji kritických standardů a v našich službách jste implementovali následující standardy.

* [Decentralizované identifikátory W3C](https://www.w3.org/TR/did-core/)
* [Pověření s možností ověření W3C](https://www.w3.org/TR/vc-data-model/)
* [Sidetree DIF](https://identity.foundation/sidetree/spec/)
* [Dobře známá konfigurace DIF](https://identity.foundation/specs/did-configuration/)
* [DIF – SIOP](https://identity.foundation/did-siop/)
* [Výměna prezentace DIF](https://identity.foundation/presentation-exchange/)


## <a name="what-are-dids"></a>Co jsou DIDs 

Než budeme rozumět DIDs, pomůže vám to porovnat s aktuálními systémy identit. E-mailové adresy a ID sociálních sítí jsou uživatelsky přívětivé aliasy pro spolupráci, ale teď jsou přetížené, aby sloužily jako řídicí body pro přístup k datům napříč mnoha scénáři po spolupráci. Tím se vytvoří potenciální problém, protože přístup k těmto identifikátorům můžete kdykoli odebrat externími stranami.

Decentralizované identifikátory (DIDs) se liší. DIDs jsou uživatelsky vygenerované, globálně jedinečné identifikátory, které jsou rootem v decentralizovaných systémech, jako je například ION. Mají jedinečné charakteristiky, jako je větší záruka na neměnnosti, odolnost proti Censorship a manipulaci evasiveness. Tyto atributy jsou klíčové pro jakýkoli systém ID, který je určen k poskytnutí vlastní vlastnictví a uživatelského ovládacího prvku. 

Řešení ověřitelných přihlašovacích údajů od Microsoftu používá decentralizované přihlašovací údaje (DIDs) k kryptografické podepisování jako důkaz, že předávající strana (Ověřovač) ověřuje informace, že se jedná o vlastníky ověřitelných přihlašovacích údajů. Proto se základní porozumění decentralizovaným identifikátorům doporučuje pro kohokoli, kdo vytvoří ověřitelné řešení pověření na základě nabídky Microsoftu.
## <a name="what-are-verifiable-credentials"></a>Co jsou ověřitelné přihlašovací údaje 

 Identifikátory používáme v každodenním životě. Máme licence na ovladače, které používáme jako důkaz naší schopnosti provozovat automobil. Diplomy s vysokými školami potvrzují, že jsme dosáhli úrovně vzdělávání. Passports používáme k důkazu toho, kdo je k dismailu, jak dorazíme do jiných zemí. Datový model popisuje, jak můžeme zvládnout tyto typy scénářů při práci přes Internet, ale zabezpečeným způsobem, který respektuje ochranu osobních údajů uživatele. Další informace můžete získat v [datovém modelu ověřitelných přihlašovacích údajů 1,0](https://www.w3.org/TR/vc-data-model/) .

V krátkých, ověřitelných přihlašovacích údajích jsou datové objekty, které se skládají z deklarací identity vystavitelů potvrzujících informace o předmětu. Tyto deklarace jsou označeny schématem a zahrnují vystavitele a předmět. Vystavitel vytvoří digitální podpis jako důkaz, že k těmto informacím potvrzují.


## <a name="how-does-decentralized-identity-work"></a>Jak decentralizovaná identita funguje? 

Potřebujeme novou formu identity. Potřebujeme identitu, která dohromady přináší technologie a standardy, aby poskytovala klíčové atributy identity, jako je vlastní vlastnictví a odolnost proti Censorship. Tyto možnosti je obtížné dosáhnout pomocí stávajících systémů.

Abychom mohli doručovat tyto příslibů, potřebujeme technickou základnu, která se skládá ze sedmi důležitých inovací. Jedním z klíčových inovací jsou identifikátory, které vlastní uživatel, uživatelský agent pro správu klíčů přidružených k takovým identifikátorům a šifrované úložiště dat řízené uživatelem.

![Přehled prostředí ověřitelných přihlašovacích údajů společnosti Microsoft](media/decentralized-identifier-overview/microsoft-did-system.png)

**1. ID decentralizovaných identifikátorů W3C (DIDs)** , které uživatelé vytvářejí, vlastní a ovládají, nezávisle na jakékoli organizaci nebo vládě. DIDs jsou globálně jedinečné identifikátory propojené s decentralizovanými metadaty infrastruktury veřejných klíčů (DPKI) složených z dokumentů JSON, které obsahují obsah veřejných klíčů, popisovačů ověřování a koncových bodů služby.

**2. decentralizované rozhraní: iontová (překrytá síť identity)** Ion je otevřená vrstva bez oprávnění založená na čistě deterministickém protokolu Sidetree, který nevyžaduje žádné speciální tokeny, důvěryhodné validátory nebo jiné mechanismy konsensu. lineární průběh časového řetězce Bitcoin je vše, co je vyžadováno pro jeho operaci. [Otevřeli jsme balíček npm source](https://www.npmjs.com/package/@decentralized-identity/ion-tools) , aby bylo možné pracovat s iontovou sítí snadno integrovat do vašich aplikací a služeb. Mezi knihovny patří vytvoření nového, generování klíčů a ukotvení vaší služby na Bitcoin blockchain. 

**3. měl uživatelský agent/peněženku: Microsoft Authenticator aplikace** umožňuje skutečným lidem používat decentralizované identity a ověřitelné přihlašovací údaje. Ověřovací data vytváří DIDs, usnadňuje vydávání a prezentační požadavky na ověřitelné přihlašovací údaje a spravuje zálohu vašeho předplatného prostřednictvím šifrovaného souboru peněženky.

**4. překladač** rozhraní API, který se připojuje k NAŠEmu iontu Ion pro vyhledání a vyřešení DIDs pomocí ```did:ion``` metody a vrácení objektu dokumentu (DDO). DDO zahrnuje DPKI Metadata přidružená k, jako jsou veřejné klíče a koncové body služby. 

**5. Azure Active Directory ověřené přihlašovací údaje. služba** vystavuje rozhraní API pro vydávání a ověřování a open-source sadu SDK pro [ověřitelné přihlašovací údaje W3C](https://www.w3.org/TR/vc-data-model/) , které jsou podepsané pomocí ```did:ion``` metody. Umožňují vlastníkům identity generovat, prezentovat a ověřovat deklarace identity. Tato forma je základem vztahu důvěryhodnosti mezi uživateli systémů.

## <a name="a-sample-scenario"></a>Vzorový scénář

Scénář, který využijeme k vysvětlení fungování rozhraní VCs, zahrnuje:

- Woodgrove Inc. společnost.
- Proseware, což je společnost, která nabízí slevy za zaměstnance Woodgrove.
- Alice, zaměstnanec na Woodgrove, Inc., který chce získat slevu z proseware



V dnešní době Alice poskytuje uživatelské jméno a heslo pro přihlášení do síťového prostředí Woodgrove. Woodgrove nasazuje řešení VC, aby poskytovalější spravovatelnější způsob, jak Alice prokázala, že je zaměstnancem Woodgrove. Proseware používá řešení VC kompatibilní s řešením VC Woodgrove a přijímá přihlašovací údaje vydané Woodgrove jako kontrolu zaměstnanosti.

Vystavitel přihlašovacích údajů, Woodgrove Inc., vytvoří veřejný klíč a privátní klíč. Veřejný klíč je uložený na ION. Když se klíč přidá do infrastruktury, záznam se zaznamená do decentralizované hlavní knihy založené na blockchain. Vystavitel poskytuje Alici soukromý klíč, který je uložený v aplikaci peněženky. Pokaždé, když Alice úspěšně používá privátní klíč, se transakce zaznamená do aplikace Peněženka.

![Přehled společnosti Microsoft](media/decentralized-identifier-overview/did-overview.png)

## <a name="roles-in-a-verifiable-credential-solution"></a>Role v řešení ověřitelných přihlašovacích údajů 

Existují tři primární aktéry v řešení ověřitelných přihlašovacích údajů. V následujícím diagramu:

- **Krok 1** **uživatel** požádá o ověřitelné přihlašovací údaje od vystavitele.
- V **kroku 2** **Vystavitel** přihlašovacích údajů potvrzuje, že důkaz, který uživatel zadal, je přesný a vytvoří ověřitelné přihlašovací údaje podepsané s jeho pomocí a uživatel je jeho subjektem.
- **V kroku 3** podepíše uživatel ověřitelnou prezentaci (náměstek) se svým výsledkem a pošle **ověřovateli.** Ověřovatel potom ověří přihlašovací údaje porovnáním s veřejným klíčem umístěným v DPKI.

Role v tomto scénáři jsou:

![role v prostředí ověřitelných přihlašovacích údajů](media/decentralized-identifier-overview/issuer-user-verifier.png)

**Vystavitel** – Vystavitel je organizace, která vytvoří řešení pro vystavování požadující informace od uživatele. Tyto informace slouží k ověření identity uživatele. Například Woodgrove, Inc. má řešení pro vystavování, které umožňuje vytvořit a distribuovat ověřitelné přihlašovací údaje (VCs) všem jejich zaměstnancům. Zaměstnanec používá ověřovací aplikaci k přihlášení pomocí svého uživatelského jména a hesla, který předá službě vydávající token ID. Jakmile Woodgrove, Inc. ověří token ID, vytvoří řešení pro vystavování VC, který obsahuje deklarace identity zaměstnanců a je podepsaný Woodgrove, Inc.. Zaměstnanec má teď ověřitelné přihlašovací údaje, které podepisuje jejich zaměstnavatel, včetně zaměstnanců, kteří byli v předmětu.  

**uživatel** – uživatel je osoba nebo entita požadující VC. Například Alice je nový zaměstnanec společnosti Woodgrove, Inc. a dříve vystavil svůj důkaz ověřitelných přihlašovacích údajů pro zaměstnanost. Když Alice potřebuje poskytnout důkaz o zaměstnanosti, aby získala slevu na proseware, může udělit přístup k přihlašovacím údajům v její ověřovací aplikaci tím, že podepisuje ověřitelnou prezentaci, která prokázala, že je Alice vlastníkem. Proseware je schopný ověřit, že pověření vydala Woodgrove, Inc. a Alice je vlastníkem přihlašovacích údajů. 

**verifier** – ověřovatel je společnost nebo entita, která potřebuje ověřit deklarace identity z jednoho nebo více vystavitelů, které důvěřují. Například proseware Trusts Woodgrove, Inc. provede odpovídající úlohu ověření identity jejich zaměstnanců a vystavení autentického a platného VC. Když se Alice pokusí objednat zařízení, které potřebuje pro jeho úlohu, proseware použije otevřené standardy, jako je SIOP a prezentační Exchange, k vyžádání přihlašovacích údajů od uživatele, který zjistí, že se jedná o zaměstnance Woodgrove, Inc. Například proseware může Alice poskytnout odkaz na web s kódem QR, který prohledává pomocí své telefonní kamery. Tím se zahájí požadavek na konkrétní VC, který ověřovatel analyzuje a poskytne Alici možnost schválit žádost, aby prokázala svoji zaměstnanost proseware. Proseware může k ověření pravosti ověřitelné prezentace použít rozhraní API nebo sadu SDK služby ověřitelných přihlašovacích údajů. Na základě informací poskytnutých Alicí poskytují Alici slevu. Pokud jiné společnosti a organizace ví, že Woodgrove, Inc. vydává svým zaměstnancům VC, můžou také vytvořit řešení ověřovače a použít přihlašovací údaje Woodgrove, Inc., aby poskytovaly speciální nabídky vyhrazené pro zaměstnance Woodgrove, Inc..

## <a name="next-steps"></a>Další kroky

Když teď víte o DIDs a ověřitelných přihlašovacích údajích, vyzkoušejte si je podle našeho článku Začínáme nebo v některém z našich článků, které poskytují podrobnější informace o ověřitelných konceptech přihlašovacích údajů.

- [Začínáme s ověřitelnými přihlašovacími údaji](get-started-verifiable-credentials.md)
- [Jak přizpůsobit přihlašovací údaje](credential-design.md)
- [Nejčastější dotazy ke ověřitelným přihlašovacím údajům](verifiable-credentials-faq.md)