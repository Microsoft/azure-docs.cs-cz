---
title: Přesměrování URL URI/odpovědi omezení a omezení – platforma identit Microsoft
description: Omezení adresy URL nebo přesměrovat adresy URL odpovědi a omezení
author: SureshJa
ms.author: sureshja
manager: CelesteDG
ms.date: 06/29/2019
ms.topic: article
ms.subservice: develop
ms.service: active-directory
ms.reviewer: lenalepa, manrath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 07be7d0c70193fec88782fea681e33d6b4cf4b40
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/01/2019
ms.locfileid: "67486230"
---
# <a name="redirect-urireply-url-restrictions-and-limitations"></a>Omezení pro adresu URL odpovědi / identifikátor URI přesměrování

Přesměrování identifikátor URI nebo adresa URL odpovědi je umístění, autorizační server odešle uživatel jednou aplikace byl úspěšně registrován a udělit k autorizační kód nebo přístup k tokenu. Kód nebo token je obsažen v přesměrování identifikátor URI nebo odpovědi tokenu je proto důležité zaregistrovat do správného umístění jako součást procesu registrace aplikací.

## <a name="maximum-number-of-redirect-uris"></a>Maximální počet identifikátory URI pro přesměrování

V následující tabulce jsou uvedeny maximálního počtu přesměrování identifikátory URI, které můžete přidat při registraci vaší aplikace. 

| Účty se přihlášení | Maximální počet identifikátory URI pro přesměrování | Popis |
|--------------------------|---------------------------------|-------------|
| Microsoft pracovní nebo školní účty v jakékoli organizaci tenanta Azure Active Directory (Azure AD) | 256 | `signInAudience` pole v manifestu aplikace je nastaven na hodnotu *AzureADMyOrg* nebo *AzureADMultipleOrgs* |
| Microsoft osobních účtů a pracovních a školních účtů | 100 | `signInAudience` v manifestu aplikace je nastaveno na *AzureADandPersonalMicrosoftAccount* |

## <a name="maximum-uri-length"></a>Maximální délka identifikátoru URI

Pro každý přesměrovaný identifikátor URI, který přidáte do registrace aplikace můžete použít maximálně 256 znaků.

## <a name="restrictions-using-a-wildcard-in-uris"></a>Omezení použití zástupného znaku v identifikátorech URI

Zástupný znak URI, například `https://*.contoso.com`, jsou vhodné, ale mělo by se vyhnout. Použití zástupných znaků v přesměrování URI má vliv na zabezpečení. Podle specifikace OAuth 2.0 ([části 3.1.2 RFC 6749](https://tools.ietf.org/html/rfc6749#section-3.1.2)), koncový bod identifikátoru URI přesměrování musí být absolutní identifikátor URI. 

Aplikačního modelu služby Azure AD nepodporuje zástupné identifikátory URI pro aplikace, které jsou nakonfigurované k osobním účtům Microsoft a pracovní nebo školní účty. Zástupný znak URI však jsou povoleny pro aplikace, které jsou nakonfigurovány pro přihlášení pracovní nebo školní účty v tenantovi Azure AD vaší organizace ještě dnes. 
 
> [!NOTE]
> Nové [registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) prostředí neumožňuje vývojářům přidat zástupný znak URI v Uživatelském rozhraní. Přidání zástupný identifikátor URI pro aplikace, které přihlášení pracovní nebo školní účty je podporováno pouze pomocí editoru manifestu aplikace. Od této chvíle, nebude možné použít zástupné znaky v identifikátoru URI pro přesměrování nových aplikací. Nicméně starší aplikace, které obsahují zástupné znaky v přesměrování identifikátory URI budou nadále fungovat.

Pokud váš scénář vyžaduje že další identifikátory URI přesměrování než maximální limit povolený místo přidání přesměrování zástupný identifikátor URI, zvažte jednu z následujících přístupů.

### <a name="use-a-state-parameter"></a>Použijte parametr stavu

Pokud máte několik subdomén a vaše situace vyžaduje přesměrovat uživatele po úspěšném ověření na stejnou stránku, kde se spouští, může být užitečné pomocí parametru state. 

V rámci tohoto přístupu:

1. Vytvořte na "sdílené" identifikátor URI přesměrování na aplikaci ke zpracování tokenů zabezpečení, který jste dostali z koncového bodu autorizace.
1. Vaše aplikace může odesílat parametry specifické pro aplikaci (například pokud uživatel vytvoří nebo cokoli, jako jsou značky informace subdomény URL) v parametru state. Při použití parametru stavu pomáhalo chránit před CSRF ochrany, jak je uvedeno v [části 10.12 RFC 6749](https://tools.ietf.org/html/rfc6749#section-10.12)). 
1. Parametry specifické pro aplikaci bude obsahovat všechny informace potřebné pro aplikaci k vykreslení správné prostředí pro uživatele, to znamená, vytvořit stav příslušné aplikace. Pruhy koncový bod autorizace Azure AD ve formátu HTML v parametru state tak jistotu, že budete nepředáváme HTML obsahu v tomto parametru.
1. Když Azure AD pošle odpověď "sdílené" identifikátor URI přesměrování, odešle parametr stavu zpět do aplikace.
1. Aplikace pak můžete použít hodnotu v parametru state určit adresu URL, která je dále odesílat uživatelům. Zajistěte, aby že ověření CSRF ochranu.

> [!NOTE]
> Tento přístup umožňuje ohrožení zabezpečení klienta k úpravě dalších parametrů v parametru state, a tím přesměrovat uživatele na jinou adresu URL, která je odeslána [otevřete přesměrovače hrozeb](https://tools.ietf.org/html/rfc6819#section-4.2.4) popsané v dokumentu RFC 6819. Klient musí tedy chránit tyto parametry šifrování stavu nebo ověření jiným způsobem, jako je například ověřování názvu domény v URI přesměrování s token.

### <a name="add-redirect-uris-to-service-principals"></a>Přidat identifikátory URI pro přesměrování na instanční objekty

Další možností je přidat identifikátory URI pro přesměrování [instanční](app-objects-and-service-principals.md#application-and-service-principal-relationship) , které představují vaše registrace aplikace v žádného tenanta Azure AD. Tento přístup můžete používat při nelze použít parametr stavu nebo vaše situace vyžaduje, můžete přidat nové identifikátory URI pro přesměrování do vaší registrace aplikace pro každého nového klienta, které podporujete. 

## <a name="next-steps"></a>Další postup

- Další informace o [manifest aplikace](reference-app-manifest.md)
