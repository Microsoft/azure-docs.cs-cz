---
title: Nastavení registrace a přihlášení pomocí účtu Amazon
titleSuffix: Azure AD B2C
description: Poskytněte zákazníkům služby pro registraci a přihlašování k účtům Amazon ve vašich aplikacích pomocí Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/05/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d1a771cb13fcfa76449500ad71c67dcf7c446fa4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85388438"
---
# <a name="set-up-sign-up-and-sign-in-with-an-amazon-account-using-azure-active-directory-b2c"></a>Nastavte si registraci a přihlaste se pomocí účtu Amazon pomocí Azure Active Directory B2C

## <a name="create-an-app-in-the-amazon-developer-console"></a>Vytvoření aplikace v konzole pro vývojáře Amazon

Pokud chcete použít účet Amazon jako federovaného poskytovatele identity v Azure Active Directory B2C (Azure AD B2C), musíte vytvořit aplikaci ve svých [vývojářských službách a technologiích Amazon](https://developer.amazon.com). Pokud ještě nemáte účet Amazon, můžete se zaregistrovat v [https://www.amazon.com/](https://www.amazon.com/) .

> [!NOTE]  
> V **kroku 8** níže použijte následující adresy URL, které nahradíte `your-tenant-name` názvem svého tenanta. Při zadávání názvu tenanta používejte malá písmena, i když je tenant definovaný velkými písmeny v Azure AD B2C.
> - U **povolených zdrojů**zadejte `https://your-tenant-name.b2clogin.com` 
> - Pro **povolené návratové adresy URL**zadejte `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`

[!INCLUDE [identity-provider-amazon-idp-register.md](../../includes/identity-provider-amazon-idp-register.md)]

## <a name="configure-an-amazon-account-as-an-identity-provider"></a>Konfigurace účtu Amazon jako zprostředkovatele identity

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) jako globální správce vašeho tenanta Azure AD B2C.
1. Ujistěte se, že používáte adresář, který obsahuje Azure AD B2C tenanta, a to tak, že v horní nabídce vyberete filtr **adresář + předplatné** a zvolíte adresář, který obsahuje vašeho tenanta.
1. Zvolte **Všechny služby** v levém horním rohu portálu Azure Portal a vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **Zprostředkovatelé identity**a pak vyberte **Amazon**.
1. Zadejte **název**. Příklad: *Amazon*.
1. Pro **ID klienta**zadejte ID klienta aplikace Amazon, kterou jste vytvořili dříve.
1. Pro **tajný klíč klienta**zadejte tajný klíč klienta, který jste si poznamenali.
1. Vyberte **Uložit**.
