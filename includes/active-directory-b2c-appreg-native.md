---
author: mmacy
ms.service: active-directory-b2c
ms.topic: include
ms.date: 09/27/2019
ms.author: marsma
ms.openlocfilehash: b83a6b9185eb4ef127da1dd3b55aba4e8b2945f9
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2019
ms.locfileid: "71326314"
---
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. V horní nabídce vyberte filtr **adresář + odběr** a potom vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.
1. V nabídce vlevo vyberte **Azure AD B2C**. Případně vyberte **všechny služby** a vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **aplikace**a pak vyberte **Přidat**.
1. Zadejte název aplikace. Například *nativeapp1*.
1. V případě **nativního klienta**vyberte **Ano**.
1. Zadejte **vlastní identifikátor URI přesměrování** s jedinečným schématem. Například, `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`. Pro výběr identifikátoru URI přesměrování existují dva důležité informace:
    1. **Jedinečné**: Schéma identifikátoru URI pro přesměrování musí být pro každou aplikaci jedinečné. V příkladu `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect` `com.onmicrosoft.contosob2c.exampleapp` je schéma. Tento vzor by měl následovat. Pokud dvě aplikace sdílejí stejné schéma, uživateli je dána možnost zvolit aplikaci. Pokud uživatel zvolí nesprávně, přihlášení se nezdařilo.
    1. **Dokončeno**: Identifikátor URI pro přesměrování musí mít schéma i cestu. Cesta musí po doméně obsahovat alespoň jedno lomítko. Například `//oauth/` funguje, když `//oauth` dojde k chybě. Nepoužívejte speciální znaky v identifikátoru URI, například podtržítka.
1. Vyberte **Vytvořit**.
