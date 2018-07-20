---
title: Dynamicky zakázaná hesla ve službě Azure AD
description: Zakázat slabé heslo z vašeho prostředí pomocí Azure AD dynamicky zakázaná hesla
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: rogoya
ms.openlocfilehash: dfeacb266d6aa6a43e49a39bd19c9699ef65ce82
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/19/2018
ms.locfileid: "39162010"
---
# <a name="eliminate-bad-passwords-in-your-organization"></a>Eliminuje chybná hesla ve vaší organizaci

|     |
| --- |
| Ochrana hesel Azure AD a v seznamu vlastních zakázaných hesel jsou funkce ve verzi public preview služby Azure Active Directory. Další informace o verzích Preview najdete v tématu [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Vedoucím postavením zjistíte to usnadňuje složité a ne se dají jednoduše jako/Password123 nepoužívejte stejné heslo na více místech. Jak může organizace zaručit, že sledujete jejich uživatelé doprovodné materiály? Jak se ujistit, že uživatelé nepoužívají společný hesel nebo hesel, která jsou známá, mají být zahrnuty v poslední úniky dat?

## <a name="global-banned-password-list"></a>Seznam globální zakázaných hesel

Microsoft pracuje vždy o krok před kybernetickými zločinci. Proto tým služby Azure AD Identity Protection průběžně běžně používané a ohrožení zabezpečení hesla vyhledat. Potom tato hesla, které se považují za moc známé v co se nazývá seznam zakázaných hesel globální blokují. Kybernetičtí zločinci také používají podobné strategie jejich útocích, proto Microsoft nebude publikován obsah tohoto seznamu veřejně. Tyto citlivá hesla jsou blokovány, než se stanou skutečnou hrozbou zákazníkům společnosti Microsoft. Další informace o aktuální zabezpečení, a to, najdete v článku [Microsoft Security Intelligence Report](https://www.microsoft.com/security/intelligence-report).

## <a name="preview-custom-banned-password-list"></a>Ve verzi Preview: Vlastní zakázané seznam hesel

Některé organizace mohou chtít využít zabezpečení jeden krok dál tak, že přidáte vlastní úpravy na seznamu zakázaných hesel globální v co Microsoft volá seznamu vlastních zakázaných hesel. Podnikoví zákazníci jako Contoso rozhodnout blokovat variant jejich názvy, podmínky specifické pro společnost nebo dalších položek.

Vlastní zakázané seznamu heslo a možnost povolit v místní službě Active Directory integration se spravuje pomocí webu Azure portal.

![Upravit vlastní zakázaných hesel seznamu metod ověřování na webu Azure Portal](./media/concept-password-ban-bad/authentication-methods-password-protection.png)

## <a name="on-premises-hybrid-scenarios"></a>Místní hybridní scénáře

Ochrana účty jenom cloudu je užitečné, ale mnoho organizací udržovat hybridních scénářů, včetně místní Windows Server Active Directory. Je možné nainstalovat ochrany hesla Azure AD pro Windows Server Active Directory (preview) agentů místní rozšířit seznamy zakázaných hesel vaší stávající infrastruktuře. Teď uživatelům a správcům, kteří změnit, nastavte ani resetovat hesla v místním se musí odpovídat zásadám stejné heslo jako uživatelů pouze cloudu.

## <a name="how-does-the-banned-password-list-work"></a>Jak funguje seznam zakázaných hesel

Seznam zakázaných hesel odpovídá hesla v seznamu převodem řetězce na malá písmena a porovnání známé zakázaných hesel v rámci úpravy vzdálenost 1 s přibližné shody.

Příklad: Heslo aplikace word je blokované pro organizaci
   - Uživatel se pokusí své heslo nastavte na "P@ssword", který je převeden na "password" a vzhledem k tomu, že je varianta heslo blokovaný.
   - Správce pokusí se nastavit heslo uživatele k "/ Password123!" který převede na "/ password123!" a protože je blokovaný hodnotu typu variant heslo.

Pokaždé, když uživatel obnoví nebo změně hesla Azure AD, který prochází přes tento proces pro potvrzení, že není na seznamu zakázaných hesel. Tato kontrola je součástí hybridní scénáře s použitím hesla pomocí samoobslužné služby obnovit, synchronizace hodnot hash hesel a předávací ověřování.

## <a name="license-requirements"></a>Licenční požadavky

Výhody seznamu zakázaných hesel globální platí pro všechny uživatele služby Azure Active Directory (Azure AD).

Seznam zakázaných hesel vlastní vyžaduje licence Azure AD Basic.

Ochrana hesel Azure AD pro Windows Server Active Directory vyžaduje licence Azure AD Premium. 

Další licenční informace, včetně nákladů, můžete najít na [cenami služby Azure Active Directory web](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="what-do-users-see"></a>Co vidí uživatelé

Když se uživatel pokusí o resetování hesla na něco, co může být vyloučen, zobrazí se následující chybová zpráva:

Vaše heslo bohužel obsahuje slovo, frázi nebo vzor, který umožňuje snadno uhodnutelných heslo. Zkuste to prosím znovu s jiným heslem.

## <a name="next-steps"></a>Další postup

* [Proveďte konfiguraci seznamu vlastních zakázaných hesel](howto-password-ban-bad.md)
* [Povolení služby Azure AD hesla ochrany agentů místní](howto-password-ban-bad-on-premises.md)
