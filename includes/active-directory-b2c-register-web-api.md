---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
ms.date: 11/03/2016
ms.author: patricka
ms.openlocfilehash: fcd71f74e0b00934958828024094773e42496b66
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/27/2018
ms.locfileid: "53797913"
---
[!INCLUDE [active-directory-b2c-portal-add-application](active-directory-b2c-portal-add-application.md)]

Pokud chcete zaregistrovat webové rozhraní API, použijte nastavení uvedená v tabulce.

![Příklad nastavení registrace nového webového rozhraní API](./media/active-directory-b2c-register-web-api/b2c-new-web-api-settings.png)

| Nastavení      | Ukázková hodnota  | Popis                                        |
| ------------ | ------- | -------------------------------------------------- |
| **Název** | API Contoso B2C | Zadejte **Název** aplikace, který popíše rozhraní API uživatelům. | 
| **Zahrnout webovou aplikaci nebo webové rozhraní API** | Ano | Pro webové rozhraní API vyberte **Ano**. |
| **Povolit implicitní tok** | Ano | Vyberte **Ano**, pokud vaše aplikace používá [přihlášení OpenID Connect](../articles/active-directory-b2c/active-directory-b2c-reference-oidc.md). |
| **Adresa URL odpovědi** | `https://localhost:44316/` | Adresy URL odpovědí jsou koncové body, kam Azure AD B2C vrací všechny tokeny, které vaše aplikace požaduje. V tomto příkladu je webového rozhraní API místní a naslouchá na portu 44316. |
| **Identifikátor URI ID aplikace** | rozhraní api | Identifikátor URI ID aplikace je identifikátor použitý pro vaše webového rozhraní API. Vygeneruje se pro vás úplný identifikátor URI včetně domény. |

Pro registraci aplikace klikněte na **Vytvořit**.

Nově zaregistrovaná aplikace se zobrazí v seznamu aplikací pro tenanta B2C. Vyberte ze seznamu vaše webové rozhraní API. Zobrazí se podokno vlastností rozhraní API.

![Vlastnosti webového rozhraní API](./media/active-directory-b2c-register-web-api/b2c-web-api-properties.png)

Poznamenejte si globálně jedinečné **ID klienta aplikace**. Toto ID použijete v kódu vaší aplikace.