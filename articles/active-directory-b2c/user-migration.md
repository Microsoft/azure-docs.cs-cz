---
title: Přístupy k migraci uživatelů
titleSuffix: Azure AD B2C
description: Migrace uživatelských účtů od jiného zprostředkovatele identity do Azure AD B2C pomocí metod hromadného importu nebo bezproblémové migrace.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 90ef9402e0891915be4ed6bb89573eced546c59a
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/29/2020
ms.locfileid: "78183138"
---
# <a name="migrate-users-to-azure-ad-b2c"></a>Migrace uživatelů na Azure AD B2C

Migrace od jiného zprostředkovatele identity na Azure Active Directory B2C (Azure AD B2C) může také vyžadovat migraci stávajících uživatelských účtů. Zde jsou popsané dvě metody migrace, *hromadné importy* a *bezproblémové migrace*. V obou případech je potřeba napsat aplikaci nebo skript, které používají [rozhraní Microsoft Graph API](manage-user-accounts-graph-api.md) k vytváření uživatelských účtů v Azure AD B2C.

## <a name="bulk-import"></a>Hromadný import

V toku hromadného importu provede aplikace pro migraci tyto kroky pro každý uživatelský účet:

1. Přečtěte si uživatelský účet od starého zprostředkovatele identity, včetně jeho aktuálních přihlašovacích údajů (uživatelské jméno a heslo).
1. V adresáři Azure AD B2C vytvořte odpovídající účet s aktuálními přihlašovacími údaji.

Postup hromadného importu použijte v obou těchto dvou situacích:

- Máte přístup k přihlašovacím údajům uživatele ve formátu prostého textu (uživatelské jméno a heslo).
- Přihlašovací údaje jsou šifrované, ale můžete je dešifrovat.

Informace o programovém vytváření uživatelských účtů najdete v tématu [správa Azure AD B2C uživatelských účtů pomocí Microsoft Graph](manage-user-accounts-graph-api.md).

## <a name="seamless-migration"></a>Bezproblémové migrace

Postup bezproblémové migrace použijte v případě, že nešifrovaná hesla ve starém zprostředkovateli identity nejsou dostupná. Například když:

- Heslo je uloženo v jednosměrovém šifrovaném formátu, například pomocí funkce hash.
- Heslo ukládá starší zprostředkovatel identity způsobem, ke kterému nemůžete získat přístup. Například když zprostředkovatel identity ověřuje přihlašovací údaje voláním webové služby.

Plynulý tok migrace ještě vyžaduje hromadnou migraci uživatelských účtů, ale pak používá [vlastní zásadu](restful-technical-profile.md) pro dotazování [REST API](rest-api-claims-exchange-dotnet.md) (kterou vytvoříte) k nastavení hesla jednotlivých uživatelů při prvním přihlášení.

Plynulý tok migrace má tedy dvě fáze: *hromadné importy* a *Nastavení přihlašovacích údajů*.

### <a name="phase-1-bulk-import"></a>Fáze 1: hromadný import

1. Vaše aplikace pro migraci přečte uživatelské účty od starého zprostředkovatele identity.
1. Aplikace pro migraci vytváří v adresáři Azure AD B2C příslušné uživatelské účty, ale *nenastavuje hesla*.

### <a name="phase-2-set-credentials"></a>Fáze 2: Nastavení přihlašovacích údajů

Po dokončení hromadné migrace účtů budou vaše vlastní zásady a REST API při přihlášení uživatele provádět tyto akce:

1. Přečtěte si Azure AD B2C uživatelský účet odpovídající zadané e-mailové adrese.
1. Vyhodnocením logického atributu rozšíření ověřte, zda je účet označen pro migraci.
    - Pokud atribut Extension vrátí `True`, zavolejte REST API a ověřte heslo proti staršímu zprostředkovateli identity.
      - Pokud REST API zjistí, že heslo není správné, uživateli se zobrazí zpráva s popisem chyby.
      - Pokud REST API určuje správné heslo, zapište heslo k účtu Azure AD B2C a změňte hodnotu atributu Boolean Extension na `False`.
    - Pokud logický atribut rozšíření vrátí `False`, pokračujte v procesu přihlášení jako normální.

Příklad vlastní zásady a REST API najdete v [ukázce bezproblémové migrace uživatelů](https://aka.ms/b2c-account-seamless-migration) na GitHubu.

![diagram vývojového diagramu bezproblémové migrace do migrace uživatelů](./media/user-migration/diagram-01-seamless-migration.png)<br />*Diagram: plynulý tok migrace*

## <a name="best-practices"></a>Osvědčené postupy

### <a name="security"></a>Zabezpečení

Postup bezproblémové migrace používá vlastní REST API k ověření přihlašovacích údajů uživatele vůči staršímu zprostředkovateli identity.

**REST API musíte chránit před útoky hrubou silou.** Útočník může poslat několik hesel na začátku a nakonec uhodnout přihlašovací údaje uživatele. Aby bylo možné tyto útoky předejít, ukončete požadavky na REST API, když počet pokusů o přihlášení přesáhne určitou prahovou hodnotu. Také Zabezpečte komunikaci mezi Azure AD B2C a REST API pomocí [klientského certifikátu](secure-rest-api-dotnet-certificate-auth.md).

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

- Identifikujte sadu uživatelských atributů, které mají být uloženy v Azure AD B2C, a migrujte pouze to, co potřebujete. V případě potřeby můžete vytvořit [vlastní atributy](custom-policy-custom-attributes.md) , které budou ukládat více dat o uživateli.
- Pokud migrujete z prostředí s více zdroji ověřování (například každá aplikace má vlastní adresář uživatele), migrujte na jednotný účet v Azure AD B2C.
- Pokud má více aplikací různá uživatelská jména, můžete je Uložit do Azure AD B2C uživatelského účtu pomocí kolekce identity. S ohledem na heslo nechte uživatele zvolit jednu a nastavit v adresáři. V případě bezproblémové migrace by například mělo být v účtu Azure AD B2C uloženo pouze zvolené heslo.
- Před migrací odeberte nepoužívané uživatelské účty nebo Nemigrujte zastaralé účty.

### <a name="password-policy"></a>Zásady hesel

Pokud mají účty, které migrujete, slabší sílu hesla, než je [bezpečná síla hesla](../active-directory/authentication/concept-sspr-policy.md) vynutilá Azure AD B2C, můžete zakázat požadavek na silný heslo. Další informace najdete v tématu [vlastnost zásady hesel](manage-user-accounts-graph-api.md#password-policy-property).

## <a name="next-steps"></a>Další kroky

Úložiště [Azure-AD-B2C/User-Migration](https://github.com/azure-ad-b2c/user-migration) na GitHubu obsahuje příklad bezproblémové migrace vlastní zásady a REST API ukázka kódu:

[Plynulá vlastní zásada migrace uživatelů & REST API Code Sample](https://aka.ms/b2c-account-seamless-migration)
