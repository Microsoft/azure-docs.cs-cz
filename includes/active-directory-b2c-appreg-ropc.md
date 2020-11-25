---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: f8d8ffe8c1802a5cf67b7dc2b0fb071b572d76e8
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "95994349"
---
K registraci aplikace ve vašem tenantovi Azure AD B2C můžete využít nové jednotné prostředí pro **Registrace aplikací** nebo naše starší verze  **aplikací (zastaralé)** . [Další informace o novém prostředí](../articles/active-directory-b2c/app-registrations-training-guide.md).

#### <a name="app-registrations"></a>[Registrace aplikací](#tab/app-reg-ga/)

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. V horní nabídce vyberte filtr **adresář + odběr** a potom vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.
1. V nabídce vlevo vyberte **Azure AD B2C**. Případně vyberte **všechny služby** a vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **Registrace aplikací** a pak vyberte **Nová registrace**.
1. Zadejte **název** aplikace. Například *ROPC_Auth_app*.
1. Ostatní hodnoty ponechte tak, jak jsou, a pak vyberte **zaregistrovat**.
1. Poznamenejte si **ID aplikace (klienta)** pro použití v pozdějším kroku.
1. V části **Spravovat** vyberte **ověřování**.
1. Vyberte **vyzkoušet nové prostředí** (Pokud je zobrazeno).
1. V části **výchozí typ klienta** vyberte **Ano** , aby se aplikace považovala za veřejného klienta. Toto nastavení se vyžaduje pro ROPC tok.
1. Vyberte **Uložit**.
1. V nabídce vlevo vyberte **manifest** a otevřete editor manifestu. 
1. Nastavte atribut **oauth2AllowImplicitFlow** na *hodnotu true*:
    ```json
    "oauth2AllowImplicitFlow": true,
    ```
1. Vyberte **Uložit**.

#### <a name="applications-legacy"></a>[Aplikace (starší verze)](#tab/applications-legacy/)

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. V horní nabídce vyberte filtr **adresář + odběr** a potom vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.
1. V nabídce vlevo vyberte **Azure AD B2C**. Případně vyberte **všechny služby** a vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **aplikace (starší verze)** a pak vyberte **Přidat**.
1. Zadejte název aplikace. Například *ROPC_Auth_app*.
1. V případě **nativního klienta** vyberte **Ano**.
1. Ostatní hodnoty ponechte tak, jak jsou, a pak vyberte **vytvořit**.
1. Poznamenejte si **ID aplikace** pro použití v pozdějším kroku.