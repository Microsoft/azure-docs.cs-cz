---
title: Nastavení registrace a přihlášení pomocí účtu Microsoft
titleSuffix: Azure AD B2C
description: Zajistěte registraci a přihlášení zákazníkům pomocí účtů Microsoft ve vašich aplikacích pomocí služby Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 25784eb161a860398b0741d1d20375cabd1c4eca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188014"
---
# <a name="set-up-sign-up-and-sign-in-with-a-microsoft-account-using-azure-active-directory-b2c"></a>Nastavení registrace a přihlášení pomocí účtu Microsoft pomocí služby Azure Active Directory B2C

## <a name="create-a-microsoft-account-application"></a>Vytvoření aplikace účtu Microsoft

Pokud chcete použít účet Microsoft jako [poskytovatele identity](openid-connect.md) ve službě Azure Active Directory B2C (Azure AD B2C), musíte vytvořit aplikaci v tenantovi Azure AD. Tenant Azure AD není stejný jako váš klient Azure AD B2C. Pokud ještě nemáte účet Microsoft, můžete ho získat [https://www.live.com/](https://www.live.com/)na webu .

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. Ujistěte se, že používáte adresář, který obsahuje vašeho klienta Azure AD výběrem filtru **directory + předplatné** v horní nabídce a výběrem adresáře, který obsahuje vašeho klienta Azure AD.
1. V levém horním rohu portálu Azure zvolte **Všechny služby** a pak **vyhledejte**a vyberte registrace aplikací .
1. Vyberte **možnost Nová registrace**.
1. Zadejte **název** aplikace. Například *MSAapp1*.
1. V části **Podporované typy účtů**vyberte Účty ve všech **organizačních adresářích a osobních účtech Microsoft (např. skype, xbox, Outlook.com).** Tato možnost se zaměřuje na nejširší sadu identit microsoftu.

   Další informace o různých typech účtů naleznete v [tématu Úvodní příručka: Registrace aplikace na platformě identit společnosti Microsoft](../active-directory/develop/quickstart-register-app.md).
1. V části **Přesměrování identifikátoru URI (volitelné)** vyberte **Web** a zadejte `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` do textového pole. Nahraďte `your-tenant-name` název klienta Azure AD B2C.
1. Vybrat **registr**
1. Zaznamenejte **ID aplikace (klienta)** zobrazené na stránce Přehled aplikace. Potřebujete to při konfiguraci zprostředkovatele identity v další části.
1. Vybrat **certifikáty & tajných kódů**
1. Klikněte na **Nový tajný klíč klienta.**
1. Zadejte **popis** tajného klíče, například *heslo aplikace 1*, a klepněte na tlačítko **Přidat**.
1. Zaznamenejte heslo aplikace zobrazené ve sloupci **Hodnota.** Potřebujete to při konfiguraci zprostředkovatele identity v další části.

## <a name="configure-a-microsoft-account-as-an-identity-provider"></a>Konfigurace účtu Microsoft jako poskytovatele identity

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) jako globální správce vašeho tenanta Azure AD B2C.
1. Ujistěte se, že používáte adresář, který obsahuje vašeho klienta Azure AD B2C výběrem directory **+ předplatné** filtr v horní nabídce a výběrem adresáře, který obsahuje vašeho klienta.
1. Zvolte **Všechny služby** v levém horním rohu portálu Azure Portal a vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **Zprostředkovatelé identity**a pak vyberte **Účet Microsoft**.
1. Zadejte **název**. Například *MSA*.
1. Pro **ID klienta**zadejte ID aplikace (klienta) aplikace Azure AD, kterou jste vytvořili dříve.
1. Pro **tajný klíč klienta**zadejte tajný klíč klienta, který jste nahráli.
1. Vyberte **Uložit**.
