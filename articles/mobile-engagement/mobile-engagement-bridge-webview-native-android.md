---
title: Android webové zobrazení most s nativní Mobile Engagement Android SDK
description: Popisuje postup vytvoření most mezi systémem Javascript a nativní Mobile Engagement Android SDK webového zobrazení
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: cf272f3f-2b09-41b1-b190-944cdca8bba2
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 717c0360042b8f2afd9c41f1ee97e64e95a76a2b
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="bridge-android-webview-with-native-mobile-engagement-android-sdk"></a>Android webové zobrazení most s nativní Mobile Engagement Android SDK
> [!IMPORTANT]
> Na 3/31/2018 IT vyřadí Azure Mobile Engagement. Tato stránka bude odstraněna krátce po.
> 

> [!div class="op_single_selector"]
> * [Most Android](mobile-engagement-bridge-webview-native-android.md)
> * [Most iOS](mobile-engagement-bridge-webview-native-ios.md)
> 
> 

Některé mobilní aplikace slouží jako hybridní aplikace, kde je aplikace vyvinuté pomocí nativní vývoj pro Android, ale některé nebo všechny obrazovky jsou vykreslovány v rámci Android webové zobrazení. Mobile Engagement Android SDK můžete i nadále využívat v rámci těchto aplikací a tento kurz popisuje, jak chcete-li přejít k provedení tohoto. Následující ukázkový kód podle Android dokumentaci [zde](https://developer.android.com/guide/webapps/webview.html#BindingJavaScript). Popisuje, jak tento zdokumentovaných přístup může k implementaci pro běžně používané metody Mobile Engagement Android SDK na stejný tak, aby webové zobrazení z hybridní aplikace lze také zahájit žádosti o sledování událostí, úloh, chyb, app-info při jejich potrubí pomocí naší sady SDK pro Android. 

1. První řadě je potřeba zajistit, že jste prošli naše [kurzu Začínáme](mobile-engagement-android-get-started.md) integrovat Mobile Engagement Android SDK v hybridní aplikace. Jakmile to uděláte, vaše `OnCreate` metoda bude vypadat podobně jako následující.  
   
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
   
            EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
            engagementConfiguration.setConnectionString("<Mobile Engagement Conn String>");
            EngagementAgent.getInstance(this).init(engagementConfiguration);
        }
2. Nyní se ujistěte, že hybridní aplikace má na obrazovce s webové zobrazení. Kód pro něj bude podobný následujícímu kde jsme jsou načítání místní soubor HTML **Sample.html** ve webovém zobrazení v `onCreate` metoda obrazovky. 
   
        private void SetWebView() {
            WebView myWebView = (WebView) findViewById(R.id.webview);
            myWebView.loadUrl("file:///android_asset/Sample.html");
        }
   
        protected void onCreate(Bundle savedInstanceState) {
            ...
            ...
            SetWebView();
        }
