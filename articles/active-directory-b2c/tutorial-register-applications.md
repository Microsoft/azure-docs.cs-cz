---
title: 'Kurz: Registrace aplikace'
titleSuffix: Azure AD B2C
description: Zjistěte, jak zaregistrovat webovou aplikaci ve službě Azure Active Directory B2C pomocí portálu Azure.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 10/16/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a688f5e75f7513d0ea4308b751f87f75a2c9510a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183087"
---
# <a name="tutorial-register-an-application-in-azure-active-directory-b2c"></a>Kurz: Registrace aplikace ve službě Azure Active Directory B2C

Než vaše [aplikace](application-types.md) můžou pracovat s Azure Active Directory B2C (Azure AD B2C), musí být registrované v tenantovi, který spravujete. Tento kurz ukazuje, jak zaregistrovat webovou aplikaci pomocí portálu Azure.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Registrace webové aplikace
> * Vytvoření tajného klíče klienta

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites"></a>Požadavky

Pokud jste ještě nevytvořili vlastní [tenanta Azure AD B2C](tutorial-create-tenant.md), vytvořte ho teď. Můžete použít existující klienta Azure AD B2C.

## <a name="register-a-web-application"></a>Registrace webové aplikace

Chcete-li zaregistrovat aplikaci ve vašem tenantovi Azure AD B2C, můžete použít aktuální prostředí **aplikací** nebo naše nové jednotné **registrace aplikací (Preview).** [Další informace o novém prostředí](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Aplikace](#tab/applications/)

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. Vyberte ikonu **Directory + Subscription** na panelu nástrojů portálu a pak vyberte adresář, který obsahuje vašeho klienta Azure AD B2C.
1. Na webu Azure Portal vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **Aplikace**a pak vyberte **Přidat**.
1. Zadejte název aplikace. Například *webapp1*.
1. V **možnostech Zahrnout webové aplikace/ webové rozhraní API** a **Povolit implicitní tok**vyberte **Ano**.
1. Pro **adresu URL odpovědi**zadejte koncový bod, kde azure AD B2C by měl vrátit všechny tokeny, které vaše aplikace požaduje. Můžete ji například nastavit tak, `https://localhost:44316`aby poslouchala místně na adrese . Pokud číslo portu ještě neznáte, můžete zadat zástupnou hodnotu a později ji změnit.

    Pro účely testování, jako je `https://jwt.ms` tento kurz, můžete jej nastavit, na který se zobrazí obsah tokenu pro kontrolu. V tomto kurzu nastavte adresu `https://jwt.ms`URL pro **odpověď** na adresu .

    Pro adresy URL odpovědí platí následující omezení:

    * Adresa URL odpovědi musí `https`začínat schématem .
    * Adresa URL odpovědi rozlišuje malá a velká písmena. Jeho případ musí odpovídat případu cesty URL spuštěné aplikace. Pokud například aplikace obsahuje jako součást `.../abc/response-oidc`své cesty `.../ABC/response-oidc` , nezadávejte v adrese URL odpovědi. Vzhledem k tomu, že webový prohlížeč považuje `.../abc/response-oidc` cesty za rozlišující malá a velká `.../ABC/response-oidc` písmena, mohou být soubory cookie přidružené k případu vyloučeny, pokud jsou přesměrovány na adresu URL neodpovídající případ.

1. Chcete-li dokončit registraci aplikace, vyberte **Vytvořit.**

#### <a name="app-registrations-preview"></a>[Registrace aplikací (náhled)](#tab/app-reg-preview/)

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. Vyberte ikonu **Directory + Subscription** na panelu nástrojů portálu a pak vyberte adresář, který obsahuje vašeho klienta Azure AD B2C.
1. Na webu Azure Portal vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **Registrace aplikací (preview)** a pak vyberte **Nová registrace**.
1. Zadejte **název** aplikace. Například *webapp1*.
1. Vyberte **účty v libovolném organizačním adresáři nebo v libovolném zprostředkovateli identity**.
1. V části **Přesměrování identifikátoru URI** `https://jwt.ms` vyberte možnost **Web**a zadejte do textového pole URL.

    Identifikátor URI přesměrování je koncový bod, ke kterému je uživatel odeslán autorizačním serverem (Azure AD B2C, v tomto případě) po dokončení jeho interakce s uživatelem a ke kterému je po úspěšné autorizaci odeslán přístupový token nebo autorizační kód. V produkční aplikaci se obvykle jedná o veřejně přístupný koncový bod, `https://contoso.com/auth-response`kde je vaše aplikace spuštěná, třeba . Pro účely testování, jako je tento `https://jwt.ms`kurz, můžete jej nastavit na , webové aplikace vlastněné společností Microsoft, která zobrazuje dekódovaný obsah tokenu (obsah tokenu nikdy neopustí váš prohlížeč). Během vývoje aplikace můžete přidat koncový bod, kde vaše `https://localhost:5000`aplikace naslouchá místně, jako je například . Identifikátory URI přesměrování adres URI můžete v registrovaných aplikacích kdykoli přidat a upravit.

    Pro přesměrování identifikátorů URI platí následující omezení:

    * Adresa URL odpovědi musí `https`začínat schématem .
    * Adresa URL odpovědi rozlišuje malá a velká písmena. Jeho případ musí odpovídat případu cesty URL spuštěné aplikace. Pokud například aplikace obsahuje jako součást `.../abc/response-oidc`své cesty `.../ABC/response-oidc` , nezadávejte v adrese URL odpovědi. Vzhledem k tomu, že webový prohlížeč považuje `.../abc/response-oidc` cesty za rozlišující malá a velká `.../ABC/response-oidc` písmena, mohou být soubory cookie přidružené k případu vyloučeny, pokud jsou přesměrovány na adresu URL neodpovídající případ.

1. V **části Oprávnění**zaškrtněte políčko *Udělit oprávnění správce k oprávněním openid a offline_access.*
1. Vyberte **Zaregistrovat**.

Po dokončení registrace aplikace povolte implicitní tok grantu:

1. V části **Manage**vyberte **Authentication**.
1. Vyberte **Vyzkoušet nové prostředí** (pokud je zobrazeno).
1. V **části Implicitní udělení**zaškrtněte políčka **Tokeny aplikace Access** i **Tokeny ID.**
1. Vyberte **Uložit**.

* * *

## <a name="create-a-client-secret"></a>Vytvoření tajného klíče klienta

Pokud si aplikace vyměňuje autorizační kód pro přístupový token, je třeba vytvořit tajný klíč aplikace.

#### <a name="applications"></a>[Aplikace](#tab/applications/)

1. Na stránce **Azure AD B2C – aplikace** vyberte aplikaci, kterou jste vytvořili, například *webapp1*.
1. Vyberte **Klávesy** a pak vyberte **Generovat klíč**.
1. Chcete-li zobrazit klíč, vyberte **možnost Uložit.** Poznamenejte si hodnotu **Klíč aplikace**. Tuto hodnotu použijete jako tajný klíč aplikace v kódu aplikace.

#### <a name="app-registrations-preview"></a>[Registrace aplikací (náhled)](#tab/app-reg-preview/)

1. Na stránce **Azure AD B2C – registrace aplikací (Preview)** vyberte aplikaci, kterou jste vytvořili, například *webapp1*.
1. V části **Správa**vyberte **možnost Certifikáty & tajných kódů**.
1. Vyberte **Nový tajný klíč klienta**.
1. Do pole Popis zadejte popis tajného klíče **klienta.** Například *clientsecret1*.
1. V části **Expires**vyberte dobu, po kterou je tajný klíč platný, a pak vyberte **Přidat**.
1. Zaznamenejte **hodnotu**tajného klíče . Tuto hodnotu použijete jako tajný klíč aplikace v kódu aplikace.

* * *

## <a name="next-steps"></a>Další kroky

V tomto článku jste se naučili:

> [!div class="checklist"]
> * Registrace webové aplikace
> * Vytvoření tajného klíče klienta

Dále se dozvíte, jak vytvořit toky uživatelů, aby se uživatelé mohli zaregistrovat, přihlásit se a spravovat své profily.

> [!div class="nextstepaction"]
> [Vytváření uživatelských toků ve službě Azure Active Directory B2C >](tutorial-create-user-flows.md)
