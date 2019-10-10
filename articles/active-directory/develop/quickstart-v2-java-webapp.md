---
title: Microsoft Identity Platform Java Web App Starter | Azure
description: Naučte se implementovat přihlašování Microsoftu na webové aplikaci Java pomocí OpenID Connect.
services: active-directory
documentationcenter: dev-center-name
author: sangonzal
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/09/2019
ms.author: sagonzal
ms.custom: aaddev
ms.openlocfilehash: 8bb9073ccb4aef81b46b3b2b87730ddede5c0ff7
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2019
ms.locfileid: "72240195"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-java-web-app"></a>Rychlý Start: přidání přihlášení do webové aplikace Java pomocí Microsoftu

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

V tomto rychlém startu se dozvíte, jak integrovat webovou aplikaci Java s platformou Microsoft identity. Vaše aplikace se přihlásí k uživateli, získá přístupový token pro volání rozhraní API Microsoft Graph a vytvoří požadavek na rozhraní Microsoft Graph API. 

Po dokončení průvodce bude aplikace přijímat přihlašovacíky osobních účtů Microsoft (včetně outlook.com, live.com a dalších) a pracovních nebo školních účtů z jakékoli společnosti nebo organizace, která používá Azure Active Directory.

![Ukazuje, jak ukázková aplikace vygenerovaná tímto rychlým startem funguje.](media/quickstart-v2-java-webapp/java-quickstart.svg)

## <a name="prerequisites"></a>Požadované součásti

K provedení této ukázky budete potřebovat:
- [Java Development Kit (JDK)](https://openjdk.java.net/) 8 nebo vyšší a [Maven](https://maven.apache.org/).

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registrace a stažení aplikace pro rychlý Start
> Máte dvě možnosti, jak spustit aplikaci pro rychlý Start: Express (možnost 1) nebo ruční (možnost 2).
> 
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Možnost 1: registrace a Automatická konfigurace aplikace a stažení ukázky kódu
>
> 1. Přejít na [Registrace aplikací Azure Portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps).
> 1. Zadejte název vaší aplikace a vyberte **zaregistrovat**.
> 1. Podle pokynů stáhněte a automaticky nakonfigurujte novou aplikaci.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Možnost 2: registrace a ruční konfigurace vaší aplikace a ukázky kódu
> 
>
> #### <a name="step-1-register-your-application"></a>Krok 1: registrace aplikace
> K registraci aplikace a ručnímu přidání registračních informací aplikace do řešení použijte následující postup:
>
> 1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
> 1. Pokud vám váš účet poskytne přístup k více než jednomu klientovi, vyberte svůj účet v pravém horním rohu a nastavte relaci portálu na požadovaného tenanta Azure AD.
> 1. Přejděte na stránku [Registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) Microsoft Identity Platform for Developers.
> 1. Vyberte **Nová registrace**.
> 1. Jakmile se zobrazí stránka **Registrovat aplikaci** , zadejte informace o registraci vaší aplikace:
>    - V části **název** zadejte smysluplný název aplikace, který se zobrazí uživatelům aplikace, například `java-webapp`.
>    - Nyní nechejte **identifikátor URI pro přesměrování** prázdné a vyberte **Registrovat**.
> 1. Vyhledejte hodnotu **ID aplikace (klienta)** aplikace. Zkopírujte tuto hodnotu, budete ji potřebovat později.
> 1. Vyhledejte hodnotu **ID adresáře aplikace (tenant)** . Zkopírujte tuto hodnotu, budete ji potřebovat později.
> 1. Vyberte nabídku **ověřování** a přidejte následující informace:
>    - Do rozevíracích **identifikátorů URI pro přesměrování**přidejte `http://localhost:8080/msal4jsamples/secure/aad` a `https://localhost:8080/msal4jsamples/graph/users`.
>    - Vyberte **Uložit**.
> 1. V nabídce vlevo vyberte **certifikáty & tajných** kódů a v části **tajné klíče klienta** klikněte na **nový tajný klíč klienta** :
>     
>    - Zadejte popis klíče (instance tajného kódu aplikace).
>    - Vyberte dobu trvání klíče **v intervalu 1 roku**.
>    - Po kliknutí na tlačítko **Přidat**se zobrazí hodnota klíče. 
>    - Zkopírujte hodnotu klíče, budete ji potřebovat později.
>
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Krok 1: Konfigurace aplikace v Azure Portal
> Ukázku kódu pro tento rychlý Start, který funguje, je třeba:
> 1. Přidejte adresy URL odpovědi jako `http://localhost:8080/msal4jsamples/secure/aad` a `https://localhost:8080/msal4jsamples/graph/users`.
> 1. Vytvořte tajný klíč klienta.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Tuto změnu pro mě udělat]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Already nakonfigurovaná na @ no__t-1 aplikace je nakonfigurovaná s těmito atributy.

