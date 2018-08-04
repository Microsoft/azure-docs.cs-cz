---
title: Začínáme se službou Azure AD iOS | Dokumentace Microsoftu
description: Jak vytvářet aplikace pro iOS, která se integruje s Azure AD pro přihlášení a volání služby Azure AD chráněné rozhraní API pomocí OAuth.
services: active-directory
documentationcenter: ios
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 42303177-9566-48ed-8abb-279fcf1e6ddb
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 04/30/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: jmprieur
ms.openlocfilehash: cc85dbe4bc1d207f18381e2a6d85461db229d6ea
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/03/2018
ms.locfileid: "39506595"
---
# <a name="azure-ad-ios-getting-started"></a>Začínáme se službou Azure AD iOS
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

Azure Active Directory (Azure AD) poskytuje Active Directory Authentication Library nebo ADAL pro iOS klienty, kteří potřebují přístup k chráněným prostředkům. ADAL zjednodušuje proces, který vaše aplikace používá k získání přístupových tokenů. K předvedení jak snadné to je v tomto článku jsme integrovali aplikaci seznamu úkolů Objective C, který:

* Získá přístupové tokeny pro volání rozhraní Azure AD Graph API s použitím [ověřovací protokol OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx).
* Vyhledá v adresáři uživatele pomocí daného aliasu.

Pokud chcete sestavit kompletní funkční aplikaci, budete muset:

1. Registrace aplikace v Azure AD.
2. Nainstalujte a nakonfigurujte ADAL.
3. Používají knihovnu ADAL k získání tokenů z Azure AD.

Abyste mohli začít, [stáhnout kostru aplikace](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/skeleton.zip) nebo [stáhnout úplnou vzorovou](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip). Budete také potřebovat tenanta služby Azure AD, ve kterém můžete vytvářet uživatele a zaregistrovat aplikaci. Pokud ještě nemáte tenanta, [zjistěte, jak získat](quickstart-create-new-tenant.md).


