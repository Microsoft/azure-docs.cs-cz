---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 31d4b65c6cd6a3dfe3cb779022329f338436c69b
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/29/2020
ms.locfileid: "78184337"
---
K registraci aplikace ve vašem tenantovi Azure AD B2C můžete použít aktuální prostředí **aplikací** nebo naše nové sjednocené **Registrace aplikací (Preview)** . [Další informace o novém prostředí](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Aplikace](#tab/applications/)

1. Přihlaste se na web [Azure Portal ](https://portal.azure.com).
1. Na panelu nástrojů na portálu vyberte ikonu **adresář + předplatné** a pak vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.
1. V Azure Portal vyhledejte a vyberte **Azure Active Directory**.
1. V části **Spravovat**vyberte **Registrace aplikací (starší verze)** .
1. Vyberte **Registrace nové aplikace**.
1. Zadejte název aplikace. Například *managementapp1*.
1. Jako **Typ aplikace**vyberte **Webová aplikace/rozhraní API**.
1. Do **přihlašovací adresy URL**zadejte libovolnou platnou adresu URL. například `https://localhost`. Koncový bod nemusí být dosažitelný, ale musí to být platná adresa URL.
1. Vyberte **Vytvořit**.
1. Poznamenejte si **ID aplikace** , které se zobrazí na stránce s přehledem **zaregistrovaných aplikací** . Tuto hodnotu použijete v pozdějším kroku.

#### <a name="app-registrations-preview"></a>[Registrace aplikací (Preview)](#tab/app-reg-preview/)

1. Přihlaste se na web [Azure Portal ](https://portal.azure.com).
1. Na panelu nástrojů na portálu vyberte ikonu **adresář + předplatné** a pak vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.
1. V Azure Portal vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **Registrace aplikací (Preview)** a pak vyberte **Nová registrace**.
1. Zadejte **název** aplikace. Například *managementapp1*.
1. Vyberte **účty jenom v tomto organizačním adresáři**.
1. V části **oprávnění**zrušte zaškrtnutí políčka *udělit souhlas správce udělit oprávnění OpenID a offline_access* .
1. Vyberte **Zaregistrovat**.
1. Poznamenejte si **ID aplikace (klienta)** , které se zobrazí na stránce s přehledem aplikace. Tuto hodnotu použijete v pozdějším kroku.