#### <a name="step-2-download-the-code-sample"></a>Krok 2: stažení ukázky kódu
 
 [Stažení ukázky kódu](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip)
 
 #### <a name="step-3-configure-the-code-sample"></a>Krok 3: Konfigurace ukázky kódu 
 
 1. Extrahujte soubor zip do místní složky.
 1. Pokud používáte integrované vývojové prostředí, otevřete ukázku v oblíbeném INTEGROVANÉm vývojovém prostředí (volitelné).
 1. Otevřete soubor **Application. Properties** , který lze najít v části *Src/Main/Resources/* .
 1. Nahraďte vlastnosti aplikace.
   1. Vyhledejte `aad.clientId` a aktualizujte hodnotu `Enter_the_Application_Id_here` s hodnotou **ID aplikace (klienta)** , kterou jste zaregistrovali. 
   1. Vyhledejte `aad.authority` a aktualizujte hodnotu `Enter_the_Tenant_Name_Here` s hodnotou **ID adresáře (tenant)** , kterou jste zaregistrovali.
   1. Vyhledejte `aad.secretKey` a aktualizujte hodnotu `Enter_the_Client_Secret_Here` s **tajným klíčem klienta** , který jste vytvořili v části **certifikáty & tajných** kódů pro aplikaci, kterou jste zaregistrovali.

> [!div renderon="docs"]
> Kde:
>
> - `Enter_the_Application_Id_here` – je ID aplikace, kterou jste zaregistrovali.
> - `Enter_the_Client_Secret_Here`-je **tajný klíč klienta** , který jste vytvořili v části **certifikáty & tajných** kódů pro zaregistrovanou aplikaci.

#### <a name="step-4-run-the-code-sample"></a>Krok 4: spuštění ukázky kódu
1. Spusťte ukázku kódu a otevřete prohlížeč a přejděte na *http://localhost:8080* .
1. Přední stránka obsahuje **přihlašovací** tlačítko. Klikněte na tlačítko **Přihlásit** se a přesměrujte na Azure Active Directory. Uživateli se zobrazí výzva k zadání přihlašovacích údajů.  
1. Po úspěšném ověření u Azure Active Directory budou přesměrovány na *http://localhost:8080/msal4jsamples/secure/aad* . Jsou oficiálně přihlášeni k aplikaci a stránka by měla zobrazovat informace pro přihlášený účet. Bude obsahovat také tlačítka pro: 
    - *Odhlásit*se: odhlásí aktuálního uživatele z aplikace a přesměruje je na domovskou stránku.
    - *Zobrazit uživatele*: Získá token pro Microsoft Graph a potom zavolejte Microsoft Graph s tokenem připojeným k žádosti o získání všech uživatelů v tenantovi.

## <a name="more-information"></a>Další informace

### <a name="getting-msal"></a>Získání MSAL
MSAL4J je knihovna používaná k přihlašování uživatelů a žádosti o tokeny používané pro přístup k rozhraní API chráněnému platformou Microsoft identity. Můžete přidat MSAL4J do aplikace pomocí Maven nebo Gradle pro správu závislostí tím, že provedete následující změny v souboru pom. XML nebo Build. Gradle ve vaší aplikaci. 

```XML
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>msal4j</artifactId>
    <version>0.5.0-preview</version>
</dependency>
```

```$xslt
compile group: 'com.microsoft.azure', name: 'msal4j', version: '0.5.0-preview'
```


### <a name="msal-initialization"></a>Inicializace MSAL
Odkaz na MSAL4J můžete přidat přidáním následujícího kódu do horní části souboru, kde budete používat MSAL4J: 

```Java
import com.microsoft.aad.msal4j.*;
```

## <a name="next-steps"></a>Další kroky

Další informace o oprávněních a souhlasu:

> [!div class="nextstepaction"]
> [Oprávnění a souhlas](https://docs.microsoft.com/en-us/azure/active-directory/develop/v2-permissions-and-consent)

Další informace o toku ověřování pro tento scénář najdete v tématu tok autorizačního kódu OAuth 2,0:

> [!div class="nextstepaction"]
> [Tok OAuth autorizačním kódem](https://docs.microsoft.com/en-us/azure/active-directory/develop/v2-oauth2-auth-code-flow)

Pomůžeme nám vylepšit platformu Microsoft identity. Řekněte nám, co si myslíte, díky krátkému průzkumu dvou dotazů.

> [!div class="nextstepaction"]
> [Microsoft Identity Platform Survey](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
