---
title: 'Rychlý Start: volání Microsoft Graph z procesu Java daemon | Azure'
titleSuffix: Microsoft identity platform
description: V tomto rychlém startu se dozvíte, jak může aplikace Java získat přístupový token a volat rozhraní API chráněné koncovým bodem Microsoft Identity Platform pomocí vlastní identity aplikace.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 01/22/2021
ms.author: nacanuma
ms.custom: aaddev, scenarios:getting-started, languages:Java, devx-track-java
ms.openlocfilehash: 9c6571793d2317097574d0afdc7137b3a3d5ad6d
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2021
ms.locfileid: "99064522"
---
# <a name="quickstart-acquire-a-token-and-call-microsoft-graph-api-from-a-java-console-app-using-apps-identity"></a>Rychlý Start: získání tokenu a volání Microsoft Graph API z konzolové aplikace Java pomocí identity aplikace

V tomto rychlém startu stáhnete a spustíte ukázku kódu, která ukazuje, jak může aplikace Java získat přístupový token pomocí identity aplikace pro volání rozhraní API Microsoft Graph a zobrazení [seznamu uživatelů](/graph/api/user-list) v adresáři. Ukázka kódu ukazuje, jak lze spustit bezobslužnou úlohu nebo službu systému Windows pomocí identity aplikace namísto identity uživatele. 

> [!div renderon="docs"]
> ![Ukazuje, jak ukázková aplikace vygenerovaná tímto rychlým startem funguje.](media/quickstart-v2-netcore-daemon/netcore-daemon-intro.svg)

## <a name="prerequisites"></a>Požadavky

K provedení této ukázky budete potřebovat:

