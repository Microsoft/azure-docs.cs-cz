---
title: Modul plug-in Smooth Streaming pro open source rozhraní Media Framework
description: Naučte se používat modul plug-in Azure Media Services Smooth Streaming pro rozhraní Adobe Open Source Media Framework.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 6068151f-b6b0-4507-9346-f03416d3d572
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 70042a5c4c27ec5d163e5f05089ae50b92140c28
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2021
ms.locfileid: "103011251"
---
# <a name="how-to-use-the-microsoft-smooth-streaming-plugin-for-the-adobe-open-source-media-framework"></a>Jak používat modul plug-in Microsoft Smooth Streaming pro rozhraní Adobe Open Source Media Framework

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

## <a name="overview"></a>Přehled
Modul plug-in Microsoft Smooth Streaming pro Open Source Media Framework 2,0 (SS pro OSMF) rozšiřuje výchozí možnosti OSMF a přidá přehrávání obsahu Microsoft Smooth Streaming na nové a stávající přehrávače OSMF. Modul plug-in také přidává Smooth Streaming možnosti přehrávání do impulzu pro přehrávání médií (SMP).

SS pro OSMF obsahuje dvě verze modulu plug-in:

* Modul plug-in statického Smooth Streaming pro OSMF (. SWC)
* Modul plug-in Dynamic Smooth Streaming pro OSMF (. swf)

