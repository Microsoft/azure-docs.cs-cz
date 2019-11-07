---
title: Nastavení registrace a přihlášení pomocí účtu Microsoft – Azure Active Directory B2C
description: Poskytněte zákazníkům registraci a přihlášení k účtům Microsoft v aplikacích pomocí Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: a921d6110fd0294012e2e59542ba2c82d63ffcde
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73642082"
---
# <a name="set-up-sign-up-and-sign-in-with-a-microsoft-account-using-azure-active-directory-b2c"></a>Nastavení registrace a přihlášení pomocí účet Microsoft s využitím Azure Active Directory B2C

## <a name="create-a-microsoft-account-application"></a>Vytvoření aplikace účet Microsoft

Pokud chcete použít účet Microsoft jako [poskytovatele identity](active-directory-b2c-reference-oidc.md) v Azure Active Directory B2C (Azure AD B2C), musíte vytvořit aplikaci v TENANTOVI Azure AD. Tenant Azure AD není stejný jako váš tenant Azure AD B2C. Pokud ještě nemáte účet Microsoft, můžete si ho získat na [https://www.live.com/](https://www.live.com/).

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Ujistěte se, že používáte adresář, který obsahuje vašeho tenanta Azure AD, a to tak, že v horní nabídce vyberete adresář a filtr **předplatného** a zvolíte adresář, který obsahuje vašeho TENANTA Azure AD.
1. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Registrace aplikací**.
1. Vyberte **Nová registrace**.
1. Zadejte **název** vaší aplikace. Například *MSAapp1*.
1. V části **podporované typy účtů**vyberte **účty v libovolném organizačním adresáři a osobní účty Microsoft (např. Skype, Xbox, Outlook.com)** . Tato možnost cílí na nejširší sadu identit Microsoftu.

   Další informace o různých výběrech typu účtu najdete v tématu [rychlý Start: registrace aplikace s platformou Microsoft Identity](../active-directory/develop/quickstart-register-app.md).
1. V části **identifikátor URI přesměrování (volitelné)** vyberte **Web** a do textového pole zadejte `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`. Nahraďte `your-tenant-name` názvem tenanta Azure AD B2C.
1. Vybrat **registraci**
1. Poznamenejte si **ID aplikace (klienta)** zobrazené na stránce Přehled aplikace. Budete ho potřebovat při konfiguraci poskytovatele identity v další části.
1. Vybrat **certifikáty & tajných** kódů
1. Klikněte na **nový tajný klíč klienta** .
1. Zadejte **Popis** tajného kódu, například *heslo aplikace 1*, a pak klikněte na **Přidat**.
1. Poznamenejte si heslo aplikace zobrazené ve sloupci **hodnota** . Budete ho potřebovat při konfiguraci poskytovatele identity v další části.

## <a name="configure-a-microsoft-account-as-an-identity-provider"></a>Konfigurace účet Microsoft jako zprostředkovatele identity

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) jako globální správce vašeho tenanta Azure AD B2C.
1. Ujistěte se, že používáte adresář, který obsahuje Azure AD B2C tenanta, a to tak, že v horní nabídce vyberete filtr **adresář + předplatné** a zvolíte adresář, který obsahuje vašeho tenanta.
1. Zvolte **Všechny služby** v levém horním rohu portálu Azure Portal a vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **Zprostředkovatelé identity**a pak vyberte **účet Microsoft**.
1. Zadejte **název**. Například *MSA*.
1. Pro **ID klienta**zadejte ID aplikace (klienta) aplikace služby Azure AD, kterou jste vytvořili dříve.
1. Pro **tajný klíč klienta**zadejte tajný klíč klienta, který jste si poznamenali.
1. Vyberte **Uložit**.
