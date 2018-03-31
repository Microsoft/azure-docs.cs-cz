---
title: Most iOS webové zobrazení pomocí nativní Mobile Engagement iOS SDK
description: Popisuje postup vytvoření most mezi systémem Javascript a nativní Mobile Engagement iOS SDK webového zobrazení
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: e1d6ff6f-cd67-4131-96eb-c3d6318de752
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 07b2b8be80c090ce533f31cc28910f3ce7b91f73
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="bridge-ios-webview-with-native-mobile-engagement-ios-sdk"></a>Most iOS webové zobrazení pomocí nativní Mobile Engagement iOS SDK
> [!IMPORTANT]
> Na 3/31/2018 IT vyřadí Azure Mobile Engagement. Tato stránka bude odstraněna krátce po.
> 

> [!div class="op_single_selector"]
> * [Most Android](mobile-engagement-bridge-webview-native-android.md)
> * [Most iOS](mobile-engagement-bridge-webview-native-ios.md)
> 
> 

Některé mobilní aplikace slouží jako hybridní aplikace, kde je aplikace vyvinuté pomocí nativní aplikace pro iOS jazyka Objective-C vývoj, ale některé nebo všechny obrazovky jsou vykreslovány v rámci iOS webové zobrazení. Můžete i nadále využívat Mobile Engagement iOS SDK v rámci těchto aplikací a tento kurz popisuje, jak chcete-li přejít k provedení tohoto. 

Jak toho docílit, když jsou obě nedokumentovanými dvěma způsoby:

* Nejdřív jednu je popsaný v tomto [odkaz](http://stackoverflow.com/questions/9826792/how-to-invoke-objective-c-method-from-javascript-and-send-back-data-to-javascrip) což zahrnuje registrace `UIWebViewDelegate` na vaše webové zobrazení a catch a okamžitě Storno umístění změnu provést v jazyce Javascript. 
* Druhý jeden je založena na tomto [WWDC 2013 relace](https://developer.apple.com/videos/play/wwdc2013/615), přístup, což je čisticí než první a který jsme bude následovat po této příručce. Všimněte si, že tento přístup funguje jenom v systému IOS 7 a vyšší. 

Pro iOS přemostění ukázkové, postupujte podle následujících kroků:

1. První řadě je potřeba zajistit, že jste prošli naše [kurzu Začínáme](mobile-engagement-ios-get-started.md) integrovat Mobile Engagement iOS SDK v hybridní aplikace. Volitelně můžete také povolit, testovací protokolování následujícím způsobem, aby mohli zobrazit metody SDK, jako je se spouští z webového zobrazení. 
   
        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
           ....
             [EngagementAgent setTestLogEnabled:YES];
           ....
        }
2. Nyní se ujistěte, že hybridní aplikace má na obrazovce s webové zobrazení. Můžete je přidat do `Main.storyboard` aplikace. 
3. Přidružte tento webové zobrazení s vaší **ViewController** kliknutím a přetažením webové zobrazení z scény řadiče zobrazení k `ViewController.h` upravit obrazovky, jeho umístění právě níže `@interface` řádku. 
4. Jakmile to uděláte, objeví se dialogové okno s výzvou k zadání názvu. Zadejte název ve tvaru **webové zobrazení**. Vaše `ViewController.h` soubor by měl vypadat takto:
   
        #import <UIKit/UIKit.h>
        #import "EngagementViewController.h"
   
        @interface ViewController : EngagementViewController
        @property (strong, nonatomic) IBOutlet UIWebView *webView;
   
        @end
5. Aktualizujeme `ViewController.m` , ale nejdřív vytvoříme soubor mostu, který vytvoří obálku přes některé běžně používané Mobile Engagement iOS SDK metody souboru později. Vytvořte nový soubor záhlaví s názvem **EngagementJsExports.h** které používá `JSExport` mechanismus popsané ve zmíněném [relace](https://developer.apple.com/videos/play/wwdc2013/615) vystavit metody nativní aplikace pro iOS. 
   
        #import <Foundation/Foundation.h>
        #import <JavaScriptCore/JavascriptCore.h>
   
        @protocol EngagementJsExports <JSExport>
   
        + (void) sendEngagementEvent:(NSString*) name :(NSString*)extras;
        + (void) startEngagementJob:(NSString*) name :(NSString*)extras;
        + (void) endEngagementJob:(NSString*) name;
        + (void) sendEngagementError:(NSString*) name :(NSString*)extras;
        + (void) sendEngagementAppInfo:(NSString*) appInfo;
   
        @end
   
        @interface EngagementJs : NSObject <EngagementJsExports>
   
        @end
6. Dále vytvoříme druhou část souboru, most. Vytvořte soubor s názvem **EngagementJsExports.m** který bude obsahovat implementace vytváření obálek skutečné voláním Mobile Engagement iOS SDK metod. Také Upozorňujeme, že jsme analýza `extras` předávány z webového zobrazení javascript a umístění, do `NSMutableDictionary` objekt, který má být předána s volání metod Engagement SDK.  
   
        #import <UIKit/UIKit.h>
        #import "EngagementAgent.h"
        #import "EngagementJsExports.h"
   
        @implementation EngagementJs
   
        +(void) sendEngagementEvent:(NSString*)name :(NSString*)extras {
           NSMutableDictionary* extrasInput = [self ParseExtras:extras];
           [[EngagementAgent shared] sendEvent:name extras:extrasInput];
        }
   
        + (void) startEngagementJob:(NSString*) name :(NSString*)extras {
           NSMutableDictionary* extrasInput = [self ParseExtras:extras];
           [[EngagementAgent shared] startJob:name extras:extrasInput];
        }
   
        + (void) endEngagementJob:(NSString*) name {
           [[EngagementAgent shared] endJob:name];
        }
   
        + (void) sendEngagementError:(NSString*) name :(NSString*)extras {
           NSMutableDictionary* extrasInput = [self ParseExtras:extras];
           [[EngagementAgent shared] sendError:name extras:extrasInput];
        }
   
        + (void) sendEngagementAppInfo:(NSString*) appInfo {
           NSMutableDictionary* appInfoInput = [self ParseExtras:appInfo];
           [[EngagementAgent shared] sendAppInfo:appInfoInput];
        }
   
        + (NSMutableDictionary*) ParseExtras:(NSString*) input {
           NSData *data = [input dataUsingEncoding:NSUTF8StringEncoding];
           NSError* error = nil;
           NSMutableDictionary* extras = [NSJSONSerialization JSONObjectWithData:data options:0 error:&error];
   
           return extras;
        }
   
        @end
7. Nyní jsme se vraťte k **ViewController.m** a aktualizovat ji následujícím kódem: 
   
        #import <JavaScriptCore/JavaScriptCore.h>
        #import "ViewController.h"
        #import "EngagementJsExports.h"
   
        @interface ViewController ()
   
        @end
   
        @implementation ViewController
   
        - (void)viewDidLoad
        {
           self.webView.delegate = self;
           [super viewDidLoad];
           [self loadWebView];
        }
   
        - (void)loadWebView {
           NSBundle* mainBundle = [NSBundle mainBundle];
           NSURL* htmlPage = [mainBundle URLForResource:@"LocalPage" withExtension:@"html"];
   
           NSURLRequest* urlReq = [NSURLRequest requestWithURL:htmlPage];
           [self.webView loadRequest:urlReq];
        }
   
        - (void)webViewDidFinishLoad:(UIWebView*)wv
        {
           JSContext* context = [wv valueForKeyPath:@"documentView.webView.mainFrame.javaScriptContext"];
   
           context[@"EngagementJs"] = [EngagementJs class];
        }
   
        - (void)webView:(UIWebView*)wv didFailLoadWithError:(NSError*)error
        {
           NSLog(@"Error for WEBVIEW: %@", [error description]);
        }
   
        - (void)didReceiveMemoryWarning {
           [super didReceiveMemoryWarning];
           // Dispose of any resources that can be recreated.
        }
   
        @end
8. Poznámka: následující body o **ViewController.m** souboru:
   
   * V `loadWebView` metoda, jsme načítání do místního souboru HTML s názvem **LocalPage.html** jejíž kód jsme budou vedle zkontrolovat. 
   * V `webViewDidFinishLoad` metoda, jsme metodou `JsContext` a přiřazení našeho obálkovou třídu s ním. To vám umožní volání metody SDK použitím popisovače, naše obálku **EngagementJs** z webové zobrazení. 
9. Vytvořte soubor s názvem **LocalPage.html** následujícím kódem:
   
        <!doctype html>
        <html>
           <head>
               <style type='text/css'>
                   html { font-family:Helvetica; color:#222; }
                   h1 { color:steelblue; font-size:22px; margin-top:16px; }
                   h2 { color:grey; font-size:14px; margin-top:18px; margin-bottom:0px; }
               </style>
   
               <script type="text/javascript">
   
               window.onerror = function(err)
               {
                   alert('window.onerror: ' + err);
               }
   
               function send(inputId)
               {
                   var input = document.getElementById(inputId);
                   if(input)
                   {
                       var value = input.value;
                       // Example of how extras info can be passed with the Engagement logs
                       var extras = '{"CustomerId":"MS290011"}';
                   }
   
                   if(value && value.length > 0)
                   {
                       switch(inputId)
                       {
                           case "event":
                           EngagementJs.sendEngagementEvent(value, extras);
                           break;
   
                           case "job":
                           EngagementJs.startEngagementJob(value, extras);
                           window.setTimeout(
                           function(){
                               EngagementJs.endEngagementJob(value);
                           }, 10000 );
                           break;
   
                           case "error":
                           EngagementJs.sendEngagementError(value, extras);
                           break;
   
                           case "appInfo":
                           var appInfo = '{"customer_name":"' + value + '"}';
                           EngagementJs.sendEngagementAppInfo(appInfo);
                           break;
                       }
                   }
               }
               </script>
   
           </head>
           <body>
               <h1>Bridge Tester</h1>
   
               <div id='engagement'>
   
                   <br/>
                   <h2>Event</h2>
                   <input type="text" id="event" size="35">
                   <button onclick="send('event')">Send</button>
   
                   <br/>
                   <h2>Job</h2>
                   <input type="text" id="job" size="35">
                   <button onclick="send('job')">Send</button>
   
                   <br/>
                   <h2>Error</h2>
                   <input type="text" id="error" size="35">
                   <button onclick="send('error')">Send</button
   
                   <br/>
                   <h2>AppInfo</h2>
                   <input type="text" id="appInfo" size="35">
                   <button onclick="send('appInfo')">Send</button>
   
               </div>
           </body>
        </html>
10. Vezměte na vědomí následující body týkající se souboru HTML výše:
    
    * Obsahuje sadu vstupních polí, kde můžete zadat data, která má být použit jako názvy pro události, úlohy, chyba, AppInfo. Když kliknete na tlačítko vedle sebe, Přišla žádost o jazyka JavaScript, který nakonec volá metody ze souboru most předat volání Mobile Engagement iOS SDK. 
    * Jsme jsou označování na některé statické doplňující informace o události, úlohy a k předvedení toho, jak to lze provést i chyby. Tyto doplňující informace o je odeslán jako řetězec JSON, který je-li hledat v `EngagementJsExports.m` souboru, rozdělování a předávání společně s odesílání událostí, úloh, chyb. 
    * Mobile Engagement úloha je spuštěna s názvem spustit 10 sekund a zadáte v dialogovém okně vstupní vypnout. 
    * Mobile Engagement appinfo nebo značka, předá s 'jméno_zákazníka' jako statické klíč a hodnotu, kterou jste zadali v vstup jako hodnotu pro značku. 
11. Spusťte aplikaci a zobrazí se následující. Nyní zadejte nějaký název pro událost testování takto a klikněte na **odeslat** vedle sebe. 
    
     ![][1]
12. Nyní, pokud přejdete do **monitorování** karta aplikace a naleznete v části **události -> Podrobnosti o**, zobrazí se tato událost objeví spolu s statické aplikace informace, které jsme odesílání. 
    
    ![][2]

<!-- Images. -->
[1]: ./media/mobile-engagement-bridge-webview-native-ios/sending-event.png
[2]: ./media/mobile-engagement-bridge-webview-native-ios/event-output.png
