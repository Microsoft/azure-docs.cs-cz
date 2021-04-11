---
title: Přístupy k migraci uživatelů
titleSuffix: Azure AD B2C
description: Migrace uživatelských účtů od jiného zprostředkovatele identity do Azure AD B2C pomocí metod migrace před migrací nebo bezproblémové migrace.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/11/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 3e60b80a4ebeaef7d31d4c0c1d9d4bfc41ec3a56
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2021
ms.locfileid: "107256204"
---
# <a name="migrate-users-to-azure-ad-b2c"></a>Migrace uživatelů do Azure AD B2C

Migrace od jiného zprostředkovatele identity na Azure Active Directory B2C (Azure AD B2C) může také vyžadovat migraci stávajících uživatelských účtů. Tady jsou popsané dvě metody migrace, *před migrací* a *bezproblémové migrace*. V obou případech je potřeba napsat aplikaci nebo skript, které používají [rozhraní Microsoft Graph API](microsoft-graph-operations.md) k vytváření uživatelských účtů v Azure AD B2C.

## <a name="pre-migration"></a>Předběžná migrace

V toku před migrací aplikace pro migraci provádí tyto kroky pro každý uživatelský účet:

1. Přečtěte si uživatelský účet od starého zprostředkovatele identity, včetně jeho aktuálních přihlašovacích údajů (uživatelské jméno a heslo).
1. V adresáři Azure AD B2C vytvořte odpovídající účet s aktuálními přihlašovacími údaji.

Tok předběžné migrace použijte v některé z těchto dvou případů:

- Máte přístup k přihlašovacím údajům uživatele ve formátu prostého textu (uživatelské jméno a heslo).
- Přihlašovací údaje jsou šifrované, ale můžete je dešifrovat.

Informace o programovém vytváření uživatelských účtů najdete v tématu [správa Azure AD B2C uživatelských účtů pomocí Microsoft Graph](microsoft-graph-operations.md).

## <a name="seamless-migration"></a>Bezproblémové migrace

Postup bezproblémové migrace použijte v případě, že nešifrovaná hesla ve starém zprostředkovateli identity nejsou dostupná. Například když:

- Heslo je uloženo v jednosměrovém šifrovaném formátu, například pomocí funkce hash.
- Heslo ukládá starší zprostředkovatel identity způsobem, ke kterému nemůžete získat přístup. Například když zprostředkovatel identity ověřuje přihlašovací údaje voláním webové služby.

Plynulý tok migrace ještě vyžaduje předem migraci uživatelských účtů, ale pak používá [vlastní zásadu](user-flow-overview.md) pro dotazování [REST API](custom-policy-rest-api-intro.md) (kterou vytvoříte) k nastavení hesla jednotlivých uživatelů při prvním přihlášení.

Plynulý tok migrace má tedy dvě fáze: *Předběžná migrace* a *Nastavení přihlašovacích údajů*.

### <a name="phase-1-pre-migration"></a>Fáze 1: předběžná migrace

1. Vaše aplikace pro migraci přečte uživatelské účty od starého zprostředkovatele identity.
1. Aplikace pro migraci vytvoří v adresáři Azure AD B2C odpovídající uživatelské účty, ale *Nastaví náhodná hesla* , která vygenerujete.

### <a name="phase-2-set-credentials"></a>Fáze 2: Nastavení přihlašovacích údajů

Po dokončení předběžné migrace účtů vaše vlastní zásady a REST API při přihlášení uživatele provádět následující akce:

1. Přečtěte si Azure AD B2C uživatelský účet odpovídající zadané e-mailové adrese.
1. Vyhodnocením logického atributu rozšíření ověřte, zda je účet označen pro migraci.
    - Pokud se atribut rozšíření vrátí `True` , zavolejte REST API a ověřte heslo proti staršímu zprostředkovateli identity.
      - Pokud REST API zjistí, že heslo není správné, uživateli se zobrazí zpráva s popisem chyby.
      - Pokud REST API určuje, zda je heslo správné, zapište heslo k účtu Azure AD B2C a změňte logický atribut rozšíření na `False` .
    - Pokud se vrátí logický atribut rozšíření `False` , pokračujte v procesu přihlášení jako normální.