3. Nyní vytvořte most soubor s názvem **WebAppInterface** běžně vytváří obálku přes některé použít metody Mobile Engagement Android SDK, pomocí `@JavascriptInterface` postup uvedený v [Android dokumentaci](https://developer.android.com/guide/webapps/webview.html#BindingJavaScript):
   
        import android.content.Context;
        import android.os.Bundle;
        import android.util.Log;
        import android.webkit.JavascriptInterface;
   
        import com.microsoft.azure.engagement.EngagementAgent;
   
        import org.json.JSONArray;
        import org.json.JSONObject;
   
        public class WebAppInterface {
            Context mContext;
   
            /** Instantiate the interface and set the context */
            WebAppInterface(Context c) {
                mContext = c;
            }
   
            @JavascriptInterface
            public void sendEngagementEvent(String name, String extras ){
                EngagementAgent.getInstance(mContext).sendEvent(name, ParseExtras(extras));
            }
   
            @JavascriptInterface
            public void startEngagementJob(String name, String extras ){
                EngagementAgent.getInstance(mContext).startJob(name, ParseExtras(extras));
            }
   
            @JavascriptInterface
            public void endEngagementJob(String name){
                EngagementAgent.getInstance(mContext).endJob(name);
            }
   
            @JavascriptInterface
            public void sendEngagementError(String name, String extras ){
                EngagementAgent.getInstance(mContext).sendError(name, ParseExtras(extras));
            }
   
            @JavascriptInterface
            public void sendEngagementAppInfo(String appInfo){
                EngagementAgent.getInstance(mContext).sendAppInfo(ParseExtras(appInfo));
            }
   
            public Bundle ParseExtras(String input) {
                Bundle extras = new Bundle();
   
                try {
                    JSONObject jObject = new JSONObject(input);
                    extras.putString(jObject.names().getString(0),
                            jObject.get(jObject.names().getString(0)).toString());
                } catch (Exception e) {
                    e.printStackTrace();
                }
                return extras;
            }
        }  
4. Jakmile jsme vytvořili výše uvedeného souboru most, potřebujeme zajistit, že je spojen s naše webové zobrazení. K tomu dojít, budete muset upravit vaše `SetWebview` metoda, takže to vypadá takto:
   
        private void SetWebView() {
            WebView myWebView = (WebView) findViewById(R.id.webview);
            myWebView.loadUrl("file:///android_asset/Sample.html");
            WebSettings webSettings = myWebView.getSettings();
            webSettings.setJavaScriptEnabled(true);
            myWebView.addJavascriptInterface(new WebAppInterface(this), "EngagementJs");
        }
5. Ve výše uvedeném fragmentu volali jsme `addJavascriptInterface` Naše třída most přidružit naše webové zobrazení a také vytvořit popisovač názvem **EngagementJs** volat metody ze souboru most. 
6. Nyní vytvoří následující soubor s názvem **Sample.html** ve projekt ve složce s názvem **prostředky** který je načten do webové zobrazení a kde metody bude volat ze souboru most.
   
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
                      log('window.onerror: ' + err);
                    }
   
                    send = function(inputId)
                    {
                        var input = document.getElementById(inputId);
                        if(input)
                        {
                            var value = input.value;
                            // Example of how extras info can be passed with the Engagement logs
                            var extras = '{"CustomerId":"MS290011"}';
   
                            if(value && value.length > 0)
                            {
                                switch(inputId)
                                {
                                    case "event":
                                    EngagementJs.sendEngagementEvent(value, extras);
                                    break;
   
                                    case "job":
                                    EngagementJs.startEngagementJob(value, extras);
                                    window.setTimeout( function()
                                    {
                                      EngagementJs.endEngagementJob(value);
                                    }, 10000 );
                                    break;
   
                                    case "error":
                                    EngagementJs.sendEngagementError(value, extras);
                                    break;
   
                                    case "appInfo":
                                    EngagementJs.sendEngagementAppInfo({"customer_name":value});
                                    break;
                                }
                            }
                        }
                    }
                </script>
            </head>
            <body>
                <h1>Bridge Tester</h1>
                <div id='engagement'>
                    <h2>Event</h2>
                    <input type="text" id="event" size="35">
                    <button onclick="send('event')">Send</button>
   
                    <h2>Job</h2>
                    <input type="text" id="job" size="35">
                    <button onclick="send('job')">Send</button>
   
                    <h2>Error</h2>
                    <input type="text" id="error" size="35">
                    <button onclick="send('error')">Send</button>
   
                    <h2>AppInfo</h2>
                    <input type="text" id="appInfo" size="35">
                    <button onclick="send('appInfo')">Send</button>
                </div>
            </body>
        </html>
7. Vezměte na vědomí následující body týkající se souboru HTML výše:
   
   * Obsahuje sadu vstupních polí, kde můžete zadat data, která má být použit jako názvy pro události, úlohy, chyba, AppInfo. Když kliknete na tlačítko vedle sebe, Přišla žádost o jazyka JavaScript, který nakonec volá metody ze souboru most předat volání Mobile Engagement Android SDK. 
   * Jsme jsou označování na některé statické doplňující informace o události, úlohy a k předvedení toho, jak to lze provést i chyby. Tyto doplňující informace o je odeslán jako řetězec JSON, který je-li hledat v `WebAppInterface` souboru, analyzovat a umístí Android `Bundle` a je předána společně s odesílání událostí, úloh, chyb. 
   * Mobile Engagement úloha je spuštěna s názvem spustit 10 sekund a zadáte v dialogovém okně vstupní vypnout. 
   * Mobile Engagement appinfo nebo značka, předá s 'jméno_zákazníka' jako statické klíč a hodnotu, kterou jste zadali v vstup jako hodnotu pro značku. 
8. Spusťte aplikaci a zobrazí se následující. Nyní zadejte nějaký název pro událost testování takto a klikněte na **odeslat** pod ním. 
   
    ![][1]
9. Nyní, pokud přejdete do **monitorování** karta aplikace a naleznete v části **události -> Podrobnosti o**, zobrazí se tato událost objeví spolu s statické aplikace informace, které jsme odesílání. 
   
   ![][2]

<!-- Images. -->
[1]: ./media/mobile-engagement-bridge-webview-native-android/sending-event.png
[2]: ./media/mobile-engagement-bridge-webview-native-android/event-output.png
