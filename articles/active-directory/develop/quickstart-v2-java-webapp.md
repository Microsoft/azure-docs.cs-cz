---
title: Microsoft Identity Platform Java Web App Starter | Azure
description: Naučte se implementovat přihlašování Microsoftu na webové aplikaci Java pomocí OpenID Connect.
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/09/2019
ms.author: sagonzal
ms.custom: aaddev, scenarios:getting-started, languages:Java
ms.openlocfilehash: 59c2b3b910a9585362643bfcf7cdf9fa2df977bc
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/26/2020
ms.locfileid: "77611998"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-java-web-app"></a>Rychlý Start: přidání přihlášení do webové aplikace Java pomocí Microsoftu

V tomto rychlém startu se dozvíte, jak integrovat webovou aplikaci Java s platformou Microsoft identity. Vaše aplikace se přihlásí k uživateli, získá přístupový token pro volání rozhraní API Microsoft Graph a vytvoří požadavek na rozhraní Microsoft Graph API.

Po dokončení tohoto rychlého startu bude vaše aplikace přijímat přihlašovacíky osobních účtů Microsoft (včetně outlook.com, live.com a dalších) a pracovních nebo školních účtů z jakékoli společnosti nebo organizace, která používá Azure Active Directory.

![Ukazuje, jak ukázková aplikace vygenerovaná tímto rychlým startem funguje.](media/quickstart-v2-java-webapp/java-quickstart.svg)

## <a name="prerequisites"></a>Požadavky

K provedení této ukázky budete potřebovat:

- [Java Development Kit (JDK)](https://openjdk.java.net/) 8 nebo vyšší a [Maven](https://maven.apache.org/).
- Tenant Azure Active Directory (Azure AD). Další informace o tom, jak získat tenanta Azure AD, najdete v tématu [Jak získat tenanta Azure AD](https://azure.microsoft.com/documentation/articles/active-directory-howto-tenant/).

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registrace a stažení aplikace pro rychlý start
> Máte dvě možnosti, jak spustit aplikaci pro rychlý Start: Express (možnost 1) nebo ruční (možnost 2).
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Možnost 1: Registrace a automatická konfigurace aplikace a následné stažení vzorového kódu
>
> 1. Přejít na [Registrace aplikací Azure Portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps).
> 1. Zadejte název vaší aplikace a Vyberte **Zaregistrovat**.
> 1. Podle pokynů stáhněte a automaticky nakonfigurujte novou aplikaci.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Možnost 2: Registrace a ruční konfigurace aplikace a vzorového kódu
>
> #### <a name="step-1-register-your-application"></a>Krok 1: Zaregistrujte si aplikaci
>
> K registraci aplikace a ručnímu přidání registračních informací aplikace do řešení použijte následující postup:
>
> 1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
> 1. Pokud váš účet umožňuje přístup k více tenantům, vyberte svůj účet v pravém horním rohu a nastavte relaci portálu na požadovaného tenanta Azure AD.
>
> 1. Přejděte na stránku [Registrace aplikací](/azure/active-directory/develop/) Microsoft Identity Platform for Developers.
> 1. Vyberte **Nová registrace**.
> 1. Když se zobrazí stránka **Registrace aplikace**, zadejte registrační informace vaší aplikace:
>    - V části **Název** zadejte smysluplný název aplikace, který se zobrazí uživatelům aplikace, například `java-webapp`.
>    - Nyní nechejte **identifikátor URI pro přesměrování** prázdné a vyberte **Registrovat**.
> 1. Na stránce **Přehled** vyhledejte **ID aplikace (klienta)** a ID adresáře aplikace ( **tenant)** . Tyto hodnoty zkopírujte pro pozdější verzi.
> 1. V nabídce vyberte **ověřování** a přidejte následující informace:
>    - V případě **identifikátorů URI pro přesměrování**přidejte `https://localhost:8080/msal4jsample/secure/aad` a `https://localhost:8080/msal4jsample/graph/me`.
>    - Vyberte **Save** (Uložit).
> 1. V nabídce vyberte **certifikáty & tajné klíče** a v části **tajné klíče klienta** klikněte na **nový tajný klíč klienta**:
>
>    - Zadejte popis klíče (např. tajný klíč aplikace).
>    - Vyberte dobu trvání klíče **v 1 roce**.
>    - Hodnota klíče se zobrazí, když vyberete **Přidat**.
>    - Zkopírujte hodnotu klíče pro pozdější verzi. Tato hodnota klíče se znovu nezobrazí ani není dostupná žádným jiným způsobem, takže ji nahrajte hned, jak je vidět z Azure Portal.
>
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Krok 1: Konfigurace aplikace v Azure Portal
>
> Ukázku kódu pro tento rychlý Start, který funguje, je třeba:
>
> 1. Přidejte adresy URL odpovědi jako `https://localhost:8080/msal4jsamples/secure/aad` a `https://localhost:8080/msal4jsamples/graph/me`.
> 1. Vytvořte tajný klíč klienta.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Provést tyto změny pro mě]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Už nakonfigurované](media/quickstart-v2-aspnet-webapp/green-check.png) Vaše aplikace je nakonfigurovaná s těmito atributy.

#### <a name="step-2-download-the-code-sample"></a>Krok 2: stažení ukázky kódu

 [Stažení ukázky kódu](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip)

