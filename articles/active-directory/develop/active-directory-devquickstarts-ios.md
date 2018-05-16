---
title: Začínáme se službou Azure AD iOS | Microsoft Docs
description: Jak vytvářet aplikace pro iOS, který se integruje s Azure AD pro přihlášení a volání služby Azure AD chráněný rozhraní API pomocí OAuth.
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
ms.openlocfilehash: 1ceae59cca5790d9d74f72ce644e31fb0949cd49
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
---
# <a name="azure-ad-ios-getting-started"></a>Začínáme se službou Azure AD iOS
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

Azure Active Directory (Azure AD) poskytuje knihovna ověřování Active Directory nebo ADAL pro iOS klienti, kteří potřebují přístup k chráněným prostředkům. ADAL zjednodušuje proces, který vaše aplikace používá k získání přístupových tokenů. K předvedení toho, jak je snadné, v tomto článku jsme sestavit seznam úkolů Objective C aplikaci, která:

* Získá přístup k tokeny pro volání rozhraní API služby Azure AD Graph pomocí [protokol ověřování OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx).
* Vyhledá adresář pro uživatele s danou alias.

Chcete-li vytvořit úplný funkční aplikaci, je potřeba:

1. Registrace vaší aplikace s Azure AD.
2. Nainstalujte a nakonfigurujte ADAL.
3. Pomocí ADAL získat tokeny z Azure AD.

Abyste mohli začít, [stáhnout kostru aplikace](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/skeleton.zip) nebo [stažení je hotová ukázka](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip). Musíte taky klient služby Azure AD, ve kterém můžete vytvořit uživatele a zaregistrovat aplikaci. Pokud ještě nemáte klienta, [zjistěte, jak získat](active-directory-howto-tenant.md).


