---
title: Kurz – registrace aplikace v Azure Active Directory B2C | Dokumentace Microsoftu
description: Informace o postupu registrace aplikace v Azure Active Directory B2C na webu Azure portal.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 01/11/2019
ms.author: davidmu
ms.openlocfilehash: 99ad1bbaa732b1207ead9da8da36f345d4978241
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54856022"
---
# <a name="tutorial-register-your-applications-in-azure-active-directory-b2c"></a>Kurz: Registrace aplikace v Azure Active Directory B2C

Před vaše [aplikací](active-directory-b2c-apps.md) mohou komunikovat s Azure Active Directory (Azure AD) B2C, musí být zaregistrovaná v tenantovi, která spravujete. V tomto kurzu se dozvíte, jak registrovat aplikace pomocí webu Azure portal.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Registrace webové aplikace
> * Registrace webové rozhraní API
> * Registrace mobilní nebo nativní aplikace

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Pokud jste ještě nevytvořili vlastní [Tenanta Azure AD B2C](tutorial-create-tenant.md), založte si ho teď. Můžete používat existujícího tenanta.

## <a name="register-a-web-application"></a>Registrace webové aplikace

1. Ujistěte se, že používáte adresáře, který obsahuje vašeho tenanta Azure AD B2C kliknutím **filtr adresářů a předplatných** v horní nabídce a výběrem adresáře, který obsahuje váš tenant.

    ![Přepnout na adresář předplatného](./media/tutorial-register-applications/switch-directories.png)

2. Zvolte **všechny služby** v horním levém horním rohu webu Azure portal a poté vyhledejte a vyberte **Azure AD B2C**.
3. Vyberte **aplikací**a pak vyberte **přidat**.

    ![Přidání aplikace](./media/tutorial-register-applications/add-application.png)

4. Zadejte název aplikace. Například *webapp1*.
5. Pro **zahrnout webovou aplikaci / webové rozhraní API** a **povolit implicitní tok**vyberte **Ano**.
6. Pro **adresy URL odpovědi**, zadejte koncový bod kam by měl Azure AD B2C vrátí všechny tokeny, které vaše aplikace požaduje. Například můžete nastavit ji pro naslouchání lokálně na `https://localhost:44316` Pokud ještě neznáte číslo portu, můžete zadat hodnotu zástupného symbolu a později změnit. Pro účely testování můžete ji nastavit `https://jwt.ms`, který se zobrazí obsah tokenu pro kontrolu. Pro účely tohoto kurzu, nastavte ho na `https://jwt.ms`. 

    Odpověď URL musí začínat schématem `https`, a všechny odpovědi, adresa URL musí sdílet jednu doménu DNS. Například, pokud aplikace obsahuje adresu URL odpovědi z `https://login.contoso.com`, jako je tato adresa URL můžete přidat k němu `https://login.contoso.com/new`. Nebo mohou odkazovat na subdoménu DNS `login.contoso.com`, jako například `https://new.login.contoso.com`. Pokud chcete, aby byla aplikace s `login-east.contoso.com` a `login-west.contoso.com` podle adresy URL odpovědí, je nutné přidat tyto adresy URL odpovědi v tomto pořadí: `https://contoso.com`, `https://login-east.contoso.com`, `https://login-west.contoso.com`. Druhé dvě můžete přidat, protože jsou subdoménami první adresy URL odpovědi, `contoso.com`.

7. Klikněte na možnost **Vytvořit**.

    ![Nastavení vlastností aplikace](./media/tutorial-register-applications/application-properties.png)

### <a name="create-a-client-secret"></a>Vytvoření tajného klíče klienta

Pokud jste aplikaci vymění kód pro token, je potřeba vytvořit tajný klíč aplikace.

1. Vyberte **klíče** a potom klikněte na tlačítko **vygenerovat klíč**.

    ![Generování klíčů](./media/tutorial-register-applications/generate-keys.png)

