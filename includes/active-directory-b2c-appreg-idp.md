---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 80e5166775b0cf5acbfce32e61d91c0889e3b086
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186351"
---
Chcete-li zaregistrovat aplikaci ve vašem tenantovi Azure AD B2C, můžete použít aktuální prostředí **aplikací** nebo naše nové jednotné **registrace aplikací (Preview).** [Další informace o novém prostředí](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Aplikace](#tab/applications/)

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. V horní nabídce vyberte filtr **Directory + subscription** a pak vyberte adresář, který obsahuje vašeho klienta Azure AD B2C.
1. V levé nabídce vyberte **Azure AD B2C**. Nebo vyberte **Všechny služby** a vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **Aplikace**a pak vyberte **Přidat**.
1. Zadejte název aplikace. Například *testapp1*.
1. Pro **webové aplikace / webové rozhraní API**vyberte **ano**.
1. Do **adresy URL odpovědi**zadejte`https://jwt.ms`
1. Vyberte **Vytvořit**.

#### <a name="app-registrations-preview"></a>[Registrace aplikací (náhled)](#tab/app-reg-preview/)

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. V horní nabídce vyberte filtr **Directory + subscription** a pak vyberte adresář, který obsahuje vašeho klienta Azure AD B2C.
1. V levé nabídce vyberte **Azure AD B2C**. Nebo vyberte **Všechny služby** a vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **Registrace aplikací (preview)** a pak vyberte **Nová registrace**.
1. Zadejte **název** aplikace. Například *testapp1*.
1. Vyberte **účty v libovolném organizačním adresáři nebo v libovolném zprostředkovateli identity**.
1. V části **Přesměrování identifikátoru URI** `https://jwt.ms` vyberte možnost **Web**a zadejte do textového pole URL.
1. V **části Oprávnění**zaškrtněte políčko *Udělit oprávnění správce k oprávněním openid a offline_access.*
1. Vyberte **Zaregistrovat**.

Po dokončení registrace aplikace povolte implicitní tok grantu:

1. V části **Manage**vyberte **Authentication**.
1. Vyberte **Vyzkoušet nové prostředí** (pokud je zobrazeno).
1. V **části Implicitní udělení**zaškrtněte políčka **Tokeny aplikace Access** i **Tokeny ID.**
1. Vyberte **Uložit**.