---
title: Azure Active Directory v2.0 aplikace pro Android | Microsoft Docs
description: "Postup vytvoření aplikace pro Android s přihlašováním uživatelů s osobní účet Microsoft a pracovní nebo školní účty a volání rozhraní Graph API pomocí knihoven jiných dodavatelů."
services: active-directory
documentationcenter: 
author: danieldobalian
manager: mtillman
editor: 
ms.assetid: 16294c07-f27d-45c9-833f-7dbb12083794
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2017
ms.author: dadobali
ms.custom: aaddev
ms.openlocfilehash: b1c30362a7b14c8f7f0c44d911c46c491b3de3c0
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2017
---
# <a name="add-sign-in-to-an-android-app-using-a-third-party-library-with-graph-api-using-the-v20-endpoint"></a>Přidání přihlášení do aplikace pro Android pomocí rozhraní Graph API pomocí koncového bodu v2.0 knihovnu třetích stran
Platforma Microsoft identity používá otevřené standardy, jako je například OAuth2 nebo OpenID Connect. Vývojáři mohou použít žádné knihovny, které chtějí integrovat našich služeb. Což vývojářům používat naše platforma s další knihovny, jsme jste zapisují několik návody podobné následujícímu abychom ukázali, jak nakonfigurovat třetích stran knihovny pro připojení k identitu platformy Microsoft. Většina knihovny, které implementují [specifikace RFC6749 OAuth2](https://tools.ietf.org/html/rfc6749) se může připojit k Microsoft identity platform.

Aplikaci, která vytváří Tento názorný postup mohou uživatelé přihlásit k jejich organizace a poté vyhledejte sami ve své organizaci pomocí rozhraní Graph API.

Pokud jste ještě OAuth2 nebo OpenID Connect, mnohem této ukázkové konfigurace nemusí mít smysl pro vás. Doporučujeme, abyste si přečetli [2.0 protokoly - toku OAuth 2.0 autorizační kód](active-directory-v2-protocols-oauth-code.md) pozadí.

> [!NOTE]
> Některé funkce naše platformy, které mají výrazu v OAuth2 nebo OpenID Connect standardy, jako je například podmíněný přístup a správu zásad Intune, musíte používat naše otevřený zdroj knihovny Identity Microsoft Azure.
> 
> 

Koncový bod v2.0 nepodporuje všechny scénáře Azure Active Directory a funkce.

> [!NOTE]
> Pokud chcete zjistit, pokud byste měli používat koncový bod v2.0, přečtěte si informace o [v2.0 omezení](active-directory-v2-limitations.md).
> 
> 

## <a name="download-the-code-from-github"></a>Stáhněte si kód z Githubu
Kód k tomuto kurzu je udržovaný [na GitHubu](https://github.com/Azure-Samples/active-directory-android-native-oidcandroidlib-v2).  Chcete-li sledovat, můžete [stáhnout kostru aplikace jako ZIP](https://github.com/Azure-Samples/active-directory-android-native-oidcandroidlib-v2/archive/skeleton.zip) nebo tuto kostru klonovat:

```
git clone --branch skeleton git@github.com:Azure-Samples/active-directory-android-native-oidcandroidlib-v2.git
```

Ukázku můžete také právě stáhnout a začít hned:

```
git@github.com:Azure-Samples/active-directory-android-native-oidcandroidlib-v2.git
```

## <a name="register-an-app"></a>Registrace aplikace
Vytvoření nové aplikace v [portálu pro registraci aplikace](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), nebo použijte podrobný postup v [postup registrace aplikace s koncovým bodem v2.0](active-directory-v2-app-registration.md).  Zkontrolujte, že:

* Kopírování **Id aplikace** , je přiřazen do vaší aplikace, protože ho budete potřebovat brzy.
* Přidat **Mobile** platformu pro vaši aplikaci.

> Poznámka: Poskytuje portálu pro registraci aplikace **identifikátor URI pro přesměrování** hodnotu. Ale v tomto příkladu musí používat výchozí hodnotu `https://login.microsoftonline.com/common/oauth2/nativeclient`.
> 
> 

## <a name="download-the-nxoauth2-third-party-library-and-create-a-workspace"></a>Stažení knihovně NXOAuth2 třetích stran a vytvořit pracovní prostor
V tomto návodu budete používat OIDCAndroidLib z Githubu, která je OAuth2 knihovny založené na kódu Google OpenID Connect. Profil nativní aplikace implementuje a podporuje koncový bod autorizace uživatele. Toto jsou všechny věci, které budete potřebovat k integraci s platformou identity Microsoft.

Naklonujte úložiště OIDCAndroidLib do vašeho počítače.

```
git@github.com:kalemontes/OIDCAndroidLib.git
```

![androidStudio](../media/active-directory-android-native-oidcandroidlib-v2/emotes-url.png)

## <a name="set-up-your-android-studio-environment"></a>Nastavení prostředí Android Studio
1. Vytvořte nový projekt Android Studio a přijměte výchozí nastavení v průvodci.
   
    ![Vytvořit nový projekt v Android Studio](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample1.PNG)
   
    ![Cílové zařízení se systémem Android](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample2.PNG)
   
    ![Přidat aktivitu do mobile](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample3.PNG)
2. Chcete-li nastavit moduly projektu, přesunete do umístění projektu klonovaný úložišti. Můžete také vytvořit projekt a poté klonovat přímo do umístění projektu.
   
    ![Moduly projektu](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample4_1.PNG)
3. Otevřete nastavení projektu moduly pomocí místní nabídky nebo klávesové zkratky Ctrl + Alt + avní + S.
   
    ![Nastavení projektu moduly](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample4.PNG)
4. Výchozí modul aplikace odeberte, protože chcete kontejneru nastavení projektu.
   
    ![Výchozí modul aplikace](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample5.PNG)
5. Importujte moduly z klonovaného úložiště do aktuálního projektu.
   
    ![Import projektu gradle](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample6.PNG) ![vytvořit novou stránku modulu](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample7.PNG)
6. Opakujte tyto kroky `oidlib-sample` modulu.
7. Zkontrolovat závislosti oidclib `oidlib-sample` modulu.
   
    ![oidclib závislosti na modulu oidlib – ukázka](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample8.PNG)
8. Klikněte na tlačítko **OK** a počkejte, než pro synchronizaci gradle.
   
    Vaše settings.gradle by měl vypadat podobně jako:
   
    ![Snímek obrazovky settings.gradle](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample8_1.PNG)
9. Vytvoření ukázkové aplikace k Ujistěte se, že ukázkový, funguje správně.
   
    Nebudete moci ještě použít s Azure Active Directory. Budeme potřebovat nejprve nakonfigurovat některé koncové body. To je potřeba zajistit, že nemáte oprávnění ke Android Studio než začneme přizpůsobení ukázkovou aplikaci.
10. Sestavení a spuštění `oidlib-sample` jako cíl v Android Studio.
    
    ![Průběh sestavení oidlib – ukázka](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample9.png)
11. Odstranit `app ` adresáře, které bylo při odebrat modul z projektu, protože Android Studio neodstraní, je pro zabezpečení.
    
    ![Struktura souborů, která obsahuje adresář aplikace](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample12.PNG)
12. Otevřete **upravit konfigurace** nabídky k odebrání spuštění konfigurace, které bylo také při odebrání modul z projektu.
    
    ![Upravit nabídku konfigurace](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample10.PNG)
    ![spustit konfigurace aplikace](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample11.PNG)

## <a name="configure-the-endpoints-of-the-sample"></a>Nakonfigurovat koncové body vzorku
Teď, když máte `oidlib-sample` spuštěna úspěšně, Pojďme upravit některé koncové body k získání tato práce s Azure Active Directory.

### <a name="configure-your-client-by-editing-the-oidcclientconfxml-file"></a>Nakonfigurujte klienta tak, že upravíte soubor oidc_clientconf.xml
1. Vzhledem k tomu, že používáte OAuth2 toky pouze k získání tokenu a zavolat rozhraní Graph API, nastavení klienta jenom udělat OAuth2. OIDC vrátí se v pozdější příkladu.
   
    ```xml
        <bool name="oidc_oauth2only">true</bool>
    ```
2. Nakonfigurujte vaše ID klienta, který jste obdrželi z portálu pro registraci.
   
    ```xml
        <string name="oidc_clientId">86172f9d-a1ae-4348-aafa-7b3e5d1b36f5</string>
        <string name="oidc_clientSecret"></string>
    ```
3. Nakonfigurujte váš identifikátor URI pro přesměrování jedna níže.
   
    ```xml
        <string name="oidc_redirectUrl">https://login.microsoftonline.com/common/oauth2/nativeclient</string>
    ```
4. Konfigurace vaší obory, které potřebujete k přístup k rozhraní Graph API.
   
    ```xml
        <string-array name="oidc_scopes">
            <item>openid</item>
            <item>https://graph.microsoft.com/User.Read</item>
            <item>offline_access</item>
        </string-array>
    ```

`User.Read` Hodnotu `oidc_scopes` umožňuje číst základní profil podepsané v uživatele.
Další informace o všechny obory, které jsou k dispozici na [obory oprávnění Microsoft Graph](https://graph.microsoft.io/docs/authorization/permission_scopes).

Pokud chcete o vysvětlení `openid` nebo `offline_access` jako obory v OpenID Connect, najdete v části [2.0 protokoly - toku OAuth 2.0 autorizační kód](active-directory-v2-protocols-oauth-code.md).

### <a name="configure-your-client-endpoints-by-editing-the-oidcendpointsxml-file"></a>Nakonfigurovat koncové body klienta úpravou souboru oidc_endpoints.xml
* Otevřete `oidc_endpoints.xml` souboru a proveďte následující změny:
  
    ```xml
    <!-- Stores OpenID Connect provider endpoints. -->
    <resources>
        <string name="op_authorizationEnpoint">https://login.microsoftonline.com/common/oauth2/v2.0/authorize</string>
        <string name="op_tokenEndpoint">https://login.microsoftonline.com/common/oauth2/v2.0/token</string>
        <string name="op_userInfoEndpoint">https://www.example.com/oauth2/userinfo</string>
        <string name="op_revocationEndpoint">https://www.example.com/oauth2/revoketoken</string>
    </resources>
    ```

Tyto koncové body nikdy změnit v případě, že používáte OAuth2 jako protokol.

> [!NOTE]
> Koncové body pro `userInfoEndpoint` a `revocationEndpoint` nejsou aktuálně podporovány službou Azure Active Directory. Pokud tyto výchozí hodnotou example.com, budete upozorněni, že nejsou k dispozici ve vzorku :-)
> 
> 

## <a name="configure-a-graph-api-call"></a>Konfigurace volání rozhraní Graph API
* Otevřete `HomeActivity.java` souboru a proveďte následující změny:
  
    ```Java
       //TODO: set your protected resource url
        private static final String protectedResUrl = "https://graph.microsoft.com/v1.0/me/";
    ```

Zde jednoduché volání rozhraní Graph API vrací naše informace.

Ty jsou všechny změny, které musíte udělat. Spustit `oidlib-sample` aplikace a klikněte na tlačítko **přihlášení**.

Když jste úspěšně ověřen, vyberte **požadavku chráněných prostředků** tlačítko můžete otestovat volání rozhraní Graph API.

## <a name="get-security-updates-for-our-product"></a>Získejte bezpečnostní aktualizace našich produktů
Doporučujeme vám získávat oznámení o bezpečnostní incidenty v oblasti navštivte stránky [Web Security TechCenter](https://technet.microsoft.com/security/dd252948) a odběr Security Advisory Alerts.

