---
title: Hladký streaming Plugin pro Open Source Media Framework
description: Přečtěte si, jak používat modul plug-in Azure Media Services Smooth Streaming pro architekturu Adobe Open Source Media Framework.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 6068151f-b6b0-4507-9346-f03416d3d572
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 71d28a19316bf1b618ec9008a5e96a503687b202
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681961"
---
# <a name="how-to-use-the-microsoft-smooth-streaming-plugin-for-the-adobe-open-source-media-framework"></a>Jak používat microsoft smooth streaming plugin pro Adobe Open Source Media Framework  
## <a name="overview"></a>Přehled
Plugin Microsoft Smooth Streaming pro Open Source Media Framework 2.0 (SS for OSMF) rozšiřuje výchozí možnosti OSMF a přidává přehrávání obsahu Microsoft Smooth Streaming novým i stávajícím přehrávačům OSMF. Plugin také přidává funkce přehrávání plynulého streamování do přehrávání médií Strobe Media (SMP).

SS pro OSMF obsahuje dvě verze pluginu:

* Statický modul pro streamování v hladkém režimu pro OSMF (.swc)
* Modul plug-in Dynamické plynulé streamování pro OSMF (.swf)

Tento dokument předpokládá, že čtenář má obecné pracovní znalosti zásuvných modulů OSMF a OSMF. Další informace o OSMF naleznete v dokumentaci na [oficiálních stránkách OSMF](http://osmf.org/).

### <a name="smooth-streaming-plugin-for-osmf-20"></a>Hladký streaming plugin pro OSMF 2.0
Plugin podporuje načítání a přehrávání obsahu plynulého streamování na vyžádání s následujícími funkcemi:

* Plynulé přehrávání datových proudů na vyžádání (přehrávání, pozastavení, hledání, zastavení)
* Živé přehrávání plynulého streamování (přehrávání)
* Funkce živého záznamu (pauza, hledání, přehrávání DVR, přehrávání v přímém přenosu)
* Podpora video kodeků - H.264
* Podpora zvukových kodeků - AAC
* Přepínání více zvukových jazyků pomocí integrovaných rozhraní API OSMF
* Maximální výběr kvality přehrávání s integrovanými API OSMF
* Sajdkařské titulky s pluginem titulků OSMF
* Adobe&reg; &reg; Flash Player 11.4 nebo vyšší.
* Tato verze podporuje pouze OSMF 2.0.

## <a name="supported-features-and-known-issues"></a>Podporované funkce a známé problémy
Úplný seznam podporovaných funkcí, nepodporovaných funkcí a známých problémů naleznete v [tomto dokumentu](https://azure.microsoft.com/blog/microsoft-adaptive-streaming-plugin-for-osmf-update/).

## <a name="loading-the-plugin"></a>Načítání pluginu
Moduly -2F lze načítat staticky (v době kompilace) nebo dynamicky (za běhu). Plugin Smooth Streaming pro stahování OSMF obsahuje dynamické i statické verze.

* Statické načítání: Chcete-li načíst staticky, je vyžadován soubor statické knihovny (SWC). Statické pluginy jsou přidány jako odkaz na projekty a sloučit uvnitř konečného výstupního souboru v době kompilace.
* Dynamické načítání: Chcete-li načíst dynamicky, je vyžadován předkompilovaný soubor (SWF). Dynamické pluginy jsou načteny v modulu runtime a nejsou zahrnuty do výstupu projektu. (Kompilovaný výstup) Dynamické pluginy lze načítat pomocí protokolů HTTP a FILE.

Další informace o statickém a dynamickém načítání najdete na oficiální [stránce pluginu OSMF](http://osmf.org/dev/osmf/OtherPDFs/osmf_plugin_dev_guide.pdf).

### <a name="ss-for-osmf-static-loading"></a>SS pro statické zatížení OSMF
Níže uvedený fragment kódu ukazuje, jak načíst plugin SS pro OSMF staticky a přehrát základní video pomocí třídy OSMF MediaFactory. Před zahrnutím kódu SS pro OSMF se ujistěte, že odkaz na projekt obsahuje statický plugin "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swc".

```
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


### <a name="ss-for-osmf-dynamic-loading"></a>SS pro dynamické zatížení OSMF
Níže uvedený fragment kódu ukazuje, jak dynamicky načíst plugin SS pro OSMF a přehrát základní video pomocí třídy OSMF MediaFactory. Před zahrnutím kódu SS pro OSMF zkopírujte dynamický plugin "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf" do složky projektu, pokud chcete načíst pomocí protokolu FILE, nebo zkopírujte pod webový server pro zatížení HTTP. Není třeba zahrnout "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swc" v odkazech na projekt.

balíček {

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

## <a name="strobe-media--playback-with-the-ss-odmf-dynamic-plugin"></a>Přehrávání médií Stroboskopy s dynamickým pluginem SS ODMF
Dynamický plugin Smooth Streaming for OSMF je kompatibilní s [přehráváním médií Strobe (SMP).](http://osmf.org/strobe_mediaplayback.html) Pomocí pluginu SS pro OSMF můžete přidat přehrávání obsahu Smooth Streaming do SMP. Chcete-li to provést, zkopírujte "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf" pod webový server pro zatížení HTTP pomocí následujících kroků:

1. Prohlédněte si [stránku nastavení přehrávání médií Strobe](http://osmf.org/dev/2.0gm/setup.html). 
2. Nastavte src na zdroj plynulého streamování (např. http:\//devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest) 
3. Proveďte požadované změny konfigurace a klikněte na Náhled a Aktualizovat.
   
   **Poznámka:** Webový server obsahu potřebuje platnou soubor crossdomain.xml. 
4. Zkopírujte a vložte kód na jednoduchou stránku HTML pomocí oblíbeného textového editoru, například v následujícím příkladu:

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



1. Přidejte modul plug-in Smooth Streaming OSMF do kódu pro vložení a uložte jej.
   
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
2. Uložte stránku HTML a publikujte na webovém serveru. Přejděte na publikovanou webovou&reg; stránku pomocí svého oblíbeného internetového prohlížeče s přehrávačem Flash Player (Internet Explorer, Chrome, Firefox atd.).
3. Vychutnejte si obsah&reg; &reg; plynulého streamování v přehrávači Adobe Flash Player.

Více informací o celkovém vývoji OSMF naleznete na oficiální [stránce vývoje OSMF](http://osmf.org/resources.html).

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Viz také
[Microsoft Adaptive Streaming Plugin pro aktualizaci OSMF](https://azure.microsoft.com/blog/2014/10/27/microsoft-adaptive-streaming-plugin-for-osmf-update/) 

