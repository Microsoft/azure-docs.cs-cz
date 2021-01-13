---
title: 'Rychlý Start: přidání přihlášení pomocí Microsoftu do webové aplikace Java | Azure'
titleSuffix: Microsoft identity platform
description: V tomto rychlém startu se dozvíte, jak přidat přihlášení pomocí Microsoftu do webové aplikace Java pomocí OpenID Connect.
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
ms.openlocfilehash: 91aa6c96c714bff26ea7e0df5b2b6971c68edec0
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/13/2021
ms.locfileid: "98178564"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-java-web-app"></a>Rychlý Start: přidání přihlášení do webové aplikace Java pomocí Microsoftu

V tomto rychlém startu si stáhnete a spustíte ukázku kódu, která ukazuje, jak se může webová aplikace Java přihlašovat uživatelům a volat rozhraní Microsoft Graph API. Uživatelé ze všech Azure Active Directory (Azure AD) se můžou přihlásit k aplikaci.

 Přehled najdete v tématu o [tom, jak ukázka funguje](#how-the-sample-works).

## <a name="prerequisites"></a>Požadavky

K provedení této ukázky budete potřebovat:

- [Java Development Kit (JDK)](https://openjdk.java.net/) 8 nebo novější. 
- [Maven](https://maven.apache.org/).

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registrace a stažení aplikace pro rychlý start
> Existují dva způsoby, jak spustit aplikaci rychlý Start: Express (možnost 1) a ruční (možnost 2).
>
> ### <a name="option-1-register-and-automatically-configure-your-app-and-then-download-the-code-sample"></a>Možnost 1: registrace a Automatická konfigurace aplikace a stažení ukázky kódu
>
> 1. Přejít k prostředí rychlý Start pro <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/JavaQuickstartPage/sourceType/docs" target="_blank">Azure Portal registrace aplikací <span class="docon docon-navigate-external x-hidden-focus"></span> </a>
> 1. Zadejte název aplikace a pak vyberte **Registrovat**.
> 1. Podle pokynů v úvodním prostředí portálu Stáhněte automaticky nakonfigurovaný kód aplikace.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Možnost 2: Registrace a ruční konfigurace aplikace a vzorového kódu
>
> #### <a name="step-1-register-your-application"></a>Krok 1: Registrace aplikace
>
> K registraci aplikace a ručnímu přidání registračních informací aplikace do ní použijte následující postup:
>
> 1. Přihlaste se <a href="https://portal.azure.com/" target="_blank">k <span class="docon docon-navigate-external x-hidden-focus"></span> Azure Portal</a>.
> 1. Máte-li přístup k více klientům, použijte filtr **adresář a odběr** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: v horní nabídce a vyberte klienta, ve kterém chcete aplikaci zaregistrovat.
> 1. Vyhledejte a vyberte **Azure Active Directory**.
> 1. V části **Spravovat** vyberte **Registrace aplikací**.
> 1. Vyberte **Nová registrace**.
> 1. Zadejte **název** vaší aplikace, například **Java-WebApp**. Uživatelé vaší aplikace můžou tento název zobrazit. Později ji můžete změnit.
> 1. Vyberte **Zaregistrovat**.
> 1. Na stránce **Přehled** si poznamenejte **ID aplikace (klienta)** a **ID adresáře (tenant)**. Tyto hodnoty budete potřebovat později.
> 1. V části **Spravovat** vyberte **ověřování**.
> 1. Vyberte **Přidat**  >  **Web** platformy.
> 1. V části **identifikátory URI pro přesměrování** zadejte `https://localhost:8443/msal4jsample/secure/aad` .
> 1. Vyberte **Konfigurovat**.
> 1. V části **Web** v části **identifikátory URI pro přesměrování** zadejte `https://localhost:8443/msal4jsample/graph/me` jako druhý identifikátor URI přesměrování.
> 1. V části **Správa** vyberte **Certifikáty a tajné kódy**. V části **tajné klíče klienta** vyberte **nový tajný klíč klienta**.
> 1. Zadejte popis klíče (například *tajný klíč aplikace*), ponechte výchozí hodnotu vypršení platnosti a vyberte **Přidat**.
> 1. Všimněte si **hodnoty** tajného klíče klienta. Budete ho potřebovat později.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Krok 1: Konfigurace aplikace v Azure Portal
>
> Chcete-li použít ukázku kódu v tomto rychlém startu, je třeba:
>
> 1. Přidejte adresy URL odpovědi `https://localhost:8443/msal4jsample/secure/aad` a `https://localhost:8443/msal4jsample/graph/me` .
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
> Stáhněte si projekt a rozbalte soubor. zip do složky poblíž kořene jednotky. Například *C:\Azure-Samples*.
>
> Chcete-li použít protokol HTTPS s localhost, zadejte `server.ssl.key` Vlastnosti. Chcete-li vygenerovat certifikát podepsaný svým držitelem, použijte nástroj pro nástroj (obsažený v JRE).
>
> Tady je příklad:
>  ```
>   keytool -genkeypair -alias testCert -keyalg RSA -storetype PKCS12 -keystore keystore.p12 -storepass password
>
>   server.ssl.key-store-type=PKCS12
>   server.ssl.key-store=classpath:keystore.p12
>   server.ssl.key-store-password=password
>   server.ssl.key-alias=testCert
>   ```
>   Vygenerovaný soubor úložiště klíčů vložte do složky *Resources* .

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Stažení ukázky kódu](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-code-sample"></a>Krok 3: Konfigurace ukázky kódu
> 1. Soubor .zip extrahujte do místní složky.
> 1. *Volitelné.* Pokud používáte integrované vývojové prostředí, otevřete ukázku v tomto prostředí.
> 1. Otevřete soubor *Application. Properties* . Můžete ji najít v části *Src/Main/Resources/* Folder. Nahraďte hodnoty v polích `aad.clientId` , `aad.authority` a `aad.secretKey` pomocí ID aplikace, ID tenanta a hodnot tajného klíče klienta v uvedeném pořadí. Jak by měl vypadat takto:
>
>    ```file
>    aad.clientId=Enter_the_Application_Id_here
>    aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
>    aad.secretKey=Enter_the_Client_Secret_Here
>    aad.redirectUriSignin=https://localhost:8443/msal4jsample/secure/aad
>    aad.redirectUriGraph=https://localhost:8443/msal4jsample/graph/me
>    aad.msGraphEndpointHost="https://graph.microsoft.com/"
>    ```
>    V předchozím kódu:
>
>    - `Enter_the_Application_Id_here` je ID aplikace pro aplikaci, kterou jste zaregistrovali.
>    - `Enter_the_Client_Secret_Here` je **tajný kód klienta** , který jste vytvořili v části **certifikáty & tajných** kódů pro aplikaci, kterou jste zaregistrovali.
>    - `Enter_the_Tenant_Info_Here` je hodnota **ID adresáře** aplikace, kterou jste zaregistrovali.
> 1. Chcete-li použít protokol HTTPS s localhost, zadejte `server.ssl.key` Vlastnosti. Chcete-li vygenerovat certifikát podepsaný svým držitelem, použijte nástroj pro nástroj (obsažený v JRE).
>
>    Tady je příklad:
>
>     ```
>      keytool -genkeypair -alias testCert -keyalg RSA -storetype PKCS12 -keystore keystore.p12 -storepass password
>
>      server.ssl.key-store-type=PKCS12
>      server.ssl.key-store=classpath:keystore.p12
>      server.ssl.key-store-password=password
>      server.ssl.key-alias=testCert
>      ```
>   1. Vygenerovaný soubor úložiště klíčů vložte do složky *Resources* .


> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-run-the-code-sample"></a>Krok 3: spuštění ukázky kódu
> [!div renderon="docs"]
> #### <a name="step-4-run-the-code-sample"></a>Krok 4: spuštění ukázky kódu

Chcete-li spustit projekt, proveďte jeden z následujících kroků:

- Spusťte ji přímo z integrovaného vývojového prostředí pomocí integrovaného spouštěcího serveru. 
- Zabalíte ho do souboru WAR pomocí [Maven](https://maven.apache.org/plugins/maven-war-plugin/usage.html)a pak ho nasadíte do řešení kontejnerů J2EE, jako je [Apache Tomcat](http://tomcat.apache.org/).

##### <a name="running-the-project-from-an-ide"></a>Spuštění projektu z integrovaného vývojového prostředí

Chcete-li spustit webovou aplikaci z rozhraní IDE, vyberte možnost spustit a poté přejít na domovskou stránku projektu. V této ukázce je adresa URL standardní domovské stránky https://localhost:8443 .

1. Na přední stránce vyberte tlačítko pro **přihlášení** a přesměrujte uživatele na Azure Active Directory a vyzvat je k zadání přihlašovacích údajů.

1. Po ověření uživatelů budou přesměrováni na `https://localhost:8443/msal4jsample/secure/aad` . Jsou nyní přihlášeni a na stránce se zobrazí informace o uživatelském účtu. Ukázková uživatelská rozhraní mají tato tlačítka:
    - **Odhlášení**: podepíše aktuálního uživatele z aplikace a přesměruje tohoto uživatele na domovskou stránku.
    - **Zobrazit informace o uživateli**: Získá token pro Microsoft Graph a zavolá Microsoft Graph s požadavkem, který obsahuje token, který vrátí základní informace o přihlášeném uživateli.

##### <a name="running-the-project-from-tomcat"></a>Spuštění projektu z Tomcat

Pokud chcete nasadit webovou ukázku na Tomcat, musíte provést několik změn zdrojového kódu.

1. Otevřete *MS-identity-Java-WebApp/pom.xml*.
    - Pod položkou `<name>msal-web-sample</name>` přidejte `<packaging>war</packaging>` .

2. Otevřete *MS-identity-Java-WebApp/src/Main/Java/com. Microsoft. Azure. msalwebsample/MsalWebSampleApplication*.

    - Odstraňte všechen zdrojový kód a nahraďte ho tímto kódem:

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

3.   Výchozí port HTTP Tomcat je 8080, ale budete potřebovat připojení HTTPS přes port 8443. Konfigurace tohoto nastavení:
        - Přejít na *Tomcat/conf/server.xml*.
        - Vyhledejte `<connector>` značku a nahraďte existující konektor tímto konektorem:

          ```xml
          <Connector
                   protocol="org.apache.coyote.http11.Http11NioProtocol"
                   port="8443" maxThreads="200"
                   scheme="https" secure="true" SSLEnabled="true"
                   keystoreFile="C:/Path/To/Keystore/File/keystore.p12" keystorePass="KeystorePassword"
                   clientAuth="false" sslProtocol="TLS"/>
          ```

4. Otevřete okno příkazového řádku. Přejít do kořenové složky této ukázky (kde se nachází soubor pom.xml) a spustit `mvn package` pro sestavení projektu.
    - Tento příkaz vytvoří v adresáři */targets* soubor *msal-web-Sample-0.1.0. War* .
    - Přejmenujte tento soubor na *msal4jsample. War*.
    - Nasaďte soubor WAR pomocí Tomcat nebo jakéhokoli jiného řešení kontejneru J2EE.
        - Pokud chcete nasadit soubor msal4jsample. War, zkopírujte ho do adresáře */webapps/* v instalaci Tomcat a potom spusťte server Tomcat.

5. Po nasazení souboru přejdete na https://localhost:8443/msal4jsample adresu pomocí prohlížeče.


> [!IMPORTANT]
> V tomto rychlém startu aplikace se k identifikaci jako důvěrného klienta používá tajný klíč klienta. Vzhledem k tomu, že se tajný klíč klienta přidá do souborů projektu jako prostý text, z bezpečnostních důvodů doporučujeme použít certifikát místo tajného klíče klienta před použitím aplikace v produkčním prostředí. Další informace o použití certifikátu najdete v tématu [přihlašovací údaje certifikátu pro ověřování aplikací](./active-directory-certificate-credentials.md).

## <a name="more-information"></a>Další informace

### <a name="how-the-sample-works"></a>Jak ukázka funguje
![Diagram, který ukazuje, jak ukázková aplikace vygenerovaná tímto rychlým startem funguje.](media/quickstart-v2-java-webapp/java-quickstart.svg)

### <a name="get-msal"></a>Získat MSAL

MSAL for Java (MSAL4J) je knihovna Java používaná k přihlašování uživatelů a žádosti o tokeny, které se používají pro přístup k rozhraní API, které je chráněné platformou Microsoft identity.

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

### <a name="initialize-msal"></a>Inicializovat MSAL

Přidáním následujícího kódu na začátek souboru, kde budete používat MSAL4J, přidejte odkaz na MSAL for Java:

```Java
import com.microsoft.aad.msal4j.*;
```

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Další kroky

Podrobnější diskuzi o sestavování webových aplikací, které se přihlásily uživatelům na platformě Microsoft identity, najdete v řadě scénářů s více částmi:

> [!div class="nextstepaction"]
> [Scénář: webová aplikace, která se přihlásí uživatelům](scenario-web-app-sign-user-overview.md?tabs=java)
