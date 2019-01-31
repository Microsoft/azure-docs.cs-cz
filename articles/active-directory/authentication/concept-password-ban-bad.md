---
title: Dynamicky zakázaná hesla ve službě Azure AD
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
ms.openlocfilehash: 916ef921bf2ad183e3fb74c640ccfa7049559a72
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55295861"
---
# <a name="eliminate-bad-passwords-in-your-organization"></a>Eliminuje chybná hesla ve vaší organizaci

|     |
| --- |
| Ochrana hesel Azure AD a v seznamu vlastních zakázaných hesel jsou funkce ve verzi public preview služby Azure Active Directory. Další informace o verzích Preview najdete v tématu [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Vedoucím postavením zjistíte to usnadňuje složité a ne se dají jednoduše jako/Password123 nepoužívejte stejné heslo na více místech. Jak může organizace zaručit, že sledujete jejich uživatelé doprovodné materiály? Jak se ujistit, že uživatelé nepoužívají společný hesel nebo hesel, která jsou známá, mají být zahrnuty v poslední úniky dat?

## <a name="global-banned-password-list"></a>Seznam globální zakázaných hesel

Microsoft se vždy snaží být o krok před kybernetickými zločinci. Proto tým služby Azure AD Identity Protection průběžně běžně používané a ohrožení zabezpečení hesla vyhledat. Potom tato hesla, které se považují za moc známé v co se nazývá seznam zakázaných hesel globální blokují. Kybernetičtí zločinci také používají podobné strategie jejich útocích, proto Microsoft nebude publikován obsah tohoto seznamu veřejně. Tyto citlivá hesla jsou blokovány, než se stanou skutečnou hrozbou zákazníkům společnosti Microsoft. Další informace o aktuální zabezpečení, a to, najdete v článku [Microsoft Security Intelligence Report](https://www.microsoft.com/security/operations/security-intelligence-report).

## <a name="preview-custom-banned-password-list"></a>Verze Preview: Vlastní seznam zakázaných hesel

Některé organizace mohou chtít využít zabezpečení jeden krok dál tak, že přidáte vlastní úpravy na seznamu zakázaných hesel globální v co Microsoft volá seznamu vlastních zakázaných hesel. Podnikoví zákazníci jako Contoso rozhodnout blokovat variant jejich názvy, podmínky specifické pro společnost nebo dalších položek.

Vlastní zakázané seznamu heslo a možnost povolit v místní službě Active Directory integration se spravuje pomocí webu Azure portal.

![Upravit vlastní zakázaných hesel seznamu metod ověřování na webu Azure Portal](./media/concept-password-ban-bad/authentication-methods-password-protection.png)

## <a name="on-premises-hybrid-scenarios"></a>Místní hybridní scénáře

Ochrana účty jenom cloudu je užitečné, ale mnoho organizací udržovat hybridních scénářů, včetně místní Windows Server Active Directory. Je možné nainstalovat ochrany hesla Azure AD pro Windows Server Active Directory (preview) agentů místní rozšířit seznamy zakázaných hesel vaší stávající infrastruktuře. Teď uživatelům a správcům, kteří změnit, nastavte ani resetovat hesla v místním se musí odpovídat zásadám stejné heslo jako uživatelů pouze cloudu.

## <a name="how-are-passwords-evaluated"></a>Jak se vyhodnocují hesla

Pokaždé, když uživatel změní nebo může resetovat své heslo, nové heslo se kontroluje u síly a složitost tím, že ověří proti globální a seznam vlastních zakázaných hesel (Pokud je druhá možnost je nakonfigurovaná).

I v případě, že heslo uživatele obsahuje zakázaných hesel, může být heslo stále přijata, když celkové heslo není dostatečně silné jinak. Následující kroky k vyhodnocení celkového silný k určení, pokud by měla přijímat nebo odmítat. budou procházet nově nastavené heslo.

### <a name="step-1-normalization"></a>Krok 1: Normalizace

Nové heslo nejprve prochází procesu normalizace. To umožňuje malého zakázaných hesel má být mapována na mnohem většímu množství potenciálně Slabá hesla.

Normalizace má dvě části.  První, všechna písmena velká písmena se změní na malá písmena.  Druhý, běžné znak nahrazení jsou prováděny, například:  

| Původní písmeno  | Nahrazeny písmeno |
| --- | --- |
| '0'  | "jednoznakový |
| '1'  | 'l' |
| '$'  | společnosti. |
| '@'  | "a" |

Příklad: Předpokládejme, že heslo "prázdný" je zakázané a uživatel se pokusí změnit své heslo k "Bl@nK". I když "Bl@nk" se konkrétně zkoumaly zakázané, převede procesu normalizace toto heslo "blank", což je zakázaných hesel.

### <a name="step-2-check-if-password-is-considered-banned"></a>Krok 2: Zkontrolujte, zda heslo je považován za zakázané

#### <a name="fuzzy-matching-behavior"></a>Přibližné odpovídající chování

Přibližné shody se používá v normalizovaných heslo a zjistěte, jestli obsahuje heslo na buď globální nebo vlastní zakázané seznamy heslo. Proces vyhledávání shody podle o úpravy vzdálenost porovnání jeden (1).  

Příklad: Předpokládejme, že heslo "abcdef" je zakázané a uživatel se pokusí změnit své heslo k jednomu z následujících akcí:

"abcdeg"    *(poslední znak změněno z "f" k "g")* "abcdefg"   *"(g" připojených do konce)* "abcde"     *(koncové 'f' byla odstraněna z end)*

Každý z výše uvedených hesla se neshoduje s konkrétně zakázaných hesel "abcdef". Ale protože každý příklad v rámci úpravy vzdálenost 1 zakázané token "abcdef", jsou všechny považují se za shodu pro "abcdef".

#### <a name="substring-matching-on-specific-terms"></a>Podřetězec odpovídající (podle konkrétních potřeb)

Porovnání podřetězců se používá v normalizovaných heslo zkontrolujte, zda uživatel je první a poslední pojmenovat stejně jako název tenanta (Všimněte si, že shoda názvu tenanta není Hotovo při ověřování hesla na řadič domény služby Active Directory).

Příklad: Předpokládejme, že máme uživatele John Doe, který chce resetovat své heslo k "J0hn123fb". Po normalizace toto heslo by se mohla stát "john123fb". Porovnání podřetězců zjistí, že heslo obsahuje jméno uživatele "John". I v případě, že "J0hn123fb" nebyl konkrétně na buď seznam zakázaných hesel, odpovídající podřetězec nalezen "John" v hesle. Proto by odmítnuty toto heslo.

#### <a name="score-calculation"></a>Výpočet skóre

Dalším krokem je identifikace všechny výskyty zakázaných hesel v normalizovaných nové heslo uživatele. Potom:

1. Každý zakázaných hesel, která se nachází v heslo uživatele se přiřadí jeden bod.
2. Každý zbývající jedinečný znak se přiřadí jeden bod.
3. Heslo musí obsahovat aspoň 5 body, aby se přijmout.

Následující dva příklady předpokládejme, že Contoso je ochrana hesel Azure AD a má "contoso" na jejich vlastní seznam. Také Předpokládejme, že "prázdný" je na globální seznam.

Příklad: uživatel změní heslo pro "C0ntos0Blank12"

Po normalizace stane se toto heslo "contosoblank12". Proces vyhledávání shody zjistí, že toto heslo obsahuje dvě hesla zakázané: contoso a prázdné. Toto heslo je pak získají skóre:

[contoso] + [prázdné] [1] = + [2] = 4 body vzhledem k tomu, že toto heslo se v části 5 body, dojde k odmítnutí.

Příklad: uživatel změní heslo pro "ContoS0Bl@nkf9!".

Po normalizace stane se toto heslo "contosoblankf9!". Proces vyhledávání shody zjistí, že toto heslo obsahuje dvě hesla zakázané: contoso a prázdné. Toto heslo je pak získají skóre:

[contoso] + [prázdné] + [f] + [9] + [!] = 5 body toto heslo je minimálně 5 body, je přijmout.

   > [!IMPORTANT]
   > Mějte prosím na paměti, že algoritmus zakázaných hesel spolu s globálního seznamu můžete a změnit kdykoli v Azure na základě analýzy průběžné zabezpečení a výzkumu. Pro službu agenta místní řadič domény aktualizované algoritmy se projeví po softwaru agenta řadiče domény se znovu nainstaluje.

## <a name="license-requirements"></a>Licenční požadavky

|   | Ochrana hesel Azure AD pomocí seznamu globální zakázaných hesel | Ochrana hesel Azure AD pomocí seznamu vlastních zakázaných hesel|
| --- | --- | --- |
| Uživatelů pouze cloudu | Azure AD Free | Azure AD Basic |
| Uživatelé, které jsou synchronizované z místní Windows Server Active Directory | Azure AD Premium P1 nebo P2 | Azure AD Premium P1 nebo P2 |

Další licenční informace, včetně nákladů, můžete najít na [cenami služby Azure Active Directory web](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="what-do-users-see"></a>Co vidí uživatelé

Když se uživatel pokusí o resetování hesla na něco, co může být vyloučen, zobrazí se následující chybová zpráva:

Vaše heslo bohužel obsahuje slovo, frázi nebo vzor, který umožňuje snadno uhodnutelných heslo. Zkuste to prosím znovu s jiným heslem.

## <a name="next-steps"></a>Další postup

* [Proveďte konfiguraci seznamu vlastních zakázaných hesel](howto-password-ban-bad.md)
* [Povolení služby Azure AD hesla ochrany agentů místní](howto-password-ban-bad-on-premises-deploy.md)
