---
title: Kurz – registrace aplikace – Azure Active Directory B2C
description: Naučte se, jak zaregistrovat webovou aplikaci v Azure Active Directory B2C pomocí Azure Portal.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 08/23/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 651c15c8206f7956bb35520f9c5837cb0c9308f9
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2019
ms.locfileid: "69980692"
---
# <a name="tutorial-register-an-application-in-azure-active-directory-b2c"></a>Kurz: Registrace aplikace v Azure Active Directory B2C

Než můžou vaše [aplikace](active-directory-b2c-apps.md) pracovat s Azure Active Directory (Azure AD) B2C, musí být zaregistrované v tenantovi, které spravujete. V tomto kurzu se dozvíte, jak zaregistrovat webovou aplikaci pomocí Azure Portal.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Registrace webové aplikace
> * Vytvoření tajného klíče klienta

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Pokud jste ještě nevytvořili vlastního [tenanta Azure AD B2C](tutorial-create-tenant.md), vytvořte ho hned teď. Můžete použít stávajícího klienta Azure AD B2C.

## <a name="register-a-web-application"></a>Registrace webové aplikace

1. Ujistěte se, že používáte adresáře, který obsahuje vašeho tenanta Azure AD B2C kliknutím **filtr adresářů a předplatných** v horní nabídce a výběrem adresáře, který obsahuje váš tenant.
1. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **aplikace**a pak vyberte **Přidat**.
1. Zadejte název aplikace. Například *WebApp1*.
1. V případě **zahrnutí webové aplikace/webového rozhraní API** a **Povolení implicitního toku**vyberte **Ano**.
1. V případě **adresy URL odpovědi**Zadejte koncový bod, kde Azure AD B2C by měl vracet jakékoli tokeny, které vaše aplikace požaduje. Například můžete nastavit, aby naslouchal místně na `https://localhost:44316`. Pokud číslo portu ještě neznáte, můžete zadat zástupnou hodnotu a později ji změnit.

    Pro účely testování, jako je tento kurz, ho můžete `https://jwt.ms` nastavit na, který zobrazuje obsah tokenu pro kontrolu. Pro tento kurz nastavte **adresu URL odpovědi** na `https://jwt.ms`.

    Pro adresy URL odpovědí platí následující omezení:

    * Adresa URL odpovědi musí začínat schématem `https`.
    * Adresa URL odpovědi rozlišuje velká a malá písmena. Jeho velikost se musí shodovat s písmenem adresy URL vaší běžící aplikace. Například pokud vaše aplikace obsahuje jako součást cesty `.../abc/response-oidc`, nezadávejte `.../ABC/response-oidc` v adrese URL odpovědi. Vzhledem k tomu, že webový prohlížeč považuje cesty jako rozlišování velkých a `.../abc/response-oidc` malých písmen, mohou být soubory cookie přidružené k vyloučeny při `.../ABC/response-oidc` přesměrování na neshodnou adresu URL.

1. Kliknutím na **vytvořit** dokončete registraci aplikace.

## <a name="create-a-client-secret"></a>Vytvoření tajného klíče klienta

Pokud vaše aplikace vyměňuje kód pro token, je nutné vytvořit tajný klíč aplikace.

1. Na stránce **Azure AD B2C-aplikace** vyberte aplikaci, kterou jste vytvořili, například *WebApp1*.
1. Vyberte **klíče** a pak vyberte **vygenerovat klíč**.
1. Vyberte **Save (Uložit** ) a zobrazte si klíč. Poznamenejte si hodnotu **Klíč aplikace**. Tuto hodnotu použijete jako tajný klíč aplikace v kódu vaší aplikace.

## <a name="next-steps"></a>Další postup

V tomto článku jste zjistili, jak:

> [!div class="checklist"]
> * Registrace webové aplikace
> * Vytvoření tajného klíče klienta

V dalším kroku se dozvíte, jak vytvořit toky uživatelů, aby se uživatelé mohli zaregistrovat, přihlašovat a spravovat své profily.

> [!div class="nextstepaction"]
> [Vytváření toků uživatelů v Azure Active Directory B2C >](tutorial-create-user-flows.md)