> [!TIP]
> Vyzkoušení verze preview nového [portál pro vývojáře](https://identity.microsoft.com/Docs/iOS) , který pomáhá začít pracovat s Azure AD během několika minut. Portál pro vývojáře vás provede procesem registrace aplikace a integrace Azure AD do kódu. Až budete hotovi, budete mít jednoduchou aplikaci, která může ověřit uživatele ve vašem tenantovi, a back-end, který může přijímat tokeny a provést ověření. 
> 
> 

## <a name="1-determine-what-your-redirect-uri-is-for-ios"></a>1. Určit, jaké vaše přesměrování je identifikátor URI pro iOS
Bezpečné spuštění aplikace v některých scénářích jednotného přihlašování, musíte vytvořit *identifikátor URI pro přesměrování* v určitém formátu. Identifikátor URI pro přesměrování se používá k zajištění, že tokeny vrátí do správné aplikace, která se zobrazí výzva pro ně.


Formát iOS přesměrování je identifikátor URI:

```
<app-scheme>://<bundle-id>
```

* **aplikace – schéma** – to je zaregistrovaný ve vašem projektu XCode. Je to, jak další aplikace může volat. Zjistíte to v souboru Info.plist -> typy adres URL -> identifikátor adresy URL. Pokud ještě nemáte jeden nebo více nakonfigurovali byste měli vytvořit jeden.
* **id sady prostředků** – Toto je identifikátor sady prostředků v části "identity" v nastavení projektu XCode.

Příklad pro tento kód rychlý start: ***msquickstart://com.microsoft.azureactivedirectory.samples.graph.QuickStart***

## <a name="2-register-the-directorysearcher-application"></a>2. Registrace aplikace DirectorySearcher
Chcete-li nastavit aplikaci k získání tokenů, musíte nejprve zaregistrovat ve vašem tenantovi Azure AD a udělit mu oprávnění pro přístup k Azure AD Graph API:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Na horním panelu klikněte na svůj účet. V části **Directory** , zvolte tenanta Active Directory, ve které chcete zaregistrovat aplikaci.
3. Klikněte na tlačítko **všechny služby** v levém navigačním podokně a pak vyberte **Azure Active Directory**.
4. Klikněte na tlačítko **registrace aplikací**a pak vyberte **přidat**.
5. Postupujte podle výzev a vytvořte nový **nativní klientská aplikace**.
  * **Název** aplikace popíše aplikaci uživatelům.
  * **Identifikátor Uri pro přesměrování** je kombinace schématu a řetězec, který používá Azure AD k vracení odpovědí na tokeny. Zadejte hodnotu, která je specifická pro vaši aplikaci a je založená na předchozí informace o identifikátor URI přesměrování.
6. Po dokončení registrace Azure AD přiřadí vaší aplikaci jedinečné ID. Tuto hodnotu budete potřebovat v následujících částech, proto zkopírujte ho na kartě aplikace.
7. Z **nastavení** stránce **požadovaná oprávnění** a pak vyberte **přidat**. Vyberte **Microsoft Graphu** jako rozhraní API a potom přidat **čtení dat adresáře** oprávnění v rámci **delegovaná oprávnění**. Tím se nastaví aplikaci k dotazování Azure AD Graph API pro uživatele.

## <a name="3-install-and-configure-adal"></a>3. Instalace a konfigurace ADAL
Teď, když máte aplikaci ve službě Azure AD, můžete nainstalovat knihovnu ADAL a napsat kód souvisejícím s identitou. Pro ADAL ke komunikaci s Azure AD budete muset poskytnout určité informace o registraci vaší aplikace.

1. Začněte tím, že přidání ADAL DirectorySearcher projektu pomocí CocoaPods.

    ```
    $ vi Podfile
    ```
2. Do tohoto souboru podfile přidejte následující:

    ```
    source 'https://github.com/CocoaPods/Specs.git'
    link_with ['QuickStart']
    xcodeproj 'QuickStart'

    pod 'ADALiOS'
    ```

3. Nyní soubor podfile načtěte pomocí CocoaPods. Tento krok vytvoří nový pracovní prostor XCode, který načtete.

    ```
    $ pod install
    ...
    $ open QuickStart.xcworkspace
    ```

4. V projektu rychlý start, otevřete soubor plist `settings.plist`. Nahraďte hodnoty prvků v části tak, aby odrážely hodnoty, které jste zadali na webu Azure Portal. Váš kód odkazuje na tyto hodnoty pokaždé, když se používá ADAL.
  * `tenant` Je doména tenanta Azure AD, například contoso.onmicrosoft.com.
  * `clientId` Je ID klienta aplikace, které jste zkopírovali z portálu.
  * `redirectUri` Je adresa URL pro přesměrování, které jste zaregistrovali na portálu.

## <a name="4-use-adal-to-get-tokens-from-azure-ad"></a>4. Použití knihovny ADAL k získání tokenů z Azure AD
Základním principem za ADAL je, že pokaždé, když se vaše aplikace potřebuje přístupového tokenu, jednoduše volá completionBlock `+(void) getToken : `, a udělá zbytek ADAL. 

1. V `QuickStart` projekt, otevřete `GraphAPICaller.m` a vyhledejte `// TODO: getToken for generic Web API flows. Returns a token with no additional parameters provided.` komentář nahoře. To je, pokud předáte ADAL souřadnice prostřednictvím CompletionBlock, ke komunikaci s Azure AD a určit, jak pro ukládání do mezipaměti tokenů.

    ```ObjC
    +(void) getToken : (BOOL) clearCache
               parent:(UIViewController*) parent
    completionHandler:(void (^) (NSString*, NSError*))completionBlock;
    {
        AppData* data = [AppData getInstance];
        if(data.userItem){
            completionBlock(data.userItem.accessToken, nil);
            return;
        }

        ADAuthenticationError *error;
        authContext = [ADAuthenticationContext authenticationContextWithAuthority:data.authority error:&error];
        authContext.parentController = parent;
        NSURL *redirectUri = [[NSURL alloc]initWithString:data.redirectUriString];

        [ADAuthenticationSettings sharedInstance].enableFullScreen = YES;
        [authContext acquireTokenWithResource:data.resourceId
                                     clientId:data.clientId
                                  redirectUri:redirectUri
                               promptBehavior:AD_PROMPT_AUTO
                                       userId:data.userItem.userInformation.userId
                        extraQueryParameters: @"nux=1" // if this strikes you as strange it was legacy to display the correct mobile UX. You most likely won't need it in your code.
                             completionBlock:^(ADAuthenticationResult *result) {

                                  if (result.status != AD_SUCCEEDED)
                                  {
                                     completionBlock(nil, result.error);
                                  }
                                  else
                                  {
                                      data.userItem = result.tokenCacheStoreItem;
                                      completionBlock(result.tokenCacheStoreItem.accessToken, nil);
                                  }
                             }];
    }

    ```

2. Teď potřebujeme používat tento token vyhledávat uživatele v grafu. Najít `// TODO: implement SearchUsersList` komentář. Tato metoda provádí požadavek GET na Azure AD Graph API k dotazování pro uživatele, jejichž hlavní název uživatele začíná daný hledaný termín. K dotazování Azure AD Graph API, je nutné zahrnout access_token v `Authorization` záhlaví požadavku. To přichází ADAL.

    ```ObjC
    +(void) searchUserList:(NSString*)searchString
                    parent:(UIViewController*) parent
          completionBlock:(void (^) (NSMutableArray* Users, NSError* error)) completionBlock
    {
        if (!loadedApplicationSettings)
       {
            [self readApplicationSettings];
        }
        
        AppData* data = [AppData getInstance];

        NSString *graphURL = [NSString stringWithFormat:@"%@%@/users?api-version=%@&$filter=startswith(userPrincipalName, '%@')", data.taskWebApiUrlString, data.tenant, data.apiversion, searchString];

        [self craftRequest:[self.class trimString:graphURL]
                    parent:parent
         completionHandler:^(NSMutableURLRequest *request, NSError *error) {

             if (error != nil)
             {
                 completionBlock(nil, error);
             }
             else
             {

                 NSOperationQueue *queue = [[NSOperationQueue alloc]init];

                 [NSURLConnection sendAsynchronousRequest:request queue:queue completionHandler:^(NSURLResponse *response, NSData *data, NSError *error) {

                     if (error == nil && data != nil){

                         NSDictionary *dataReturned = [NSJSONSerialization JSONObjectWithData:data options:0 error:nil];

                         // We can grab the JSON node at the top to get our graph data.
                         NSArray *graphDataArray = [dataReturned objectForKey:@"value"];

                         // Don't be thrown off by the key name being "value". It really is the name of the
                         // first node. :-)

                         // Each object is a key value pair
                         NSDictionary *keyValuePairs;
                         NSMutableArray* Users = [[NSMutableArray alloc]init];

                         for(int i =0; i < graphDataArray.count; i++)
                         {
                             keyValuePairs = [graphDataArray objectAtIndex:i];

                             User *s = [[User alloc]init];
                             s.upn = [keyValuePairs valueForKey:@"userPrincipalName"];
                             s.name =[keyValuePairs valueForKey:@"givenName"];

                             [Users addObject:s];
                         }

                         completionBlock(Users, nil);
                     }
                     else
                     {
                         completionBlock(nil, error);
                     }

                }];
             }
         }];

    }

    ```


3. Pokud vaše aplikace požaduje token voláním `getToken(...)`, ADAL se pokusí vrátit token bez s žádostí o zadání přihlašovacích údajů. Pokud ADAL zjistí, že uživatel musí přihlásit a získat token, bude zobrazit dialogové okno pro přihlášení, shromažďování přihlašovacích údajů uživatele a potom vrátí token po úspěšném ověření. Když k vrácení tokenu z nějakého důvodu nedokáže ADAL, vyvolá výjimku `AdalException`.

> [!Note] 
> `AuthenticationResult` Obsahuje objekt `tokenCacheStoreItem` objekt, který můžete použít ke shromažďování informací, které vaše aplikace může potřebovat. V tomto rychlém startu `tokenCacheStoreItem` slouží k určení, pokud se už dokončilo ověřování.
>
>

## <a name="5-build-and-run-the-application"></a>5. Sestavení a spuštění aplikace
Blahopřejeme! Teď máte funkční aplikaci iOS, která můžete ověřovat uživatele, bezpečně volat webové rozhraní API s použitím OAuth 2.0 a získat základní informace o uživateli. Pokud jste tak dosud neučinili, teď je čas k naplnění vašeho tenanta s některými uživateli. Spusťte aplikaci rychlý start a pak se přihlaste pomocí jedné z těchto uživatelů. Hledání jiných uživatelů podle jejich hlavní název uživatele. Zavřete aplikaci a znovu jej spusťte. Všimněte si, že uživatelské relace zůstává beze změny.

ADAL umožňuje snadno zahrnout všechny tyto běžné funkce identity do vaší aplikace. To se postará o všechnu práci čistý, jako je Správa mezipaměti, podporu protokolu OAuth, nabízí ten samý uživatele pomocí uživatelského rozhraní pro přihlášení, a aktualizuje vypršení platnosti tokenů. Všechno, co skutečně potřebujete znát jediného volání rozhraní API je `getToken`.

Pro srovnání je hotová ukázka (bez vašich hodnot nastavení) je k dispozici na [Githubu](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip). 

## <a name="next-steps"></a>Další postup
Se teď můžete přejít k další scénáře. Můžete vyzkoušet:

* [Zabezpečení webové aplikace Node.JS API ve službě Azure AD](active-directory-devquickstarts-webapi-nodejs.md)
* Přečtěte si [postup povolení jednotného přihlašování napříč aplikacemi pro iOS pomocí knihovny ADAL](active-directory-sso-ios.md)  

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]