Příklad vlastní zásady a REST API najdete v [ukázce bezproblémové migrace uživatelů](https://aka.ms/b2c-account-seamless-migration) na GitHubu.

![Vývojový diagram plynulého přístupu k migraci uživatelům](./media/user-migration/diagram-01-seamless-migration.png)<br />*Diagram: plynulý tok migrace*

## <a name="best-practices"></a>Osvědčené postupy

### <a name="security"></a>Zabezpečení

Postup bezproblémové migrace používá vlastní REST API k ověření přihlašovacích údajů uživatele vůči staršímu zprostředkovateli identity.

**REST API musíte chránit před útoky hrubou silou.** Útočník může poslat několik hesel na začátku a nakonec uhodnout přihlašovací údaje uživatele. Aby bylo možné tyto útoky předejít, ukončete požadavky na REST API, když počet pokusů o přihlášení přesáhne určitou prahovou hodnotu. Zabezpečte také komunikaci mezi Azure AD B2C a REST API. Informace o tom, jak zabezpečit rozhraní API RESTful pro produkční prostředí, najdete v tématu [Secure RESTFUL API](secure-rest-api.md).

### <a name="user-attributes"></a>Atributy uživatele

Ne všechny informace ze staršího zprostředkovatele identity by měly být migrovány do adresáře Azure AD B2C. Identifikujte odpovídající sadu atributů uživatele pro uložení v Azure AD B2C před migrací.

- **Uložit do** Azure AD B2C
  - Uživatelské jméno, heslo, e-mailové adresy, telefonní čísla, čísla nebo identifikátory členství.
  - Značky souhlasu pro zásady ochrany osobních údajů a licenční smlouvy pro koncové uživatele.
- **Neukládat v** Azure AD B2C
  - Citlivá data, jako jsou čísla kreditních karet, rodné údaje (SSN), lékařské záznamy nebo jiná data, která jsou regulována orgány pro dodržování předpisů pro státní správu nebo obory.
  - Marketingové nebo komunikační preference, chování uživatelů a přehledy.

### <a name="directory-clean-up"></a>Vyčištění adresáře

Než zahájíte proces migrace, využijte možnost vyčištění adresáře.

- Identifikujte sadu uživatelských atributů, které mají být uloženy v Azure AD B2C, a migrujte pouze to, co potřebujete. V případě potřeby můžete vytvořit [vlastní atributy](user-flow-custom-attributes.md) , které budou ukládat více dat o uživateli.
- Pokud migrujete z prostředí s více zdroji ověřování (například každá aplikace má vlastní adresář uživatele), migrujte na jednotný účet v Azure AD B2C.
- Pokud má více aplikací různá uživatelská jména, můžete je Uložit do Azure AD B2C uživatelského účtu pomocí kolekce identity. S ohledem na heslo nechte uživatele zvolit jednu a nastavit v adresáři. V případě bezproblémové migrace by například mělo být v účtu Azure AD B2C uloženo pouze zvolené heslo.
- Před migrací odeberte nepoužívané uživatelské účty nebo Nemigrujte zastaralé účty.

### <a name="password-policy"></a>Zásady hesel

Pokud mají účty, které migrujete, slabší sílu hesla, než je [bezpečná síla hesla](../active-directory/authentication/concept-sspr-policy.md) vynutilá Azure AD B2C, můžete zakázat požadavek na silný heslo. Další informace najdete v tématu [vlastnost zásady hesel](user-profile-attributes.md#password-policy-attribute).

## <a name="next-steps"></a>Další kroky

Úložiště [Azure-AD-B2C/User-Migration](https://github.com/azure-ad-b2c/user-migration) na GitHubu obsahuje příklad bezproblémové migrace vlastní zásady a REST API ukázka kódu:

[Plynulá vlastní zásada migrace uživatelů & REST API Code Sample](https://aka.ms/b2c-account-seamless-migration)
