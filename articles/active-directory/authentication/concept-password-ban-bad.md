---
title: Dynamicky Zakázaní. hesla ve službě Azure AD
description: Zakázat slabý hesla ze svého prostředí pomocí hesel služby Azure AD dynamicky Zakázaní.
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 06/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: rogoya
ms.openlocfilehash: 89cbe386d87c6ccb81df7fabd86b197bb69e41e1
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2018
ms.locfileid: "36295607"
---
# <a name="eliminate-bad-passwords-in-your-organization"></a>Odstranění chybná hesla ve vaší organizaci

|     |
| --- |
| Ochrana heslem Azure AD a seznamu zakázaných vlastního hesla jsou funkce verze public preview služby Azure Active Directory. Další informace o verze Preview najdete v tématu [doplňkové podmínky použití pro Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Vedoucími zjistíte nepoužívejte stejné heslo v několika místech, aby složitých a není vytvoření jednoduché jako Password123. Jak může organizace zaručit, že uživatelé jsou pokynů? Jak se zajistit, že uživatelé nejsou s použitím běžných hesel nebo hesel, která jsou známá, mají být zahrnuty v poslední úniky dat?

## <a name="global-banned-password-list"></a>Seznam globální zakázaného hesel

Společnost Microsoft pracuje vždy zůstala krok před zločinci. Proto tým služby Azure AD Identity Protection průběžně běžně používané a ohroženými hesla vyhledat. Potom blokují těchto hesla, které se považují za příliš běžné v co se nazývá seznamu globální zakázaného heslo. Zločinci také použít podobné strategie při jejich útoku, proto společnost Microsoft nepublikuje obsah tohoto seznamu veřejně. Tato citlivé hesla jsou zablokovány, předtím, než vstoupí skutečné hrozby zákazníkům společnosti Microsoft. Další informace o aktuální úsilí zabezpečení najdete v tématu [sestavy Intelligence zabezpečení Microsoft](https://www.microsoft.com/security/intelligence-report).

## <a name="preview-custom-banned-password-list"></a>Ve verzi Preview: Vlastní zakázané seznamu heslo

Některé organizace chtít provést další krok zabezpečení jeden přidáním vlastních úprav nad seznamu globální zakázaného heslo v co Microsoft volá seznamu zakázaných vlastního hesla. Podnikoví zákazníci jako Contoso může zvolte blokování variant jejich názvy značky, podmínky specifické pro společnost nebo jiných položek.

Vlastní zakázané seznamu hesla a možnost povolit v místní službě Active Directory integrace je spravován pomocí portálu Azure.

![Upravte seznam zakázaných vlastního hesla v rámci metod ověřování na portálu Azure](./media/concept-password-ban-bad/authentication-methods-password-protection.png)

## <a name="on-premises-hybrid-scenarios"></a>Místní hybridní scénáře

Ochrana jen cloudové účty je užitečné, ale celá řada organizací udržovat hybridní scénáře, včetně místní Windows Server Active Directory. Je možné nainstalovat Azure AD ochrana heslem pro Windows Server Active Directory (preview) agenty místní rozšíření seznamů zakázaných heslo vaší stávající infrastruktuře. Teď uživatelům a správcům, kteří změnit, nastavte ani resetovat hesla jsou místní musí být v souladu se zásadami stejné heslo jako uživatele jenom pro cloud.

## <a name="how-does-the-banned-password-list-work"></a>Jak funguje seznamu zakázaných heslo

Převod řetězce na malá písmena a porovnáním různých známé zakázaného hesla v rámci je upravit vzdálenost 1 s přibližné shody odpovídá seznamu zakázaných heslo hesla v seznamu.

Příklad: Slova heslo je blokován v organizaci
   - Uživatel se pokusí nastavte své heslo na "P@ssword", jsou převedeny na "password" a protože se jedná o hodnotu typu variant hesla je blokovaná.
   - Správce, pokusí se nastavit heslo uživatele k "Password123!" kterou převést na "password123!" a protože se jedná hodnotu typu variant hesla je blokovaná.

Pokaždé, když uživatel resetuje nebo změny hesla Azure AD, které ven prochází přes tento proces potvrďte, že není v seznamu zakázaných heslo. Tato kontrola je součástí hybridní scénáře použití hesla pomocí samoobslužné služby resetováno, synchronizace hodnot hash hesel a předávací ověřování.

## <a name="license-requirements"></a>Licenční požadavky

Výhody seznamu globální zakázaného heslo platí pro všechny uživatele služby Azure Active Directory (Azure AD).

Seznam zakázaných vlastního hesla vyžaduje Azure AD Basic licence.

Heslo ochrany služby Azure AD pro Windows Server Active Directory vyžaduje licence Azure AD Premium. 

Další licenční informace, včetně náklady, naleznete na [Azure Active Directory ceny lokality](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="what-do-users-see"></a>Co vidí uživatelé

Když se uživatel pokusí resetovat heslo na jinou hodnotu, která může být vyloučen, zobrazí se následující chybová zpráva:

Bohužel heslo obsahuje slova, fráze nebo vzor, který umožňuje snadno uhodnutelných heslo. Zkuste to prosím znovu s jiným heslem.

## <a name="next-steps"></a>Další postup

* [Konfigurace seznamu zakázaných vlastního hesla](howto-password-ban-bad.md)
* [Povolení služby Azure AD heslo ochrany agentů na místě](howto-password-ban-bad-on-premises.md)
