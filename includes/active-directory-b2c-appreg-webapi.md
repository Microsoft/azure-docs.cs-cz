---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 7cbf6ca7606210b70ae9efa69a8cc6551eb6f36f
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106072985"
---
Prostředky webového rozhraní API musí být ve vašem tenantovi zaregistrované, aby mohly přijímat a reagovat na požadavky na chráněné prostředky klientskými aplikacemi, které prezentují přístupový token.

K registraci aplikace ve vašem tenantovi Azure AD B2C můžete využít nové jednotné prostředí pro **Registrace aplikací** nebo naše starší verze  **aplikací (zastaralé)** . [Další informace o novém prostředí](../articles/active-directory-b2c/app-registrations-training-guide.md).

#### <a name="app-registrations"></a>[Registrace aplikací](#tab/app-reg-ga/)

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. V horní nabídce vyberte filtr **adresář + odběr** a potom vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.
1. V nabídce vlevo vyberte **Azure AD B2C**. Případně vyberte **všechny služby** a vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **Registrace aplikací** a pak vyberte **Nová registrace**.
1. Zadejte **název** aplikace. Například *webapi1*.
1. V části **identifikátor URI pro přesměrování** vyberte **Web** a potom zadejte koncový bod, kde Azure AD B2C by měl vracet jakékoli tokeny, které vaše aplikace požaduje. V tomto kurzu se ukázka spouští místně a naslouchá na `http://localhost:5000` .
1. Vyberte **Zaregistrovat**.
1. Poznamenejte si **ID aplikace (klienta)** pro použití v pozdějším kroku.

Potom povolte tok implicitního udělení:

1. V části **Spravovat** vyberte **ověřování**.
1. Vyberte **vyzkoušet nové prostředí** (Pokud je zobrazeno).
1. V části **implicitní udělení** vyberte zaškrtávací políčka **přístupové tokeny** i **tokeny ID** .
1. Vyberte **Uložit**.

#### <a name="applications"></a>[Aplikace](#tab/applications/)

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. V horní nabídce vyberte filtr **adresář + odběr** a potom vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.
1. V nabídce vlevo vyberte **Azure AD B2C**. Případně vyberte **všechny služby** a vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **aplikace (starší verze)** a pak vyberte **Přidat**.
1. Zadejte název aplikace. Například *webapi1*.
1. Pro **webovou aplikaci nebo webové rozhraní API** vyberte **Ano**.
1. Pro možnost **Povolení implicitního toku** vyberte **Ano**.
1. V případě **adresy URL odpovědi** Zadejte koncový bod, kde Azure AD B2C by měl vracet jakékoli tokeny, které vaše aplikace požaduje. V tomto kurzu se ukázka spouští místně a naslouchá na `https://localhost:5000` .
1. Pro **identifikátor URI ID aplikace** přidejte identifikátor koncového bodu rozhraní API k ZOBRAZENÉmu identifikátoru URI. Pro tento kurz zadejte `api` , aby úplný identifikátor URI byl podobný `https://contosob2c.onmicrosoft.com/api` .
1. Vyberte **Vytvořit**.
1. Poznamenejte si **ID aplikace** pro použití v pozdějším kroku.