- [Java Development Kit (JDK)](https://openjdk.java.net/) 8 nebo vyšší
- [Maven](https://maven.apache.org/).

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registrace a stažení aplikace pro rychlý start

> [!div renderon="docs" class="sxs-lookup"]
>
> Máte dvě možnosti, jak spustit aplikaci pro rychlý Start: Express (možnost 1 níže) a ruční (možnost 2).
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Možnost 1: Registrace a automatická konfigurace aplikace a následné stažení vzorového kódu
>
> 1. Přejít na nové podokno [Azure Portal-registrace aplikací](https://portal.azure.com/?Microsoft_AAD_RegisteredApps=true#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/JavaDaemonQuickstartPage/sourceType/docs) .
> 1. Zadejte název vaší aplikace a Vyberte **Zaregistrovat**.
> 1. Postupujte podle pokynů ke stažení a automatické konfiguraci nové aplikace jedním kliknutím.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Možnost 2: Registrace a ruční konfigurace aplikace a vzorového kódu

> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>Krok 1: Registrace aplikace
> Pokud chcete zaregistrovat aplikaci a ručně přidat informace o registraci aplikace ke svému řešení, postupujte následovně:
>
> 1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
> 1. Pokud váš účet umožňuje přístup k více tenantům, vyberte svůj účet v pravém horním rohu a nastavte relaci portálu na požadovaného tenanta Azure AD.
> 1. Přejděte na stránku [Registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) Microsoft Identity Platform for Developers.
> 1. Vyberte **Nová registrace**.
> 1. Jakmile se zobrazí stránka **Registrovat aplikaci** , zadejte registrační informace vaší aplikace.
> 1. V části **název** zadejte smysluplný název aplikace, který se zobrazí uživatelům aplikace, například `Daemon-console` Vyberte možnost **Registrovat** a vytvořte aplikaci.
> 1. Po registraci vyberte nabídku **certifikáty & tajných klíčů** .
> 1. V části **tajné klíče klienta** vyberte **+ nový tajný klíč klienta**. Zadejte název a vyberte **Přidat**. Zkopírujte tajný klíč na bezpečném místě. Budete ho potřebovat pro použití ve vašem kódu.
> 1. Nyní vyberte nabídku **oprávnění rozhraní API** , vyberte **+ Přidat oprávnění** tlačítko a vyberte možnost **Microsoft Graph**.
> 1. Vyberte **oprávnění aplikace**.
> 1. V části **uživatelský** uzel vyberte **uživatel. číst. vše** a pak vyberte **Přidat oprávnění** .

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="download-and-configure-your-quickstart-app"></a>Stažení a konfigurace aplikace pro rychlý Start
>
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Krok 1: Nakonfigurujte si aplikaci na portálu Azure Portal
> Aby ukázka kódu pro tento rychlý Start fungovala, je nutné vytvořit tajný klíč klienta a přidat **uživatele Graph API. číst. všechna** oprávnění aplikace.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Provést tyto změny pro mě]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Už nakonfigurované](media/quickstart-v2-netcore-daemon/green-check.png) Vaše aplikace je nakonfigurovaná s těmito atributy.

#### <a name="step-2-download-your-java-project"></a>Krok 2: stažení projektu Java

> [!div renderon="docs"]
> [Stáhnout projekt démona Java](https://github.com/Azure-Samples/ms-identity-java-daemon/archive/master.zip)

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Stažení ukázky kódu](https://github.com/Azure-Samples/ms-identity-java-daemon/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`


> [!div renderon="docs"]
> #### <a name="step-3-configure-your-java-project"></a>Krok 3: konfigurace projektu Java
>
> 1. Extrahujte soubor zip do místní složky blízko ke kořenovému adresáři disku, například **C:\Azure-Samples**.
> 1. Přejděte do složky sub **msal-Client-Credential-Secret**.
> 1. Upravte **src\main\resources\application.Properties** a nahraďte hodnoty polí `AUTHORITY` , `CLIENT_ID` a `SECRET` následujícím fragmentem kódu:
>
>    ```
>    AUTHORITY=https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/
>    CLIENT_ID=Enter_the_Application_Id_Here
>    SECRET=Enter_the_Client_Secret_Here
>    ```
>    Kde:
>    - Hodnota `Enter_the_Application_Id_Here` je **ID aplikace (klienta)**, kterou jste zaregistrovali.
>    - `Enter_the_Tenant_Id_Here` – Nahraďte tuto hodnotu **ID tenanta** nebo **názvem tenanta** (například contoso.Microsoft.com).
>    - `Enter_the_Client_Secret_Here` – Nahraďte tuto hodnotu tajným klíčem klienta vytvořeným v kroku 1.
>
> > [!TIP]
> > Pokud chcete najít hodnoty **ID aplikace (klienta)**, **ID adresáře (tenanta)**, navštivte stránku **Přehled** aplikace v Azure Portal. Pokud chcete vygenerovat nový klíč, otevřete stránku **certifikáty & tajných** kódů.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-admin-consent"></a>Krok 3: souhlas správce

> [!div renderon="docs"]
> #### <a name="step-4-admin-consent"></a>Krok 4: souhlas správce

Pokud se pokusíte spustit aplikaci v tomto okamžiku, obdržíte chybu *HTTP 403 – zakázáno* : `Insufficient privileges to complete the operation` . K této chybě dochází, protože jakékoli *oprávnění pouze k aplikacím* vyžaduje souhlas správce: globální správce vašeho adresáře musí udělit souhlas vaší aplikaci. V závislosti na vaší roli vyberte jednu z následujících možností:

##### <a name="global-tenant-administrator"></a>Globální správce klienta

> [!div renderon="docs"]
> Pokud jste globální správce klienta, v registraci aplikace na webu Azure Portal klikněte na stránku **oprávnění rozhraní API** (Preview) a vyberte **udělit souhlas správce pro {název tenanta}** (kde {název tenanta} je název vašeho adresáře).

> [!div renderon="portal" class="sxs-lookup"]
> Pokud jste globální správce, přejít na stránku **oprávnění rozhraní API** , vyberte **udělit souhlas správce pro Enter_the_Tenant_Name_Here**
> > [!div id="apipermissionspage"]
> > [Přejít na stránku oprávnění API]()

##### <a name="standard-user"></a>Standardní uživatel

Pokud jste standardní uživatel vašeho tenanta, musíte požádat globálního správce o udělení souhlasu správce vaší aplikace. Pokud to chcete provést, poskytněte správci následující adresu URL:

```url
https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/adminconsent?client_id=Enter_the_Application_Id_Here
```

> [!div renderon="docs"]
>> Kde:
>> * `Enter_the_Tenant_Id_Here` – Nahraďte tuto hodnotu **ID tenanta** nebo **názvem tenanta** (například contoso.Microsoft.com).
>> * Hodnota `Enter_the_Application_Id_Here` je **ID aplikace (klienta)**, kterou jste zaregistrovali.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-4-run-the-application"></a>Krok 4: spuštění aplikace

> [!div renderon="docs"]
> #### <a name="step-5-run-the-application"></a>Krok 5: spuštění aplikace

Ukázku můžete otestovat přímo spuštěním metody Main souboru ClientCredentialGrant. Java z integrovaného vývojového prostředí (IDE).

Z prostředí nebo příkazového řádku:

```
$ mvn clean compile assembly:single
```

Tím se vygeneruje soubor msal-Client-Credential-Secret-1.0.0. jar v adresáři/TARGETS. Spusťte to pomocí spustitelného souboru Java jako v následujícím příkladu:

```
$ java -jar msal-client-credential-secret-1.0.0.jar
```

Po spuštění aplikace by se měl zobrazit seznam uživatelů v nakonfigurovaném tenantovi.


> [!IMPORTANT]
> Tato aplikace rychlý Start používá k identifikaci jako důvěrného klienta tajný klíč klienta. Vzhledem k tomu, že se tajný klíč klienta přidá do souborů projektu jako prostý text, doporučuje se místo toho použít certifikát namísto tajného klíče klienta před tím, než aplikaci vyberou jako produkční aplikaci. Další informace o tom, jak použít certifikát, najdete v [těchto pokynech](https://github.com/Azure-Samples/ms-identity-java-daemon/tree/master/msal-client-credential-certificate) ve stejném úložišti GitHub pro tuto ukázku, ale ve druhé složce **msal-Client-Credential-Certificate**

## <a name="more-information"></a>Další informace

### <a name="msal-java"></a>MSAL v Javě

[MSAL Java](https://github.com/AzureAD/microsoft-authentication-library-for-java) je knihovna používaná k přihlašování uživatelů a žádosti o tokeny, které se používají pro přístup k rozhraní API chráněnému platformou Microsoft identity. Jak je popsáno, tento rychlý Start žádá o tokeny pomocí vlastní identity aplikace namísto delegovaných oprávnění. Tok ověřování použitý v tomto případě se označuje jako *[tok OAuth přihlašovacími údaji klienta](v2-oauth2-client-creds-grant-flow.md)*. Další informace o tom, jak používat MSAL Java s aplikacemi démon, najdete v [tomto článku](scenario-daemon-overview.md).

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

Potom inicializujte knihovnu MSAL pomocí následujícího kódu:

```Java
IClientCredential credential = ClientCredentialFactory.createFromSecret(CLIENT_SECRET);

ConfidentialClientApplication cca =
        ConfidentialClientApplication
                .builder(CLIENT_ID, credential)
                .authority(AUTHORITY)
                .build();
```

> | Kde: |Description |
> |---------|---------|
> | `CLIENT_SECRET` | Vytvoří se tajný klíč klienta pro aplikaci na webu Azure Portal. |
> | `CLIENT_ID` | Je **ID aplikace (klienta)**, kterou jste zaregistrovali na webu Azure Portal. Tuto hodnotu najdete na stránce **Přehled** aplikace na webu Azure Portal. |
> | `AUTHORITY`    | Koncový bod služby tokenů zabezpečení pro uživatele k ověření, Obvykle `https://login.microsoftonline.com/{tenant}` pro veřejný cloud, kde {tenant} je název vašeho tenanta nebo ID tenanta.|

### <a name="requesting-tokens"></a>Žádosti o tokeny

K vyžádání tokenu pomocí identity aplikace použijte `acquireToken` metodu:

```Java
IAuthenticationResult result;
     try {
         SilentParameters silentParameters =
                 SilentParameters
                         .builder(SCOPE)
                         .build();

         // try to acquire token silently. This call will fail since the token cache does not
         // have a token for the application you are requesting an access token for
         result = cca.acquireTokenSilently(silentParameters).join();
     } catch (Exception ex) {
         if (ex.getCause() instanceof MsalException) {

             ClientCredentialParameters parameters =
                     ClientCredentialParameters
                             .builder(SCOPE)
                             .build();

             // Try to acquire a token. If successful, you should see
             // the token information printed out to console
             result = cca.acquireToken(parameters).join();
         } else {
             // Handle other exceptions accordingly
             throw ex;
         }
     }
     return result;
```

> |Kde:| Description |
> |---------|---------|
> | `SCOPE` | Obsahuje požadované obory. U důvěrných klientů by se měla použít formát podobný tomuto jako `{Application ID URI}/.default` k označení toho, že požadované obory jsou staticky definované v sadě objektů aplikace na webu Azure Portal (pro Microsoft Graph, `{Application ID URI}` které odkazují na `https://graph.microsoft.com` ). Pro vlastní webová rozhraní API `{Application ID URI}` se definuje v části **vystavení rozhraní API** v registraci aplikace na webu Azure Portal (Preview). |

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Další kroky

Další informace o aplikacích démona najdete na cílové stránce scénáře.

> [!div class="nextstepaction"]
> [Aplikace démona, která volá webová rozhraní API](scenario-daemon-overview.md)
