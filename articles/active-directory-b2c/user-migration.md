---
title: Přístupy k migraci uživatelů
titleSuffix: Azure AD B2C
description: Migrace uživatelských účtů z jiného poskytovatele identity do Azure AD B2C pomocí metod hromadného importu nebo bezproblémové migrace.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b3ee069985fd39288a562d3caafc50b12290c060
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80332333"
---
# <a name="migrate-users-to-azure-ad-b2c"></a>Migrace uživatelů do Azure AD B2C

Migrace z jiného poskytovatele identity do služby Azure Active Directory B2C (Azure AD B2C) může také vyžadovat migraci existujících uživatelských účtů. Zde jsou popsány dvě metody migrace, *hromadný import* a *bezproblémová migrace*. S oběma přístupy, budete muset napsat aplikaci nebo skript, který používá [rozhraní MICROSOFT Graph API](manage-user-accounts-graph-api.md) k vytvoření uživatelských účtů v Azure AD B2C.

## <a name="bulk-import"></a>Hromadný import

V toku hromadného importu provede aplikace migrace pro každý uživatelský účet tyto kroky:

1. Přečtěte si uživatelský účet od starého zprostředkovatele identity, včetně jeho aktuálních pověření (uživatelské jméno a heslo).
1. Vytvořte odpovídající účet v adresáři Azure AD B2C s aktuálními přihlašovacími údaji.

Použijte tok hromadného importu v jedné z těchto dvou situací:

- Máte přístup k pověření uživatele ve formátu prostého textu (jeho uživatelské jméno a heslo).
- Přihlašovací údaje jsou zašifrovány, ale můžete je dešifrovat.

Informace o programovém vytváření uživatelských účtů najdete v [tématu Správa uživatelských účtů Azure AD B2C pomocí Microsoft Graphu](manage-user-accounts-graph-api.md).

## <a name="seamless-migration"></a>Bezproblémová migrace

Bezproblémový tok migrace použijte, pokud hesla ve formátu prostého textu ve starém zprostředkovateli identity nejsou přístupná. Například když:

- Heslo je uloženo v jednosměrném šifrovaném formátu, například pomocí funkce hash.
- Heslo je uloženo starším poskytovatelem identity způsobem, ke kterému nemáte přístup. Například když poskytovatel identity ověří pověření voláním webové služby.

Bezproblémový tok migrace stále vyžaduje hromadnou migraci uživatelských účtů, ale pak používá [vlastní zásady](custom-policy-get-started.md) k dotazování [rozhraní REST API](custom-policy-rest-api-intro.md) (které vytvoříte) k nastavení hesla jednotlivých uživatelů při prvním přihlášení.

Bezproblémový migrační tok má tedy dvě fáze: *hromadný import* a *nastavení přihlašovacích údajů*.

### <a name="phase-1-bulk-import"></a>Fáze 1: Hromadný import

1. Aplikace migrace čte uživatelské účty od starého zprostředkovatele identity.
1. Aplikace pro migraci vytvoří odpovídající uživatelské účty v adresáři Azure AD B2C, ale *nenastaví hesla*.

### <a name="phase-2-set-credentials"></a>Fáze 2: Nastavení přihlašovacích údajů

Po dokončení hromadné migrace účtů proveďte vlastní zásady a rozhraní REST API následující, když se uživatel přihlásí:

1. Přečtěte si uživatelský účet Azure AD B2C odpovídající zadané e-mailové adrese.
1. Vyhodnocením atributu logického rozšíření zkontrolujte, zda je účet označen příznakem pro migraci.
    - Pokud se atribut `True`rozšíření vrátí , zavolejte rozhraní REST API k ověření hesla proti staršímu poskytovateli identity.
      - Pokud rozhraní REST API zjistí, že heslo je nesprávné, vraťte uživateli popisnou chybu.
      - Pokud rozhraní REST API zjistí, že heslo je správné, zapište heslo do účtu Azure `False`AD B2C a změňte atribut logického rozšíření na .
    - Pokud atribut logického `False`rozšíření vrátí , pokračujte v procesu přihlášení jako obvykle.

