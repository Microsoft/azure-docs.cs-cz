---
title: 'Kurz: registrace aplikace'
titleSuffix: Azure AD B2C
description: Naučte se, jak zaregistrovat webovou aplikaci v Azure Active Directory B2C pomocí Azure Portal.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 10/16/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: dbb689182a45367061ae129304a98a8ee9962051
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2020
ms.locfileid: "76840107"
---
# <a name="tutorial-register-an-application-in-azure-active-directory-b2c"></a>Kurz: registrace aplikace v Azure Active Directory B2C

Než můžou vaše [aplikace](application-types.md) pracovat s Azure Active Directory B2C (Azure AD B2C), musí být zaregistrované v tenantovi, které spravujete. V tomto kurzu se dozvíte, jak zaregistrovat webovou aplikaci pomocí Azure Portal.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Registrace webové aplikace
> * Vytvoření tajného klíče klienta

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Pokud jste ještě nevytvořili vlastního [tenanta Azure AD B2C](tutorial-create-tenant.md), vytvořte ho hned teď. Můžete použít stávajícího klienta Azure AD B2C.

## <a name="register-a-web-application"></a>Registrace webové aplikace

K registraci aplikace ve vašem tenantovi Azure AD B2C můžete použít aktuální prostředí **aplikací** nebo naše nové sjednocené **Registrace aplikací (Preview)** . [Další informace o novém prostředí](https://aka.ms/b2cappregintro).

#### <a name="applicationstabapplications"></a>[Aplikace](#tab/applications/)

1. Přihlaste se na web [Azure Portal](https://portal.azure.com).
1. Na panelu nástrojů na portálu vyberte ikonu **adresář + předplatné** a pak vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.
1. V Azure Portal vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **aplikace**a pak vyberte **Přidat**.
1. Zadejte název aplikace. Například *WebApp1*.
1. V případě **zahrnutí webové aplikace/webového rozhraní API** a **Povolení implicitního toku**vyberte **Ano**.
1. V případě **adresy URL odpovědi**Zadejte koncový bod, kde Azure AD B2C by měl vracet jakékoli tokeny, které vaše aplikace požaduje. Můžete ji například nastavit tak, aby naslouchala místně na `https://localhost:44316`. Pokud číslo portu ještě neznáte, můžete zadat zástupnou hodnotu a později ji změnit.

    Pro účely testování, jako je tento kurz, můžete nastavit `https://jwt.ms`, který zobrazuje obsah tokenu pro kontrolu. Pro tento kurz nastavte **adresu URL odpovědi** na `https://jwt.ms`.

    Pro adresy URL odpovědí platí následující omezení:

    * Adresa URL odpovědi musí začínat `https`schématu.
    * Adresa URL odpovědi rozlišuje velká a malá písmena. Jeho velikost se musí shodovat s písmenem adresy URL vaší běžící aplikace. Pokud například vaše aplikace obsahuje jako součást cesty `.../abc/response-oidc`, nezadávejte `.../ABC/response-oidc` v adrese URL odpovědi. Vzhledem k tomu, že webový prohlížeč považuje cesty jako rozlišování velkých a malých písmen, mohou být soubory cookie přidružené k `.../abc/response-oidc` vyloučeny při přesměrování na neshodnou `.../ABC/response-oidc` adresu URL.

1. Výběrem **vytvořit** dokončete registraci aplikace.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Registrace aplikací (Preview)](#tab/app-reg-preview/)

1. Přihlaste se na web [Azure Portal](https://portal.azure.com).
1. Na panelu nástrojů na portálu vyberte ikonu **adresář + předplatné** a pak vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.
1. V Azure Portal vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **Registrace aplikací (Preview)** a pak vyberte **Nová registrace**.
1. Zadejte **název** aplikace. Například *WebApp1*.
1. Vyberte **účty v libovolném organizačním adresáři nebo jakémkoli poskytovateli identity**.
1. V části **identifikátor URI pro přesměrování**vyberte **Web**a potom do textového pole Adresa URL zadejte `https://jwt.ms`.

    Identifikátor URI přesměrování je koncový bod, na který se uživatel pošle prostřednictvím autorizačního serveru (Azure AD B2C, v tomto případě) po dokončení jeho interakce s uživatelem a ke kterému se po úspěšné autorizaci pošle přístupový token nebo autorizační kód. V produkční aplikaci je obvykle veřejně přístupný koncový bod, ve kterém je vaše aplikace spuštěná, například `https://contoso.com/auth-response`. Pro účely testování, jako je tento kurz, můžete nastavit, aby `https://jwt.ms`, webové aplikace vlastněné společností Microsoft, která zobrazuje dekódování obsahu tokenu (obsah tokenu nikdy neopouští prohlížeč). Během vývoje aplikace můžete přidat koncový bod, ve kterém vaše aplikace naslouchá místně, například `https://localhost:5000`. V registrovaných aplikacích můžete kdykoli přidat a změnit identifikátory URI pro přesměrování.

    Pro identifikátory URI přesměrování platí následující omezení:

    * Adresa URL odpovědi musí začínat `https`schématu.
    * Adresa URL odpovědi rozlišuje velká a malá písmena. Jeho velikost se musí shodovat s písmenem adresy URL vaší běžící aplikace. Pokud například vaše aplikace obsahuje jako součást cesty `.../abc/response-oidc`, nezadávejte `.../ABC/response-oidc` v adrese URL odpovědi. Vzhledem k tomu, že webový prohlížeč považuje cesty jako rozlišování velkých a malých písmen, mohou být soubory cookie přidružené k `.../abc/response-oidc` vyloučeny při přesměrování na neshodnou `.../ABC/response-oidc` adresu URL.

1. V části **oprávnění**zaškrtněte políčko *udělit souhlas správcům oprávnění OpenID a offline_access* .
1. Vyberte **Zaregistrovat**.

Po dokončení registrace aplikace povolte tok implicitního udělení:

1. V části **Spravovat**vyberte **ověřování**.
1. Vyberte **vyzkoušet nové prostředí** (Pokud je zobrazeno).
1. V části **implicitní udělení**vyberte zaškrtávací políčka **přístupové tokeny** i **tokeny ID** .
1. Vyberte **Uložit**.

* * *

## <a name="create-a-client-secret"></a>Vytvoření tajného klíče klienta

Pokud vaše aplikace vyměňuje autorizační kód pro přístupový token, je nutné vytvořit tajný klíč aplikace.

#### <a name="applicationstabapplications"></a>[Aplikace](#tab/applications/)

1. Na stránce **Azure AD B2C-aplikace** vyberte aplikaci, kterou jste vytvořili, například *WebApp1*.
1. Vyberte **klíče** a pak vyberte **vygenerovat klíč**.
1. Vyberte **Save (Uložit** ) a zobrazte si klíč. Poznamenejte si hodnotu **Klíč aplikace**. Tuto hodnotu použijete jako tajný klíč aplikace v kódu vaší aplikace.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Registrace aplikací (Preview)](#tab/app-reg-preview/)

1. Na stránce **Azure AD B2C-registrace aplikací (Preview)** vyberte aplikaci, kterou jste vytvořili, například *WebApp1*.
1. V části **Spravovat**vyberte **certifikáty & tajných**kódů.
1. Vyberte **Nový tajný klíč klienta**.
1. Do pole **Popis** zadejte popis tajného klíče klienta. Například *clientsecret1*.
1. Pod položkou **konec platnosti**vyberte dobu, po kterou je tajný kód platný, a pak vyberte **Přidat**.
1. Poznamenejte si **hodnotu**tajného klíče. Tuto hodnotu použijete jako tajný klíč aplikace v kódu vaší aplikace.

* * *

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak:

> [!div class="checklist"]
> * Registrace webové aplikace
> * Vytvoření tajného klíče klienta

V dalším kroku se dozvíte, jak vytvořit toky uživatelů, aby se uživatelé mohli zaregistrovat, přihlašovat a spravovat své profily.

> [!div class="nextstepaction"]
> [Vytváření toků uživatelů v Azure Active Directory B2C >](tutorial-create-user-flows.md)
