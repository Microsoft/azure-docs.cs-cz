---
title: Vlastní atributy služby Azure Active Directory B2C | Dokumentace Microsoftu
description: Jak používat vlastní atributy v Azure Active Directory B2C ke shromažďování informací o uživatelích.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2016
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 893dfbae96d2cfea01b1f281f888e9281bf582f9
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37441912"
---
# <a name="azure-active-directory-b2c-use-custom-attributes-to-collect-information-about-your-consumers"></a>Azure Active Directory B2C: Použití vlastních atributů ke shromažďování informací o uživatelích
Služby Azure Active Directory (Azure AD) B2C se dodává s integrovanou sadou informace (atributy): křestní jméno, příjmení, města, PSČ a dalších atributů. Každá aplikace zaměřené na spotřebitele, ale má jedinečné požadavky na atributy, které shromažďovat od příjemců. S Azure AD B2C můžete rozšířit sadu atributů, které jsou uložené na každý uživatelský účet. Můžete vytvořit vlastní atributy na [webu Azure portal](https://portal.azure.com/) a používat ho v vaše zásady registrace, jak je znázorněno níže. Může číst a zapisovat pomocí těchto atributů [Azure AD Graph API](active-directory-b2c-devquickstarts-graph-dotnet.md).

> [!NOTE]
> Vlastní atributy použití [Azure AD Graph API adresáře schématu rozšíření](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-directory-schema-extensions).
> 
> 

## <a name="create-a-custom-attribute"></a>Vytvoření vlastního atributu
1. [Přejděte do okna s funkcemi B2C na webu Azure portal pomocí těchto kroků](active-directory-b2c-app-registration.md#navigate-to-b2c-settings).
2. Klikněte na tlačítko **atributy uživatele**.
3. Klikněte na **Přidat** v horní části okna.
4. Zadejte **název** pro vlastní atribut (například "ShoeSize") a volitelně **popis**. Klikněte na možnost **Vytvořit**.
   
   > [!NOTE]
   > Pouze "String", "Logické" a "Int" **datové typy** jsou aktuálně k dispozici.
   > 
   > 

Vlastní atribut je nyní k dispozici v seznamu **atributy uživatele**a pro použití ve vaší zásady registrace.

## <a name="use-a-custom-attribute-in-your-sign-up-policy"></a>Použít vlastní atribut ve svojí registrační zásadě
1. [Přejděte do okna s funkcemi B2C na webu Azure portal pomocí těchto kroků](active-directory-b2c-app-registration.md#navigate-to-b2c-settings).
2. Klikněte na **Zásady registrace**.
3. Klikněte na svojí registrační zásadě (například "B2C_1_SiUp") a otevřete ho. Klikněte na tlačítko **upravit** v horní části okna.
4. Klikněte na tlačítko **atributy registrace** a vybrat vlastní atribut (například "ShoeSize"). Klikněte na **OK**.
5. Klikněte na tlačítko **deklarace identit aplikace** a vybrat vlastní atribut. Klikněte na **OK**.
6. Klikněte na tlačítko **Uložit** v horní části okna.

Funkce "Spustit nyní" na zásadu, kterou můžete použít k ověření uživatelské prostředí. Teď by měl naleznete v části "ShoeSize" v seznam atributů shromážděných během registrace uživatelů a vidět ji v tokenu odesílaných zpět do aplikace.

## <a name="notes"></a>Poznámky
* Spolu s zásady registrace vlastní atributy lze také zásady registrace nebo přihlašování a zásady upravování profilu.
* Je známé omezení vlastních atributů. Je pouze vytvoří při prvním se používá ve všech zásad, a ne v případě, že ho přidáte do seznamu **atributy uživatele**.

