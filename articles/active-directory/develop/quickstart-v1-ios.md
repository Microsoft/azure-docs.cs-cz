---
title: Sestavení aplikace pro iOS, která se pro přihlášení integruje do Azure AD a volá chráněná rozhraní API pomocí OAuth 2.0 | Microsoft Docs
description: Zjistěte, jak přihlásit uživatele a zavolat rozhraní API pro Microsoft Graph z aplikace pro iOS.
services: active-directory
documentationcenter: ios
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 42303177-9566-48ed-8abb-279fcf1e6ddb
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: quickstart
ms.date: 09/24/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: brandwe
ms.collection: M365-identity-device-management
ms.openlocfilehash: 53efe44f17f174073ea5ed39f94c9dc8078a9779
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56206765"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-ios-app"></a>Rychlý start: Přihlašování uživatelů a volání rozhraní Microsoft Graph API z aplikace pro iOS

[!INCLUDE [active-directory-develop-applies-v1-adal](../../../includes/active-directory-develop-applies-v1-adal.md)]

Azure Active Directory (Azure AD) poskytuje klientům iOS, kteří potřebují přístup k chráněným prostředkům, knihovnu Active Directory Authentication Library (ADAL). Knihovna ADAL zjednodušuje proces, který vaše aplikace používá k získání přístupových tokenů. 

V tomto rychlém startu sestavíte v Objective C aplikaci pro seznam úkolů, která:

* Získá pomocí ověřovacího protokolu OAuth 2.0 přístupové tokeny pro volání rozhraní Azure AD Graph API.
* Vyhledá adresář pro uživatele s daným aliasem.

Pokud chcete sestavit úplnou funkční aplikaci, budete k tomu potřebovat:

1. Zaregistrovat aplikaci v Azure AD
1. Nainstalovat a nakonfigurovat knihovnu ADAL
1. Použít knihovnu ADAL k získání tokenů z Azure AD

## <a name="prerequisites"></a>Požadavky

Než začnete, musíte splnit následující požadavky:

* Stáhnout si [kostru aplikace](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/skeleton.zip) nebo [úplnou ukázku](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip).
* Tenanta služby Azure AD, ve kterém můžete vytvářet uživatele a zaregistrovat aplikaci. Pokud ještě tenanta nemáte, [zjistěte, jak ho získat](quickstart-create-new-tenant.md).

