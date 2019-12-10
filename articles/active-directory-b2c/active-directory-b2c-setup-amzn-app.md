---
title: Nastavení registrace a přihlášení pomocí účtu Amazon
titleSuffix: Azure AD B2C
description: Poskytněte zákazníkům služby pro registraci a přihlašování k účtům Amazon ve vašich aplikacích pomocí Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: e43c880fb2e38a235c38e19854da9e2b6e2a10c0
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2019
ms.locfileid: "74947710"
---
# <a name="set-up-sign-up-and-sign-in-with-an-amazon-account-using-azure-active-directory-b2c"></a>Nastavte si registraci a přihlaste se pomocí účtu Amazon pomocí Azure Active Directory B2C

## <a name="create-an-amazon-application"></a>Vytvoření aplikace Amazon

Pokud chcete použít účet Amazon jako [poskytovatele identity](active-directory-b2c-reference-oauth-code.md) v Azure Active Directory B2C (Azure AD B2C), musíte ve svém tenantovi vytvořit aplikaci, která ho bude představovat. Pokud ještě nemáte účet Amazon, můžete se zaregistrovat na [https://www.amazon.com/](https://www.amazon.com/).

1. Přihlaste se k [centru pro vývojáře Amazon](https://login.amazon.com/) pomocí svých přihlašovacích údajů k účtu Amazon.
1. Pokud jste to ještě neudělali, klikněte na **zaregistrovat**, postupujte podle kroků registrace pro vývojáře a přijměte zásadu.
1. Vyberte **Registrovat novou aplikaci**.
1. Zadejte **název**, **Popis**a **adresu URL pro oznámení o ochraně osobních údajů**a potom klikněte na **Uložit**. Oznámení o ochraně osobních údajů je stránka, kterou spravujete, která poskytuje uživatelům informace o ochraně osobních údajů.
1. V části **Nastavení webu** ZKOPÍRUJTE hodnoty **ID klienta**. Pokud chcete získat tajný klíč klienta a pak ho zkopírovat, vyberte **Zobrazit tajný kód** . K nakonfigurování účtu Amazon jako poskytovatele identity ve vašem tenantovi potřebujete obě tyto služby. **Tajný kód klienta** je důležité bezpečnostní pověření.
1. V části **Nastavení webu** vyberte **Upravit**a potom zadejte `https://your-tenant-name.b2clogin.com` v části **Povolené zdroje JavaScriptu** a `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` v části **povolené návratové adresy URL**. Nahraďte `your-tenant-name` názvem vašeho tenanta. Při zadávání názvu tenanta musíte použít malá písmena, i když je tenant definovaný velkými písmeny v Azure AD B2C.
1. Klikněte na **Uložit**.

## <a name="configure-an-amazon-account-as-an-identity-provider"></a>Konfigurace účtu Amazon jako zprostředkovatele identity

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) jako globální správce vašeho tenanta Azure AD B2C.
1. Ujistěte se, že používáte adresář, který obsahuje Azure AD B2C tenanta, a to tak, že v horní nabídce vyberete filtr **adresář + předplatné** a zvolíte adresář, který obsahuje vašeho tenanta.
1. Zvolte **Všechny služby** v levém horním rohu portálu Azure Portal a vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **Zprostředkovatelé identity**a pak vyberte **Amazon**.
1. Zadejte **název**. Příklad: *Amazon*.
1. Pro **ID klienta**zadejte ID klienta aplikace Amazon, kterou jste vytvořili dříve.
1. Pro **tajný klíč klienta**zadejte tajný klíč klienta, který jste si poznamenali.
1. Vyberte **Save** (Uložit).
