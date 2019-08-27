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
ms.date: 08/11/2019
ms.author: sagonzal
ms.custom: aaddev
ms.openlocfilehash: de2a59f878e0c0258b0619895d8f4c8bfd0670a8
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/26/2019
ms.locfileid: "70036427"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-java-web-app"></a>Rychlý start: Přidání přihlašování do webové aplikace Java pomocí Microsoftu

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

V tomto rychlém startu se dozvíte, jak integrovat webovou aplikaci Java s platformou Microsoft identity. Vaše aplikace se přihlásí k uživateli, získá přístupový token pro volání rozhraní API Microsoft Graph a vytvoří požadavek na rozhraní Microsoft Graph API. 

Po dokončení průvodce bude aplikace přijímat přihlašovacíky osobních účtů Microsoft (včetně outlook.com, live.com a dalších) a pracovních nebo školních účtů z jakékoli společnosti nebo organizace, která používá Azure Active Directory.

![Ukazuje, jak ukázková aplikace vygenerovaná tímto rychlým startem funguje.](media/quickstart-v2-java-webapp/java-quickstart.svg)

> ## <a name="prerequisites"></a>Požadavky
> K provedení této ukázky budete potřebovat: 
> - Připojení k Internetu.
> - Funkční instalace jazyků Java a Maven.
> - Tenant Azure Active Directory (Azure AD). Další informace o tom, jak získat tenanta Azure AD, najdete v tématu [Jak získat tenanta Azure AD.](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registrace a stažení aplikace pro rychlý start
> Aplikaci pro rychlý start můžete spustit dvěma způsoby:
> * Express [Možnost 1: Zaregistrujte a automaticky nakonfigurujte svoji aplikaci a Stáhněte si ukázku kódu.](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * Zásah [Možnost 2: Zaregistrujte a manuálně nakonfigurujte svou aplikaci a ukázku kódu.](#option-2-register-and-manually-configure-your-application-and-code-sample)
> 
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Možnost 1: Zaregistrujte a automaticky nakonfigurujte svoji aplikaci a Stáhněte si ukázku kódu.
>
> 1. Přejít na [Registrace aplikací Azure Portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps).
> 1. Zadejte název vaší aplikace a Vyberte **Zaregistrovat**.
> 1. Podle pokynů stáhněte a automaticky nakonfigurujte novou aplikaci.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Možnost 2: Registrace a ruční konfigurace vaší aplikace a ukázky kódu
> 
>
> #### <a name="step-1-download-the-code-sample"></a>Krok 1: Stažení ukázky kódu
> 
> - [Stažení ukázky kódu](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip)
>
> #### <a name="step-2-open-applicationproperties"></a>Krok 2: Otevřít Application. Properties
>
> 1. Extrahujte soubor zip do místní složky.
> 1. Volitelné Pokud používáte integrované vývojové prostředí, otevřete ukázku ve svém oblíbeném integrovaném vývojovém prostředí (IDE).
> 1. Otevřete soubor *Application. Properties* . Při registraci aplikace v dalším `aad.clientId`kroku `aad.authority`budete vkládat `aad.secretKey` hodnoty pro, a.


> #### <a name="step-3-register-your-application"></a>Krok 3: Zaregistrujte svoji aplikaci.
> K registraci aplikace a ručnímu přidání registračních informací aplikace do řešení použijte následující postup:
>
> 1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
> 1. Pokud váš účet umožňuje přístup k více tenantům, vyberte svůj účet v pravém horním rohu a nastavte relaci portálu na požadovaného tenanta Azure AD.
> 1. Přejděte na stránku [Registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) Microsoft Identity Platform for Developers.
> 1. Vyberte **Nová registrace**.
> 1. Když se zobrazí stránka **Registrace aplikace**, zadejte registrační informace vaší aplikace:
>    - V části **Název** zadejte smysluplný název aplikace, který se zobrazí uživatelům aplikace, například `java-webapp`.
>    - Nyní nechejte **identifikátor URI pro přesměrování** prázdné a vyberte **Registrovat**.
> 1. Vyhledejte hodnotu **ID aplikace (klienta)** aplikace. Aktualizujte hodnotu `Enter_the_Application_Id_here` v souboru *Application. Properties* .
> 1. Vyhledejte hodnotu **ID adresáře aplikace (tenant)** . Aktualizujte hodnotu `Enter_the_Tenant_Info_Here` v souboru *Application. Properties* . 
> 1. Vyberte nabídku **ověřování** a přidejte následující informace:
>    - V případě **identifikátorů URI přesměrování**přidejte `http://localhost:8080/msal4jsamples/secure/aad` a `https://localhost:8080/msal4jsamples/graph/users`.
>    - Vyberte **Uložit**.
> 1. V nabídce vlevo vyberte **certifikáty & tajných** kódů a v části **tajné klíče klienta** klikněte na **nový tajný klíč klienta** :
>     
>    - Zadejte popis klíče (instance tajného kódu aplikace).
>    - Vyberte dobu trvání klíče **v intervalu 1 roku**.
>    - Po kliknutí na tlačítko **Přidat**se zobrazí hodnota klíče. 
>    - Zkopírujte hodnotu klíče. Otevřete soubor *Application. Properties* , který jste stáhli dříve, a aktualizujte `Enter_the_Client_Secret_Here` hodnotu s hodnotou klíče. 
>
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Krok 1: Konfigurace aplikace v Azure Portal
> Ukázku kódu pro tento rychlý Start, který funguje, je třeba:
> 1. Přidejte adresy URL odpovědi `http://localhost:8080/msal4jsamples/secure/aad` jako `https://localhost:8080/msal4jsamples/graph/users`a.
> 1. Vytvořte tajný klíč klienta.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Udělat změnu za mě]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Už nakonfigurované](media/quickstart-v2-aspnet-webapp/green-check.png) Vaše aplikace je nakonfigurovaná s těmito atributy.
> 
> #### <a name="step-2-download-the-code-sample"></a>Krok 2: Stažení ukázky kódu
> 
> - [Stažení ukázky kódu](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip)
> 
> #### <a name="step-3-configure-the-code-sample"></a>Krok 3: Konfigurace ukázky kódu 
> 
> 1. Extrahujte soubor zip do místní složky.
> 1. Pokud používáte integrované vývojové prostředí, otevřete ukázku v oblíbeném INTEGROVANÉm vývojovém prostředí (volitelné).
> 1. Otevřete soubor **Application. Properties** , který lze najít v části *Src/Main/Resources/* .
> 1. Nahraďte vlastnosti aplikace.
>   1. Vyhledejte `aad.clientId` a aktualizujte `Enter_the_Application_Id_here` hodnotu s hodnotou **ID aplikace (klienta)** aplikace, kterou jste zaregistrovali. 
>   1. Vyhledejte `aad.authority` a aktualizujte `Enter_the_Tenant_Name_Here` hodnotu s hodnotou **ID adresáře (tenant)** v registrované aplikaci.
>   1. Vyhledejte `aad.secretKey` a aktualizujte `Enter_the_Client_Secret_Here` hodnotu pomocí tajného **klíče klienta** , který jste vytvořili v části **certifikáty & tajných** kódů pro aplikaci, kterou jste zaregistrovali.

#### <a name="step-4-run-the-code-sample"></a>Krok 4: Spuštění ukázky kódu
1. Spusťte ukázku kódu a otevřete prohlížeč a přejděte na *http://localhost:8080* adresu.
1. Přední stránka obsahuje **přihlašovací** tlačítko. Klikněte na tlačítko **Přihlásit** se a přesměrujte na Azure Active Directory. Uživateli se zobrazí výzva k zadání přihlašovacích údajů.  
1. Po úspěšném ověření u Azure Active Directory budou přesměrovány na *http://localhost:8080/msal4jsamples/secure/aad* . Jsou oficiálně přihlášeni k aplikaci a stránka by měla zobrazovat informace pro přihlášený účet. Bude obsahovat také tlačítka pro: 
    - *Odhlásit*se: Vypíše z aplikace aktuálního uživatele a přesměruje je na domovskou stránku.
    - *Zobrazit uživatele*: Získá token pro Microsoft Graph a potom zavolejte Microsoft Graph s tokenem připojeným k žádosti o získání všech uživatelů v tenantovi.


## <a name="more-information"></a>Další informace

### <a name="getting-msal"></a>Získání MSAL
MSAL4J je knihovna používaná k přihlašování uživatelů a žádosti o tokeny používané pro přístup k rozhraní API chráněnému platformou Microsoft identity. Můžete přidat MSAL4J do aplikace pomocí Maven nebo Gradle pro správu závislostí tím, že provedete následující změny v souboru pom. XML nebo Build. Gradle ve vaší aplikaci. 

```
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>msal4j</artifactId>
    <version>0.5.0-preview</version>
</dependency>
```

```$xslt
compile group: 'com.microsoft.azure', name: 'msal4j', version: '0.5.0-preview'
```


### <a name="msal-initialization"></a>Inicializace Msal
Odkaz na MSAL4J můžete přidat přidáním následujícího kódu do horní části souboru, kde budete používat MSAL4J: 

```
import com.microsoft.aad.msal4j.*;
```

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
