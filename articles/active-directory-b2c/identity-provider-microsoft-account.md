---
title: Nastavení registrace a přihlášení pomocí účtu Microsoft
titleSuffix: Azure AD B2C
description: Poskytněte zákazníkům registraci a přihlášení k účtům Microsoft v aplikacích pomocí Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/12/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ad4b08a12e63b4ae3eed0eb09e295d9730de97bb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85387996"
---
# <a name="set-up-sign-up-and-sign-in-with-a-microsoft-account-using-azure-active-directory-b2c"></a>Nastavení registrace a přihlášení pomocí účet Microsoft s využitím Azure Active Directory B2C

## <a name="create-a-microsoft-account-application"></a>Vytvoření aplikace účet Microsoft

Pokud chcete použít účet Microsoft jako [poskytovatele identity](openid-connect.md) v Azure Active Directory B2C (Azure AD B2C), musíte vytvořit aplikaci v TENANTOVI Azure AD. Tenant Azure AD není stejný jako váš tenant Azure AD B2C. Pokud ještě nemáte účet Microsoft, můžete si ho získat na adrese [https://www.live.com/](https://www.live.com/) .

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).
1. Ujistěte se, že používáte adresář, který obsahuje vašeho tenanta Azure AD, a to tak, že v horní nabídce vyberete adresář a filtr **předplatného** a zvolíte adresář, který obsahuje vašeho TENANTA Azure AD.
1. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Registrace aplikací**.
1. Vyberte **Nová registrace**.
1. Zadejte **název** vaší aplikace. Například *MSAapp1*.
1. V části **podporované typy účtů**vyberte **účty v jakémkoli adresáři organizace (libovolný adresář Azure AD – víceklientské) a osobní účty Microsoft (např. Skype, Xbox)**.

   Další informace o různých výběrech typu účtu najdete v tématu [rychlý Start: registrace aplikace s platformou Microsoft Identity](../active-directory/develop/quickstart-register-app.md).
1. V části **identifikátor URI přesměrování (volitelné)** vyberte **Web** a `https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/oauth2/authresp` do textového pole zadejte. Nahraďte `<tenant-name>` názvem vašeho tenanta Azure AD B2C.
1. Vybrat **registraci**
1. Poznamenejte si **ID aplikace (klienta)** zobrazené na stránce Přehled aplikace. Budete ho potřebovat při konfiguraci poskytovatele identity v další části.
1. Vybrat **certifikáty & tajných** kódů
1. Klikněte na **Nový tajný kód klienta**.
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