> [!TIP]
> Vyzkoušejte [portál pro vývojáře](https://identity.microsoft.com/Docs/iOS) a zprovozněte Azure AD během pár minut. Portál pro vývojáře vás provede procesem registrace aplikace a integrace Azure AD do kódu. Jakmile skončíte, budete mít jednoduchou aplikaci, která může ověřovat uživatele v tenantovi, a back-end, který může přijímat tokeny a provádět ověření.

## <a name="step-1-determine-what-your-redirect-uri-is-for-ios"></a>Krok 1: Určit, jaké vaše přesměrování je identifikátor URI pro iOS

Pokud chcete své aplikace v určitých scénářích jednotného přihlašování spouštět zabezpečeně, musíte *identifikátor URI pro přesměrování* vytvořit v konkrétním formátu. Identifikátor URI pro přesměrování slouží k zajištění, že se tokeny vrátí do správné aplikace, která o ně požádala.

Formát identifikátoru URI pro přesměrování v iOS vypadá následovně:

```
<app-scheme>://<bundle-id>
```

* **app-scheme** – schéma aplikace je zaregistrované v projektu XCode a tímto způsobem můžou ostatní aplikace provádět volání. Schéma aplikace najdete v souboru **Info.plist > URL types > URL Identifier** (Info.plist > Typy adres URL > Identifikátor adresy URL). Pokud ještě žádné schéma aplikace nemáte, vytvořte si ho.
* **bundle-id** – je identifikátor sady prostředků, který najdete v nastavení projektu XCode v části **identity** (identita).

Příklad tohoto kódu QuickStart:

***msquickstart://com.microsoft.azureactivedirectory.samples.graph.QuickStart***

## <a name="step-2-register-the-directorysearcher-application"></a>Krok 2: Registrace aplikace DirectorySearcher

Když chcete aplikaci nastavit tak, aby získávala tokeny, musíte ji zaregistrovat v tenantovi Azure AD a udělit mu oprávnění pro přístup k rozhraní Azure AD Graph API.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Na horním panelu vyberte svůj účet. Ze seznamu **Directory** vyberte tenanta Active Directory, do kterého chcete aplikaci zaregistrovat.
3. V levém navigačním podokně vyberte **Všechny služby** a potom vyberte **Azure Active Directory**.
4. Vyberte **Registrace aplikací** a potom vyberte **Přidat**.
5. Postupujte podle pokynů a vytvořte novou **nativní** klientskou aplikaci.
    * **Název** je název aplikace, který aplikaci popisuje koncovým uživatelům.
    * **Identifikátor URI pro přesměrování** je schéma a kombinace řetězců, které Azure AD používá k vrácení odpovědí týkajících se tokenů. Zadejte hodnotu, která je specifická pro vaši aplikaci a je založená na předchozí informaci o identifikátoru URI pro přesměrování.
6. Po dokončení registrace přiřadí Azure AD aplikaci jedinečné ID aplikace. Tuto hodnotu budete potřebovat v následujících částech, proto si ji z karty aplikace zkopírujte.
7. Na stránce **Nastavení** vyberte **Požadovaná oprávnění > Přidat > Microsoft Graph** a potom v části **Delegovaná oprávnění** přidejte oprávnění **Čtení dat z adresáře**. Toto oprávnění nastaví aplikaci, aby za uživatele dotazovala rozhraní Azure AD Graph API.

## <a name="step-3-install-and-configure-adal"></a>Krok 3: Instalace a konfigurace ADAL

Když už máte v Azure AD aplikaci, můžete nainstalovat knihovnu ADAL a zapsat svůj kód související s identitou. Aby knihovna ADAL komunikovala s Azure AD, musíte jí poskytnout určité informace o registraci aplikace.

1. Začněte přidáním knihovny ADAL do projektu DirectorySearcher pomocí CocoaPods.

    ```
    $ vi Podfile
    ```
1. Do tohoto souboru podfile přidejte následující:

    ```
    source 'https://github.com/CocoaPods/Specs.git'
    link_with ['QuickStart']
    xcodeproj 'QuickStart'

    pod 'ADALiOS'
    ```

1. Soubor podfile načtěte pomocí CocoaPods. Tento krok vytvoří nový pracovní prostor XCode, který načtete.

    ```
    $ pod install
    ...
    $ open QuickStart.xcworkspace
    ```

1. V projektu QuickStart otevřete soubor `settings.plist`.
1. Nahraďte hodnoty prvků v této části stejnými hodnotami, jako jste zadali na webu Azure Portal. Váš kód bude na tyto hodnoty odkazovat vždy, když bude používat knihovnu ADAL.
    * `tenant` je doména tenanta Azure AD – například contoso.onmicrosoft.com.
    * `clientId` je ID klienta vaší aplikace, které jste zkopírovali z webu Azure Portal.
    * `redirectUri` je adresa URL pro přesměrování, kterou jste na webu Azure Portal zaregistrovali.

## <a name="step-4-use-adal-to-get-tokens-from-azure-ad"></a>Krok 4: Použití knihovny ADAL k získání tokenů z Azure AD

Základním principem knihovny ADAL je, že kdykoli aplikace potřebuje přístupový token, jednoduše zavolá příkaz CompletionBlock `+(void) getToken : ` a ADAL provede zbytek.

1. V projektu `QuickStart` otevřete `GraphAPICaller.m` a v horní části vyhledejte komentář `// TODO: getToken for generic Web API flows. Returns a token with no additional parameters provided.` (ÚKOL: Příkaz getToken pro generické toky webového rozhraní API vrátí token bez dalších poskytnutých parametrů).

    Tady prostřednictvím příkazu CompletionBlock předáte knihovně ADAL souřadnice ke komunikaci s Azure AD a určíte, jak tokeny uložit do mezipaměti.

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

2. Tento token budete muset použít, abyste v grafu vyhledali uživatele. Najděte komentář `// TODO: implement SearchUsersList` (ÚKOL: Implementovat SearchUsersList). Tato metoda vytvoří pro rozhraní Azure AD Graph API požadavek GET, aby se uživatelů dotázal, který hlavní název uživatele (UPN) začíná na daný hledaný termín. 

    Pokud se chcete rozhraní Azure AD Graph API na něco dotázat, musíte do hlavičky požadavku `Authorization` zahrnout prvek access_token. A to je úkol knihovny ADAL.

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

3. Když aplikace zažádá o token pomocí příkazu `getToken(...)`, knihovna ADAL se pokusí vrátit token bez požádání uživatele o přihlašovací údaje. Pokud knihovna ADAL zjistí, že se uživatel musí kvůli získání tokenu přihlásit, zobrazí dialogové okno pro přihlášení, shromáždí uživatelské údaje a po úspěšném ověření token vrátí. Pokud ADAL nedokáže z libovolného důvodu token vrátit, vyvolá výjimku `AdalException`.

> [!NOTE]
> Objekt `AuthenticationResult` obsahuje objekt `tokenCacheStoreItem`, který můžete použít ke shromáždění informací, které může vaše aplikace potřebovat. V projektu QuickStart se příkaz `tokenCacheStoreItem` používá k určení, zda se už ověřování dokončilo.

## <a name="step-5-build-and-run-the-application"></a>Krok 5: Sestavení a spuštění aplikace

Blahopřejeme! Máte teď funkční aplikaci pro iOS, která může ověřovat uživatele, bezpečně volat webová rozhraní API pomocí OAuth 2.0 a získat základní informace o uživateli.

Pokud jste to ještě neudělali, je teď čas naplnit tenanta několika uživateli.

1. Spusťte aplikaci QuickStart a potom se jedním z těchto uživatelů přihlaste.
1. Na základě hlavního názvu uživatele (UPN) vyhledejte další uživatele.
1. Zavřete aplikaci a spusťte ji znovu. Všimněte si, že uživatelská relace zůstala beze změny.

ADAL zahrnutí všech těchto běžných funkcí identity do aplikace usnadňuje. Udělá všechnu tu špinavou práci, jako je správa mezipaměti, podpora protokolu OAuth, zobrazení uživatelského rozhraní pro přihlášení a aktualizace tokenů s vypršenou platností, za vás. Potřebujete znát jen jedno volání rozhraní API – `getToken`.

Pro srovnání je hotová ukázka (bez vašich hodnot nastavení) k dispozici na [GitHubu](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip).

## <a name="next-steps"></a>Další postup

Můžete teď přejít na další scénáře. Jako další doporučujeme vyzkoušet tyto:

* [Zabezpečení webového rozhraní API Node.js pomocí Azure AD](quickstart-v1-nodejs-webapi.md)
* Zjistit, [jak na iOS pomocí knihovny ADAL povolit jednotné přihlašování napříč aplikacemi](howto-v1-enable-sso-ios.md).  