Chcete-li zobrazit příklad vlastní zásady a rozhraní REST API, podívejte se na [ukázku bezproblémové migrace uživatelů](https://aka.ms/b2c-account-seamless-migration) na GitHubu.

![Diagram vývojového diagramu bezproblémového přístupu k migraci uživatelů](./media/user-migration/diagram-01-seamless-migration.png)<br />*Diagram: Bezproblémový tok migrace*

## <a name="best-practices"></a>Osvědčené postupy

### <a name="security"></a>Zabezpečení

Bezproblémový přístup migrace používá vlastní rozhraní REST API k ověření pověření uživatele proti staršímu poskytovateli identity.

**Je nutné chránit rozhraní REST API před útoky hrubou silou.** Útočník může odeslat několik hesel v naději, že nakonec uhádne pověření uživatele. Chcete-li pomoci porazit takové útoky, přestat zobrazovat požadavky na rozhraní REST API, když počet pokusů o přihlášení překročí určitou prahovou hodnotu. Zabezpečte také komunikaci mezi Azure AD B2C a rozhraní REST API. Informace o tom, jak zabezpečit rozhraní API RESTful pro produkční prostředí, naleznete [v tématu Secure RESTful API](secure-rest-api.md).

### <a name="user-attributes"></a>Uživatelské atributy

Ne všechny informace ve starším zprostředkovateli identity by měly být migrovány do adresáře Azure AD B2C. Identifikujte příslušnou sadu uživatelských atributů, které chcete uložit v Azure AD B2C před migrací.

- **Do** store v Azure AD B2C
  - Uživatelské jméno, heslo, e-mailové adresy, telefonní čísla, členské čísla / identifikátory.
  - Značky souhlasu pro zásady ochrany osobních údajů a licenční smlouvy s koncovým uživatelem.
- **NEUkládat** v Azure AD B2C
  - Citlivá data, jako jsou čísla kreditních karet, čísla sociálního pojištění (SSN), lékařské záznamy nebo jiné údaje regulované vládními nebo průmyslovými orgány pro dodržování předpisů.
  - Předvolby marketingu nebo komunikace, chování uživatelů a přehledy.

### <a name="directory-clean-up"></a>Vyčištění adresáře

Před zahájením procesu migrace využijte příležitosti k vyčištění adresáře.

- Identifikujte sadu uživatelských atributů, které mají být uloženy v Azure AD B2C a migrovat jenom to, co potřebujete. V případě potřeby můžete vytvořit [vlastní atributy](custom-policy-custom-attributes.md) pro uložení více dat o uživateli.
- Pokud migrujete z prostředí s více zdroji ověřování (například každá aplikace má svůj vlastní uživatelský adresář), migrujte do jednotného účtu v Azure AD B2C.
- Pokud více aplikací mají různá uživatelská jména, můžete uložit všechny z nich v uživatelském účtu Azure AD B2C pomocí kolekce identit. Pokud jde o heslo, nechte uživatele vybrat jeden a nastavte jej v adresáři. Například s bezproblémovou migrací by mělo být v účtu Azure AD B2C uloženo pouze zvolené heslo.
- Před migrací odeberte nepoužívané uživatelské účty nebo nemigrujte zastaralé účty.

### <a name="password-policy"></a>Zásady hesel

Pokud účty, které migrujete mají slabší sílu hesla než [silná síla hesla](../active-directory/authentication/concept-sspr-policy.md) vynucená Azure AD B2C, můžete zakázat požadavek na silné heslo. Další informace naleznete v tématu [Password policy property](manage-user-accounts-graph-api.md#password-policy-property).

## <a name="next-steps"></a>Další kroky

[Azure-ad-b2c/user-migration](https://github.com/azure-ad-b2c/user-migration) repository na GitHubu obsahuje ukázku vlastních zásad migrace a ukázku kódu rozhraní REST API:

[Bezproblémová migrace uživatelů vlastní zásady & ukázkový kód rozhraní REST API](https://aka.ms/b2c-account-seamless-migration)
