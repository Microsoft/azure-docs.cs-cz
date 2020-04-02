---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: cea3245176e6c38137d68e3ad4b47477bedc78be
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529159"
---
Chcete-li zaregistrovat aplikaci ve vašem tenantovi Azure AD B2C, můžete použít aktuální prostředí **aplikací** nebo naše nové jednotné **registrace aplikací (Preview).** [Další informace o novém prostředí](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Aplikace](#tab/applications/)

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. V horní nabídce vyberte filtr **Directory + subscription** a pak vyberte adresář, který obsahuje vašeho klienta Azure AD B2C.
1. V levé nabídce vyberte **Azure AD B2C**. Nebo vyberte **Všechny služby** a vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **Aplikace**a pak vyberte **Přidat**.
1. Zadejte název aplikace. Například *ROPC_Auth_app*.
1. V **seznamu Nativní klient**vyberte možnost **Ano**.
1. Ostatní hodnoty ponechejte tak, jak jsou, a pak vyberte **Vytvořit**.
1. Zaznamenejte **ID aplikace** pro pozdější krok.

#### <a name="app-registrations-preview"></a>[Registrace aplikací (náhled)](#tab/app-reg-preview/)

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. V horní nabídce vyberte filtr **Directory + subscription** a pak vyberte adresář, který obsahuje vašeho klienta Azure AD B2C.
1. V levé nabídce vyberte **Azure AD B2C**. Nebo vyberte **Všechny služby** a vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **Registrace aplikací (preview)** a pak vyberte **Nová registrace**.
1. Zadejte **název** aplikace. Například *ROPC_Auth_app*.
1. Ostatní hodnoty ponechejte tak, jak jsou, a pak vyberte **Registrovat**.
1. Zaznamenejte **ID aplikace (klienta)** pro pozdější krok.
1. V části **Manage**vyberte **Authentication**.
1. Vyberte **Vyzkoušet nové prostředí** (pokud je zobrazeno).
1. V části **Výchozí typ klienta**vyberte **Ano,** chcete-li aplikaci považovat za veřejného klienta. Toto nastavení je vyžadováno pro tok ROPC.
1. Vyberte **Uložit**.
1. V levé nabídce vyberte **Manifest,** chcete-li otevřít editor manifestu. 
1. Nastavte atribut **oauth2AllowImplicitFlow** na *hodnotu true*:
    ```json
    "oauth2AllowImplicitFlow": true,
    ```
1. Vyberte **Uložit**.