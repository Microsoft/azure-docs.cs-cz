---
author: mmacy
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: marsma
ms.openlocfilehash: 35ddec2d605e17a1bb91b338e4e5402c6d47db57
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73474785"
---
K registraci aplikace ve vašem tenantovi Azure AD B2C můžete použít aktuální prostředí **aplikací** nebo naše nové sjednocené **Registrace aplikací (Preview)** . [Přečtěte si další informace o prostředí verze Preview](https://aka.ms/b2cappregintro).

#### <a name="applicationstabapplications"></a>[Aplikace](#tab/applications/)

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. V horní nabídce vyberte filtr **adresář + odběr** a potom vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.
1. V nabídce vlevo vyberte **Azure AD B2C**. Případně vyberte **všechny služby** a vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **aplikace**a pak vyberte **Přidat**.
1. Zadejte název aplikace. Například *ROPC_Auth_app*.
1. V případě **nativního klienta**vyberte **Ano**.
1. Ostatní hodnoty ponechte tak, jak jsou, a pak vyberte **vytvořit**.
1. Poznamenejte si **ID aplikace** pro použití v pozdějším kroku.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Registrace aplikací (Preview)](#tab/app-reg-preview/)

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. V horní nabídce vyberte filtr **adresář + odběr** a potom vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.
1. V nabídce vlevo vyberte **Azure AD B2C**. Případně vyberte **všechny služby** a vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **Registrace aplikací (Preview)** a pak vyberte **Nová registrace**.
1. Zadejte **název** aplikace. Například *ROPC_Auth_app*.
1. Ostatní hodnoty ponechte tak, jak jsou, a pak vyberte **zaregistrovat**.
1. Poznamenejte si **ID aplikace (klienta)** pro použití v pozdějším kroku.
1. V části **Spravovat**vyberte **ověřování**.
1. Vyberte **vyzkoušet nové prostředí** (Pokud je zobrazeno).
1. V části **výchozí typ klienta**vyberte **Ano** , aby se aplikace považovala za veřejného klienta. Toto nastavení se vyžaduje pro ROPC tok.
1. Vyberte **Uložit**.