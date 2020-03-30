---
title: Dynamicky zakázaná hesla – Azure Active Directory
description: Zákaz slabých hesel z vašeho prostředí pomocí dynamicky zakázaných hesel Azure AD
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263995"
---
# <a name="eliminate-bad-passwords-in-your-organization"></a>Eliminace špatných hesel v rámci organizace

Vedoucí představitelé průmyslu vám říkají, abyste nepoužívali stejné heslo na více místech, aby bylo složité a aby to nebylo jednoduché jako "Password123". Jak mohou organizace zaručit, že se jejich uživatelé řídí pokyny pro osvědčené postupy? Jak se mohou ujistit, že uživatelé nepoužívají slabá hesla, nebo dokonce variace na slabá hesla?

Prvním krokem v tom, že silnější hesla je poskytnout pokyny pro vaše uživatele. Aktuální pokyny společnosti Microsoft k tomuto tématu naleznete na následujícím odkazu:

[Pokyny pro hesla společnosti Microsoft](https://www.microsoft.com/research/publication/password-guidance)

Mít dobré vedení je důležité, ale i s tím víme, že mnoho uživatelů bude stále nakonec vybrat slabá hesla. Azure AD Password Protection chrání vaši organizaci tím, že detekuje a blokuje známá slabá hesla a jejich varianty, stejně jako volitelně blokuje další slabé podmínky, které jsou specifické pro vaši organizaci.

Další informace o aktuálním úsilí o zabezpečení naleznete v [protokolu Microsoft Security Intelligence Report](https://www.microsoft.com/security/operations/security-intelligence-report).

## <a name="global-banned-password-list"></a>Globální seznam zakázaných hesel

Tým Azure AD Identity Protection neustále analyzuje telemetrická data zabezpečení Azure AD, která hledají běžně používaná slabá nebo kompromitovaná hesla, nebo konkrétněji slabé základní termíny, které se často používají jako základ pro slabá hesla. Pokud jsou tyto slabé termíny nalezeny, jsou přidány do globálního seznamu zakázaných hesel. Obsah globálního seznamu zakázaných hesel není založen na žádném externím zdroji dat. Globální seznam zakázaných hesel je založen výhradně na průběžných výsledcích telemetrie a analýzy zabezpečení Azure AD.

Při každé změně nebo resetování nového hesla pro libovolného uživatele v libovolném klientovi ve službě Azure AD se aktuální verze globálního seznamu zakázaných hesel používá jako klíčový vstup při ověřování síly hesla. Toto ověření má za následek mnohem silnější hesla pro všechny zákazníky Azure AD.

> [!NOTE]
> Počítačoví zločinci také používají podobné strategie při svých útocích. Proto společnost Microsoft nepublikuje obsah tohoto seznamu veřejně.

## <a name="custom-banned-password-list"></a>Vlastní seznam zakázaných hesel

Některé organizace mohou chtít ještě více zlepšit zabezpečení přidáním vlastního vlastního vlastního nastavení nad globální seznam zakázaných hesel v seznamu, který společnost Microsoft nazývá seznam emitovaných zakázaných hesel. Společnost Microsoft doporučuje, aby termíny přidané do tohoto seznamu byly primárně zaměřeny na termíny specifické pro organizaci, jako jsou:

- Značky
- Názvy produktů
- Umístění (například sídlo společnosti)
- Interní podmínky specifické pro společnost
- Zkratky, které mají specifický význam společnosti.

Jakmile jsou termíny přidány do seznamu vlastních zakázaných hesel, budou při ověřování hesel kombinovány s podmínkami v globálním seznamu zakázaných hesel.

> [!NOTE]
> Vlastní seznam zakázaných hesel je omezen na maximálně 1000 termínů. Není určen pro blokování extrémně velkých seznamů hesel. Chcete-li plně využít výhod seznamu vlastních zakázaných hesel, společnost Microsoft doporučuje nejprve zkontrolovat a pochopit algoritmus hodnocení hesla (viz [Jak jsou vyhodnocována hesla)](concept-password-ban-bad.md#how-are-passwords-evaluated)před přidáním nových termínů do vlastního seznamu zakázaných. Pochopení toho, jak algoritmus funguje, umožní vašemu podniku efektivně detekovat a blokovat velké množství slabých hesel a jejich variant.

Například: zvažte zákazníka s názvem "Contoso", který sídlí v Londýně a který vytváří produkt s názvem "Widget". Pro takového zákazníka by bylo nehospodárné a méně bezpečné pokusit se zablokovat konkrétní varianty těchto pojmů, jako jsou:

- "Contoso!1"
- "Contoso@London"
- "ContosoWidget"
- "! Contoso"
- "LondonHQ"
- ... Etcetera

Místo toho je mnohem efektivnější a bezpečnější blokovat pouze základní klíčové termíny:

- "Contoso"
- "Londýn"
- "Widget"

Algoritmus ověření hesla pak automaticky zablokuje slabé varianty a kombinace výše uvedených.

Vlastní seznam zakázaných hesel a možnost povolit místní integraci služby Active Directory se spravuje pomocí portálu Azure.

![Úprava seznamu vlastních zakázaných hesel v části Metody ověřování](./media/concept-password-ban-bad/authentication-methods-password-protection.png)

## <a name="password-spray-attacks-and-third-party-compromised-password-lists"></a>Útoky sprejem heslem a seznamy kompromitovaných hesel třetích stran

Jednou z klíčových výhod ochrany heslem Azure AD je pomoci vám bránit proti útokům sprej hesla. Většina útoků stříkacím sprejem hesla se nepokouší zaútočit na daný individuální účet více než několikrát, protože takové chování výrazně zvyšuje pravděpodobnost detekce, a to buď prostřednictvím uzamčení účtu nebo jinými prostředky. Většina útoků stříkacími hesly proto závisí na odeslání pouze malého počtu známých nejslabších hesel proti každému z účtů v podniku. Tato technika umožňuje útočníkovi rychle vyhledat snadno ohrožený účet a současně se vyhnout potenciálním prahovým hodnotám detekce.

Azure AD ochrana heslem je navržen tak, aby efektivně blokovat všechna známá slabá hesla, které se pravděpodobně používají při útoku spravsprejhesla, na základě dat telemetrie zabezpečení reálného světa, jak je vidět ve službě Azure AD.  Společnost Microsoft si je vědoma webů třetích stran, které vyčíslují počet milionů hesel, která byla ohrožena v předchozích veřejně známých narušenízabezpečení. Je běžné, že produkty pro ověření hesla třetích stran jsou založeny na porovnání hrubou silou s těmito miliony hesel. Společnost Microsoft se domnívá, že tyto techniky nejsou nejlepší způsob, jak zlepšit celkovou sílu hesla vzhledem k typickým strategiím používaným útočníky s rozprašovacím heslem.

> [!NOTE]
> Globální seznam zakázaných hesel společnosti Microsoft není založen na žádných zdrojích dat třetích stran, včetně seznamů kompromitovaných hesel.

Ačkoli globální seznam zakázaných položek společnosti Microsoft je malý ve srovnání s některými hromadnými seznamy třetích stran, jeho bezpečnostní účinky jsou zesíleny skutečností, že pochází z telemetrie reálného zabezpečení na skutečných útocích s heslem a skutečností, že společnost Microsoft algoritmus ověření hesla používá inteligentní techniky přibližování. Konečným výsledkem je, že bude efektivně detekovat a blokovat miliony nejběžnějších slabých hesel z použití ve vašem podniku. Zákazníci, kteří se rozhodnou přidat podmínky specifické pro organizaci do seznamu vlastních zakázaných hesel, mají také prospěch ze stejného algoritmu.

Další informace o problémech se zabezpečením založeným na heslech mohou být přezkoumány na [adrese Vaše pa$ $word nezáleží](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984).

## <a name="on-premises-hybrid-scenarios"></a>Místní hybridní scénáře

Ochrana účtů pouze pro cloud je užitečná, ale mnoho organizací udržuje hybridní scénáře včetně místníslužby Windows Server Active Directory. Výhody zabezpečení ochrany heslem Azure AD může být také rozšířena do prostředí Windows Server Active Directory prostřednictvím instalace místních agentů. Nyní uživatelé a správci, kteří mění nebo resetují hesla ve službě Active Directory, musí dodržovat stejné zásady hesel jako uživatelé pouze v cloudu.

## <a name="how-are-passwords-evaluated"></a>Jak jsou vyhodnocována hesla

Kdykoli uživatel změní nebo resetuje své heslo, nové heslo je kontrolována na sílu a složitost ověřením proti kombinovanému seznamu podmínek z globálních a vlastních seznamů zakázaných hesel (pokud je nakonfigurováno).

I v případě, že heslo uživatele obsahuje zakázané heslo, může být heslo stále přijato, pokud je celkové heslo dostatečně silné. Nově nakonfigurované heslo provede následující kroky k posouzení jeho celkové síly k určení, zda by měla být přijata nebo odmítnuta.

### <a name="step-1-normalization"></a>Krok 1: Normalizace

Nové heslo nejprve prochází procesem normalizace. Tato technika umožňuje malou sadu zakázaných hesel, které mají být mapovány na mnohem větší sadu potenciálně slabá hesla.

Normalizace má dvě části.  Za prvé, všechna velká písmena jsou změněna na malá písmena.  Za druhé, jsou prováděny běžné substituce znaků, například:  

| Původní dopis  | Nahrazené písmeno |
| --- | --- |
| '0'  | "o" |
| '1'  | "l" |
| '$'  | "s" |
| '\@'  | "a" |

Příklad: předpokládejme, že heslo "prázdné" je zakázáno aBl@nKuživatel se pokusí změnit své heslo na " ". I kdyžBl@nk" " není výslovně zakázáno, proces normalizace převede toto heslo na "prázdné", což je zakázané heslo.

### <a name="step-2-check-if-password-is-considered-banned"></a>Krok 2: Kontrola, zda je heslo považováno za zakázané

#### <a name="fuzzy-matching-behavior"></a>Chování přibližné shody

Přibližná shoda se používá na normalizované heslo k identifikaci, pokud obsahuje heslo nalezené na globální nebo vlastní zakázané heslo seznamy. Proces párování je založen na vzdálenosti úprav jednoho (1) porovnání.  

Příklad: předpokládejme, že heslo "abcdef" je zakázáno a uživatel se pokusí změnit své heslo na jednu z následujících možností:

'abcdeg' *(poslední znak změněn z 'f' na 'g')* 'abcdefg' *'(g' připojen o konec)* 'abcde' *(koncové 'f' byl odstraněn z konce)*

Každé z výše uvedených hesel neodpovídá konkrétně zakázané heslo "abcdef". Nicméně, protože každý příklad je v rámci editační vzdálenosti 1 zakázaného termínu "abcdef", jsou všechny považovány za shodu s "abcdef".

#### <a name="substring-matching-on-specific-terms"></a>Párování podřetězců (za určitých podmínek)

Porovnávání podřetězců se používá u normalizovaného hesla ke kontrole křestního jména a příjmení uživatele a názvu klienta (všimněte si, že při ověřování hesel na řadiči domény služby Active Directory se neprovádí párování názvů klienta).

Příklad: předpokládejme, že máme uživatele, Pol, který chce obnovit své heslo na "P0l123fb". Po normalizaci by se toto heslo stalo "pol123fb". Porovnávání podřetězců zjistí, že heslo obsahuje křestní jméno uživatele "Pol". I když "P0l123fb" nebyl konkrétně na seznamu zakázaných hesel, podřetězec odpovídající našel "Pol" v hesle. Proto by toto heslo bylo odmítnuto.

#### <a name="score-calculation"></a>Výpočet skóre

Dalším krokem je identifikace všech výskytů zakázaných hesel v novém novém hesle uživatele. Potom:

1. Každé zakázané heslo, které se nachází v uživatelském hesle, je přiděleno jeden bod.
2. Každý zbývající jedinečný znak je uveden jeden bod.
3. Heslo musí být alespoň pět (5) bodů, aby bylo přijato.

Pro další dva příklady předpokládejme, že Contoso používá Azure AD Password Protection a má "contoso" na jejich vlastní seznam. Předpokládejme také, že "prázdné" je na globálním seznamu.

Příklad: uživatel změní své heslo na "C0ntos0Blank12"

Po normalizaci se toto heslo stane "contosoblank12". Proces párování zjistí, že toto heslo obsahuje dvě zakázaná hesla: contoso a prázdné. Toto heslo je pak uveden skóre:

[contoso] + [blank] + [1] + [2] = 4 body Vzhledem k tomu, že toto heslo je pod pěti (5) body, bude odmítnuto.

Příklad: uživatel změní svéContoS0Bl@nkf9heslo na "!".

Po normalizaci se toto heslo stane "contosoblankf9!". Proces párování zjistí, že toto heslo obsahuje dvě zakázaná hesla: contoso a prázdné. Toto heslo je pak uveden skóre:

[contoso] + [blank] + [f] + [9] + [!] = 5 bodů Vzhledem k tomu, že toto heslo je alespoň pět (5) bodů, je přijato.

   > [!IMPORTANT]
   > Vezměte prosím na vědomí, že algoritmus zakázaných hesel spolu s globálním seznamem se může kdykoli změnit v Azure na základě průběžné analýzy zabezpečení a výzkumu. Pro místní službu agenta řadiče domény aktualizované algoritmy se projeví až po opětovné instalaci softwaru agenta řadiče domény.

## <a name="license-requirements"></a>Licenční požadavky

|   | Ochrana heslem Azure AD s globálním seznamem zakázaných hesel | Ochrana heslem Azure AD s vlastním seznamem zakázaných hesel|
| --- | --- | --- |
| Uživatelé pouze pro cloud | Azure AD Free | Azure AD Premium P1 nebo P2 |
| Uživatelé synchronizovaní z místní služby Windows Server Active Directory | Azure AD Premium P1 nebo P2 | Azure AD Premium P1 nebo P2 |

> [!NOTE]
> Místní uživatelé služby Windows Server Active Directory, kteří nejsou synchronizováni se službou Azure Active Directory, také těží z ochrany heslem služby Azure AD na základě existujících licencí pro synchronizované uživatele.

Další informace o licencích, včetně nákladů, najdete na [cenovém webu Služby Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="what-do-users-see"></a>Co vidí uživatelé

Když se uživatel pokusí obnovit heslo na něco, co by bylo zakázáno, zobrazí se následující chybová zpráva:

Bohužel, vaše heslo obsahuje slovo, frázi nebo vzor, který umožňuje heslo snadno uhodnout. Zkuste to prosím znovu s jiným heslem.

## <a name="next-steps"></a>Další kroky

- [Konfigurace seznamu vlastních zakázaných hesel](howto-password-ban-bad.md)
- [Povolení místních agentů ochrany heslem Azure AD](howto-password-ban-bad-on-premises-deploy.md)
