---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: d3a323a28d92e2a5834b65316d61c7d53a42aacf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183770"
---
Chcete-li zaregistrovat aplikaci ve vašem tenantovi Azure AD B2C, můžete použít aktuální prostředí **aplikací** nebo naše nové jednotné **registrace aplikací (Preview).** [Další informace o novém prostředí](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Aplikace](#tab/applications/)

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. V horní nabídce vyberte filtr **Directory + subscription** a pak vyberte adresář, který obsahuje vašeho klienta Azure AD B2C.
1. V levé nabídce vyberte **Azure AD B2C**. Nebo vyberte **Všechny služby** a vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **Aplikace**a pak vyberte **Přidat**.
1. Zadejte název aplikace. Například *nativeapp1*.
1. V **seznamu Nativní klient**vyberte možnost **Ano**.
1. Zadejte **identifikátor URI vlastního přesměrování** s jedinečným schématem. Například, `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`. Při výběru identifikátoru URI přesměrování jsou důležité dvě důležité aspekty:
    * **Jedinečný**: Schéma identifikátoru URI přesměrování musí být jedinečné pro každou aplikaci. V `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`příkladu `com.onmicrosoft.contosob2c.exampleapp` je schéma. Tento vzor by měl být dodržován. Pokud dvě aplikace sdílejí stejné schéma, má uživatel možnost zvolit aplikaci. Pokud uživatel zvolí nesprávně, přihlášení se nezdaří.
    * **Dokončení**: Identifikátor URI přesměrování musí mít schéma i cestu. Cesta musí obsahovat alespoň jedno lomítko za doménou. Například `//oauth/` funguje `//oauth` při selhání. Nezahrnujte do identifikátoru URI speciální znaky, například podtržítka.
1. Vyberte **Vytvořit**.

#### <a name="app-registrations-preview"></a>[Registrace aplikací (náhled)](#tab/app-reg-preview/)

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. V horní nabídce vyberte filtr **Directory + subscription** a pak vyberte adresář, který obsahuje vašeho klienta Azure AD B2C.
1. V levé nabídce vyberte **Azure AD B2C**. Nebo vyberte **Všechny služby** a vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **Registrace aplikací (preview)** a pak vyberte **Nová registrace**.
1. Zadejte **název** aplikace. Například *nativeapp1*.
1. V části **Podporované typy účtů**vyberte Účty v **libovolném organizačním adresáři nebo v libovolném poskytovateli identity**.
1. V části **Identifikátor URI přesměrování**vyberte pomocí rozevíracího seznamu Veřejný **klient/nativní (mobilní & plochy).**
1. Zadejte identifikátor URI přesměrování s jedinečným schématem. Například, `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`. Při výběru identifikátoru URI přesměrování jsou důležité dvě důležité aspekty:
    * **Jedinečný**: Schéma identifikátoru URI přesměrování musí být jedinečné pro každou aplikaci. V `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`příkladu `com.onmicrosoft.contosob2c.exampleapp` je schéma. Tento vzor by měl být dodržován. Pokud dvě aplikace sdílejí stejné schéma, má uživatel možnost zvolit aplikaci. Pokud uživatel zvolí nesprávně, přihlášení se nezdaří.
    * **Dokončení**: Identifikátor URI přesměrování musí mít schéma i cestu. Cesta musí obsahovat alespoň jedno lomítko za doménou. Například `//oauth/` funguje `//oauth` při selhání. Nezahrnujte do identifikátoru URI speciální znaky, například podtržítka.
1. V **části Oprávnění**zaškrtněte políčko *Udělit oprávnění správce k oprávněním openid a offline_access.*
1. Vyberte **Zaregistrovat**.