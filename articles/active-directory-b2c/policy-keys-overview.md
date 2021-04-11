---
title: Přehled klíčů zásad – Azure Active Directory B2C
description: Přečtěte si o typech klíčů zásad šifrování, které se dají použít v Azure Active Directory B2C pro podepisování a ověřování tokenů, klientských tajných certifikátů, certifikátů a hesel.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 37ff3300935e90304754b5f2b375df0adecdaefa
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2021
ms.locfileid: "107256238"
---
# <a name="overview-of-policy-keys-in-azure-active-directory-b2c"></a>Přehled klíčů zásad v Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) ukládá tajné klíče a certifikáty ve formě klíčů zásad, aby bylo možné navázat důvěryhodnost se službami, se kterými se integruje. Tyto vztahy důvěryhodnosti se skládají z těchto vztahů:

- Zprostředkovatelé externích identit
- Připojení ke [službám REST API Services](restful-technical-profile.md)
- Podepisování a šifrování tokenů

 Tento článek popisuje, co potřebujete znát o klíčích zásad, které používá Azure AD B2C.

> [!NOTE]
> V současné době se konfigurace klíčů zásad omezí jenom na [vlastní zásady](./user-flow-overview.md) .

Tajné klíče a certifikáty pro vytvoření vztahu důvěryhodnosti mezi službami v Azure Portal můžete nakonfigurovat v nabídce **klíče zásad** . Klíč může být symetrický nebo asymetrický. *Symetrická* kryptografie nebo kryptografie s privátním klíčem, je místo, kde se používá sdílený tajný klíč pro šifrování a dešifrování dat. *Asymetrická* kryptografie nebo kryptografie s veřejným klíčem jsou šifrovací systém, který používá páry klíčů, které se skládají z veřejných klíčů, které jsou sdílené s aplikací předávající strany, a soukromými klíči, které jsou známé pouze pro Azure AD B2C.

## <a name="policy-keyset-and-keys"></a>Sada klíčů a klíče zásad

Prostředek nejvyšší úrovně pro klíče zásad v Azure AD B2C je kontejner sady **klíčů** . Každá sada klíčů obsahuje alespoň jeden **klíč**. Klíč má následující atributy:

| Atribut |  Vyžadováno | Poznámky |
| --- | --- |--- |
| `use` | Yes | Použití: identifikuje zamýšlené použití veřejného klíče. Šifrování dat `enc` nebo ověřování signatury dat `sig` .|
| `nbf`| No | Datum a čas aktivace. |
| `exp`| No | Datum a čas vypršení platnosti. |

Doporučujeme nastavit hodnoty aktivace klíče a vypršení platnosti podle vašich standardů infrastruktury veřejných klíčů. Tyto certifikáty možná budete muset pravidelně otáčet z důvodů zabezpečení nebo zásad. Můžete mít například zásadu pro otočení všech certifikátů každý rok.

Chcete-li vytvořit klíč, můžete vybrat jednu z následujících metod:

- **Ruční** – vytvořte tajný klíč s vámi definovaným řetězcem. Tajný klíč je symetrický klíč. Můžete nastavit datum aktivace a vypršení platnosti.
- **Vygenerováno** – automaticky vygeneruje klíč. Můžete nastavit data aktivace a vypršení platnosti. Existují dvě možnosti:
  - **Tajný kód** – vygeneruje symetrický klíč.
  - **RSA** – vygeneruje pár klíčů (asymetrické klíče).
- **Nahrání** – nahrání certifikátu nebo PKCS12 klíče. Certifikát musí obsahovat privátní a veřejné klíče (asymetrické klíče).

## <a name="key-rollover"></a>Výměna klíčů

Z bezpečnostních důvodů může Azure AD B2C v případě nouze pravidelně převádět klíče nebo okamžitě. Všechny aplikace, zprostředkovatele identity nebo REST API, které jsou integrovány s Azure AD B2C, by měly být připraveny zpracovat událost výměny klíčů bez ohledu na to, jak často k ní může dojít. V opačném případě, pokud se vaše aplikace nebo Azure AD B2C pokusí k provedení kryptografické operace použít klíč s vypršenou platností, žádost o přihlášení se nezdaří.

