---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 31d4b65c6cd6a3dfe3cb779022329f338436c69b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78184337"
---
Chcete-li zaregistrovat aplikaci ve vašem tenantovi Azure AD B2C, můžete použít aktuální prostředí **aplikací** nebo naše nové jednotné **registrace aplikací (Preview).** [Další informace o novém prostředí](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Aplikace](#tab/applications/)

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. Vyberte ikonu **Directory + Subscription** na panelu nástrojů portálu a pak vyberte adresář, který obsahuje vašeho klienta Azure AD B2C.
1. Na webu Azure Portal vyhledejte a vyberte **Službu Azure Active Directory**.
1. V části **Správa**vyberte **Registrace aplikací (starší verze).**
1. Vyberte **Registrace nové aplikace**.
1. Zadejte název aplikace. Například *managementapp1*.
1. Pro **typ aplikace**vyberte **Webová aplikace / ROZHRANÍ API**.
1. Do **přihlašovací adresy URL**zadejte libovolnou platnou adresu URL . Například, `https://localhost`. Koncový bod nemusí být dostupný, ale musí být platná adresa URL.
1. Vyberte **Vytvořit**.
1. Zaznamenejte **ID aplikace,** které se zobrazí na stránce **Přehled registrované aplikace.** Tuto hodnotu použijete v pozdějším kroku.

#### <a name="app-registrations-preview"></a>[Registrace aplikací (náhled)](#tab/app-reg-preview/)

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. Vyberte ikonu **Directory + Subscription** na panelu nástrojů portálu a pak vyberte adresář, který obsahuje vašeho klienta Azure AD B2C.
1. Na webu Azure Portal vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **Registrace aplikací (preview)** a pak vyberte **Nová registrace**.
1. Zadejte **název** aplikace. Například *managementapp1*.
1. Vyberte **možnost Účty pouze v tomto organizačním adresáři**.
1. V **části Oprávnění**zrušte zaškrtnutí políčka *Udělit oprávnění správce k oprávněním openid a offline_access.*
1. Vyberte **Zaregistrovat**.
1. **Zaznamenejte ID aplikace (klienta),** které se zobrazí na stránce s přehledem aplikace. Tuto hodnotu použijete v pozdějším kroku.