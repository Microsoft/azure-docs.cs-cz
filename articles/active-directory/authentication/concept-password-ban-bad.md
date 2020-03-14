---
title: Dynamicky zakázaná hesla – Azure Active Directory
description: Zakázání slabých hesel z vašeho prostředí pomocí dynamicky se zakázaných hesel Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: ef127d120b32f5344bce0f68d79f48401087f0ce
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79263995"
---
# <a name="eliminate-bad-passwords-in-your-organization"></a>Eliminujte chybná hesla ve vaší organizaci.

Vedoucí v oboru průmyslu oznamují, že nebudete používat stejné heslo na více místech, abyste ho mohli složitě a neměli jednoduché jako "Password123". Jak můžou organizace zaručit, že se jejich uživatelé budou starat o osvědčené postupy? Jak zajistí, aby uživatelé nepoužívali slabé heslo, nebo dokonce variace na slabých heslech?

Prvním krokem při zadávání silnějších hesel je poskytnutí pokynů uživatelům. Aktuální pokyny Microsoftu k tomuto tématu najdete na následujícím odkazu:

[Pokyny k heslu Microsoftu](https://www.microsoft.com/research/publication/password-guidance)

Je důležité mít dobrý návod, ale i s tím, že ví, že mnoho uživatelů stále ještě ukončí výběr slabého hesla. Ochrana heslem Azure AD chrání vaši organizaci tím, že detekuje a blokuje známá slabá hesla a jejich varianty a volitelně blokuje i další slabé výrazy, které jsou specifické pro vaši organizaci.

Další informace o aktuálním úsilí zabezpečení naleznete v [sestavě Microsoft Security Intelligence](https://www.microsoft.com/security/operations/security-intelligence-report).

## <a name="global-banned-password-list"></a>Globální seznam zakázaných hesel

Tým Azure AD Identity Protection průběžně analyzuje data telemetrie zabezpečení Azure AD, která hledají běžně používaná slabá nebo napadená hesla, nebo přesněji základní výrazy, které se často používají jako základ pro slabá hesla. Když se tyto slabé výrazy najde, přidají se do seznamu globálních zakázaných hesel. Obsah seznamu globálních zakázaných hesel není založený na žádném externím zdroji dat. Globální seznam zakázaných hesel je založený výhradně na průběžných výsledcích telemetrie a analýzy zabezpečení Azure AD.

Pokaždé, když se nové heslo změní nebo obnoví pro libovolného uživatele v rámci Azure AD, jako vstup klíče při ověřování síly hesla se použije aktuální verze seznamu globálních zakázaných hesel. Toto ověření má za následek mnohem silnější hesla pro všechny zákazníky Azure AD.

> [!NOTE]
> Internetoví – zločinci využívají podobné strategie i při jejich útokech. Proto Microsoft nepublikuje obsah tohoto seznamu veřejně.

## <a name="custom-banned-password-list"></a>Vlastní seznam zakázaných hesel

Některé organizace můžou chtít ještě víc zlepšit zabezpečení přidáním vlastních vlastních nastavení na seznam globálních zakázaných hesel, ve kterém Microsoft zavolá vlastní seznam zakázaných hesel. Společnost Microsoft doporučuje, aby se výrazy přidané do tohoto seznamu primárně zaměřily na konkrétní organizační výrazy, například:

- Názvy značek
- Názvy produktů
- Umístění (například ústředí společnosti)
- Interní výrazy specifické pro společnost
- Zkratky, které mají konkrétní význam společnosti.

Po přidání podmínek do vlastního seznamu zakázaných hesel budou v kombinaci s podmínkami v seznamu globální zakázané heslo při ověřování hesel kombinovány.

> [!NOTE]
> Vlastní seznam zakázaných hesel je omezen na maximálně 1000 podmínek. Není navržený pro blokování extrémně velkých seznamů hesel. Aby bylo možné plně využívat výhody vlastního seznamu zakázaných hesel, společnost Microsoft doporučuje, abyste před přidáním nových podmínek do vlastního seznamu zakázaných hesel nejprve zkontrolovali a porozuměli algoritmu pro vyhodnocení hesla (viz [jak jsou vyhodnocena hesla](concept-password-ban-bad.md#how-are-passwords-evaluated)). Porozumět tomu, jak algoritmus funguje, umožní vašemu podniku efektivně detekovat a blokovat velký počet slabých hesel a jejich variant.

Příklad: Uvažujme zákazníka s názvem "contoso", který je založený na Londýně a který vytvoří produkt s názvem "widget". U takového zákazníka by byl wasteful i méně bezpečný a snaží se zablokovat konkrétní variace těchto podmínek, například:

- Contoso! 1
- Contoso@London
- "ContosoWidget"
- "! Contoso
- "LondonHQ"
- ...etcetera

Místo toho je mnohem efektivnější a bezpečné zablokovat jenom základní Klíčové výrazy:

- Contoso
- London
- Pomůck

Algoritmus ověřování hesla pak automaticky zablokuje slabé varianty a kombinace výše uvedeného.

Vlastní seznam zakázaných hesel a možnost Povolit místní integraci služby Active Directory se spravují pomocí Azure Portal.

![Úprava vlastního seznamu zakázaných hesel v části metody ověřování](./media/concept-password-ban-bad/authentication-methods-password-protection.png)

## <a name="password-spray-attacks-and-third-party-compromised-password-lists"></a>Útoky prostřednictvím spreje hesla a seznamy hesel pro zneužití třetích stran

Jednou z klíčových výhod ochrany heslem Azure AD je pomáhat při ochraně proti útokům prostřednictvím postřiku hesla. Většina útoků založených na heslech se nepokusí o útok na konkrétní účet více než několikrát, protože takové chování významně zvyšuje pravděpodobnost detekce, a to buď prostřednictvím uzamčení účtu, nebo jiným způsobem. Většina útoků proti postřikům hesla proto spoléhá na odeslání jenom malého počtu známých slabých hesel proti každému účtu v podniku. Tato technika umožňuje útočníkovi rychle vyhledat snadno narušený účet a zároveň zabránit potenciálním prahům detekce.

Ochrana heslem v Azure AD je navržená tak, aby efektivně blokovala všechna známá hesla, která se budou pravděpodobně používat v útocích proti rozstřiku hesla, a to na základě dat telemetrie zabezpečení, jak je vidět v Azure AD.  Společnost Microsoft si uvědomuje weby třetích stran, které si vyčíslují miliony hesel, které byly ohroženy předchozími veřejně známými narušeními zabezpečení. Je běžné, že produkty pro ověřování hesel třetích stran vycházejí z porovnání hrubou silou s těmito miliony hesel. Společnost Microsoft si neosvědčila, že tyto techniky nejsou nejlepším způsobem, jak vylepšit celkovou sílu hesla, a to s využitím typických strategií používaných útočníky pro postřik hesel.

> [!NOTE]
> Seznam pro globální zakázané heslo společnosti Microsoft není založen na jakýchkoli zdrojích dat třetích stran, včetně seznamů napadených hesel.

I když je seznam globálních zakázaných společností Microsoft malým porovnáním s některými hromadnými seznamy třetích stran, projeví se jeho bezpečnostní účinky tím, že se z reálné telemetrie zabezpečení na vlastní útoky na základě hesla doplní. navíc to znamená, že společnost Microsoft algoritmus ověřování hesla používá inteligentní techniky pro přibližné porovnání. Konečným výsledkem je, že bude efektivně detekovat a blokovat miliony nejběžnějších slabých hesel, které se ve vašem podniku používají. Zákazníci, kteří se rozhodnou přidat konkrétní firemní údaje do seznamu vlastních zakázaných hesel, mají také výhodu stejný algoritmus.

Další informace o problémech zabezpečení založených na heslech se dají zkontrolovat na [vašem PA $ $Word nezáleží](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984)na tom.

## <a name="on-premises-hybrid-scenarios"></a>Místní hybridní scénáře

Ochrana účtů jenom v cloudu je užitečná, ale mnoho organizací udržuje hybridní scénáře, včetně místní služby Windows Server Active Directory. Výhody zabezpečení ochrany heslem Azure AD se taky můžou rozšířit do prostředí Windows Server Active Directory pomocí instalace místních agentů. Uživatelé a správci, kteří mění nebo resetují hesla ve službě Active Directory, vyžadují, aby vyhověly stejným zásadám hesel jako pouze cloudovým uživatelům.

## <a name="how-are-passwords-evaluated"></a>Jak se vyhodnocují hesla

Pokaždé, když uživatel změní nebo obnoví heslo, bude nové heslo kontrolováno na sílu a složitost tím, že je ověří podle kombinovaného seznamu podmínek z globálních a vlastních seznamů zakázaných hesel (Pokud je nakonfigurováno).

I v případě, že heslo uživatele obsahuje zakázané heslo, může být heslo stále přijatelné v případě, že je celé heslo dostatečně silné. Nově nakonfigurované heslo provede následující kroky, abyste zjistili jeho celkovou sílu, abyste zjistili, jestli by se měl přijmout nebo odmítnout.

### <a name="step-1-normalization"></a>Krok 1: normalizace

Nové heslo se nejprve prochází procesem normalizace. Tato technika umožňuje mapovat malou sadu zakázaných hesel na mnohem větší sadu potenciálně slabých hesel.

Normalizace má dvě části.  Nejprve se všechna velká písmena změní na malá písmena.  Za druhé, jsou provedeny běžné náhrady znaků, například:  

| Původní dopis  | Nahrazené písmeno |
| --- | --- |
| 0,8  | zápis |
| první  | 'l' |
| '$'  | jeho |
| '\@'  | určitého |

Příklad: Předpokládejme, že heslo "prázdné" je zakázáno a uživatel se pokusí změnit heslo na "Bl@nK". I když neníBl@nkvýslovně zakázaná, proces normalizace toto heslo převede na "prázdné", což je zakázané heslo.

### <a name="step-2-check-if-password-is-considered-banned"></a>Krok 2: ověření, jestli se heslo považuje za zakázané

#### <a name="fuzzy-matching-behavior"></a>Chování při přibližném porovnání

V normalizovaném hesle se používá přibližná shoda k identifikaci, zda obsahuje heslo nalezené v globálním nebo vlastním seznamu zakázaných hesel. Shodný proces je založen na jedné z úprav vzdálenosti (1) porovnání.  

Příklad: Předpokládejme, že heslo "ABCDEF" je zakázané a uživatel se pokusí změnit heslo na jednu z následujících možností:

' abcdeg ' *(poslední znak se změnil z ' f ' na ' g ')* ' abcdefg ' *' (g ' připojeno ke konci)* ' abcde ' *(koncová ' f ' byla odstraněna z elementu end)*

Každé z výše uvedených hesel se konkrétně neshoduje se zakázaným heslem "ABCDEF". Vzhledem k tomu, že každý příklad je v rámci úpravy vzdálenosti 1 zakázaného termínu "ABCDEF", jsou všechny považovány za shodné s "ABCDEF".

#### <a name="substring-matching-on-specific-terms"></a>Shoda podřetězců (za určitých podmínek)

Pro porovnání podřetězců se používá normalizované heslo ke kontrole křestního jména a příjmení uživatele a také názvu tenanta (Všimněte si, že při ověřování hesel v řadiči domény služby Active Directory se neprovádí shoda názvů klientů.)

Příklad: Předpokládejme, že máme uživatele, Pol, který chce resetovat heslo na "P0l123fb". Po normalizaci se toto heslo stane "pol123fb". Shoda podřetězců najde, že heslo obsahuje křestní jméno uživatele "Pol". I když "P0l123fb" nebylo konkrétně výslovně na seznam zakázaných hesel, v hesle byl nalezen řetězec shodný se znakem "Pol". Proto by toto heslo bylo odmítnuto.

#### <a name="score-calculation"></a>Výpočet skóre

Dalším krokem je identifikace všech instancí zakázaných hesel v normalizovaném novém heslu uživatele. Potom:

1. Každé zakázané heslo, které se nachází v uživatelském hesle, je přiděleno na jeden bod.
2. Každý zbývající jedinečný znak je dán jedním bodem.
3. Heslo musí být alespoň pět (5) bodů, aby je bylo možné přijmout.

V následujících dvou příkladech se předpokládá, že contoso používá ochranu heslem Azure AD a má ve svém vlastním seznamu "contoso". Pojďme také předpokládat, že je "prázdná" v globálním seznamu.

Příklad: uživatel změní heslo na "C0ntos0Blank12".

Po normalizaci se toto heslo bude "contosoblank12". Odpovídající proces zjistí, že toto heslo obsahuje dvě zakázaná hesla: contoso a prázdné. Toto heslo pak předané skóre:

[contoso] + [prázdné] + [1] + [2] = 4 body vzhledem k tomu, že toto heslo je pod pěti (5) body, bude odmítnuto.

Příklad: uživatel změní heslo naContoS0Bl@nkf9!.

Po normalizaci se toto heslo bude "contosoblankf9!". Odpovídající proces zjistí, že toto heslo obsahuje dvě zakázaná hesla: contoso a prázdné. Toto heslo pak předané skóre:

[contoso] + [prázdné] + [f] + [9] + [!] = 5 bodů vzhledem k tomu, že toto heslo je aspoň pět (5) bodů, je přijaté.

   > [!IMPORTANT]
   > Počítejte s tím, že algoritmus zakázaného hesla spolu s globálním seznamem může v Azure kdykoli a kdykoli změnit v závislosti na probíhající analýze zabezpečení a výzkumu. U místní služby agenta řadiče domény se aktualizované algoritmy projeví až po opětovné instalaci softwaru agenta DC.

## <a name="license-requirements"></a>Licenční požadavky

|   | Ochrana heslem Azure AD s globálním seznamem zakázaných hesel | Ochrana heslem Azure AD s vlastním seznamem zakázaných hesel|
| --- | --- | --- |
| Jenom cloudové uživatele | Azure AD Free | Azure AD Premium P1 nebo P2 |
| Uživatelé synchronizovaný z místní služby Windows Server Active Directory | Azure AD Premium P1 nebo P2 | Azure AD Premium P1 nebo P2 |

> [!NOTE]
> Místní uživatelé služby Windows Server Active Directory, kteří nejsou synchronizováni, Azure Active Directory také výhody ochrany heslem Azure AD na základě stávajícího licencování pro synchronizované uživatele.

Další informace o licencování, včetně nákladů, najdete na [webu Azure Active Directory ceny](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="what-do-users-see"></a>Co vidí uživatelé

Když se uživatel pokusí resetovat heslo na něco, co by bylo zakázané, zobrazí se tato chybová zpráva:

Vaše heslo bohužel obsahuje slovo, frázi nebo vzor, který umožňuje snadnou možnost uhodnout heslo. Zkuste to prosím znovu s jiným heslem.

## <a name="next-steps"></a>Další kroky

- [Konfigurovat vlastní seznam zakázaných hesel](howto-password-ban-bad.md)
- [Povolení agentů ochrany hesel služby Azure AD v místním prostředí](howto-password-ban-bad-on-premises-deploy.md)
