---
title: Hledání a filtrování skupin členů a vlastníků (preview) – Azure Active Directory | Dokumenty společnosti Microsoft
description: Vyhledávání a filtrování skupin členů a vlastníků na webu Azure Portal.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 02/28/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a815446b79b3e5ec0a75e5d179953956643b16c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206108"
---
# <a name="search-groups-and-members-preview-in-azure-active-directory"></a>Vyhledávací skupiny a členové (preview) ve službě Azure Active Directory

Tento článek vám řekne, jak hledat členy a vlastníky skupiny a jak používat vyhledávací filtry jako součást náhledu vylepšení skupin na portálu Azure Active Directory (Azure AD). Existuje mnoho vylepšení ve skupinách prostředí, které vám pomohou spravovat vaše skupiny, včetně členů a vlastníků, rychle a snadno. Další informace o náhledech najdete v [tématu Doplňkové podmínky použití pro Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Změny v tomto náhledu zahrnují:

- Nové možnosti vyhledávání skupin, například vyhledávání podřetězců v názvech skupin
- Nové možnosti filtrování a řazení v seznamech členů a vlastníků
- Nové možnosti vyhledávání pro seznamy členů a vlastníků
- Přesnější počet skupin pro velké skupiny

## <a name="enabling-and-managing-the-preview"></a>Povolení a správa náhledu

Usnadnili jsme připojení k náhledu:

  1. Přihlaste se k [portálu Azure AD](https://portal.azure.com)a vyberte **Skupiny**.
  2. Na stránce Skupiny – všechny skupiny vyberte banner v horní části stránky, abyste se připojili k náhledu.

Nejnovější funkce a vylepšení můžete také zobrazit výběrem **náhledu na** stránce **Všechny skupiny.** Po připojení k náhledu uvidíte značku náhledu na všech stránkách skupin, které mají vylepšení a jsou součástí náhledu. Ne všechny stránky skupin y byly v rámci tohoto náhledu aktualizovány.

Pokud máte nějaké problémy, můžete přepnout zpět starší verzi prostředí výběrem banner v horní části stránky **Všechny skupiny.** Vážíme si vaší zpětné vazby, abychom mohli zlepšit naše zkušenosti.

## <a name="group-search-and-sorting"></a>Skupinové vyhledávání a řazení

Hledání seznamu skupin bylo vylepšeno tak, že když můžete zadat vyhledávací `startswith` řetězec, hledání automaticky provede hledání a podřetězec v seznamu názvů skupin. Hledání podřetězce se provádí pouze u celých slov a neobsahuje speciální znaky. Hledání podřetězců rozlišuje malá a velká písmena.

![hledání nového podřetězce na stránce Všechny skupiny](./media/groups-members-owners-search/groups-search-preview.png)

Například hledání "zásady" nyní vrátí jak "zásady MDM – západní" a "skupiny zásad". Skupina s názvem "New_policy" by nebyla vrácena.

- Stejné vyhledávání můžete provádět také v seznamech členů skupiny.
- Seznam skupin můžete nyní seřadit podle názvu pomocí šipek vpravo od záhlaví sloupce názvu a seřadit seznam vzestupně nebo sestupně.

## <a name="group-member-search-and-filtering"></a>Vyhledávání a filtrování členů skupiny

### <a name="search-group-member-and-owner-lists"></a>Seznam členů a vlastníků vyhledávací skupiny

Nyní můžete prohledávat členy určité skupiny podle názvu a provádět stejné vyhledávání v seznamu vlastníků skupiny. V novém prostředí, pokud zadáte řetězec do vyhledávacího pole, spustí se hledání automaticky. Například hledání "Scott" vrátí Scott Wilkinson.

![hledání nového podřetězce v seznamech členů a vlastníků skupiny](./media/groups-members-owners-search/members-list.png)

### <a name="filter-member-and-owners-list"></a>Seznam členů a vlastníků filtru

Kromě vyhledávání můžete nyní filtrovat seznamy členů a vlastníků podle typu uživatele. Toto jsou informace nalezené ve sloupci Typ uživatele v seznamu. Takže můžete filtrovat seznam podle členů a hostů, abyste zjistili, zda jsou ve skupině hosté.

### <a name="view-and-manage-membership"></a>Zobrazení a správa členství

Kromě zobrazení přímých členů určité skupiny můžete nyní zobrazit seznam všech členů skupiny na stránce Členové. Seznam členů obsahuje všechny jedinečné členy skupiny včetně všech tranzitivních členů.

Můžete také vyhledávat a filtrovat seznam přímých členů a seznam všech členů jednotlivě. Filtrování seznamu všech členů nemá vliv na filtry použité v seznamu přímých členů.

## <a name="improved-group-member-counts"></a>Vylepšené počty členů skupiny

Vylepšili jsme stránku **Přehled** skupiny tak, aby poskytovala počty členů skupiny pro skupiny všech velikostí. Počet členů se zobrazí i pro skupiny s více než 1 000 členy. Nyní můžete zobrazit celkový počet přímých členů pro skupinu a celkový počet členů (všichni unikátní členové skupiny včetně přenositých členů) na stránce **Přehled.**

![Vyšší přesnost ve skupině se počítá](./media/groups-members-owners-search/member-numbers.png)

## <a name="next-steps"></a>Další kroky

Tyto články poskytují další informace o práci se skupinami ve službě Azure AD.

- [Zobrazení skupin a členů](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Správa členství ve skupinách](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Správa dynamických pravidel pro uživatele ve skupině](groups-create-rule.md)
- [Úprava nastavení skupiny](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Správa přístupu k prostředkům pomocí skupin](../fundamentals/active-directory-manage-groups.md)
- [Správa přístupu k aplikacím SaaS pomocí skupin](groups-saasapps.md)
- [Správa skupin pomocí powershellových příkazů](groups-settings-v2-cmdlets.md)
- [Přidání předplatného Azure do Služby Azure Active Directory](../fundamentals/active-directory-how-subscriptions-associated-directory.md)
