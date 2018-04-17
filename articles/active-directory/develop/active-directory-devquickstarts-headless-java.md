---
title: Začínáme se službou Azure AD Java příkazového řádku | Microsoft Docs
description: Jak vytvořit aplikaci Java příkazového řádku, který se přihlásí uživatele pro přístup k rozhraní API.
services: active-directory
documentationcenter: java
author: navyasric
manager: mtillman
editor: ''
ms.assetid: 51e1a8f9-6ff0-4643-a350-0ba794e26fd1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 01/23/2017
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: a0e12711e4a7e67861d61ae4575c4956531cf841
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="using-java-command-line-app-to-access-an-api-with-azure-ad"></a>Pomocí aplikace v jazyce Java příkazového řádku pro přístup k rozhraní API s Azure AD
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Azure AD je jednoduchá a přímočará využít Správa identit pro webové aplikace, k poskytování jednoho přihlášení a odhlášení jenom pár řádků kódu.  V jazyce Java webové aplikace můžete to provést pomocí implementace ADAL4J komunitou vytvářený společnosti Microsoft.

  Zde použijeme ADAL4J na:

* Přihlášení uživatele do aplikace pomocí Azure AD jako zprostředkovatele identity.
* Zobrazí některé informace o uživateli.
* Přihlášení uživatele mimo aplikaci.

Chcete-li to provést, budete muset:

1. Zaregistrovat aplikaci s Azure AD
2. Nastavte aplikaci pro použití knihovny ADAL4J.
3. V knihovně ADAL4J pro zasílání požadavků na přihlášení a odhlášení do Azure AD.
4. Tisknout data o uživateli.

Abyste mohli začít, [stáhnout kostru aplikace](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect/archive/skeleton.zip) nebo [stažení je hotová ukázka](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect\\/archive/complete.zip).  Budete také potřebovat klient služby Azure AD, do kterého chcete registrace vaší aplikace.  Pokud již, nemáte [zjistěte, jak získat](active-directory-howto-tenant.md).

## <a name="1--register-an-application-with-azure-ad"></a>1.  Zaregistrovat aplikaci s Azure AD
Pokud chcete povolit aplikaci k ověřování uživatelů, budete nejprve muset zaregistrujte novou aplikaci v klientovi.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Na horním panelu klikněte na tlačítko na vašem účtu a v části **Directory** vyberte klienta služby Active Directory, kam chcete registrace vaší aplikace.
3. Klikněte na **všechny služby** v navigaci vlevo a zvolte **Azure Active Directory**.
4. Klikněte na **registrace aplikace** a zvolte **přidat**.
5. Postupujte podle výzev a vytvořte novou **webové aplikace nebo WebAPI**.
  * **Název** aplikace popíše aplikaci pro koncové uživatele
  * **Přihlašovací adresa URL** je základní adresu URL aplikace.  Výchozí hodnota kostru je `http://localhost:8080/adal4jsample/`.
6. Po dokončení registrace AAD přiřadí vaší aplikace, jedinečné ID aplikace  Tuto hodnotu budete potřebovat v další části, zkopírujte jej na kartě aplikace.
7. Z **nastavení** -> **vlastnosti** stránky pro aplikace, aktualizujte identifikátor ID URI aplikace. **Identifikátor ID URI aplikace** je jedinečný identifikátor pro vaši aplikaci.  Konvence, je použít `https://<tenant-domain>/<app-name>`, například `http://localhost:8080/adal4jsample/`.

Jednou na portálu pro vaši aplikaci vytvořit **klíč** z **nastavení** stránky pro aplikace a zkopírujte ho dolů.  Budete ho potřebovat za chvíli.

## <a name="2-set-up-your-app-to-use-adal4j-library-and-prerequisites-using-maven"></a>2. Nastavit aplikaci používat knihovny ADAL4J a požadavky pomocí nástroje Maven
Zde nakonfigurujeme ADAL4J pro použití ověřovacího protokolu OpenID Connect.  ADAL4J se použije k vydávání požadavků na přihlášení a odhlášení, spravovat relace uživatele a získat informace o uživateli, mimo jiné.

* V kořenovém adresáři projektu, otevírání nebo vytváření `pom.xml` a najděte `// TODO: provide dependencies for Maven` a nahraďte následujícím kódem:

