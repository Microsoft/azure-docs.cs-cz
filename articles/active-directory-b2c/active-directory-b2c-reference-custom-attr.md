---
title: 'Azure Active Directory B2C: Vlastní atributy | Microsoft Docs'
description: Jak používat vlastní atributy ke shromažďování informací o uživatelích v Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 12/06/2016
ms.author: davidmu
ms.openlocfilehash: 6f285c10b7d8ff92c8568c42b6a78dc4ea9bcc74
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-use-custom-attributes-to-collect-information-about-your-consumers"></a>Azure Active Directory B2C: Použijte vlastní atributy ke shromažďování informací o uživatelích
Adresáře Azure Active Directory (Azure AD) B2C se dodává s integrovanou sadu informace (atributy): křestní jméno, příjmení, Město, PSČ a další atributy. Každá aplikace určených však má jedinečné požadavky na atributy, které ke shromáždění od příjemce. S Azure AD B2C můžete rozšířit sadu atributů, které jsou uložené na každý uživatelský účet. Můžete vytvořit vlastní atributy na [portál Azure](https://portal.azure.com/) a použít ho v registraci zásady, jak je uvedeno níže. Můžete také číst a zapsat pomocí těchto atributů [Azure AD Graph API](active-directory-b2c-devquickstarts-graph-dotnet.md).

> [!NOTE]
> Vlastní atributy použití [Azure AD Graph API rozšíření schématu služby Directory](https://msdn.microsoft.com/library/azure/dn720459.aspx).
> 
> 

## <a name="create-a-custom-attribute"></a>Vytvořit vlastní atribut
1. [Postupujte podle těchto kroků přejděte do okna s funkcemi B2C na portálu Azure](active-directory-b2c-app-registration.md#navigate-to-b2c-settings).
2. Klikněte na tlačítko **uživatelské atributy**.
3. Klikněte na **Přidat** v horní části okna.
4. Zadejte **název** pro vlastní atribut (například "ShoeSize") a volitelně **popis**. Klikněte na možnost **Vytvořit**.
   
   > [!NOTE]
   > Pouze "Řetězec", "Logická hodnota" a "Int" **datové typy** jsou nyní k dispozici.
   > 
   > 

Vlastní atribut je nyní k dispozici v seznamu **uživatelské atributy**a pro použití ve vaší registrační zásady.

## <a name="use-a-custom-attribute-in-your-sign-up-policy"></a>Použít vlastní atribut ve svojí registrační zásadě
1. [Postupujte podle těchto kroků přejděte do okna s funkcemi B2C na portálu Azure](active-directory-b2c-app-registration.md#navigate-to-b2c-settings).
2. Klikněte na **Zásady registrace**.
3. Klikněte na tlačítko svojí registrační zásadě (například "B2C_1_SiUp") a ten se otevře. Klikněte na tlačítko **upravit** v horní části okna.
4. Klikněte na tlačítko **atributy registrace** a vyberte vlastní atribut (například "ShoeSize"). Klikněte na **OK**.
5. Klikněte na tlačítko **deklarace identity aplikace** a vyberte vlastní atribut. Klikněte na **OK**.
6. Klikněte na tlačítko **Uložit** v horní části okna.

Funkci "Spustit nyní" v zásadách slouží k ověření prostředí pro uživatele. Teď by měla v seznamu atributů shromážděná během registrace uživatelů najdete v části "ShoeSize" a najdete v části v tokenu odeslána zpět do vaší aplikace.

## <a name="notes"></a>Poznámky
* Společně s registraci zásady vlastní atributy lze také v zásadách registrace nebo přihlášení a zásady pro úpravy profilu.
* Není o známé omezení vlastních atributů. Je jen vytvořen při prvním se používá v žádné zásady, a ne v případě, že ho přidáte do seznamu **uživatelské atributy**.

