---
title: Dynamicky zakázaná hesla – Azure Active Directory
description: Zakázat slabé heslo z vašeho prostředí pomocí Azure AD dynamicky zakázaná hesla
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: c043b2ed1a626e362d7edd1a83429aa14046f8ac
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2019
ms.locfileid: "67703062"
---
# <a name="eliminate-bad-passwords-in-your-organization"></a>Eliminuje chybná hesla ve vaší organizaci

Vedoucím postavením zjistit nechcete použít stejné heslo na několika místech, aby komplexní a ne se dají jednoduše jako "/"Password123. Jak může organizace zaručit, že jejich uživatelé jsou následující doporučené postupy pro moduly? Jak se ujistit, že uživatelé nepoužívají Slabá hesla nebo dokonce odchylky na Slabá hesla?

V prvním kroku tím, že volba bezpečnějších hesel je můžete svým uživatelům poskytnout pokyny. Aktuální pokyny Microsoftu k tomuto tématu najdete na následující odkaz:

[Pokyny Microsoftu heslo](https://www.microsoft.com/research/publication/password-guidance)

Dobrý návod je důležité, ale i s víme, že mnoho uživatelů bude stále skončit výběr Slabá hesla. Azure AD hesla ochrana chrání vaše organizace zjištění a blokování známé Slabá hesla a jejich variant, stejně jako volitelně blokování další slabé podmínky, které jsou specifické pro vaši organizaci.

Další informace o aktuální zabezpečení, a to, najdete v článku [Microsoft Security Intelligence Report](https://www.microsoft.com/security/operations/security-intelligence-report).

## <a name="global-banned-password-list"></a>Seznam globální zakázaných hesel

Tým služby Azure AD Identity Protection neustále analyzuje data hledání pro běžně používané slabá nebo ohrožení zabezpečení hesla nebo přesněji řečeno, weak základní podmínky, které často slouží jako základ pro slabá hesla telemetrie zabezpečení Azure AD. Pokud se nenajdou tyto slabé podmínky, se přidají do seznamu zakázaných hesel globální. Obsah seznamu zakázaných hesel globální nejsou založena na žádný externí zdroj dat. Seznam globální zakázaných hesel je zcela podle průběžné výsledky telemetrie zabezpečení Azure AD a analýzy.

Pokaždé, když se nové heslo se změněným nebo resetovaným pro všechny uživatele ve žádného tenanta ve službě Azure AD, aktuální verzi seznamu globální zakázaných hesel slouží jako klíč vstup při ověření síly hesla. Toto ověření za následek mnohem silnější hesla pro všechny zákazníky Azure AD.

> [!NOTE]
> Kybernetičtí zločinci také používají podobné strategie jejich útocích. Proto Microsoft Nepublikujte obsah tohoto seznamu veřejně.

## <a name="custom-banned-password-list"></a>Vlastní zakázané seznam hesel

V některých organizacích může být vhodné pro zlepšení zabezpečení ještě více tak, že přidáte vlastní úpravy na seznamu zakázaných hesel globální v co Microsoft volá seznamu vlastních zakázaných hesel. Společnost Microsoft doporučuje, aby podmínky přidat do tohoto seznamu jsou primárně zaměřen na podmínky specifické pro organizaci, jako:

- Názvy
- Názvy produktů
- (Například třeba sídle společnosti)
- Interní podmínky specifické pro společnost
- Zkratky, které mají určité společnosti, což znamená.

Jakmile podmínky jsou přidány do seznamu vlastních zakázaných hesel, že bude přidána do seznamu zakázaných hesel globální při ověřování hesla.

> [!NOTE]
> Seznam vlastních zakázaných hesel je omezen na maximálně 1000 podmínky. Není určená pro blokování velmi rozsáhlých seznamů hesel. Abyste mohli plně využít výhod seznamu vlastních zakázaných hesel, společnost Microsoft doporučuje, abyste nejdřív zkontrolovali a seznamte se s algoritmus vyhodnocení heslo (naleznete v tématu [jak se vyhodnocují hesla](concept-password-ban-bad.md#how-are-passwords-evaluated)) před přidáním nové podmínky, abyste mohli vlastní seznam zakázaných. Ke zjištění, jak funguje algoritmus umožní vaší organizace efektivně detekovat a blokovat velkého počtu Slabá hesla a jejich variant.

Příklad: Zákazník s názvem "Contoso", který je založen v Londýně, a díky tomu se produkt s názvem "Widget" vezměte v úvahu. Pro zákazníky bylo by plýtvání, jakož i méně bezpečné se pokouší blokovat konkrétní varianty těchto podmínek jako například:

- "Contoso! 1"
- "Contoso@London"
- "ContosoWidget"
- "!Contoso"
- "LondonHQ"
- ...etcetera

Místo toho je mnohem efektivnější a zabezpečené blokovat pouze klíčové základní pojmy:

- "Contoso"
- "Londýn"
- "Widget"

Algoritmus pro ověření hesla se pak automaticky blokovat slabé variant a kombinace výše.

Vlastní zakázané seznamu heslo a možnost povolit v místní službě Active Directory integration se spravuje pomocí webu Azure portal.

![Upravte seznam vlastních zakázaných hesel v rámci metody ověřování](./media/concept-password-ban-bad/authentication-methods-password-protection.png)

## <a name="password-spray-attacks-and-third-party-compromised-password-lists"></a>Heslo zařízení útoky a seznamy ohrožení zabezpečení hesla třetích stran

Jeden klíč výhody ochrany hesla Azure AD je pomoci vám bránit proti útokům na hesla zařízení. Většina útoků heslo zařízení nebude pokoušet o útoku jakékoli dané individuálního účtu více než několikrát, protože takové chování výrazně zvyšuje pravděpodobnost detekce, přes uzamčení účtu nebo jiným způsobem. Většina útoků heslo zařízení se proto spoléhají na odesílání pouze malý počet známých nejslabší hesla pro všechny tyto účty v organizaci. Tato technika umožňuje útočníkovi rychle vyhledat snadno ohrožení bezpečnosti účtu, zatímco ve stejnou dobu, jak se vyhnout potenciálním detekce prahové hodnoty.

Ochrana hesel Azure AD je navržena pro efektivní blokování všechny známé Slabá hesla, které se pravděpodobně použijí v útoky zařízení heslo podle skutečných zabezpečení telemetrická data, jak je vidět ve službě Azure AD.  Microsoft si je vědoma webů třetích stran, které výčet miliony hesla, které jsou ohrožené v předchozí veřejně bezpečnostními porušení. Je běžné pro produkty třetích stran heslo ověřování založený na porovnání tyto milióny hesla hrubou silou. Microsoft domnívá, že tyto metody nejsou nejlepším způsobem, jak zlepšit sílu hesla celkové uvedené typické strategie používají útočníci heslo zařízení.

> [!NOTE]
> Microsoft, seznam zakázaných hesel globální není založen na všechna data třetích stran zdrojů, včetně seznamů ohrožení zabezpečení hesla.

I když globální seznam Microsoft zakázané je malá porovnání některé seznamy hromadné třetích stran, jsou fakt, že pochází z každodenní praxe zabezpečení telemetrických dat na útoky skutečná hesla zařízení a navíc skutečnost rozšířena její vliv na zabezpečení, které Microsoft algoritmus pro ověření hesla využívá i metody inteligentní přibližné shody. Konečný výsledek je, že bude efektivně detekovat a blokovat miliony nejběžnější Slabá hesla z používán ve vašem podniku. Zákazníci, kteří se rozhodli přidat do seznamu zakázaných hesel vlastní podmínky specifické pro organizaci také využívat stejný algoritmus.

## <a name="on-premises-hybrid-scenarios"></a>Místní hybridní scénáře

Ochrana účty jenom cloudu je užitečné, ale mnoho organizací udržovat hybridních scénářů, včetně místní Windows Server Active Directory. Je možné nainstalovat ochrany hesla Azure AD pro Windows Server Active Directory agentů místní rozšířit seznamy zakázaných hesel vaší stávající infrastruktuře. Teď uživatelům a správcům, kteří změnit, nastavte ani resetovat hesla v místním se musí odpovídat zásadám stejné heslo jako uživatelů pouze cloudu.

## <a name="how-are-passwords-evaluated"></a>Jak se vyhodnocují hesla

Pokaždé, když uživatel změní nebo může resetovat své heslo, nové heslo se kontroluje u síly a složitost tím, že ověří proti globální a seznam vlastních zakázaných hesel (Pokud je druhá možnost je nakonfigurovaná).

I v případě, že heslo uživatele obsahuje zakázaných hesel, může být heslo stále přijata, když celkové heslo není dostatečně silné jinak. Následující kroky k vyhodnocení celkového silný k určení, pokud by měla přijímat nebo odmítat. budou procházet nově nastavené heslo.

### <a name="step-1-normalization"></a>Krok 1: Normalizace

Nové heslo nejprve prochází procesu normalizace. Tato technika umožňuje malého zakázaných hesel má být mapována na mnohem většímu množství potenciálně Slabá hesla.

Normalizace má dvě části.  První, všechna písmena velká písmena se změní na malá písmena.  Druhý, běžné znak nahrazení jsou prováděny, například:  

| Původní písmeno  | Nahrazeny písmeno |
| --- | --- |
| '0'  | "jednoznakový |
| '1'  | 'l' |
| '$'  | společnosti. |
| '\@'  | "a" |

Příklad: Předpokládejme, že heslo "prázdný" je zakázané a uživatel se pokusí změnit své heslo k "Bl@nK". I když "Bl@nk" se konkrétně zkoumaly zakázané, převede procesu normalizace toto heslo "blank", což je zakázaných hesel.

### <a name="step-2-check-if-password-is-considered-banned"></a>Krok 2: Zkontrolujte, zda heslo je považován za zakázané

#### <a name="fuzzy-matching-behavior"></a>Přibližné odpovídající chování

Přibližné shody se používá v normalizovaných heslo a zjistěte, jestli obsahuje heslo na buď globální nebo vlastní zakázané seznamy heslo. Proces vyhledávání shody podle o úpravy vzdálenost porovnání jeden (1).  

Příklad: Předpokládejme, že heslo "abcdef" je zakázané a uživatel se pokusí změnit své heslo k jednomu z následujících akcí:

"abcdeg"    *(poslední znak změněno z "f" k "g")* "abcdefg"   *"(g" připojených do konce)* "abcde"     *(koncové 'f' byla odstraněna z end)*

Každý z výše uvedených hesla se neshoduje s konkrétně zakázaných hesel "abcdef". Ale protože každý příklad v rámci úpravy vzdálenost 1 zakázané termín "abcdef", jsou všechny považují se za shodu pro "abcdef".

#### <a name="substring-matching-on-specific-terms"></a>Podřetězec odpovídající (podle konkrétních potřeb)

Porovnání podřetězců se používá v normalizovaných heslo zkontrolujte, zda uživatel je první a poslední pojmenovat stejně jako název tenanta (Všimněte si, že shoda názvu tenanta není Hotovo při ověřování hesla na řadič domény služby Active Directory).

Příklad: Předpokládejme, že máme uživatele, Pol, kdo chce, aby se resetování hesla na "P0l123fb". Po normalizace toto heslo by se mohla stát "pol123fb". Porovnání podřetězců zjistí, že heslo obsahuje jméno uživatele "Pol". I v případě, že "P0l123fb" nebyl konkrétně na buď seznam zakázaných hesel, odpovídající podřetězec nalezen "Pol" v hesle. Proto by odmítnuty toto heslo.

#### <a name="score-calculation"></a>Výpočet skóre

Dalším krokem je identifikace všechny výskyty zakázaných hesel v normalizovaných nové heslo uživatele. Potom:

1. Každý zakázaných hesel, která se nachází v heslo uživatele se přiřadí jeden bod.
2. Každý zbývající jedinečný znak se přiřadí jeden bod.
3. Heslo musí být body alespoň pět (5), aby se přijmout.

Následující dva příklady předpokládejme, že Contoso je ochrana hesel Azure AD a má "contoso" na jejich vlastní seznam. Také Předpokládejme, že "prázdný" je na globální seznam.

Příklad: uživatel změní heslo pro "C0ntos0Blank12"

Po normalizace stane se toto heslo "contosoblank12". Proces vyhledávání shody zjistí, že toto heslo obsahuje dvě hesla zakázané: contoso a prázdné. Toto heslo je pak získají skóre:

[contoso] + [prázdné] + [1] + [2] = 4 body vzhledem k tomu, že toto heslo se v části pět (5) body, dojde k odmítnutí.

Příklad: uživatel změní heslo pro "ContoS0Bl@nkf9!".

Po normalizace stane se toto heslo "contosoblankf9!". Proces vyhledávání shody zjistí, že toto heslo obsahuje dvě hesla zakázané: contoso a prázdné. Toto heslo je pak získají skóre:

[contoso] + [prázdné] + [f] + [9] + [!] = 5 bodů tohoto hesla je alespoň pět (5) body, je přijmout.

   > [!IMPORTANT]
   > Mějte prosím na paměti, že algoritmus zakázaných hesel spolu s globálního seznamu můžete a změnit kdykoli v Azure na základě analýzy průběžné zabezpečení a výzkumu. Pro službu agenta řadiče domény s místními aktualizované algoritmy se projeví po softwaru agenta řadiče domény se znovu nainstaluje.

## <a name="license-requirements"></a>Licenční požadavky

|   | Ochrana hesel Azure AD pomocí seznamu globální zakázaných hesel | Ochrana hesel Azure AD pomocí seznamu vlastních zakázaných hesel|
| --- | --- | --- |
| Uživatelů pouze cloudu | Azure AD Free | Azure AD Premium P1 nebo P2 |
| Uživatelé, které jsou synchronizované z místní Windows Server Active Directory | Azure AD Premium P1 nebo P2 | Azure AD Premium P1 nebo P2 |

> [!NOTE]
> Místní Windows Server Active Directory, kteří nejsou synchronizovány do Azure Active Directory také využít výhody ochrany hesla Azure AD podle stávajících licencí pro synchronizovaní uživatelé.

Další licenční informace, včetně nákladů, můžete najít na [cenami služby Azure Active Directory web](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="what-do-users-see"></a>Co vidí uživatelé

Když se uživatel pokusí o resetování hesla na něco, co může být vyloučen, zobrazí se následující chybová zpráva:

Vaše heslo bohužel obsahuje slovo, frázi nebo vzor, který umožňuje snadno uhodnutelných heslo. Zkuste to prosím znovu s jiným heslem.

## <a name="next-steps"></a>Další postup

- [Proveďte konfiguraci seznamu vlastních zakázaných hesel](howto-password-ban-bad.md)
- [Povolení služby Azure AD hesla ochrany agentů místní](howto-password-ban-bad-on-premises-deploy.md)
