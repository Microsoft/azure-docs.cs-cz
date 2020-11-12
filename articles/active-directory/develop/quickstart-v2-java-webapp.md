---
title: 'Rychlý Start: přidání přihlášení pomocí Microsoftu do webové aplikace Java | Azure'
titleSuffix: Microsoft identity platform
description: V tomto rychlém startu se dozvíte, jak implementovat přihlašování Microsoftu v rámci webové aplikace Java pomocí OpenID Connect.
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/09/2019
ms.author: sagonzal
ms.custom: aaddev, scenarios:getting-started, languages:Java, devx-track-java
ms.openlocfilehash: 620039ec43009693d09f732913264eff94d662c9
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94533238"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-java-web-app"></a>Rychlý Start: přidání přihlášení do webové aplikace Java pomocí Microsoftu

V tomto rychlém startu se dozvíte, jak integrovat webovou aplikaci Java s platformou Microsoft identity. Vaše aplikace se přihlásí k uživateli, získá přístupový token pro volání rozhraní API Microsoft Graph a vytvoří požadavek na rozhraní Microsoft Graph API.

Po dokončení tohoto rychlého startu bude vaše aplikace přijímat přihlašovacíky osobních účtů Microsoft (včetně outlook.com, live.com a dalších) a pracovních nebo školních účtů z jakékoli společnosti nebo organizace, která používá Azure Active Directory. (Podívejte [se, jak ukázka funguje](#how-the-sample-works) pro ilustraci.)

## <a name="prerequisites"></a>Požadavky

K provedení této ukázky budete potřebovat:

- [Java Development Kit (JDK)](https://openjdk.java.net/) 8 nebo vyšší a [Maven](https://maven.apache.org/).

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registrace a stažení aplikace pro rychlý start
> Máte dvě možnosti, jak spustit aplikaci pro rychlý Start: Express (možnost 1) nebo ruční (možnost 2).
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Možnost 1: Registrace a automatická konfigurace aplikace a následné stažení vzorového kódu
>
> 1. Přejít k prostředí rychlý Start pro [Azure Portal registrace aplikací](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/JavaQuickstartPage/sourceType/docs)
> 1. Zadejte název vaší aplikace a Vyberte **Zaregistrovat**.
> 1. Podle pokynů v úvodním prostředí portálu Stáhněte automaticky nakonfigurovaný kód aplikace.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Možnost 2: Registrace a ruční konfigurace aplikace a vzorového kódu
>
> #### <a name="step-1-register-your-application"></a>Krok 1: Registrace aplikace
>
> K registraci aplikace a ručnímu přidání registračních informací aplikace do aplikace použijte následující postup:
>
> 1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
> 1. Pokud váš účet umožňuje přístup k více tenantům, vyberte svůj účet v pravém horním rohu a nastavte relaci portálu na požadovaného tenanta Azure AD.
>
> 1. Přejděte na stránku [Registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) Microsoft Identity Platform for Developers.
> 1. Vyberte **Nová registrace**.
> 1. Když se zobrazí stránka **Registrace aplikace** , zadejte registrační informace vaší aplikace:
>    - V části **Název** zadejte smysluplný název aplikace, který se zobrazí uživatelům aplikace, například `java-webapp`.
>    - Vyberte **Zaregistrovat**.
> 1. Na stránce **Přehled** vyhledejte **ID aplikace (klienta)** a ID adresáře aplikace ( **tenant)** . Tyto hodnoty zkopírujte pro pozdější verzi.
> 1. V nabídce vyberte **ověřování** a přidejte následující informace:
>    - Přidejte konfiguraci **webové** platformy.  Přidejte `https://localhost:8443/msal4jsample/secure/aad` je a `https://localhost:8443/msal4jsample/graph/me` jako **identifikátory URI přesměrování**..
>    - Vyberte **Uložit**.
> 1. V nabídce vyberte **certifikáty & tajné klíče** a v části **tajné klíče klienta** klikněte na **nový tajný klíč klienta** :
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
> 1. Přidejte adresy URL odpovědi jako `https://localhost:8443/msal4jsample/secure/aad` a `https://localhost:8443/msal4jsample/graph/me`
> 1. Vytvořte tajný klíč klienta.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Provést tyto změny pro mě]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Už nakonfigurované](media/quickstart-v2-aspnet-webapp/green-check.png) Vaše aplikace je nakonfigurovaná s těmito atributy.

#### <a name="step-2-download-the-code-sample"></a>Krok 2: stažení ukázky kódu
> [!div renderon="docs"]
> [Stažení ukázky kódu](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> Stáhněte si projekt a extrahujte soubor zip do místní složky blíže ke kořenové složce – například **C:\Azure-Samples**
>
> Pokud chcete použít protokol HTTPS s localhost, vyplňte vlastnosti Server. SSL. Key. Chcete-li vygenerovat certifikát podepsaný svým držitelem, použijte nástroj pro nástroj (obsažený v JRE).
>
>  ```
>   Example:
>   keytool -genkeypair -alias testCert -keyalg RSA -storetype PKCS12 -keystore keystore.p12 -storepass password
>
>   server.ssl.key-store-type=PKCS12
>   server.ssl.key-store=classpath:keystore.p12
>   server.ssl.key-store-password=password
>   server.ssl.key-alias=testCert
>   ```
>   Vygenerovaný soubor úložiště klíčů vložte do složky Resources (prostředky).

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Stažení ukázky kódu](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-code-sample"></a>Krok 3: Konfigurace ukázky kódu
> 1. Soubor .zip extrahujte do místní složky.
> 1. Pokud používáte integrované vývojové prostředí, otevřete ukázku v oblíbeném INTEGROVANÉm vývojovém prostředí (volitelné).
> 1. Otevřete soubor Application. Properties, který se nachází ve složce src/Main/Resources/Folder a nahraďte hodnotu polí *AAD. ClientID* , *AAD. Authority* a *AAD. SecretKey* s příslušnými hodnotami **ID aplikace** , **ID tenanta** a **tajného klíče klienta** následujícím způsobem:
>
>    ```file
>    aad.clientId=Enter_the_Application_Id_here
>    aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
>    aad.secretKey=Enter_the_Client_Secret_Here
>    aad.redirectUriSignin=https://localhost:8443/msal4jsample/secure/aad
>    aad.redirectUriGraph=https://localhost:8443/msal4jsample/graph/me
>    aad.msGraphEndpointHost="https://graph.microsoft.com/"
>    ```
> Kde:
>
> - `Enter_the_Application_Id_here` je ID aplikace, kterou jste zaregistrovali.
> - `Enter_the_Client_Secret_Here` – je **tajný klíč klienta** , který jste vytvořili v části **certifikáty & tajných** kódů pro aplikaci, kterou jste zaregistrovali.
> - `Enter_the_Tenant_Info_Here` – je hodnota **ID adresáře** aplikace, kterou jste zaregistrovali.
> 1. Pokud chcete použít protokol HTTPS s localhost, vyplňte vlastnosti Server. SSL. Key. Chcete-li vygenerovat certifikát podepsaný svým držitelem, použijte nástroj pro nástroj (obsažený v JRE).
>
>  ```
>   Example:
>   keytool -genkeypair -alias testCert -keyalg RSA -storetype PKCS12 -keystore keystore.p12 -storepass password
>
>   server.ssl.key-store-type=PKCS12
>   server.ssl.key-store=classpath:keystore.p12
>   server.ssl.key-store-password=password
>   server.ssl.key-alias=testCert
>   ```
>   Vygenerovaný soubor úložiště klíčů vložte do složky Resources (prostředky).


> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-run-the-code-sample"></a>Krok 3: spuštění ukázky kódu
> [!div renderon="docs"]
> #### <a name="step-4-run-the-code-sample"></a>Krok 4: spuštění ukázky kódu

Chcete-li spustit projekt, můžete provést jednu z těchto akcí:

Spusťte ji přímo z integrovaného vývojového prostředí pomocí integrovaného spouštěcího serveru nebo ho zabalíte do souboru WAR pomocí [Maven](https://maven.apache.org/plugins/maven-war-plugin/usage.html) a nasadíte ho do řešení kontejnerů J2EE, jako je [Apache Tomcat](http://tomcat.apache.org/).

##### <a name="running-from-ide"></a>Spuštění z IDE

Pokud používáte webovou aplikaci z rozhraní IDE, klikněte na spustit a pak přejděte na domovskou stránku projektu. V této ukázce je adresa URL standardní domovské stránky https://localhost:8443

1. Na přední stránce vyberte tlačítko **přihlášení** , které chcete přesměrovat na Azure Active Directory a vyzvat uživatele k zadání přihlašovacích údajů.

1. Po ověření uživatele budou přesměrováni na *https://localhost:8443/msal4jsample/secure/aad* . Nyní jsou přihlášeni a na stránce se zobrazí informace o přihlášeném účtu. Ukázkové uživatelské rozhraní obsahuje následující tlačítka:
    - *Odhlášení* : podepíše aktuálního uživatele z aplikace a přesměruje je na domovskou stránku.
    - *Zobrazit informace o uživateli* : Získá token pro Microsoft Graph a zavolá Microsoft Graph s požadavkem, který obsahuje token, který vrátí základní informace o přihlášeném uživateli.

##### <a name="running-from-tomcat"></a>Spuštění z Tomcat

Chcete-li nasadit webovou ukázku na Tomcat, bude nutné provést několik změn zdrojového kódu.

1. Otevřete MS-identity-Java-WebApp/pom.xml
    - V části `<name>msal-web-sample</name>` Přidat `<packaging>war</packaging>`

2. Otevřete MS-identity-Java-WebApp/src/Main/Java/com. Microsoft. Azure. msalwebsample/MsalWebSampleApplication

    - Odstraňte veškerý zdrojový kód a nahraďte ho následujícím:

   ```Java
    package com.microsoft.azure.msalwebsample;

    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.boot.builder.SpringApplicationBuilder;
    import org.springframework.boot.web.servlet.support.SpringBootServletInitializer;

    @SpringBootApplication
    public class MsalWebSampleApplication extends SpringBootServletInitializer {

     public static void main(String[] args) {
      SpringApplication.run(MsalWebSampleApplication.class, args);
     }

     @Override
     protected SpringApplicationBuilder configure(SpringApplicationBuilder builder) {
      return builder.sources(MsalWebSampleApplication.class);
     }
    }
   ```

3.   Výchozí port HTTP Tomcat je 8080, i když je potřeba připojení HTTPS přes port 8443. Postup při konfiguraci:
        - Přejít na Tomcat/conf/server.xml
        - Vyhledejte `<connector>` značku a nahraďte existující konektor pomocí:

        ```xml
        <Connector
                   protocol="org.apache.coyote.http11.Http11NioProtocol"
                   port="8443" maxThreads="200"
                   scheme="https" secure="true" SSLEnabled="true"
                   keystoreFile="C:/Path/To/Keystore/File/keystore.p12" keystorePass="KeystorePassword"
                   clientAuth="false" sslProtocol="TLS"/>
        ```

4. Otevřete příkazový řádek, klikněte na kořenovou složku této ukázky (kde se nachází soubor pom.xml) a spusťte příkaz `mvn package` pro sestavení projektu.
    - Tím se vygeneruje `msal-web-sample-0.1.0.war` soubor v adresáři/TARGETS.
    - Přejmenovat tento soubor na `msal4jsample.war`
    - Tento soubor War nasaďte pomocí Tomcat nebo jakéhokoli jiného řešení kontejneru J2EE.
        - Chcete-li nasadit, zkopírujte soubor msal4jsample. War do `/webapps/` adresáře v instalaci Tomcat a poté spusťte server Tomcat.

5. Po nasazení přejít do https://localhost:8443/msal4jsample v prohlížeči


> [!IMPORTANT]
> Tato aplikace rychlý Start používá k identifikaci jako důvěrného klienta tajný klíč klienta. Vzhledem k tomu, že se tajný klíč klienta přidá do souborů projektu jako prostý text, doporučuje se z bezpečnostních důvodů použít certifikát místo tajného klíče klienta před zvážením aplikace jako produkční aplikace. Další informace o použití certifikátu najdete v tématu [přihlašovací údaje certifikátu pro ověřování aplikací](./active-directory-certificate-credentials.md).

## <a name="more-information"></a>Další informace

### <a name="how-the-sample-works"></a>Jak ukázka funguje
![Ukazuje, jak ukázková aplikace vygenerovaná tímto rychlým startem funguje.](media/quickstart-v2-java-webapp/java-quickstart.svg)

### <a name="getting-msal"></a>Získání MSAL

MSAL for Java (MSAL4J) je knihovna jazyka Java používaná k přihlašování uživatelů a žádosti o tokeny používané pro přístup k rozhraní API chráněnému platformou Microsoft identity.

Přidejte do své aplikace MSAL4J pomocí Maven nebo Gradle pro správu závislostí provedením následujících změn v souboru aplikace pom.xml (Maven) nebo Build. Gradle (Gradle).

V pom.xml:

```XML
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>msal4j</artifactId>
    <version>1.0.0</version>
</dependency>
```

V sestavení. Gradle:

```$xslt
compile group: 'com.microsoft.azure', name: 'msal4j', version: '1.0.0'
```

### <a name="msal-initialization"></a>Inicializace knihovny MSAL

Přidejte odkaz na MSAL pro jazyk Java přidáním následujícího kódu do horní části souboru, kde budete používat MSAL4J:

```Java
import com.microsoft.aad.msal4j.*;
```

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Další kroky

Podrobnější diskuzi o sestavování webových aplikací, které se přihlásily uživatelům na platformě Microsoft identity, můžete přejít na naši řadu scénářů s více částmi:

> [!div class="nextstepaction"]
> [Scénář: webová aplikace, která se přihlásí uživatelům](scenario-web-app-sign-user-overview.md?tabs=java)
