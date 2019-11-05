---
author: mmacy
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: marsma
ms.openlocfilehash: bdc9c8f5a782704ec7f8cbaa31dd4cc27ed376cc
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73468955"
---
K registraci aplikace ve vašem tenantovi Azure AD B2C můžete použít aktuální prostředí **aplikací** nebo naše nové sjednocené **Registrace aplikací (Preview)** . [Přečtěte si další informace o prostředí verze Preview](https://aka.ms/b2cappregintro).

#### <a name="applicationstabapplications"></a>[Aplikace](#tab/applications/)

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. V horní nabídce vyberte filtr **adresář + odběr** a potom vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.
1. V nabídce vlevo vyberte **Azure AD B2C**. Případně vyberte **všechny služby** a vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **aplikace**a pak vyberte **Přidat**.
1. Zadejte název aplikace. Například *nativeapp1*.
1. V případě **nativního klienta**vyberte **Ano**.
1. Zadejte **vlastní identifikátor URI přesměrování** s jedinečným schématem. například `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`. Pro výběr identifikátoru URI přesměrování existují dva důležité informace:
    * **Jedinečné**: schéma identifikátoru URI pro přesměrování musí být pro každou aplikaci jedinečné. V příkladu `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect``com.onmicrosoft.contosob2c.exampleapp` je schéma. Tento vzor by měl následovat. Pokud dvě aplikace sdílejí stejné schéma, uživateli je dána možnost zvolit aplikaci. Pokud uživatel zvolí nesprávně, přihlášení se nezdařilo.
    * **Dokončeno**: identifikátor URI pro přesměrování musí mít schéma i cestu. Cesta musí po doméně obsahovat alespoň jedno lomítko. `//oauth/` například funguje, zatímco `//oauth` selžou. Nepoužívejte speciální znaky v identifikátoru URI, například podtržítka.
1. Vyberte **Vytvořit**.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Registrace aplikací (Preview)](#tab/app-reg-preview/)

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. V horní nabídce vyberte filtr **adresář + odběr** a potom vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.
1. V nabídce vlevo vyberte **Azure AD B2C**. Případně vyberte **všechny služby** a vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **Registrace aplikací (Preview)** a pak vyberte **Nová registrace**.
1. Zadejte **název** aplikace. Například *nativeapp1*.
1. V části **podporované typy účtů**vyberte **účty v libovolném organizačním adresáři nebo jakémkoli poskytovateli identity**.
1. V části **identifikátor URI pro přesměrování**vyberte v rozevíracím seznamu možnost **veřejný klient/nativní (mobilní & Desktop)** .
1. Zadejte identifikátor URI přesměrování s jedinečným schématem. například `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`. Pro výběr identifikátoru URI přesměrování existují dva důležité informace:
    * **Jedinečné**: schéma identifikátoru URI pro přesměrování musí být pro každou aplikaci jedinečné. V příkladu `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect``com.onmicrosoft.contosob2c.exampleapp` je schéma. Tento vzor by měl následovat. Pokud dvě aplikace sdílejí stejné schéma, uživateli je dána možnost zvolit aplikaci. Pokud uživatel zvolí nesprávně, přihlášení se nezdařilo.
    * **Dokončeno**: identifikátor URI pro přesměrování musí mít schéma i cestu. Cesta musí po doméně obsahovat alespoň jedno lomítko. `//oauth/` například funguje, zatímco `//oauth` selžou. Nepoužívejte speciální znaky v identifikátoru URI, například podtržítka.
1. V části **oprávnění**zaškrtněte políčko *udělit souhlas správce oprávnění OpenID a offline_access* .
1. Vyberte **Zaregistrovat**.