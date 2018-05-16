---
title: Přidání přihlášení do aplikace pro iOS pomocí koncového bodu v2.0 Azure AD | Microsoft Docs
description: Jak sestavit aplikaci pro iOS, který se přihlásí uživatele s i osobní účet Microsoft a pracovní nebo školní účty pomocí knihovny třetích stran.
services: active-directory
author: CelesteDG
manager: mtillman
ms.assetid: fd3603c0-42f7-438c-87b5-a52d20d6344b
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 01/07/2017
ms.author: celested
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 7476417e6585976ea2404a83602a6d9aa77d9c7a
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
---
# <a name="add-sign-in-to-an-ios-app-using-a-third-party-library-with-graph-api-using-the-v20-endpoint"></a>Přidání přihlášení k aplikaci pro iOS pomocí rozhraní Graph API pomocí koncového bodu v2.0 knihovnu třetích stran
Platforma Microsoft identity používá otevřené standardy, jako je například OAuth2 nebo OpenID Connect. Vývojáři mohou použít žádné knihovny, které chtějí integrovat našich služeb. Což vývojářům používat naše platforma s další knihovny, jsme jste zapisují několik návody podobné následujícímu abychom ukázali, jak nakonfigurovat třetích stran knihovny pro připojení k identitu platformy Microsoft. Většina knihovny, které implementují [specifikace RFC6749 OAuth2](https://tools.ietf.org/html/rfc6749) se může připojit k Microsoft identity platform.

Aplikaci, která vytváří Tento názorný postup mohou uživatelé přihlásit k jejich organizace a poté vyhledejte ostatními v organizaci pomocí rozhraní Graph API.

Pokud jste ještě OAuth2 nebo OpenID Connect, mnohem této ukázkové konfigurace nemusí mít smysl pro vás. Doporučujeme, abyste si přečetli [v2.0 protokoly - toku OAuth 2.0 autorizační kód](active-directory-v2-protocols-oauth-code.md) pozadí.

> [!NOTE]
> Některé funkce naše platformy, které mají výrazu v OAuth2 nebo OpenID Connect standardy, jako je například podmíněný přístup a správu zásad Intune, musíte používat naše otevřený zdroj knihovny Identity Microsoft Azure.
> 
> 

Koncový bod v2.0 nepodporuje všechny scénáře Azure Active Directory a funkce.

> [!NOTE]
> Pokud chcete zjistit, pokud byste měli používat koncový bod v2.0, přečtěte si informace o [v2.0 omezení](active-directory-v2-limitations.md).
> 
> 

## <a name="download-code-from-github"></a>Stáhněte si kód z Githubu
Kód k tomuto kurzu je udržovaný [na GitHubu](https://github.com/Azure-Samples/active-directory-ios-native-nxoauth2-v2). Chcete-li sledovat, můžete [stáhnout kostru aplikace jako ZIP](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/skeleton.zip) nebo tuto kostru klonovat:

```
git clone --branch skeleton git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-v2.git
```

Ukázku můžete také právě stáhnout a začít hned:

```
git clone git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-v2.git
```

## <a name="register-an-app"></a>Registrace aplikace
Vytvoření nové aplikace v [portálu pro registraci aplikace](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), nebo použijte podrobný postup v [postup registrace aplikace s koncovým bodem v2.0](active-directory-v2-app-registration.md). Zkontrolujte, že:

* Kopírování **Id aplikace** , je přiřazen do vaší aplikace, protože ho budete potřebovat brzy.
* Přidat **Mobile** platformu pro vaši aplikaci.
* Kopírování **identifikátor URI pro přesměrování** z portálu. Musíte použít výchozí hodnotu `urn:ietf:wg:oauth:2.0:oob`.

## <a name="download-the-third-party-nxoauth2-library-and-create-a-workspace"></a>Stažení knihovně NXOAuth2 třetích stran a vytvořit pracovní prostor
V tomto návodu budete používat OAuth2Client z Githubu, která je již OAuth2 knihovny pro Mac OS X a iOS (kakao a kakao touch). Tahle knihovna je založena na konceptu 10 specifikace OAuth2. Profil nativní aplikace implementuje a podporuje koncový bod autorizace uživatele. Toto jsou všechny věci, které budete potřebovat k integraci s platformou identity Microsoft.

### <a name="add-the-library-to-your-project-by-using-cocoapods"></a>Do projektu přidejte knihovny pomocí CocoaPods
CocoaPods je správce závislostí pro projekty Xcode. Automaticky spravuje předchozí kroky instalace.

```
$ vi Podfile
```
1. Do tohoto souboru podfile přidejte následující:
   
    ```
     platform :ios, '8.0'
   
     target 'QuickStart' do
   
     pod 'NXOAuth2Client'
   
     end
    ```
2. Načtěte podfile pomocí CocoaPods. Tím se vytvoří nový pracovní prostor Xcode, který načtete.
   
    ```
    $ pod install
    ...
    $ open QuickStart.xcworkspace
    ```

## <a name="explore-the-structure-of-the-project"></a>Prozkoumat strukturu projektu
Pro naše projekt v kostru je nastavit následující strukturou:

* Hlavní zobrazení s UPN vyhledávání
* Zobrazení podrobností pro data o vybraného uživatele
* Přihlášení zobrazení, kde se uživatel může přihlásit k aplikaci a dotaz grafu

Jsme se přesune do různých souborů kostru přidat ověřování. Ostatní části kódu, třeba kód visual se netýkají identity, ale jsou k dispozici pro vás.

## <a name="set-up-the-settingsplst-file-in-the-library"></a>Nastavení souboru settings.plst v knihovně
* V projektu pro rychlý start, otevřete `settings.plist` souboru. Nahraďte hodnoty elementů v části tak, aby odrážela hodnoty, které jste použili na webu Azure portal. Váš kód bude odkazovat tyto hodnoty vždy, když používá knihovnu ověřování služby Active Directory.
  * `clientId` Je ID klienta aplikace, který jste zkopírovali z portálu.
  * `redirectUri` Je adresa URL přesměrování, které poskytuje portálu.

## <a name="set-up-the-nxoauth2client-library-in-your-loginviewcontroller"></a>Nastavení knihovny NXOAuth2Client ve vašem LoginViewController
Knihovna NXOAuth2Client vyžaduje, aby některé hodnoty získat nastavení. Po dokončení této úlohy můžete získal token pro volání rozhraní Graph API. Protože `LoginView` bude volána vždy, když je potřeba ověřit, má smysl put hodnoty konfigurace v souboru.

* Přidejme do některé hodnoty `LoginViewController.m` souboru nastavit kontext pro ověřování a autorizaci. Podrobnosti o hodnotách podle kódu.
  
    ```objc
    NSString *scopes = @"openid offline_access User.Read";
    NSString *authURL = @"https://login.microsoftonline.com/common/oauth2/v2.0/authorize";
    NSString *loginURL = @"https://login.microsoftonline.com/common/login";
    NSString *bhh = @"urn:ietf:wg:oauth:2.0:oob?code=";
    NSString *tokenURL = @"https://login.microsoftonline.com/common/oauth2/v2.0/token";
    NSString *keychain = @"com.microsoft.azureactivedirectory.samples.graph.QuickStart";
    static NSString * const kIDMOAuth2SuccessPagePrefix = @"session_state=";
    NSURL *myRequestedUrl;
    NSURL *myLoadedUrl;
    bool loginFlow = FALSE;
    bool isRequestBusy;
    NSURL *authcode;
    ```

Podívejme se na podrobnosti o kód.

První řetězec je pro `scopes`. `User.Read` Hodnota umožňuje číst základní profil přihlášeného uživatele.

Další informace o všechny obory, které jsou k dispozici na [obory oprávnění Microsoft Graph](https://graph.microsoft.io/docs/authorization/permission_scopes).

Pro `authURL`, `loginURL`, `bhh`, a `tokenURL`, měli byste použít hodnoty uvedeny dříve. Pokud používáte Microsoft Azure Identity knihovny open source, jsme stahují tato data můžete pomocí našeho koncový bod metadat. Náročnou práci jsme udělali za vás a tyhle hodnoty jsme extrahovali.

Hodnota `keychain` je kontejner, který knihovna NXOAuth2Client použije k vytvoření řetězce klíčů pro ukládání tokenů. Pokud chcete získat jednotné přihlašování (SSO) napříč více aplikací, můžete zadat stejný řetězce klíčů v každé z vašich aplikací a požádat o použití tohoto řetězce klíčů ve vašem nárocích Xcode. To je vysvětleno v dokumentaci Apple.

Zbývající tyto hodnoty jsou zapotřebí pro používání knihovny a vytvořit prostor k provádění hodnoty do kontextu.

### <a name="create-a-url-cache"></a>Vytvoření mezipaměti se adresa URL
Uvnitř `(void)viewDidLoad()`, která je volána vždy po načtení zobrazení, následující kód primes mezipaměti pro naše používání.

Přidejte následující kód:

```objc
- (void)viewDidLoad {
    [super viewDidLoad];
    self.loginView.delegate = self;
    [self setupOAuth2AccountStore];
    [self requestOAuth2Access];
    NSURLCache *URLCache = [[NSURLCache alloc] initWithMemoryCapacity:4 * 1024 * 1024
                                                         diskCapacity:20 * 1024 * 1024
                                                             diskPath:nil];
    [NSURLCache setSharedURLCache:URLCache];

}
```

### <a name="create-a-webview-for-sign-in"></a>Vytvořit webové zobrazení pro přihlášení
Webové zobrazení můžete požádat uživatele o dalších faktorů, jako je SMS textová zpráva (Pokud je nakonfigurovaná), nebo může vracet chybové zprávy pro uživatele. Zde budete nastavit až webové zobrazení a zapište kód pro zpracování zpětná volání, které se stane ve webovém zobrazení ze služeb identity později.

```objc
-(void)requestOAuth2Access {
    //to sign in to Microsoft APIs using OAuth2, we must show an embedded browser (UIWebView)
    [[NXOAuth2AccountStore sharedStore] requestAccessToAccountWithType:@"myGraphService"
                                   withPreparedAuthorizationURLHandler:^(NSURL *preparedURL) {
                                       //navigate to the URL returned by NXOAuth2Client

                                       NSURLRequest *r = [NSURLRequest requestWithURL:preparedURL];
                                       [self.loginView loadRequest:r];
                                   }];
}
```

### <a name="override-the-webview-methods-to-handle-authentication"></a>Přepište metody webového zobrazení pro práci s ověřováním
Co se stane, když uživatel potřebuje k přihlášení, jak je popsáno dříve říct webové zobrazení, můžete vložte následující kód.

```objc
- (void)resolveUsingUIWebView:(NSURL *)URL {

    // We get the auth token from a redirect so we need to handle that in the webview.

    if (![NSThread isMainThread]) {
        [self performSelectorOnMainThread:@selector(resolveUsingUIWebView:) withObject:URL waitUntilDone:YES];
        return;
    }

    NSURLRequest *hostnameURLRequest = [NSURLRequest requestWithURL:URL cachePolicy:NSURLRequestUseProtocolCachePolicy timeoutInterval:10.0f];
    isRequestBusy = YES;
    [self.loginView loadRequest:hostnameURLRequest];

    NSLog(@"resolveUsingUIWebView ready (status: UNKNOWN, URL: %@)", self.loginView.request.URL);
}

- (BOOL)webView:(UIWebView *)webView shouldStartLoadWithRequest:(NSURLRequest *)request navigationType:(UIWebViewNavigationType)navigationType {

    NSLog(@"webView:shouldStartLoadWithRequest: %@ (%li)", request.URL, (long)navigationType);

    // The webview is where all the communication happens. Slightly complicated.

    myLoadedUrl = [webView.request mainDocumentURL];
    NSLog(@"***Loaded url: %@", myLoadedUrl);

    //if the UIWebView is showing our authorization URL or consent URL, show the UIWebView control
    if ([request.URL.absoluteString rangeOfString:authURL options:NSCaseInsensitiveSearch].location != NSNotFound) {
        self.loginView.hidden = NO;
    } else if ([request.URL.absoluteString rangeOfString:loginURL options:NSCaseInsensitiveSearch].location != NSNotFound) {
        //otherwise hide the UIWebView, we've left the authorization flow
        self.loginView.hidden = NO;
    } else if ([request.URL.absoluteString rangeOfString:bhh options:NSCaseInsensitiveSearch].location != NSNotFound) {
        //otherwise hide the UIWebView, we've left the authorization flow
        self.loginView.hidden = YES;
        [[NXOAuth2AccountStore sharedStore] handleRedirectURL:request.URL];
    }
    else {
        self.loginView.hidden = NO;
        //read the Location from the UIWebView, this is how Microsoft APIs is returning the
        //authentication code and relation information. This is controlled by the redirect URL we chose to use from Microsoft APIs
        //continue the OAuth2 flow
       // [[NXOAuth2AccountStore sharedStore] handleRedirectURL:request.URL];
    }

    return YES;

}
```

### <a name="write-code-to-handle-the-result-of-the-oauth2-request"></a>Napište kód pro zpracování výsledku požadavku OAuth2
Následující kód bude zpracovávat redirectURL, který vrací z webové zobrazení. Pokud ověření nebylo úspěšné, bude kód akci opakujte. Knihovny mezitím, poskytne chybu, která se vám zobrazí v konzole nebo asynchronní zpracování.

```objc
- (void)handleOAuth2AccessResult:(NSString *)accessResult {

    AppData* data = [AppData getInstance];

    //parse the response for success or failure
     if (accessResult)
    //if success, complete the OAuth2 flow by handling the redirect URL and obtaining a token
     {
         [[NXOAuth2AccountStore sharedStore] handleRedirectURL:accessResult];
    } else {
        //start over
        [self requestOAuth2Access];
    }
}
```

### <a name="set-up-the-oauth-context-called-account-store"></a>Nastavit kontext OAuth (označovanou jako účet úložiště)
Zde můžete volat `-[NXOAuth2AccountStore setClientID:secret:authorizationURL:tokenURL:redirectURL:forAccountType:]` na sdíleného účtu úložiště pro každou službu, kterou chcete aplikaci, která bude mít přístup. Typ účtu je řetězec, který se používá jako identifikátor pro určité služby. Protože přistupujete k rozhraní Graph API, kód odkazuje na jej jako `"myGraphService"`. Potom nastavíte pozorovatele při nic změnu s tokenem. Po získání tokenu návratu uživatele zpět na `masterView`.

```objc
- (void)setupOAuth2AccountStore {


        AppData* data = [AppData getInstance];

    [[NXOAuth2AccountStore sharedStore] setClientID:data.clientId
                                             secret:data.secret
                                              scope:[NSSet setWithObject:scopes]
                                   authorizationURL:[NSURL URLWithString:authURL]
                                           tokenURL:[NSURL URLWithString:tokenURL]
                                        redirectURL:[NSURL URLWithString:data.redirectUriString]
                                      keyChainGroup: keychain
                                     forAccountType:@"myGraphService"];

    [[NSNotificationCenter defaultCenter] addObserverForName:NXOAuth2AccountStoreAccountsDidChangeNotification
                                                      object:[NXOAuth2AccountStore sharedStore]
                                                       queue:nil
                                                  usingBlock:^(NSNotification *aNotification) {
                                                      if (aNotification.userInfo) {
                                                          //account added, we have access
                                                          //we can now request protected data
                                                          NSLog(@"Success!! We have an access token.");
                                                          dispatch_async(dispatch_get_main_queue(),^ {

                                                              MasterViewController* masterViewController = [self.storyboard instantiateViewControllerWithIdentifier:@"masterView"];
                                                              [self.navigationController pushViewController:masterViewController animated:YES];
                                                          });
                                                      } else {
                                                          //account removed, we lost access
                                                      }
                                                  }];

    [[NSNotificationCenter defaultCenter] addObserverForName:NXOAuth2AccountStoreDidFailToRequestAccessNotification
                                                      object:[NXOAuth2AccountStore sharedStore]
                                                       queue:nil
                                                  usingBlock:^(NSNotification *aNotification) {
                                                      NSError *error = [aNotification.userInfo objectForKey:NXOAuth2AccountStoreErrorKey];
                                                      NSLog(@"Error!! %@", error.localizedDescription);
                                                  }];
}
```

## <a name="set-up-the-master-view-to-search-and-display-the-users-from-the-graph-api"></a>Nastavení hlavního zobrazení k vyhledání a zobrazení uživatele z rozhraní Graph API
Aplikaci Master-View-Controller (MVC), která zobrazuje vrácená data v mřížce je nad rámec tohoto návodu a mnoho online kurzy vysvětlují, jak vytvořit jeden. Tento kód je v kostru souboru. Však nutné řešení v této aplikaci MVC pár věcí:

* Zachycení, pokud uživatel zadá něco v poli vyhledávání
* Poskytnout objekt data zpět do MasterView, abyste ho můžete zobrazit výsledky v mřížce

Provedeme ty níže.

### <a name="add-a-check-to-see-if-youre-logged-in"></a>Přidat zkontrolujte, pokud jste přihlášeni
Aplikace nemá málo Pokud se uživatel není přihlášen, tak, aby byl inteligentní zkontrolovat, pokud je již token v mezipaměti. Pokud ne, přesměrovat na LoginView pro uživatele k přihlášení. Pokud si Vzpomínáte, nejlepší způsob, jak dělat při načtení zobrazení se má používat `viewDidLoad()` metoda, která poskytuje Apple nás.

```objc
- (void)viewDidLoad {
    [super viewDidLoad];


    NXOAuth2AccountStore *store = [NXOAuth2AccountStore sharedStore];
    NSArray *accounts = [store accountsWithAccountType:@"myGraphService"];

        if (accounts.count == 0) {

        dispatch_async(dispatch_get_main_queue(),^ {

            LoginViewController* userSelectController = [self.storyboard instantiateViewControllerWithIdentifier:@"LoginUserView"];
            [self.navigationController pushViewController:userSelectController animated:YES];
        });
        }
```

### <a name="update-the-table-view-when-data-is-received"></a>Aktualizace tabulky zobrazení při přijetí dat
Když se rozhraní Graph API vrátí data, je třeba zobrazit data. Pro jednoduchost zde je veškerý kód k aktualizaci tabulky. Můžete vložit pouze správné hodnoty v kódu standardní MVC.

```objc
#pragma mark - Table View

- (NSInteger)numberOfSectionsInTableView:(UITableView *)tableView {
    return 1;
}

- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section {

        return [upnArray count];
}

- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath {

    UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:@"TaskPrototypeCell" forIndexPath:indexPath];

    if ( cell == nil ) {
        cell = [[UITableViewCell alloc] initWithStyle:UITableViewCellStyleDefault reuseIdentifier:@"TaskPrototypeCell"];
    }

    User *user = nil;
     user = [upnArray objectAtIndex:indexPath.row];


    // Configure the cell
    cell.textLabel.text = user.name;
    [cell setAccessoryType:UITableViewCellAccessoryDisclosureIndicator];

    return cell;
}

```

### <a name="provide-a-way-to-call-the-graph-api-when-someone-types-in-the-search-field"></a>Zadejte způsob, jak zavolat rozhraní Graph API, když někdo typy v poli vyhledávání
Pokud uživatel zadá do pole hledání, budete muset shove, rozhraní Graph API. `GraphAPICaller` Třídy, které vytvoříte v následujícím kódu, která odděluje funkce vyhledávání od prezentaci. Teď můžete napsat kód, který kanály znaky vyhledávání pro rozhraní Graph API. Provedeme to tím, že poskytuje metodu s názvem `lookupInGraph`, která přebírá řetězec, který chcete vyhledat.

```objc

-(void)lookupInGraph:(NSString *)searchText {
if (searchText.length > 0) {

    };



        [GraphAPICaller searchUserList:searchText completionBlock:^(NSMutableArray* returnedUpns, NSError* error) {
            if (returnedUpns) {


                upnArray = returnedUpns;


            }
            else
            {
                UIAlertView *alertView = [[UIAlertView alloc]initWithTitle:nil message:[[NSString alloc]initWithFormat:@"Error : %@", error.localizedDescription] delegate:nil cancelButtonTitle:@"Retry" otherButtonTitles:@"Cancel", nil];

                [alertView setDelegate:self];

                dispatch_async(dispatch_get_main_queue(),^ {
                    [alertView show];
                });
            }


        }];


}
```

## <a name="write-a-helper-class-to-access-the-graph-api"></a>Zápis pomocná třída pro přístup k rozhraní Graph API
Toto je základní naše aplikace. Zatímco zbytek byl vložení kódu ve vzoru MVC výchozí od společnosti Apple, sem můžete napsat kód pro dotazování grafu jako typy uživatelů a pak vrátit tato data. Zde je kód a podrobné vysvětlení následujícího.

### <a name="create-a-new-objective-c-header-file"></a>Vytvořit nový soubor záhlaví Objective C
Název souboru `GraphAPICaller.h`a přidejte následující kód.

```objc
@interface GraphAPICaller : NSObject<NSURLConnectionDataDelegate>

+(void) searchUserList:(NSString*)searchString
       completionBlock:(void (^) (NSMutableArray*, NSError* error))completionBlock;

@end
```

Tady vidíte, že zadaná metoda přebírá řetězec a vrátí completionBlock. Tato completionBlock, jak vám může mít uhádnout, bude aktualizovat a poskytovat objekt vyplněná data v reálném čase jako hledání uživatele.

### <a name="create-a-new-objective-c-file"></a>Vytvoření nového souboru Objective C
Název souboru `GraphAPICaller.m`a přidejte následující metodu.

```objc
+(void) searchUserList:(NSString*)searchString
       completionBlock:(void (^) (NSMutableArray* Users, NSError* error)) completionBlock
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    AppData* data = [AppData getInstance];

    NSString *graphURL = [NSString stringWithFormat:@"%@%@/users", data.graphApiUrlString, data.apiversion];

    NXOAuth2AccountStore *store = [NXOAuth2AccountStore sharedStore];
    NSDictionary* params = [self convertParamsToDictionary:searchString];

    NSArray *accounts = [store accountsWithAccountType:@"myGraphService"];
    [NXOAuth2Request performMethod:@"GET"
                        onResource:[NSURL URLWithString:graphURL]
                   usingParameters:params
                       withAccount:accounts[0]
               sendProgressHandler:^(unsigned long long bytesSend, unsigned long long bytesTotal) {
                   // e.g., update a progress indicator
               }
                   responseHandler:^(NSURLResponse *response, NSData *responseData, NSError *error) {
                       // Process the response
                       if (responseData) {
                           NSError *error;
                           NSDictionary *dataReturned = [NSJSONSerialization JSONObjectWithData:responseData options:0 error:nil];
                           NSLog(@"Graph Response was: %@", dataReturned);

                           // We can grab the top most JSON node to get our graph data.
                           NSArray *graphDataArray = [dataReturned objectForKey:@"value"];

                           // Don't be thrown off by the key name being "value". It really is the name of the
                           // first node. :-)

                           //each object is a key value pair
                           NSDictionary *keyValuePairs;
                           NSMutableArray* Users = [[NSMutableArray alloc]init];

                           for(int i =0; i < graphDataArray.count; i++)
                           {
                               keyValuePairs = [graphDataArray objectAtIndex:i];

                               User *s = [[User alloc]init];
                               s.upn = [keyValuePairs valueForKey:@"userPrincipalName"];
                               s.name =[keyValuePairs valueForKey:@"displayName"];
                               s.mail =[keyValuePairs valueForKey:@"mail"];
                               s.businessPhones =[keyValuePairs valueForKey:@"businessPhones"];
                               s.mobilePhones =[keyValuePairs valueForKey:@"mobilePhone"];


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

```

Prostřednictvím této metody podrobně přejděte.

Základní tohoto kódu je v `NXOAuth2Request`, metoda, která přebírá parametry, které jste jste již definována v souboru settings.plist.

Prvním krokem je vytvoření správné volání rozhraní Graph API. Protože jsou volání `/users`, můžete určit, že připojením k rozhraní Graph API prostředku společně s verzí. Má smysl v souboru s nastavením externí uvést, protože tyto můžete změnit podle zpracovaní rozhraní API.

```objc
NSString *graphURL = [NSString stringWithFormat:@"%@%@/users", data.graphApiUrlString, data.apiversion];
```

Dále je třeba zadat parametry, které také bude poskytovat volání rozhraní Graph API. Je *velmi důležité* neumisťujte parametry v koncovém bodě prostředků, vzhledem k tomu, který se očistí pro všechny znaky vyhovující – identifikátor URI za běhu. Všechny kód dotazu je třeba zadat v parametrech.

```objc

NSDictionary* params = [self convertParamsToDictionary:searchString];
```

Možná jste si všimli volá `convertParamsToDictionary` metoda, která nebyly dosud zapsána. Pojďme se proto nyní na konci souboru:

```objc
+(NSDictionary*) convertParamsToDictionary:(NSString*)searchString
{
    NSMutableDictionary* dictionary = [[NSMutableDictionary alloc]init];

        NSString *query = [NSString stringWithFormat:@"startswith(givenName, '%@')", searchString];

           [dictionary setValue:query forKey:@"$filter"];



    return dictionary;
}

```
V dalším kroku použijeme `NXOAuth2Request` metodu vrátit data z rozhraní API ve formátu JSON.

```objc
NSArray *accounts = [store accountsWithAccountType:@"myGraphService"];
    [NXOAuth2Request performMethod:@"GET"
                        onResource:[NSURL URLWithString:graphURL]
                   usingParameters:params
                       withAccount:accounts[0]
               sendProgressHandler:^(unsigned long long bytesSend, unsigned long long bytesTotal) {
                   // e.g., update a progress indicator
               }
                   responseHandler:^(NSURLResponse *response, NSData *responseData, NSError *error) {
                       // Process the response
                       if (responseData) {
                           NSError *error;
                           NSDictionary *dataReturned = [NSJSONSerialization JSONObjectWithData:responseData options:0 error:nil];
                           NSLog(@"Graph Response was: %@", dataReturned);

                           // We can grab the top most JSON node to get our graph data.
                           NSArray *graphDataArray = [dataReturned objectForKey:@"value"];
```

Nakonec se podíváme, jak vrátit data do MasterViewController. Data se vrátí jako serializovat a je třeba deserializovat a načíst v objektu, který MainViewController spotřebovat. Pro tento účel kostru má `User.m/h` soubor, který vytvoří objekt uživatele. Můžete naplnit tohoto objektu uživatele s informacemi z grafu.

```objc
                           // We can grab the top most JSON node to get our graph data.
                           NSArray *graphDataArray = [dataReturned objectForKey:@"value"];

                           // Don't be thrown off by the key name being "value". It really is the name of the
                           // first node. :-)

                           //each object is a key value pair
                           NSDictionary *keyValuePairs;
                           NSMutableArray* Users = [[NSMutableArray alloc]init];

                           for(int i =0; i < graphDataArray.count; i++)
                           {
                               keyValuePairs = [graphDataArray objectAtIndex:i];

                               User *s = [[User alloc]init];
                               s.upn = [keyValuePairs valueForKey:@"userPrincipalName"];
                               s.name =[keyValuePairs valueForKey:@"displayName"];
                               s.mail =[keyValuePairs valueForKey:@"mail"];
                               s.businessPhones =[keyValuePairs valueForKey:@"businessPhones"];
                               s.mobilePhones =[keyValuePairs valueForKey:@"mobilePhone"];


                               [Users addObject:s];
```


## <a name="run-the-sample"></a>Spuštění ukázky
Pokud jste používali kostru nebo a potom společně s průvodce měli spustit nyní vaší aplikace. Spusťte simulátoru a klikněte na **přihlášení** k používání aplikace.

## <a name="get-security-updates-for-our-product"></a>Získejte bezpečnostní aktualizace našich produktů
Doporučujeme vám získávat oznámení o bezpečnostních incidentech navštivte stránky [Web Security TechCenter](https://technet.microsoft.com/security/dd252948) a odběr Security Advisory Alerts.

