---
title: Microsoft identity platformy Java webapp rychlý start | Azure
description: Přečtěte si, jak implementovat Microsoft Sign-In na Java Web App pomocí OpenID Connect
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
ms.openlocfilehash: f3ede3ef0557c5ca425901e7404746b4e85aefcb
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80991122"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-java-web-app"></a>Úvodní příručka: Přidání přihlášení s Microsoftem do webové aplikace Java

V tomto rychlém startu se dozvíte, jak integrovat webovou aplikaci Java s platformou identit Microsoftu. Vaše aplikace se přihlásí k uživateli, získá přístupový token pro volání rozhraní Microsoft Graph API a požádá o rozhraní Microsoft Graph API.

Po dokončení tohoto rychlého startu bude vaše aplikace přijímat přihlášení k osobním účtům Microsoft (včetně outlook.com, live.com a dalších) a pracovních nebo školních účtů od jakékoli společnosti nebo organizace, která používá Azure Active Directory. (Viz [jak ukázka funguje](#how-the-sample-works) pro ilustraci.)

## <a name="prerequisites"></a>Požadavky

Chcete-li spustit tuto ukázku, budete potřebovat:

- [Java Development Kit (JDK)](https://openjdk.java.net/) 8 nebo vyšší, a [Maven](https://maven.apache.org/).

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registrace a stažení aplikace pro rychlý start
> Máte dvě možnosti spuštění aplikace pro rychlý start: express (možnost 1) nebo ruční (možnost 2)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Možnost 1: Registrace a automatická konfigurace aplikace a následné stažení vzorového kódu
>
> 1. Přejděte na portál Azure – rychlé spuštění [registrace aplikací.](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/JavaQuickstartPage/sourceType/docs)
> 1. Zadejte název vaší aplikace a Vyberte **Zaregistrovat**.
> 1. Podle pokynů v úvodním startu portálu stáhněte automaticky nakonfigurovaný kód aplikace.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Možnost 2: Registrace a ruční konfigurace aplikace a vzorového kódu
>
> #### <a name="step-1-register-your-application"></a>Krok 1: Registrace aplikace
>
> Chcete-li zaregistrovat přihlášku a ručně přidat registrační údaje aplikace do aplikace, postupujte takto:
>
> 1. Přihlaste se k [portálu Azure](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
> 1. Pokud váš účet umožňuje přístup k více tenantům, vyberte svůj účet v pravém horním rohu a nastavte relaci portálu na požadovaného tenanta Azure AD.
>
> 1. Přejděte na platformu identit Microsoftpro vývojáře [Registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) stránky.
> 1. Vyberte **možnost Nová registrace**.
> 1. Když se zobrazí stránka **Registrace aplikace**, zadejte registrační informace vaší aplikace:
>    - V části **Název** zadejte smysluplný název aplikace, který se zobrazí uživatelům aplikace, například `java-webapp`.
>    - Vyberte **Zaregistrovat**.
> 1. Na stránce **Přehled** vyhledejte **ID aplikace (klienta)** a hodnoty **ID adresáře (klienta)** aplikace. Zkopírujte tyto hodnoty pro pozdější.
> 1. V nabídce vyberte **ověřování** a přidejte následující informace:
>    - Přidejte konfiguraci **webové** platformy.  Přidejte `https://localhost:8080/msal4jsample/secure/aad` `https://localhost:8080/msal4jsample/graph/me` tyto a jako **přesměrování identifikátorů URI**..
>    - Vyberte **Uložit**.
> 1. V nabídce vyberte **tajný &ch kódů certifikátů** a v části **Tajné klíče klienta** klikněte na **Nový tajný klíč klienta**:
>
>    - Zadejte popis klíče (například tajný klíč aplikace).
>    - Vyberte dobu trvání klíče **Do 1 roku**.
>    - Hodnota klíče se zobrazí, když vyberete **Přidat**.
>    - Zkopírujte hodnotu klíče na později. Tato hodnota klíče se znovu nezobrazí ani nelze ji získat žádným jiným způsobem, takže ji zaznamenejte, jakmile je viditelná z portálu Azure.
>
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Krok 1: Konfigurace aplikace na webu Azure Portal
>
> Pro ukázku kódu pro tento rychlý start do práce, je třeba:
>
> 1. Přidejte adresy URL `https://localhost:8080/msal4jsample/secure/aad` `https://localhost:8080/msal4jsample/graph/me`odpovědí jako a .
> 1. Vytvořte tajný klíč klienta.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Proveďte tyto změny pro mě]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Už nakonfigurované](media/quickstart-v2-aspnet-webapp/green-check.png) Vaše aplikace je nakonfigurovaná s těmito atributy.

#### <a name="step-2-download-the-code-sample"></a>Krok 2: Stažení ukázky kódu
> [!div renderon="docs"]
> [Stáhnout ukázku kódu](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> Stáhněte projekt a extrahujte soubor ZIP do místní složky blíže ke kořenové složce – například **C:\Azure-Samples**
>
> Chcete-li použít https s localhost, vyplňte vlastnosti server.ssl.key. Chcete-li vygenerovat certifikát podepsaný svým držitelem, použijte nástroj keytool (součástí jre).
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
>   Vložte generovaný soubor úložiště klíčů do složky "zdroje".

> [!div renderon="portal" id="autoupdate" class="nextstepaction"]
> [Stáhnout ukázku kódu](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-code-sample"></a>Krok 3: Konfigurace ukázky kódu
> 1. Soubor .zip extrahujte do místní složky.
> 1. Pokud používáte integrované vývojové prostředí, otevřete ukázku ve vašem oblíbeném integrovaném vývojovém prostředí (volitelné).
> 1. Otevřete soubor application.properties, který lze nalézt ve složce src/main/resources/ a nahraďte hodnotu polí *aad.clientId*, *aad.authority* a *aad.secretKey* s příslušnými hodnotami **Id aplikace**, **Id klienta** a **Tajného klienta** jako následující:
>
>    ```file
>    aad.clientId=Enter_the_Application_Id_here
>    aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
>    aad.secretKey=Enter_the_Client_Secret_Here
>    aad.redirectUriSignin=https://localhost:8080/msal4jsample/secure/aad
>    aad.redirectUriGraph=https://localhost:8080/msal4jsample/graph/me
>    aad.msGraphEndpointHost="https://graph.microsoft.com/"
>    ```
> Kde:
>
> - `Enter_the_Application_Id_here` je ID aplikace, kterou jste zaregistrovali.
> - `Enter_the_Client_Secret_Here`- je **tajný klíč klienta,** který jste vytvořili v **& tajných kódů** pro aplikaci, kterou jste zaregistrovali.
> - `Enter_the_Tenant_Info_Here`- je **ID adresáře (tenanta)** aplikace, kterou jste zaregistrovali.
> 1. Chcete-li použít https s localhost, vyplňte vlastnosti server.ssl.key. Chcete-li vygenerovat certifikát podepsaný svým držitelem, použijte nástroj keytool (součástí jre).
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
>   Vložte generovaný soubor úložiště klíčů do složky "zdroje".


> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-run-the-code-sample"></a>Krok 3: Spuštění ukázky kódu
> [!div renderon="docs"]
> #### <a name="step-4-run-the-code-sample"></a>Krok 4: Spuštění ukázky kódu

Chcete-li projekt spustit, můžete buď:

Spusťte jej přímo z vašeho IDE pomocí vloženého jarního spouštěcího serveru nebo jej zabalte do souboru WAR pomocí [maven](https://maven.apache.org/plugins/maven-war-plugin/usage.html) a nasaďte jej do kontejnerového řešení J2EE, jako je [Apache Tomcat](http://tomcat.apache.org/).

##### <a name="running-from-ide"></a>Spuštění z ide

Pokud používáte webovou aplikaci z ide, klikněte na spustit, přejděte na domovskou stránku projektu. Pro tuto ukázku je https://localhost:8080standardní adresa URL domovské stránky .

1. Na titulní stránce vyberte tlačítko **Přihlásit** se k přesměrování do služby Azure Active Directory a vyzvat uživatele k zadání přihlašovacích údajů.

1. Po ověření uživatele jsou přesměrováni *https://localhost:8080/msal4jsample/secure/aad*na . Nyní jsou přihlášeni a na stránce se zobrazí informace o přihlášeném účtu. Ukázkové ui má následující tlačítka:
    - *Odhlásit*: Odhlásí aktuálního uživatele z aplikace a přesměruje ho na domovskou stránku.
    - *Zobrazit informace o uživateli*: Získá token pro Microsoft Graph a zavolá Microsoft Graph s požadavkem obsahujícím token, který vrátí základní informace o přihlášeném uživateli.

##### <a name="running-from-tomcat"></a>Útěk z Tomcatu

Pokud chcete nasadit ukázku webu do Aplikace Tomcat, budete muset provést několik změn zdrojového kódu.

1. Otevřít ms-identity-java-webapp/pom.xml
    - V `<name>msal-web-sample</name>` části přidat`<packaging>war</packaging>`
    - Přidat závislost:

         ```xml
         <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-tomcat</artifactId>
          <scope>provided</scope>
         </dependency>
         ```

2. Otevřít ms-identity-java-webapp/src/main/java/com.microsoft.azure.msalwebsample/MsalWebSampleApplication

    - Odstraňte veškerý zdrojový kód a nahraďte je:

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

3. Otevření příkazového řádku, přejděte do kořenové složky projektu a spusťte`mvn package`
    - Tím se `msal-web-sample-0.1.0.war` vygeneruje soubor v adresáři /targets.
    - Přejmenujte tento soubor na`ROOT.war`
    - Nasaďte tento válečný soubor pomocí Tomcatu nebo jiného řešení kontejneru J2EE.
        - Chcete-li nasazení v kontejneru Tomcat, zkopírujte soubor .war do složky webapps v rámci instalace Tomcat a spusťte server Tomcat.

Tato funkce WAR bude https://localhost:8080/automaticky hostována na adrese .

> [!IMPORTANT]
> Tato aplikace rychlého startu používá tajný klíč klienta k identifikaci jako důvěrný klient. Vzhledem k tomu, že tajný klíč klienta je přidán jako prostý text do souborů projektu, z bezpečnostních důvodů se doporučuje použít certifikát namísto tajného klíče klienta před zvážením aplikace jako produkční aplikace. Další informace o použití certifikátu naleznete v [tématu Pověření certifikátu pro ověřování aplikací](https://docs.microsoft.com/azure/active-directory/develop/active-directory-certificate-credentials).

## <a name="more-information"></a>Další informace

### <a name="how-the-sample-works"></a>Jak ukázka funguje
![Ukazuje, jak ukázková aplikace vygenerovaná tímto rychlým startem funguje.](media/quickstart-v2-java-webapp/java-quickstart.svg)

### <a name="getting-msal"></a>Získání MSAL

MSAL for Java (MSAL4J) je java knihovna používaná k přihlášení uživatelů a vyžádání tokenů používaných pro přístup k rozhraní API chráněnému platformou identit y Microsoft.

Přidejte MSAL4J do aplikace pomocí Maven nebo Gradle ke správě závislostí provedením následujících změn v souboru pom.xml (Maven) nebo build.gradle (Gradle) aplikace.

V pom.xml:

```XML
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>msal4j</artifactId>
    <version>1.0.0</version>
</dependency>
```

V build.gradle:

```$xslt
compile group: 'com.microsoft.azure', name: 'msal4j', version: '1.0.0'
```

### <a name="msal-initialization"></a>Inicializace knihovny MSAL

Přidejte odkaz na Jazyk MSAL pro jazyk Java přidáním následujícího kódu do horní části souboru, kde budete používat MSAL4J:

```Java
import com.microsoft.aad.msal4j.*;
```

## <a name="next-steps"></a>Další kroky

Další informace o oprávněních a souhlasu:

> [!div class="nextstepaction"]
> [Oprávnění a souhlas](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent)

Další informace o toku auth pro tento scénář naleznete v toku autorizačního kódu Oauth 2.0:

> [!div class="nextstepaction"]
> [Tok autorizačního kódu Oauth](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