V tomto dokumentu se předpokládá, že čtenář má obecnou znalost OSMF a modulů plug-in OSMF. Další informace o OSMF najdete v dokumentaci k [oficiálnímu webu OSMF](http://osmf.org/).

### <a name="smooth-streaming-plugin-for-osmf-20"></a>Modul plug-in Smooth Streaming pro OSMF 2,0
Modul plug-in podporuje načítání a přehrávání obsahu Smooth Streaming na vyžádání s následujícími funkcemi:

* Přehrávání Smooth Streaming na vyžádání (přehrát, pozastavit, vyhledat, zastavit)
* Přehrávání živě Smooth Streaming (přehrávání)
* Živé funkce DVR (pozastavení, hledání, přehrávání záznamu a přechod na Live)
* Podpora pro kodeky videa – H. 264
* Podpora zvukových kodeků – AAC
* Přepínání vícejazyčného jazyka pomocí integrovaných rozhraní API OSMF
* Maximální velikost výběru kvality přehrávání s integrovanými rozhraními API OSMF
* Titulky titulků na postranení s modulem plug-in OSMF titulků
* Adobe &reg; Flash &reg; Player 11,4 nebo vyšší.
* Tato verze podporuje pouze OSMF 2,0.

## <a name="supported-features-and-known-issues"></a>Podporované funkce a známé problémy
Úplný seznam podporovaných funkcí, nepodporované funkce a známé problémy najdete v [tomto dokumentu](https://azure.microsoft.com/blog/microsoft-adaptive-streaming-plugin-for-osmf-update/).

## <a name="loading-the-plugin"></a>Načítá se modul plug-in.
Moduly plug-in OSMF lze načíst staticky (v době kompilace) nebo dynamicky (za běhu). Modul plug-in Smooth Streaming pro stahování OSMF zahrnuje dynamickou i statickou verzi.

* Statické načítání: pro statické načtení je vyžadován soubor statické knihovny (SWC). Statické moduly plug-in jsou přidány jako odkazy na projekty a sloučení v konečném výstupním souboru v době kompilace.
* Dynamické načítání: pro dynamicky načtený soubor předkompilovaného (SWF) je povinný. Dynamické moduly plug-in jsou načteny do modulu runtime a nejsou zahrnuty do výstupu projektu. (Kompilovaný výstup) Dynamické moduly plug-in lze načíst pomocí protokolů HTTP a souborů.

Další informace o statickém a dynamickém načítání najdete na stránce oficiálního [modulu plug-in OSMF](http://osmf.org/dev/osmf/OtherPDFs/osmf_plugin_dev_guide.pdf).

### <a name="ss-for-osmf-static-loading"></a>SS pro statické načtení OSMF
Následující fragment kódu ukazuje, jak načíst modul plug-in SS pro OSMF staticky a přehrát základní video pomocí třídy OSMF MediaFactory. Před zahrnutím SS pro kód OSMF Prosím zajistěte, aby odkaz na projekt zahrnoval statický modul plug-in MSAdaptiveStreamingPlugin-v 1.0.3-OSMF 2.0. SWC.

```csharp
package 
{

    import com.microsoft.azure.media.AdaptiveStreamingPluginInfo;

    import flash.display.*;
    import org.osmf.media.*;
    import org.osmf.containers.MediaContainer;
    import org.osmf.events.MediaErrorEvent;
    import org.osmf.events.MediaFactoryEvent;
    import org.osmf.events.MediaPlayerStateChangeEvent;
    import org.osmf.layout.*;



    [SWF(width="1024", height="768", backgroundColor='#405050', frameRate="25")]
    public class TestPlayer extends Sprite
    {        
        public var _container:MediaContainer;
        public var _mediaFactory:DefaultMediaFactory;
        private var _mediaPlayerSprite:MediaPlayerSprite;


        public function TestPlayer( )
        {
            stage.quality = StageQuality.HIGH;

            initMediaPlayer();

        }

        private function initMediaPlayer():void
        {

            // Create the container (sprite) for managing display and layout
            _mediaPlayerSprite = new MediaPlayerSprite();    
            _mediaPlayerSprite.addEventListener(MediaErrorEvent.MEDIA_ERROR, onPlayerFailed);
            _mediaPlayerSprite.addEventListener(MediaPlayerStateChangeEvent.MEDIA_PLAYER_STATE_CHANGE, onPlayerStateChange);
            _mediaPlayerSprite.scaleMode = ScaleMode.NONE;
            _mediaPlayerSprite.width = stage.stageWidth;
            _mediaPlayerSprite.height = stage.stageHeight;
            //Adds the container to the stage
            addChild(_mediaPlayerSprite);

            // Create a mediafactory instance
            _mediaFactory = new DefaultMediaFactory();

            // Add the listeners for PLUGIN_LOADING
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD,onPluginLoaded);
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD_ERROR, onPluginLoadFailed );

            // Load the plugin class 
            loadAdaptiveStreamingPlugin( );  

        }

        private function loadAdaptiveStreamingPlugin( ):void
        {
            var pluginResource:MediaResourceBase;

            pluginResource = new PluginInfoResource(new AdaptiveStreamingPluginInfo( )); 
            _mediaFactory.loadPlugin( pluginResource ); 
        }

        private function onPluginLoaded( event:MediaFactoryEvent ):void
        {
            // The plugin is loaded successfully.
            // Your web server needs to host a valid crossdomain.xml file to allow plugin to download Smooth Streaming files.
        loadMediaSource("http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest")

        }

        private function onPluginLoadFailed( event:MediaFactoryEvent ):void
        {
            // The plugin is failed to load ...
        }


        private function onPlayerStateChange(event:MediaPlayerStateChangeEvent) : void
        {
            var state:String;

            state =  event.state;

            switch (state)
            {
                case MediaPlayerState.LOADING: 

                    // A new source is started to load.

                    break;

                case  MediaPlayerState.READY :   
                    // Add code to deal with Player Ready when it is hit the first load after a source is loaded. 

                    break;

                case MediaPlayerState.BUFFERING :

                    break;

                case  MediaPlayerState.PAUSED :
                    break;      
                // other states ...          
            }
        }

        private function onPlayerFailed(event:MediaErrorEvent) : void
        {
            // Media Player is failed .           
        }

        private function loadMediaSource(sourceURL : String):void 
        {
            // Take an URL of SmoothStreamingSource's manifest and add it to the page.

            var resource:URLResource= new URLResource( sourceURL );

            var element:MediaElement = _mediaFactory.createMediaElement( resource );
            _mediaPlayerSprite.scaleMode = ScaleMode.LETTERBOX;
            _mediaPlayerSprite.width = stage.stageWidth;
            _mediaPlayerSprite.height = stage.stageHeight;

            // Add the media element
            _mediaPlayerSprite.media = element;
        }     

    }
}
```


### <a name="ss-for-osmf-dynamic-loading"></a>SS pro dynamické načítání OSMF
Následující fragment kódu ukazuje, jak dynamicky načíst modul plug-in SS pro OSMF a přehrát základní video pomocí třídy MediaFactory OSMF. Před zahrnutím SS pro OSMF kód zkopírujte dynamický modul plug-in "MSAdaptiveStreamingPlugin-v 1.0.3-OSMF 2.0. swf" do složky projektu, pokud chcete načíst pomocí protokolu souborů nebo zkopírovat na webovém serveru pro načtení HTTP. V odkazech na projekt není nutné zahrnout "MSAdaptiveStreamingPlugin-v 1.0.3-OSMF 2.0. SWC".

```csharp
package 
{

    import flash.display.*;
    import org.osmf.media.*;
    import org.osmf.containers.MediaContainer;
    import org.osmf.events.MediaErrorEvent;
    import org.osmf.events.MediaFactoryEvent;
    import org.osmf.events.MediaPlayerStateChangeEvent;
    import org.osmf.layout.*;
    import flash.events.Event;
    import flash.system.Capabilities;


    //Sets the size of the SWF

    [SWF(width="1024", height="768", backgroundColor='#405050', frameRate="25")]
    public class TestPlayer extends Sprite
    {        
        public var _container:MediaContainer;
        public var _mediaFactory:DefaultMediaFactory;
        private var _mediaPlayerSprite:MediaPlayerSprite;


        public function TestPlayer( )
        {
            stage.quality = StageQuality.HIGH;
            initMediaPlayer();
        }

        private function initMediaPlayer():void
        {

            // Create the container (sprite) for managing display and layout
            _mediaPlayerSprite = new MediaPlayerSprite();    
            _mediaPlayerSprite.addEventListener(MediaErrorEvent.MEDIA_ERROR, onPlayerFailed);
            _mediaPlayerSprite.addEventListener(MediaPlayerStateChangeEvent.MEDIA_PLAYER_STATE_CHANGE, onPlayerStateChange);

            //Adds the container to the stage
            addChild(_mediaPlayerSprite);

            // Create a mediafactory instance
            _mediaFactory = new DefaultMediaFactory();

            // Add the listeners for PLUGIN_LOADING
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD,onPluginLoaded);
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD_ERROR, onPluginLoadFailed );

            // Load the plugin class 
            loadAdaptiveStreamingPlugin( );  

        }

        private function loadAdaptiveStreamingPlugin( ):void
        {
            var pluginResource:MediaResourceBase;
            var adaptiveStreamingPluginUrl:String;

            // Your dynamic plugin web server needs to host a valid crossdomain.xml file to allow loading plugins.

            adaptiveStreamingPluginUrl = "http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf";
            pluginResource = new URLResource(adaptiveStreamingPluginUrl);
            _mediaFactory.loadPlugin( pluginResource ); 

        }

        private function onPluginLoaded( event:MediaFactoryEvent ):void
        {
            // The plugin is loaded successfully.

            // Your web server needs to host a valid crossdomain.xml file to allow plugin to download Smooth Streaming files.

    loadMediaSource("http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest")
        }

        private function onPluginLoadFailed( event:MediaFactoryEvent ):void
        {
            // The plugin is failed to load ...
        }


        private function onPlayerStateChange(event:MediaPlayerStateChangeEvent) : void
        {
            var state:String;

            state =  event.state;

            switch (state)
            {
                case MediaPlayerState.LOADING: 

                    // A new source is started to load.

                    break;

                case  MediaPlayerState.READY :   
                    // Add code to deal with Player Ready when it is hit the first load after a source is loaded. 

                    break;

                case MediaPlayerState.BUFFERING :

                    break;

                case  MediaPlayerState.PAUSED :
                    break;      
                // other states ...          
            }
        }

        private function onPlayerFailed(event:MediaErrorEvent) : void
        {
            // Media Player is failed .           
        }

        private function loadMediaSource(sourceURL : String):void 
        {
            // Take an URL of SmoothStreamingSource's manifest and add it to the page.

            var resource:URLResource= new URLResource( sourceURL );

            var element:MediaElement = _mediaFactory.createMediaElement( resource );
            _mediaPlayerSprite.scaleMode = ScaleMode.LETTERBOX;
            _mediaPlayerSprite.width = stage.stageWidth;
            _mediaPlayerSprite.height = stage.stageHeight;
            // Add the media element
            _mediaPlayerSprite.media = element;
        }     

    }
}
```

## <a name="strobe-media--playback-with-the-ss-odmf-dynamic-plugin"></a>Přehrávání nevracení médií pomocí dynamického modulu plug-in ODMF SS
Smooth Streaming pro dynamický modul plug-in OSMF je kompatibilní s [přehráváním médií (SMP)](https://sourceforge.net/adobe/smp/home/Strobe%20Media%20Playback/). K přidání Smooth Streaming přehrávání obsahu do SMP můžete použít modul plug-in SS pro OSMF. Uděláte to tak, že zkopírujete soubor "MSAdaptiveStreamingPlugin-v 1.0.3-OSMF 2.0. swf" pod webový server pro načtení HTTP pomocí následujících kroků:

1. Projděte si [stránku nastavení přehrávání média impulzu](http://osmf.org/dev/2.0gm/setup.html). 
2. Nastavte src na zdroj Smooth Streaming (např. http: \/ /devplatem.vo.msecnd.NET/Sintel/Sintel_H264.ISM/manifest). 
3. Proveďte požadované změny konfigurace a klikněte na náhled a aktualizovat.
   
   **Poznámka:** Webový server obsahu potřebuje platné crossdomain.xml. 
4. Zkopírujte a vložte kód na jednoduchou stránku HTML pomocí oblíbeného textového editoru, jako je například v následujícím příkladu:

    ```html
    <html>
    <body>
    <object width="920" height="640"> 
    <param name="movie" value="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf"></param>
    <param name="flashvars" value="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest &autoPlay=true"></param>
    <param name="allowFullScreen" value="true"></param>
    <param name="allowscriptaccess" value="always"></param>
    <param name="wmode" value="direct"></param>
    <embed src="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf" 
        type="application/x-shockwave-flash" 
        allowscriptaccess="always" 
        allowfullscreen="true" 
        wmode="direct" 
        width="920" 
        height="640" 
        flashvars=" src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true">
    </embed>
    </object>
    </body>
    </html>
    ```


1. Přidejte do kódu pro vložení Smooth Streaming modul plug-in OSMF a uložte ho.
   
    ```html
    <html>
    <object width="920" height="640"> 
    <param name="movie" value="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf"></param>
    <param name="flashvars" value="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true&plugin_AdaptiveStreamingPlugin=http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf&AdaptiveStreamingPlugin_retryLive=true&AdaptiveStreamingPlugin_retryInterval=10"></param>
    <param name="allowFullScreen" value="true"></param>
    <param name="allowscriptaccess" value="always"></param>
    <param name="wmode" value="direct"></param>
    <embed src="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf" 
        type="application/x-shockwave-flash" 
        allowscriptaccess="always" 
        allowfullscreen="true" 
        wmode="direct" 
        width="920" 
        height="640" 
        flashvars="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true&plugin_AdaptiveStreamingPlugin=http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf&AdaptiveStreamingPlugin_retryLive=true&AdaptiveStreamingPlugin_retryInterval=10">
    </embed>
    </object>
    </html>
    ```

2. Uložte stránku HTML a publikujte ji na webový server. Přejděte na publikovanou webovou stránku pomocí &reg; internetového prohlížeče povoleného v přehrávači Flash (Internet Explorer, Chrome, Firefox atd.).
3. Užívejte Smooth Streaming obsahu uvnitř Adobe &reg; Flash &reg; Playeru.

Další informace o obecném vývoji OSMF najdete na oficiální [stránce OSMF Development](http://osmf.org/resources.html).

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Viz také
[Modul plug-in adaptivního streamování Microsoft pro OSMF Update](https://azure.microsoft.com/blog/2014/10/27/microsoft-adaptive-streaming-plugin-for-osmf-update/) 