Pokud má Azure AD B2Cá sada klíčů více klíčů, bude v jednom okamžiku aktivní jenom jeden z klíčů, a to na základě následujících kritérií:

- Aktivace klíče vychází z **data aktivace**.
  - Klíče jsou seřazené podle data aktivace ve vzestupném pořadí. Klíče s daty aktivace v budoucnu se zobrazí v seznamu níže. Klíče bez data aktivace jsou umístěné na konci seznamu.
  - Když je aktuální datum a čas delší než datum aktivace klíče, Azure AD B2C aktivuje klíč a přestane používat předchozí aktivní klíč.
- Když uplynul čas vypršení platnosti aktuálního *klíče a kontejner* klíčů obsahuje nový klíč s platným časem a časem *vypršení platnosti* , nový klíč se automaticky aktivuje.
- Když uplynul čas vypršení platnosti aktuálního klíče *a kontejner klíčů neobsahuje nový* klíč *s platným* časem a časem *vypršení platnosti* , Azure AD B2C nebude moci použít klíč s vypršenou platností. Azure AD B2C vyvolá chybovou zprávu v rámci závislé součásti vašich vlastních zásad. Chcete-li se tomuto problému vyhnout, můžete vytvořit výchozí klíč bez aktivace a data vypršení platnosti jako bezpečnostní síť.
- Koncový bod klíče (identifikátoru URI JWKS) OpenId připojit dobře známý konfigurační bod odráží klíče nakonfigurované v kontejneru klíčů, když se na klíč odkazuje v [technickém profilu JwtIssuer](./jwt-issuer-technical-profile.md). Aplikace používající knihovnu OIDC automaticky načte tato metadata, aby zajistila, že používá správné klíče k ověřování tokenů. Další informace najdete v tématu použití [knihovny Microsoft Authentication Library](../active-directory/develop/msal-b2c-overview.md), která vždy automaticky načte nejnovější podpisové klíče tokenu.

## <a name="policy-key-management"></a>Správa klíčů zásad

Pokud chcete získat aktuální aktivní klíč v rámci kontejneru klíčů, použijte koncový bod rozhraní API Microsoft Graph [getActiveKey](/graph/api/trustframeworkkeyset-getactivekey) .

Postup přidání nebo odstranění podpisových a šifrovacích klíčů:

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. Na panelu nástrojů na portálu vyberte ikonu **adresář + předplatné** a pak vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.
1. V Azure Portal vyhledejte a vyberte **Azure AD B2C**.
1. Na stránce Přehled v části **zásady** vyberte **Architektura prostředí identity**.
1. Vybrat **klíče zásad** 
    1. Pokud chcete přidat nový klíč, vyberte **Přidat**.
    1. Chcete-li odebrat nový klíč, vyberte klíč a pak vyberte **Odstranit**. Pokud chcete odstranit klíč, zadejte název kontejneru klíčů, který se má odstranit. Azure AD B2C odstraní klíč a vytvoří kopii klíče s příponou. bak.

### <a name="replace-a-key"></a>Nahradit klíč

Klíče v rámci sady klíčů není nahraditelný nebo vyměnitelná. Pokud potřebujete změnit existující klíč:

- Doporučujeme přidat nový klíč s nastaveným **datem aktivace** na aktuální datum a čas. Azure AD B2C bude nový klíč aktivovat a přestane používat předchozí aktivní klíč.
- Případně můžete vytvořit novou sadu klíčů se správnými klíči. Aktualizujte zásady tak, aby používaly novou sadu klíčů, a pak odeberte starou sadu klíčů. 

## <a name="next-steps"></a>Další kroky

- Naučte [se používat](microsoft-graph-operations.md#trust-framework-policy-keyset) Microsoft Graph k automatizaci nasazení [klíčů a klíčů zásad](microsoft-graph-operations.md#trust-framework-policy-key) .