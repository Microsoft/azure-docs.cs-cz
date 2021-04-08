---
title: Hledání a filtrování členů a vlastníků skupin (Preview) – Azure Active Directory | Microsoft Docs
description: Hledání a filtrování členů a vlastníků skupin v Azure Portal.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d78a2a4e0f14d99a7a1ecada915857f59422bb58
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96547368"
---
# <a name="search-groups-and-members-preview-in-azure-active-directory"></a>Hledat skupiny a členy (Preview) v Azure Active Directory

V tomto článku se dozvíte, jak vyhledat členy a vlastníky skupiny a jak používat vyhledávací filtry jako součást zlepšení ve verzi Preview v portálu Azure Active Directory (Azure AD). Existují spousty vylepšení v prostředí skupiny, které vám pomůžou spravovat skupiny, včetně členů a vlastníků, rychle a snadno. Další informace o verzi Preview najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)verze Preview.

Změny v této verzi Preview zahrnují:

- Nové skupiny hledají možnosti hledání, například hledání v podřetězcůch v názvech skupin.
- Nové možnosti filtrování a řazení u seznamů členů a vlastníků
- Nové možnosti vyhledávání pro seznamy členů a vlastníků
- Přesnější počty skupin pro velké skupiny

## <a name="enabling-and-managing-the-preview"></a>Povolení a správa verze Preview

Provedli jsme snadné připojení k verzi Preview:

  1. Přihlaste se k [portálu Azure AD](https://portal.azure.com)a vyberte **skupiny**.
  2. Na stránce skupiny – všechny skupiny vyberte banner v horní části stránky a připojte se k verzi Preview.

Nejnovější funkce a vylepšení můžete zobrazit také tak, že na stránce **všechny skupiny** vyberete možnost **informace o verzi Preview** . Po připojení ke službě Preview uvidíte značku Preview na všech stránkách, které mají vylepšení a jsou součástí verze Preview. V rámci této verze Preview se neaktualizovala stránka všechny skupiny.

Pokud máte nějaké problémy, můžete předchozí možnosti přepnout zpátky tak, že v horní části stránky **všechny skupiny** vyberete banner. Vážíme si vašich názorů, abychom mohli vylepšit naše prostředí.

## <a name="group-search-and-sorting"></a>Hledání a řazení skupin

Hledání v seznamu skupin bylo vylepšeno, takže když můžete zadat hledaný řetězec, hledání provede v `startswith` seznamu názvů skupin automatické vyhledávání a dílčí řetězce. Hledání podřetězců je provedeno pouze na celá slova a neobsahuje speciální znaky. Při hledání podřetězců se rozlišují velká a malá písmena.

![Nová dílčí řetězcová vyhledává na stránce všechny skupiny.](./media/groups-members-owners-search/groups-search-preview.png)

Hledání "zásady" teď například vrátí "zásady MDM – Západ" i "skupina zásad". Skupina s názvem "New_policy" nebyla vrácena.

- Můžete také provádět stejné hledání v seznamech členství ve skupině.
- Seznam skupin teď můžete seřadit podle názvu pomocí šipek napravo od záhlaví sloupce název a seřadit seznam ve vzestupném nebo sestupném pořadí.

## <a name="group-member-search-and-filtering"></a>Hledání členů skupiny a filtrování

### <a name="search-group-member-and-owner-lists"></a>Seznam členů skupiny hledání a vlastníků

Nyní můžete hledat členy konkrétní skupiny podle názvu a provádět stejné hledání i v seznamu vlastníků skupiny. Pokud v novém prostředí zadáte řetězec do vyhledávacího pole, bude provedeno automatické hledání pomocí StartsWith. Hledání "Scott" například vrátí Scott Wilkinson.

![Nová dílčí řetězcová hledání v seznamech členů skupiny a vlastníků](./media/groups-members-owners-search/members-list.png)

### <a name="filter-member-and-owners-list"></a>Seznam členů a vlastníků filtru

Kromě hledání teď můžete filtrovat seznam členů a vlastníků podle typu uživatele. Toto jsou informace, které se nacházejí ve sloupci Typ uživatele v seznamu. Proto můžete filtrovat seznam podle členů a hostů, abyste zjistili, jestli jsou ve skupině nějaké hosty.

### <a name="view-and-manage-membership"></a>Zobrazit a spravovat členství

Kromě zobrazení přímých členů určité skupiny teď můžete zobrazit seznam všech členů skupiny v rámci stránky členové. Seznam členů obsahuje všechny jedinečné členy skupiny včetně všech přenositelných členů.

Můžete také vyhledat a filtrovat seznam přímých členů a seznam všech členů jednotlivě. Filtrování seznamu všech členů nemá vliv na filtry, které jsou použité v seznamu přímých členů.

## <a name="improved-group-member-counts"></a>Vylepšené počty členů skupiny

Vylepšili jsme stránku **Přehled** skupiny, která poskytuje počty členů skupin pro skupiny všech velikostí. Můžete vidět, že se člen počítá i pro skupiny s více než 1 000 členy. Nyní můžete zobrazit celkový počet přímých členů pro skupinu a celkový počet členů (všechny jedinečné členy skupiny včetně přenositelných členů) na stránce **Přehled** .

![Vyšší přesnost počtů členství ve skupinách](./media/groups-members-owners-search/member-numbers.png)

## <a name="next-steps"></a>Další kroky

Tyto články poskytují další informace o práci se skupinami ve službě Azure AD.

- [Zobrazení skupin a členů](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Správa členství ve skupinách](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Správa dynamických pravidel pro uživatele ve skupině](groups-create-rule.md)
- [Úprava nastavení skupiny](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Správa přístupu k prostředkům pomocí skupin](../fundamentals/active-directory-manage-groups.md)
- [Správa přístupu k aplikacím SaaS pomocí skupin](groups-saasapps.md)
- [Správa skupin pomocí powershellových příkazů](../enterprise-users/groups-settings-v2-cmdlets.md)
- [Přidání předplatného Azure do Azure Active Directory](../fundamentals/active-directory-how-subscriptions-associated-directory.md)
