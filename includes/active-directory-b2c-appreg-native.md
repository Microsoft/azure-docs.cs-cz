---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 06/02/2020
ms.author: mimart
ms.openlocfilehash: 2ad6b90616077a6d18550e86692b109bda622af7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96026286"
---
K registraci aplikace ve vašem tenantovi Azure AD B2C můžete využít nové jednotné prostředí pro **Registrace aplikací** nebo naše starší verze  **aplikací (zastaralé)** . [Další informace o novém prostředí](../articles/active-directory-b2c/app-registrations-training-guide.md).

#### <a name="app-registrations"></a>[Registrace aplikací](#tab/app-reg-ga/)

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. V horní nabídce vyberte filtr **adresář + odběr** a potom vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.
1. V nabídce vlevo vyberte **Azure AD B2C**. Případně vyberte **všechny služby** a vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **Registrace aplikací** a pak vyberte **Nová registrace**.
1. Zadejte **název** aplikace. Například *nativeapp1*.
1. V části **podporované typy účtů** vyberte **účty v libovolném organizačním adresáři nebo jakémkoli poskytovateli identity**.
1. V části **identifikátor URI pro přesměrování** vyberte v rozevíracím seznamu možnost **veřejný klient/nativní (mobilní & Desktop)**.
1. Zadejte identifikátor URI přesměrování s jedinečným schématem. Například, `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`. Při volbě identifikátoru URI přesměrování jsou důležité důležité informace:
    * **Vývoj** Pro vývojové použití můžete nastavit identifikátor URI přesměrování na `http://localhost` a Azure AD B2C bude brát v platnost libovolný port v žádosti. Pokud registrovaný identifikátor URI obsahuje port, Azure AD B2C bude používat pouze tento port. Pokud je například registrovaný identifikátor URI pro přesměrování `http://localhost` , může být identifikátor URI přesměrování v žádosti `http://localhost:<randomport>` . Pokud je registrovaný identifikátor URI pro přesměrování `http://localhost:8080` , musí být identifikátor URI přesměrování v žádosti `http://localhost:8080` .
    * **Jedinečné**: schéma identifikátoru URI pro přesměrování musí být pro každou aplikaci jedinečné. V příkladu `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect` `com.onmicrosoft.contosob2c.exampleapp` je schéma. Tento vzor by měl následovat. Pokud dvě aplikace sdílejí stejné schéma, uživateli je dána možnost zvolit aplikaci. Pokud uživatel zvolí nesprávně, přihlášení se nezdařilo.
    * **Dokončeno**: identifikátor URI pro přesměrování musí mít schéma i cestu. Cesta musí po doméně obsahovat alespoň jedno lomítko. Například funguje v případě, že se `//oauth/` `//oauth` nezdařila. Nepoužívejte speciální znaky v identifikátoru URI, například podtržítka.
1. V části **oprávnění** zaškrtněte políčko *udělit souhlas správcům oprávnění OpenID a offline_access* .
2. Vyberte **Zaregistrovat**.

#### <a name="applications-legacy"></a>[Aplikace (starší verze)](#tab/applications-legacy/)

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. V horní nabídce vyberte filtr **adresář + odběr** a potom vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.
1. V nabídce vlevo vyberte **Azure AD B2C**. Případně vyberte **všechny služby** a vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **aplikace (starší verze)** a pak vyberte **Přidat**.
1. Zadejte název aplikace. Například *nativeapp1*.
1. V případě **nativního klienta** vyberte **Ano**.
1. Zadejte **vlastní identifikátor URI přesměrování** s jedinečným schématem. Například, `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`. Pro výběr identifikátoru URI přesměrování existují dva důležité informace:
    * **Jedinečné**: schéma identifikátoru URI pro přesměrování musí být pro každou aplikaci jedinečné. V příkladu `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect` `com.onmicrosoft.contosob2c.exampleapp` je schéma. Tento vzor by měl následovat. Pokud dvě aplikace sdílejí stejné schéma, uživateli je dána možnost zvolit aplikaci. Pokud uživatel zvolí nesprávně, přihlášení se nezdařilo.
    * **Dokončeno**: identifikátor URI pro přesměrování musí mít schéma i cestu. Cesta musí po doméně obsahovat alespoň jedno lomítko. Například funguje v případě, že se `//oauth/` `//oauth` nezdařila. Nepoužívejte speciální znaky v identifikátoru URI, například podtržítka.
1. Vyberte **Vytvořit**.