2. Vyberte **Uložit** zobrazíte klíče. Poznamenejte si hodnotu **Klíč aplikace**. Tuto hodnotu použijete jako tajný klíč aplikace v kódu vaší aplikace.

    ![Uložte klíč](./media/tutorial-register-applications/save-key.png)
    
3. Vyberte **přístup přes rozhraní API**, klikněte na tlačítko **přidat**a vyberte vaše webové rozhraní API a obory (oprávnění).

    ![Konfigurovat přístup k rozhraní API](./media/tutorial-register-applications/api-access.png)

## <a name="register-a-web-api"></a>Registrace webové rozhraní API

1. Vyberte **aplikací**a pak vyberte **přidat**.
3. Zadejte název aplikace. Například *webapi1*.
4. Pro **zahrnout webovou aplikaci / webové rozhraní API** a **povolit implicitní tok**vyberte **Ano**.
5. Pro **adresy URL odpovědi**, zadejte koncový bod kam by měl Azure AD B2C vrátí všechny tokeny, které vaše aplikace požaduje. Například můžete nastavit ji pro naslouchání lokálně na `https://localhost:44316`. Pokud ještě neznáte číslo portu, můžete zadat hodnotu zástupného symbolu a později změnit.
6. Pro **identifikátor ID URI aplikace**, zadejte identifikátor použitý pro vaše webového rozhraní API. Vygeneruje se pro vás úplný identifikátor URI včetně domény. Například, `https://contosotenant.onmicrosoft.com/api`.
7. Klikněte na možnost **Vytvořit**.
8. Vyberte *webapi1* aplikaci, která jste vytvořili a pak vyberte **publikované obory** na podle potřeby přidejte další obory. Ve výchozím nastavení `user_impersonation` rozsah je definován. `user_impersonation` Oboru umožňuje aplikacím přístup k tomuto rozhraní API jménem přihlášeného uživatele. Pokud chcete, `user_impersonation` obor je možné odebrat.

    ![Nastavte publikované obory](./media/tutorial-register-applications/published-scopes.png)


## <a name="register-a-mobile-or-native-application"></a>Registrace mobilní nebo nativní aplikace

1. Vyberte **aplikací**a pak vyberte **přidat**.
2. Zadejte název aplikace. Například *nativeapp1*.
3. Pro **zahrnout webovou aplikaci / webové rozhraní API**vyberte **ne**.
4. Pro **zahrnout nativního klienta**vyberte **Ano**.
5. Pro **identifikátor URI pro přesměrování**, zadejte na platný identifikátor URI přesměrování s vlastním schématem. Existují dva důležité aspekty při výběru identifikátoru URI přesměrování:

    - **Jedinečné** – schéma identifikátoru URI přesměrování by mělo být pro každou aplikaci jedinečné. V příkladu `com.onmicrosoft.contoso.appname://redirect/path`, `com.onmicrosoft.contoso.appname` schéma com.onmicrosoft.contoso.appname. Tento model má následovat. Pokud dvě aplikace sdílejí stejné schéma, uživateli je možnost volby vybrat aplikace. Pokud uživatel použije nesprávnou volbu, přihlášení selže.
    - **Kompletní** – identifikátor URI pro přesměrování musí mít schéma a cestu. Cesta musí obsahovat za doménou alespoň jedno lomítko. Například `//contoso/` funguje a `//contoso` selže. Ujistěte se, že identifikátor URI přesměrování neobsahuje speciální znaky jako podtržítka.

6. Klikněte na možnost **Vytvořit**.

## <a name="next-steps"></a>Další postup

V tomto článku jste zjistili, jak:

> [!div class="checklist"]
> * Registrace webové aplikace
> * Registrace webové rozhraní API
> * Registrace mobilní nebo nativní aplikace

> [!div class="nextstepaction"]
> [Vytvořit toky uživatelů v Azure Active Directory B2C](tutorial-create-user-flows.md)