> [!TIP]
> Vyzkoušejte verzi preview našeho nového [portál pro vývojáře](https://identity.microsoft.com/Docs/iOS) , umožňuje zprovoznění s Azure AD za několik minut. Portál pro vývojáře vás provede procesem registrace aplikace a integraci služby Azure AD do vašeho kódu. Až budete hotoví, budete mít jednoduchou aplikaci, která může ověřit uživatele ve vašem klientovi a back-end, můžete přijímat tokeny a provést ověření. 
> 
> 

## <a name="1-determine-what-your-redirect-uri-is-for-ios"></a>1. Určit, jaké vaše přesměrování je identifikátor URI pro iOS
Bezpečně spuštění aplikace v některých scénářích jednotné přihlašování, musíte vytvořit *identifikátor URI pro přesměrování* v určitém formátu. Identifikátor URI pro přesměrování slouží k zajištění, že tokeny vrátit k správné aplikaci, která je žádali.


Formát iOS pro přesměrování je identifikátor URI:

```
<app-scheme>://<bundle-id>
```

* **aplikace – schéma** – to je zaregistrován ve vašem projektu XCode. Je, jak jiné aplikace může volat. Můžete najít to pod Info.plist -> adresa URL typy -> identifikátoru adresy URL. Pokud ještě nemáte jeden nebo více nakonfigurované byste měli vytvořit jednu.
* **id sady** -Toto je identifikátor svazku v části "identity" v nastavení projektu XCode.

Příklad pro tento kód rychlý start: ***msquickstart://com.microsoft.azureactivedirectory.samples.graph.QuickStart***

## <a name="2-register-the-directorysearcher-application"></a>2. Registrace aplikace DirectorySearcher
Chcete-li nastavit aplikaci získat tokeny, musíte nejprve zaregistrovat v klientovi služby Azure AD a udělit mu oprávnění k přístupu k Azure AD Graph API:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Na horním panelu klikněte na váš účet. V části **Directory** vyberte klienta služby Active Directory, kde chcete registrace vaší aplikace.
3. Klikněte na tlačítko **všechny služby** v levém navigačním podokně a potom vyberte **Azure Active Directory**.
4. Klikněte na tlačítko **registrace aplikace**a potom vyberte **přidat**.
5. Postupujte podle výzev a vytvořte novou **nativní klientská aplikace**.
  * **Název** aplikace popisuje vaší aplikace pro koncové uživatele.
  * **Identifikátor Uri pro přesměrování** je kombinace schématu a řetězec, Azure AD se používá k vrácení odpovědi tokenu. Zadejte hodnotu, která je specifický pro vaši aplikaci a je založena na předchozí informace o identifikátor URI přesměrování.
6. Po dokončení registrace Azure AD přiřadí vaší aplikace ID jedinečný aplikace. Tuto hodnotu budete potřebovat v další části, zkopírujte jej na kartě aplikace.
7. Z **nastavení** vyberte **požadovaných oprávnění** a pak vyberte **přidat**. Vyberte **Microsoft Graph** jako rozhraní API a poté přidejte **čtení dat adresáře** oprávnění v rámci **delegovaná oprávnění**. Toto nastaví aplikace zpracovat dotaz rozhraní Azure AD Graph API pro uživatele.

## <a name="3-install-and-configure-adal"></a>3. Instalace a konfigurace ADAL
Teď, když máte aplikaci ve službě Azure AD, můžete nainstalovat ADAL a zadejte kód, týkající se identity. Pro ADAL ke komunikaci s Azure AD budete muset poskytnout některé informace o registraci vaší aplikace.

1. Začněte tím, že přidání ADAL do projektu DirectorySearcher pomocí CocoaPods.

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

3. Nyní načtěte podfile pomocí CocoaPods. Tento krok vytvoří nový pracovní prostor XCode, který můžete načíst.

    ```
    $ pod install
    ...
    $ open QuickStart.xcworkspace
    ```

4. V projektu pro rychlý start, otevřete soubor plist `settings.plist`. Nahraďte hodnoty elementů v části tak, aby odrážela hodnoty, které jste zadali v portálu Azure. Váš kód odkazuje na tyto hodnoty vždy, když ho využívá ADAL.
  * `tenant` Je doména klienta služby Azure AD, například contoso.onmicrosoft.com.
  * `clientId` Je ID klienta aplikace, který jste zkopírovali z portálu.
  * `redirectUri` Je adresa URL přesměrování, který je zaregistrovaný v portálu.

## <a name="4-use-adal-to-get-tokens-from-azure-ad"></a>4. Získat tokeny z Azure AD pomocí ADAL
Základní princip za ADAL je, že vždy, když aplikace potřebuje přístupový token, jednoduše volá completionBlock `+(void) getToken : `, a zbývající ADAL. 

1. V `QuickStart` projekt, otevřete `GraphAPICaller.m` a najděte `// TODO: getToken for generic Web API flows. Returns a token with no additional parameters provided.` komentář horní části. Toto je, kde je předat ADAL souřadnice prostřednictvím CompletionBlock, komunikovat s Azure AD, a určit, jak pro ukládání do mezipaměti tokenů.

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

2. Teď je potřeba tento token slouží k vyhledání uživatele v grafu. Najít `// TODO: implement SearchUsersList` komentář. Tato metoda vytváří požadavek GET na Azure AD Graph API k dotazu pro uživatele, jehož UPN začíná zadaný hledaný termín. Zpracovat dotaz rozhraní Azure AD Graph API, je nutné zahrnout access_token v `Authorization` hlavičky žádosti. Toto je, kde odeslán ADAL.

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


3. Když vaše aplikace vyžaduje token voláním `getToken(...)`, ADAL pokusí vrátit token bez požadavku uživatele na přihlašovací údaje. Pokud ADAL zjistí, že uživatel musí pro přihlášení k získání tokenu, bude zobrazit dialogové okno pro přihlášení, shromažďování přihlašovacích údajů uživatele a pak se vraťte token po úspěšném ověření. Pokud není možné vrátit token z jakéhokoli důvodu ADAL, vyvolá `AdalException`.

> [!Note] 
> `AuthenticationResult` Objekt obsahuje `tokenCacheStoreItem` objekt, který můžete použít ke shromažďování informací, které vaše aplikace může být nutné. V rychlé spuštění `tokenCacheStoreItem` slouží k určení, pokud je už hotové ověřování.
>
>

## <a name="5-build-and-run-the-application"></a>5. Sestavení a spuštění aplikace
Blahopřejeme! Teď máte funkční aplikaci iOS, která můžete ověřovat uživatele, bezpečně volání webového rozhraní API pomocí standardu OAuth 2.0 a získat základní informace o uživateli. Pokud jste to ještě neudělali, nyní je čas k naplnění vašeho klienta s některými uživateli. Spusťte aplikaci rychlý start a pak se přihlaste pomocí jeden z těchto uživatelů. Hledání jiných uživatelů podle jejich UPN. Zavřete aplikaci a pak spusťte znovu. Všimněte si, že uživatelské relace zůstává beze změn.

ADAL usnadňuje všechny tyto běžné funkce identity začlenit do vaší aplikace. Se postará všechnu práci dirty, jako je Správa mezipaměti podpora protokolu OAuth, představuje uživatele pomocí uživatelského rozhraní pro přihlášení, a aktualizovat platnost tokenů. Všechny skutečně potřebujete vědět, je jednoho volání rozhraní API `getToken`.

Pro srovnání je hotová ukázka (bez vašich hodnot nastavení) zajišťuje na [Githubu](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip). 

## <a name="next-steps"></a>Další postup
Nyní se můžete přesunout dalších scénářů. Můžete se pokusit:

* [Zabezpečení webové aplikace Node.JS API s Azure AD](active-directory-devquickstarts-webapi-nodejs.md)
* Další informace [postup povolení jednotného přihlašování napříč aplikacemi v systému iOS pomocí ADAL](active-directory-sso-ios.md)  

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]

