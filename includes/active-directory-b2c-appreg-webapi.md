---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 9367719b1a1050ce8abf36804b3d0d0d58c7484b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "78183365"
---
Prostředky webového rozhraní API musí být registrovány ve vašem tenantovi, než budou moci přijímat a reagovat na požadavky chráněných prostředků klientskými aplikacemi, které představují přístupový token.

Chcete-li zaregistrovat aplikaci ve vašem tenantovi Azure AD B2C, můžete použít aktuální prostředí **aplikací** nebo naše nové jednotné **registrace aplikací (Preview).** [Další informace o novém prostředí](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Aplikace](#tab/applications/)

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. V horní nabídce vyberte filtr **Directory + subscription** a pak vyberte adresář, který obsahuje vašeho klienta Azure AD B2C.
1. V levé nabídce vyberte **Azure AD B2C**. Nebo vyberte **Všechny služby** a vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **Aplikace**a pak vyberte **Přidat**.
1. Zadejte název aplikace. Například *webapi1*.
1. Pro **webové aplikace / webové rozhraní API**vyberte **ano**.
1. V **povolit implicitní tok**vyberte **ano**.
1. Pro **adresu URL odpovědi**zadejte koncový bod, kde azure AD B2C by měl vrátit všechny tokeny, které vaše aplikace požaduje. V tomto kurzu ukázka spustí místně `https://localhost:5000`a naslouchá na .
1. Pro **identifikátor URI ID aplikace**přidejte identifikátor koncového bodu rozhraní API do zobrazeného identifikátoru URI. Pro účely tohoto `api`kurzu zadejte , aby `https://contosob2c.onmicrosoft.com/api`byl úplný identifikátor URI podobný .
1. Vyberte **Vytvořit**.
1. Zaznamenejte **ID aplikace** pro pozdější krok.

#### <a name="app-registrations-preview"></a>[Registrace aplikací (náhled)](#tab/app-reg-preview/)

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. V horní nabídce vyberte filtr **Directory + subscription** a pak vyberte adresář, který obsahuje vašeho klienta Azure AD B2C.
1. V levé nabídce vyberte **Azure AD B2C**. Nebo vyberte **Všechny služby** a vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **Registrace aplikací (preview)** a pak vyberte **Nová registrace**.
1. Zadejte **název** aplikace. Například *webapi1*.
1. V části **Přesměrování identifikátoru URI**vyberte **web**a zadejte koncový bod, ve kterém by měl Azure AD B2C vrátit všechny tokeny, které vaše aplikace požaduje. V tomto kurzu ukázka spustí místně `http://localhost:5000`a naslouchá na .
1. Vyberte **Zaregistrovat**.
1. Zaznamenejte **ID aplikace (klienta)** pro pozdější krok.

Dále povolte implicitní tok grantů:

1. V části **Manage**vyberte **Authentication**.
1. Vyberte **Vyzkoušet nové prostředí** (pokud je zobrazeno).
1. V **části Implicitní udělení**zaškrtněte políčka **Tokeny aplikace Access** i **Tokeny ID.**
1. Vyberte **Uložit**.