```Java
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>public-client-adal4j-sample</artifactId>
    <packaging>jar</packaging>
    <version>0.0.1-SNAPSHOT</version>
    <name>public-client-adal4j-sample</name>
    <url>http://maven.apache.org</url>
    <properties>
        <spring.version>3.0.5.RELEASE</spring.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>adal4j</artifactId>
            <version>1.1.2</version>
        </dependency>
        <dependency>
            <groupId>com.nimbusds</groupId>
            <artifactId>oauth2-oidc-sdk</artifactId>
            <version>4.5</version>
        </dependency>
        <dependency>
            <groupId>org.json</groupId>
            <artifactId>json</artifactId>
            <version>20090211</version>
        </dependency>
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>3.0.1</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
            <version>1.7.5</version>
        </dependency>
</dependencies>
    <build>
        <finalName>public-client-adal4j-sample</finalName>
        <plugins>
                <plugin>
            <groupId>org.codehaus.mojo</groupId>
            <artifactId>exec-maven-plugin</artifactId>
            <version>1.2.1</version>
            <configuration>
                <mainClass>PublicClient</mainClass>
            </configuration>
        </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <configuration>
                    <source>1.7</source>
                    <target>1.7</target>
                    <encoding>UTF-8</encoding>
                </configuration>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-dependency-plugin</artifactId>
                <executions>
                    <execution>
                        <id>install</id>
                        <phase>install</phase>
                        <goals>
                            <goal>sources</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-resources-plugin</artifactId>
                <version>2.5</version>
                <configuration>
                    <encoding>UTF-8</encoding>
                </configuration>
            </plugin>
            <plugin>
        <artifactId>maven-assembly-plugin</artifactId>
        <executions>
          <execution>
            <phase>package</phase>
            <goals>
              <goal>single</goal>
            </goals>
          </execution>
        </executions>
        <configuration>
          <descriptorRefs>
            <descriptorRef>jar-with-dependencies</descriptorRef>
          </descriptorRefs>
        </configuration>
      </plugin>
      <plugin>
  <groupId>org.apache.maven.plugins</groupId>
  <artifactId>maven-assembly-plugin</artifactId>
  <configuration>
    <archive>
      <manifest>
        <mainClass>PublicClient</mainClass>
      </manifest>
    </archive>
  </configuration>
</plugin>
        </plugins>
    </build>

</project>


```



## <a name="3-create-the-java-publicclient-file"></a>3. Vytvoření souboru Java PublicClient
Jak je uvedeno výše, použijeme rozhraní Graph API k získání dat o přihlášeného uživatele. To měla být pro nás měli vytvoříme i soubor představují **objektu adresáře** a soubor představují **uživatele** tak, aby bylo možné použít vzor ú jazyka Java.

* Vytvořte soubor s názvem `DirectoryObject.java` které použijete k uložení základní data o žádné DirectoryObject (obav později využít pro jiné dotazy grafu, může provádět). Vám může vyjímání a vkládání toto z následujícího seznamu:

```Java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.Future;

import javax.naming.ServiceUnavailableException;

import com.microsoft.aad.adal4j.AuthenticationContext;
import com.microsoft.aad.adal4j.AuthenticationResult;

public class PublicClient {

    private final static String AUTHORITY = "https://login.microsoftonline.com/common/";
    private final static String CLIENT_ID = "2a4da06c-ff07-410d-af8a-542a512f5092";

    public static void main(String args[]) throws Exception {

        try (BufferedReader br = new BufferedReader(new InputStreamReader(
                System.in))) {
            System.out.print("Enter username: ");
            String username = br.readLine();
            System.out.print("Enter password: ");
            String password = br.readLine();

            AuthenticationResult result = getAccessTokenFromUserCredentials(
                    username, password);
            System.out.println("Access Token - " + result.getAccessToken());
            System.out.println("Refresh Token - " + result.getRefreshToken());
            System.out.println("ID Token - " + result.getIdToken());
        }
    }

    private static AuthenticationResult getAccessTokenFromUserCredentials(
            String username, String password) throws Exception {
        AuthenticationContext context = null;
        AuthenticationResult result = null;
        ExecutorService service = null;
        try {
            service = Executors.newFixedThreadPool(1);
            context = new AuthenticationContext(AUTHORITY, false, service);
            Future<AuthenticationResult> future = context.acquireToken(
                    "https://graph.windows.net", CLIENT_ID, username, password,
                    null);
            result = future.get();
        } finally {
            service.shutdown();
        }

        if (result == null) {
            throw new ServiceUnavailableException(
                    "authentication result was null");
        }
        return result;
    }
}


```


## <a name="compile-and-run-the-sample"></a>Zkompilování a spuštění vzorku
Přejděte zpět na kořenového adresáře a spusťte následující příkaz k sestavit ukázku, můžete zadat pouze společně s použitím `maven`. Tímto dojde k použití `pom.xml` soubor jste napsali závislosti.

`$ mvn package`

Teď byste měli mít `adal4jsample.war` ve vaší `/targets` adresáře. Můžete nasadit, v kontejneru vaší Tomcat a navštívíte adresu 

`http://localhost:8080/adal4jsample/`

> [!NOTE]
> Je velmi snadno nasadit WAR s nejnovější serverů Tomcat. Jednoduše přejděte na `http://localhost:8080/manager/` a postupujte podle pokynů na odesílání vašeho `adal4jsample.war` souboru. Autodeploy ho bude pro vás správný koncový bod.
> 
> 

## <a name="next-steps"></a>Další kroky
Blahopřejeme! Teď máte funkční aplikaci Java, která má schopnost ověřovat uživatele bezpečně volání webového rozhraní API pomocí OAuth 2.0 a získat základní informace o uživateli.  Pokud jste to ještě neudělali, nyní je čas k naplnění vašeho klienta s některými uživateli.

Pro srovnání je hotová ukázka (bez vašich hodnot nastavení) [je k dispozici jako ZIP zde](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect/archive/complete.zip), nebo ji můžete klonovat z Githubu:

```git clone --branch complete https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect.git```