#### <a name="step-3-configure-the-code-sample"></a>Krok 3: Konfigurace ukázky kódu

 1. Extrahujte soubor zip do místní složky.
 1. Pokud používáte integrované vývojové prostředí, otevřete ukázku v oblíbeném INTEGROVANÉm vývojovém prostředí (volitelné).
 1. Otevřete soubor Application. Properties, který se nachází ve složce src/Main/Resources/Folder a nahraďte hodnotu polí *AAD. ClientID*, *AAD. Authority* a *AAD. SecretKey* s příslušnými hodnotami **ID aplikace**, **ID tenanta** a **tajného klíče klienta** následujícím způsobem:

    ```file
    aad.clientId=Enter_the_Application_Id_here
    aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
    aad.secretKey=Enter_the_Client_Secret_Here
    aad.redirectUriSignin=https://localhost:8080/msal4jsample/secure/aad
    aad.redirectUriGraph=https://localhost:8080/msal4jsample/graph/me
    aad.msGraphEndpointHost="https://graph.microsoft.com/"
    ```

    > [!div renderon="docs"]
    > Kde:
    >
    > - `Enter_the_Application_Id_here` je ID aplikace, kterou jste zaregistrovali.
    > - `Enter_the_Client_Secret_Here` – je **tajný klíč klienta** , který jste vytvořili v části **certifikáty & tajných** kódů pro zaregistrovanou aplikaci.
    > - `Enter_the_Tenant_Info_Here` – hodnota **ID adresáře (tenant)** aplikace, kterou jste zaregistrovali.

 1. Pokud chcete použít protokol HTTPS s localhost, vyplňte vlastnosti Server. SSL. Key. Chcete-li vygenerovat certifikát podepsaný svým držitelem, použijte nástroj pro nástroj (obsažený v JRE).

   ```
   Example:
   keytool -genkeypair -alias testCert -keyalg RSA -storetype PKCS12 -keystore keystore.p12 -storepass password

   server.ssl.key-store-type=PKCS12  
   server.ssl.key-store=classpath:keystore.p12  
   server.ssl.key-store-password=password  
   server.ssl.key-alias=testCert
   ```

   Vygenerovaný soubor úložiště klíčů vložte do složky Resources (prostředky).

#### <a name="step-4-run-the-code-sample"></a>Krok 4: spuštění ukázky kódu

Chcete-li spustit projekt, můžete provést jednu z těchto akcí:

Spusťte ji přímo z integrovaného vývojového prostředí pomocí integrovaného spouštěcího serveru nebo ho zabalíte do souboru WAR pomocí [Maven](https://maven.apache.org/plugins/maven-war-plugin/usage.html) a nasadíte ho do řešení kontejnerů J2EE, jako je [Apache Tomcat](http://tomcat.apache.org/).

##### <a name="running-from-ide"></a>Spuštění z IDE

Pokud používáte webovou aplikaci z rozhraní IDE, klikněte na spustit a pak přejděte na domovskou stránku projektu. V této ukázce je adresa URL standardní domovské stránky https://localhost:8080.

1. Na přední stránce vyberte tlačítko **přihlášení** , které chcete přesměrovat na Azure Active Directory a vyzvat uživatele k zadání přihlašovacích údajů.

1. Po ověření uživatele budou přesměrovány na *https://localhost:8080/msal4jsample/secure/aad* . Nyní jsou přihlášeni a na stránce se zobrazí informace o přihlášeném účtu. Ukázkové uživatelské rozhraní obsahuje následující tlačítka:
    - *Odhlášení*: podepíše aktuálního uživatele z aplikace a přesměruje je na domovskou stránku.
    - *Zobrazit informace o uživateli*: Získá token pro Microsoft Graph a zavolá Microsoft Graph s požadavkem, který obsahuje token, který vrátí základní informace o přihlášeném uživateli.

> [!IMPORTANT]
> Tato aplikace rychlý Start používá k identifikaci jako důvěrného klienta tajný klíč klienta. Vzhledem k tomu, že se tajný klíč klienta přidá do souborů projektu jako prostý text, doporučuje se z bezpečnostních důvodů použít certifikát místo tajného klíče klienta před zvážením aplikace jako produkční aplikace. Další informace o použití certifikátu najdete v tématu [přihlašovací údaje certifikátu pro ověřování aplikací](https://docs.microsoft.com/azure/active-directory/develop/active-directory-certificate-credentials).

## <a name="more-information"></a>Další informace

### <a name="getting-msal"></a>Získání MSAL

MSAL for Java (MSAL4J) je knihovna jazyka Java používaná k přihlašování uživatelů a žádosti o tokeny používané pro přístup k rozhraní API chráněnému platformou Microsoft identity.

Přidejte do své aplikace MSAL4J pomocí Maven nebo Gradle pro správu závislostí tak, že provedete následující změny souboru pom. XML (Maven) nebo Build. Gradle (Gradle) aplikace.

```XML
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>msal4j</artifactId>
    <version>1.0.0</version>
</dependency>
```

```$xslt
compile group: 'com.microsoft.azure', name: 'msal4j', version: '1.0.0'
```

### <a name="msal-initialization"></a>Inicializace knihovny MSAL

Přidejte odkaz na MSAL pro jazyk Java přidáním následujícího kódu do horní části souboru, kde budete používat MSAL4J:

```Java
import com.microsoft.aad.msal4j.*;
```

## <a name="next-steps"></a>Další kroky

Další informace o oprávněních a souhlasu:

> [!div class="nextstepaction"]
> [Oprávnění a souhlas](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent)

Další informace o toku ověřování pro tento scénář najdete v tématu tok autorizačního kódu OAuth 2,0:

> [!div class="nextstepaction"]
> [Tok OAuth autorizačním kódem](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow)

Pomůžeme nám vylepšit platformu Microsoft identity. Řekněte nám, co si myslíte, díky krátkému průzkumu dvou dotazů.

> [!div class="nextstepaction"]
> [Microsoft Identity Platform Survey](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
